---
layout: page
---

# High Level Comments

Every paper makes claims and tells a story.  The trick is to discern the important parts from the unimportant, and to articulate why.
This involves:

* clarifying the trade-offs that affect the validity of the design decision, 
* remarking on where along the trade-off point is realistic,
* looking for potential unintended side-effects


# Ingres


Interesting bits

* "Keep it simple" philosophy is everwhere
* QUEL
* Leveraging UNIX features
* Concurrency control arguments

### QUEL

Elegant, compact language.  

* Simple syntax.  Looks like a program!
* Subsumes multiple specialized statements in SQL
  * INSERT, SELECT, CREATE, DELETE, UPDATE, WITH

        [  range variable declarations ]
        [  program statements  ]

  * note that range variables have "global" scope for the entire user program (not just a single quel statement).


For instance, a SELECT query

        RANGE T
        RANGE S
        RETREIVE INTO W(T.x + S.y, S.t)
        WHERE    T.t = S.s

Other thoughts

Neatly setup for interesting types of queries!

* recursive queries

        RANGE T
        RETREIVE INTO W(W.x + T.t)
        WHERE    W.x < 10 

* Multi-statement table declarations

        RANGE T
        RETRIEVE INTO TMP(...)
        DELETE TMP WHERE ...
        RETREIVE INTO TMP2(...)


### UNIX

Goal: leverage unix facilities

Memory management

* LRU turns out not to be best for memory management
* DB actually wants fine-grained control over in-mmemory pages
* See DBMin paper

Processes and Pipes.

* Constrained by max process sizes anyways
* Processes for parser, executor, admin/utilities
* Pipes for IPC
* Commands flow one direction, errors/data flow the other direction
* issues
  * IPC via pipes can be slow
  * Serialization/deserialization overheads
  * Shared memory better


             --------------------[commands]------------>
            |
        user     parser/rewrite     query exec     utilities
                                                        |
             <-------------------[errs/data]------------


Updates are copy on write + deferred application

* Similar to Shadow pages in System R

File system

* Relations as files (postgres adopts same methodology)
* Catalogs are just files with special permissions
  * Read only from INGRES
  * Writable only by admin
* Borrow UNIX users and permissions
* Per-file management, including clustering decisions
  * SystemR can co-cluster via links

#### Concurrency Control

Lays out the design space and comments on each option (rather matter of factly)

1. smaller than an INGRES command
  * can't make any real guarantees
2. 1 INGRESS command
3. >1 commands, no C
4. >1 commands, with C, no syscalls
  * called UDFs.  See later systems
5. >1 commands, with C and syscalls
  * Exposing via syscalls to "the world" can't really be backed out from
  * Same thing with giving user intermediate results in the flight example

Comments

* Options 4, 5 are mostly thanks to EQUEL
* Concurrency control via table locks

#### Query Execution

Tries to flatten joins/cross-products iteratively.  

Tuple Substitution

* Basically flatten a relation by turning each row into a separate query.  Pretty neat

        T contains [1], [2]

        RANGE T
        RANGE S
        WHERE T.t = S.s

        Rewrite into:

        RANGE S
        WHERE S.s = 1 OR S.s = 2

* Can play games with tiny tables (if fits into cache, memory, node, etc)
  * Broadcast joins have similar flavor


# Postgres


Background

* INGRES was retiring.  Want to work on new, exciting ideas based on RDBMS V0 (INGRES)
* Cottage industry of papers that 
  * try RDBMS on favorite app domain, 
  * didn't work, 
  * extend relational model
  * build/extend some system
* All these weird extensions won't scale, hard to manage
  * Points towards need for extensibility
* Folks are starting to talk about adding if-then type of rules to the DBMS
* Log-based recovery (ARIES) is really really complicated.
* Also avoids Fred Brooks' second system effect!
  * Thanks to a clean extensibility core.

The paper

* very colloquial in tone
* parts hard to follow


### ADT

Overview

* Good and weird ideas
  * queries as types
    * query-attributes for different rows may return incompatible schemas.  What happens?
    * What does an update query in a query-attribute mean?
  * functions as types
  * lists as types 
  * complex objects as types
  * These serve as precursors to "nested models" ala XML databases, JSON support, etc
