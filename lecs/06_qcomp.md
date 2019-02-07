## Overview

Overarching Questions

* How to take advantage of low level hardware features?
* Do programming abstractions (for developer ease) necessarily mean performance overheads?
* How to balance high level programming with input-and-data-specific low level optimizations?

Three phases

* Templates
  * pull-based, but compiled operators.  whole query is a C/C++ file
* "Move code to the data" to the max
  * Hyper produce/consume codegen into LLVM.  C++ for data structures/access methods
* Make compilation easier
  * Make query compilation easier ala futamura projections (walk through)
  * Compiler friendly "intermediate algebras" (VooDoo, Weld, etc)

It's worth noting that a 10x over a legacy architecture is lame.  A 10x win over recent state of the art is impressive.  This paper is somewhat in-between, with 2-4x wins over Vectorwise.  That's still impressive!


## Krikellas et al

Overview

* Year after Monet/X100 paper!
  * no comparison 
* Topologically sort operator tree from bottom to root, code gen in this order ensures inputs always ready
* Compile joins, aggregates, and order-by.  
* Generic template for filter+column projection+scan/index lookup
* Per operator template -- compile expressions and plop template code out
  * Stage operator inputs by filtering and projecting, and any other preprocessing (e.g., sorting, partition)
  * Shared template across nested loops, hash-partition, sort merge join, can configure based on input characteristics. 
* Operator communication: materialize between operators 
* Each operator is a function.  A "main()" pieces them together and (de)allocates resources
* Compiler does black magic
* Page oriented execution still

Why compilation?

* remove dynamic attribute lookups (tuple.get(attr)) with direct lookups
* fixed len tuples == array offset lookups

Limitations

* Lacks Hyper-style pipelining across operators
* C++ compilation is slow, and still high performance costs

## Hyper LLVM paper

Overview

* Code template for physical operator makes sense
* Remove operator boundaries for pipelines (operators are nonblocking)
* Use produce/consume to merge pipelined code blocks
* use LLVM to reduce compilation times, and write even more optimized code.
  * more fine-grained control over registers, vs c++
* Still use DB C++ code for complex data structures, algorithms, etc
* Note
  * In the paper, they build hash table on the left, and probe using the right.  
    DataBass does the opposite.

Philosophy

* Processing is data centric.  Keep in CPU registers as long as possible.   
  Extreme version of "move code to the data"
* Push-based execution.  Notice they don't take a stand on physical layout (NSM, DSM, PAX, etc)

Pipelines and pipeline breakers

* Pipeline: sequence of operators than can keep tuple(s) in CPU registers and compute over them
* Breaker: If it takes an incoming tuple out of CPU registers
* Full Breaker: If it needs to materialize (sort, build hash table)
* Claim: "any iterator-style processing ... risks breaking the pipeline"
  * Child operator is forced to act as linear scan, even if internally it can produce more efficiently in batches.

Experiments

* Compile time: 100x faster than C++ compile time
* Performance:
  * 2-5x wins over C++ compilation due to more compact code.
* Compare locality vs MonetDB:
  * Monet is vectorized, but inefficient across operators due to full materialization. 
    * Could be artifact of BATs.  Vectorwise would be better comparison.
  * Monet has >10x branches since no pipelining
  * Monet has 2x lower branch misprediction RATE than LLVM, but more in absolute terms
  * 10x cache misses than monet
* Engineering complexity/maintainability: ??

## Query Compiler Revisited


Overview

* Produce/consume is just manually unrolling the call stack when executing the interpretor
* Can an interpretor be turned into a compiler automatically?
* The revisited paper, and many of Tarik's papers explores the duality (futamura projections)
* Excellent, compact history of query compilation

Let's take a look at two ideas that help contextualize what produce consume are actually trying to do:

#### Callback-based execution and query compilation


		data = [ dict(a=0), dict(a=1), dict(a=2)]

		def cond(row):
			return row['a'] == 1
			
		def filter_run(cb):
			def f(row):
				if cond(row):
					cb(row)
			scan_run(f)
			
		def scan_run(cb):
			for row in data:
				cb(row)

		def print_run(row):
			print row

		filter_run(print_run)

Consider when `scan_run`'s inner loop runs.  What if we inline all of the callbacks?

        # in scan:
        for row in data:
          cb(row)

Inline `cb`'s code:

        for row in data:
          if cond(row):
            cb(row)  # filter_run's callback

Inline the `cond` and `cb` functions:

        for row in data:
          if row['a'] == 1:
            print row

This is the same result as implementing produce consume.  


#### Interpretors and Query Compilation

Idea

* Interpretors are there to provide input flexibility
* But if you know what subset of the expressive space will be fixed (aka the query), then can you run the interpretor to strip out all unneeded indirection?

The basics:

        def pow(x, n):
          if n == 0:
            return 1
          return x * pow(x, n-1)

Let's say we ran `pow(2, 2)`, then there are lots of function calls etc.  The snippet below shows the nested execution trace:

        call pow(2, 2)
          if 2 == 0: pass

          arg1 = 2 - 1
          res1 = pow(2, arg1)
          call pow(2, 1)
            if 1 == 0: pass

            arg2 = 1 - 1
            res2 = pow(2, arg2)
            call pow(2, 0)
              if 0 == 0:
                return 1       # res2 = 1
            return 2 * res2    # x * res2;   res1 = 2
          return 2 * res1        # x * res1;   output is 4

That is pretty expensive, but what if we replaced x with an object that works the same way, but keeps track of all operators performed on it?

        call pow(x, 2)
          if 2 == 0: pass
          arg1 = 2-1
          res1 = pow(x, arg1)
          if 1 == 0: pass
          arg2 = 1-1
          res2 = pow(x, arg2)
          if 0 ==0:
            res2 = 1
          res1 = x * res2    # x tracks that "x * 1"
        return x * res1      # x tracks that "x * (x * 1)"

        # operations performed on x are:
          x * (x * 1)

In practice, you need to track all operations performed on x.  Something like: 

        class Int
          def __init__(self, varname):
            self.varname = varname

          def __mul__(self, y):
            varname = new_varname()
            print "%s = %s * %s" % (varname, self.varname, y.varname)
            return Int(varname)

Let's look at iterator approach

        filter(child, cond):
          for row in child:
            if cond(child):
              yield child

        scan():
          for row in accessmethod():
            yield child