* Challenges with ADTs
  * Access Methods (indexes, lookups, range/inequality predicates)
  * Rule-based optimization (reordering ops, negation, associativity, etc)
    * Joe's thesis: Optimization Techniques for Queries with Expensive Methods (aka UDFs)
  * UDFs and static typing/casting
    * how to represent functions.  Functions as types
* Motivating use case: GIS.  
  * how to support points, lines, polygons, line groups, etc?
  * Normal languages have them, why not the DBMS?


High level

* Create new type rect <binary format>;
* str2rect(str) --> rect binary format
* overlaps(left:rect, right:rect) --> true/false


          rect    overlaps   rect
           ^                  ^
           |      casting     | 
           |                  | 
        "0,0,2,3" overlaps "1,1,4,5"


Display object example

* objects are composed of sub-objects
* objects are rendered by transforming into a "display-list" of polygons

        name        obj                      display
        ---------------------------------------------------------
        apple       retreive (polygon.*)     f(): ... code ...
                    where id = 10
                    retreive (circle.*)
                    where id = 40
        ---------------------------------------------------------
        orange      retreive (polygon.*)     f(): ... code ...
                    where id = 10
                    retreive (line.*)
                    where id = 17 


* Idea is that each object will know how to display itself.  
  What might the display code look like?  For apple:
        
        f():
          retreive (polygon.*) where id = 0
          retreive (circle.*) where id = 40
          .. turn into a rasterized image ...
          return

* Problems
  * f() is pretty much duplicated everywhere
  * f() internally reimplements obj
* Solution: refactor aka normalize
  * Part 1: create a UDF table

        procs:
        name           code
        f()            ... code ...

  * Part 2: display is now a query attr

         name        obj                      display
        ---------------------------------------------------------
        apple       retreive (polygon.*)     exec(procs.code) 
                    where id = 10            where procs.name = 'f'
                    retreive (circle.*)
                    where id = 40
        
  * Part 3: f() calls fruits.obj 

        f(fruitname):
          exec(fruits.obj) where name = fruitname
          .. turn into a rasterized image ...
          return

Lessons

* Today:  query types aren't a thing, but we have function types
* UDFs are a good idea: move code to the data, not data to the code
* UDFs are in every database, trend of deeper integration with languages 

### Triggers, Rules, Etc

Recursive queries using *

        retreive* into T(name, mgr)
        from E, T
        WHERE E.name = 'jones' or E.mgr = T.name

* They are weird because it implements exhaustive search (BFS)
* What if you only want the row with max() of some function?
  * Turns into a search optimization problem

Triggers

* Why?  Chris Date's 6 Referential integrity cases (on update, insert, delete, what could happen?)
  * Date solved via rule-based triggers 
  * Wanted something "cleaner"
* add the "ALWAYS" modifier to a query
  * alert if bill changes

        retreive always (emp.*) where emp.name = 'bill'

  * delete if department is empty (mix of constraint and reconciliation rule)

        delete always dept
        where count(..emps in deprt..) == 0

  * when should this run?  before a change?  in lieu of a change? after a change? in what order? 
  * Couldn't get to be general enough to solve Date's cases

Views with multiple statements

        view EMP (name, desk='cheap') where age < 40
        view EMP (name, desk='good') where name = 'bigshot'

* bigshot and age predicates may overlap.  now what?

Lesson

* A major value of relational algebra is that it is an algebra with commutative and associative properties
* Forcing the dev to specify priority orderings 
  * negates much of the benefits
  * hard to reason about

### Time Travel and Portals

Time Travel/Versions

* Time travel via `Emp[tstamp, tstamp]` is elegant
  * table valid during a date range can be tricky to reason about unless version is queryable
* versions have weird update semantics

        newversion EmpTest from EMP

* updates to base relation are visible unless value was modified in the version
  * if/else statements are the source of headaches -- counter to declarative language  


Cursor++

* can read pages at a time, and go back and forward
* can delete and replace at the cursor's position
  * previously, treat as directly updating a view.  semantics issues
  * proposed: directly edit base tables.  
    * hard to reason about
    * how to propogate updates to app?  a separate cursor?  what if sequence of base edits?
    * how to represent updates?  
    * transaction semantics?  block the world?
* tied a bit too much with what app may actually want

### Stored procedures (aka compilation)

Idea

* Preregister a query, cache the plan.  The plan attribute is "hidden" from user.

        CODE(id, owner, query, plan)

* Skip parser/optimizer directly

        exec(query)
        with (arg1, arg2)
        where id = 1

* Faster path, get a direct handle to the plan.  Basically, a C api to the engine

        exec-fp(1, arg1, arg2)

### Query Processing

Three challenges

* user-defined access methods
* support query and udf attributes
* triggers/rules

Access methods

* discusses 1D case. 
* Define (in)equality operators aka ordering for a data type.
  * essentially specify comparison operators that map to euclidean space
  * transitivity, negation, etc
* Define estimator for  
  * predicates of the form `column OP value`
  * allowable join predicates of form `col OP col`
  * for sort-merge, need to know sort key
  * for hash join, need to know hash function/attr
* [Optimizer support for UDFs/expensive predicates](./files/papers/postgres-expensivepredicates.pdf)


### Storage system

Overview

* Recovery is all about replicas
* WAL and ARIES was very very complicated, hard to test
  * Data and log are logically same
  * Physically separate (in terms of code too)
* Idea: the data is a log.  Same representation for log and data.

Sketch 

* global info
  * TID global tuple id
  * XID unique xact id
  * timestamps
* tuples contain
  * TID
  * tmin, tmax: lifespan of tuple's valid state
  * bxid: xid that assigned tmin
  * exid: xid that assigned tmax
  * VTID: TID of this or next version of tuple (forms a linked list)
* insert sets tmin/bxid, delete sets tmax/exid.  update = delete + insert
* add following predicate to find valid tuples at time T (time travel!)

        tmin < T < tmax and bxid, exid committed
        tmin < T, tmax null, bxid committed
        tmin < T, bxid committed, exid not committed

* on commit, force new tuple versions to stable storage
  
* recovery is super simple.  Abort all tuples whose exid is not committed

### Process structure

Terms

* Process: kernel thread + private addr space.  more context overhead
* OS Thread: shares addr space with other threads in process
* User-level Thread: 
  * scheduled by user-level scheduler, not kernel scheduler.  
  * Avoids kernel mode switch for scheduling.
  * synchronous io causes all threads to block.  only use async/non-blocking calls
* DBMS thread: user-level thread implemented in the DB rather than a 3rd party library

Options 

* Query Per Process/Process Pool
  * easier to program, let OS handle scheduling etc
  * needs shared memory for lock/buffer manager, shared data structures.  tricky to code.
  * limits number of concurrent connections
* Query Per thread
  * more concurrent requests 
  * tricky to program, race conditions, etc
* Lambda functions:
  * If you had fast shared state + infinite threading capacity, how could you design the engine?

Despite threads/processes, there are numerous data sharing "choke points" (shared state)

* Moving data pages between disk to memory
* Moving log pages out
  * log is FIFO.  
  * need ability to flush up to a log record (e.g., on commit).  Block until flushed.
  * may flush commits in batches (group commit)

Popular: DBMS threads (lightweight threads) on top of OS process pool

* how to map queries to threads to processes can be tricky
* thread migration: move existing DBMS thread to another process (perhaps its free)

Why one query per DBMS thread?

* Finer granularities?  Operators?  Plan fragments?  
* Becomes big scheduling problem
* See eddies, [stagedb](http://www.cs.cmu.edu/~stageddb/), [quickstep](https://github.com/apache/incubator-quickstep)
  
##  Tidbits from Turing lecture

Rewrites

* INGRES was in C, didn't want to use it again.  
* C++ wasn't mature, UNIX didn't run much else
* Went with the new hotness: lisp
* Implemented Postgres-v0, very very slow
* threw it away, back to C. 
* there were many rewrites.