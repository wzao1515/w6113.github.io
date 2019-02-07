---
layout: page
---

## Comments on random reviews

Overall, I enjoyed reading the reviews.  The points are getting sharper!




This is a good point, the concern seemed to be pushed aside as "LLVM magic".

      W1 - The author mentions that a single tuple might be too
      large for available CPU registers while his proposed
      improvement heavily relies on the ability to keep data in
      registers and swapping registers as infrequently as possible.
      He caveats this later by saying that using LLVM hides the
      problem by offering an unbounded number of registers. But
      the LLVM implementation also requires register swapping,
      just on a lower level.

Multiple folks noted that maintaining the LLVM-level coding, keeping up with and combining compilation with other optimizations, and managing _code_ is going to be painful.  
That's fairly spot on, and the impetus of the later LegoBase etc work (see the Revisited paper).

      As much as S2 could be a strength, using LLVM involves low-level
      coding, which makes the codebase difficult to manage and
      manipulate

      Sometimes it might not be pushed until reaching the next
      breaker. I found a system called Peloton, which relaxed the
      pipeline breakers of HyPer to create mini-batches for operators
      that can be vectorized.

On the other hand, the following comment is a good counterpoint

      One thing that is promising is that not the entire query processing
      logic needs to be written in LLVM assembler. Instead, LLVM and C++
      could be mixed in a way such that the complex data structure is
      written in C++ and connected via LLVM code to simplify the code
      structure and improve performance.

In fact, do we really need these LLVM-level tricks, or can we continue to trust compilers and algorithmic smarts?

      Tahboub et al. also points this out and devised the system
      LB2 [How to Architect a Query Compiler, Revisited] implemented
      in high-level language. They compared the performance of LB2
      and Hyper and showed that LB2 exhibited rivaling performance
      with much shorted code (2k compared to 11k of Hyper). This
      implies that query compilation may be simpler and more
      straightforward to achieve similar if not better performance.

What about scalability?  The general question is probably orthogonal to the paper.  Does query compilation of multi-threaded code (multi-core) make sense?

      W1. They addressed scalability lightly.

      W1 My concern is a little bit same as for the previous OLTP paper, that is the senario is only about single-thread (that could be a future work I think). 


Finally, the points about the weaker-than-usal experiments are fair.  Still pretty impressive to stuff so many ideas into 12 pages!

#### Possible Improvements to Reviews

Some comments may be a bit harsh to choose as a major weakness (as oppossed to mentioning as a detail).    They referenced the HIQUE paper which came out the year before.  

      W3. Compiling query plan isnt a new idea and the authors didnt emphasize that.
      
Be specific.  In what ways?

      D2. I found it unclear what Inter vs intra-query parallelism are about. 


Something not noted in the reviews is that the branching and cache locality table is compared against Monet, even though the earlier experiments also included vectorwise.  If vectorwise is available, choosing the slower system weakens the point that's being made.


## Your Reviews


<style>
p.review {
  border-left: 2px solid grey;
}

div.block {
  margin-bottom: 1em;
}
</style>


<p class="review">
  <h3>---</h3>

  <div class="block">Queries on modern systems is CPU bound, necessitating a framework to automatically optimize query compilation beyond the vocano strategy of chaining operators. A push based approach aimed at maximzing data locality and reducing branching results in  better performance compared to pull based, in part by combining operators that don't require materialization by compiling it into assembly with LLVM.  </div>
  <div class="block">S1 - Examines system with respect to both OLAP and OLTP applications, compares against column store database, vectorwise database and  tuple based system using the traditional volcano strategy.

S2 - Very detailed performance breakdown and analysis strongly supports hypothesis. Considers compilation time, code maintainability, parallelization. Final analysis includes micro-benchmarks of time per operator, branches, mispredicts and misses for different caches. </div>
  <div class="block">W1 - The paper is very strong overall. A minor quibble that was brushed off is that gains is not significant with complex queries with a high number of joins. Given that OLAP queries can get very complicated, beyond even the benchmark, this could be a issue.</div>
  <div class="block">D1 - The paper had a fairly thorough review of existing approches to query compilation. In the past, when queries are I/O bound, it did not matter if the code compilation is less than optimal since it is dominated by I/O costs. The traditional way of compiling   As noted by the OLTP systems, memory at the time the paper is published is large enough to store the entire database system, which removes much of the disk I/O cost. While Hyper does occasionally write to disk, it is still CPU bound. I really like the approach the authors framed their problem solving procedure. First, they identified the sources of poor performance, namely missed branch prediction and lack of locality which results in data constantly being replaced in cpu registers. They then identified the cause of poor predictions, namely that Interpreted compilation, where each operator pull from the previous, have a high degree of freedom causes misses. They than ruled out vector wise and column wise compuation by showing the tradeoff due to space requirements, finally concluding that since the goal is to minimize data transfer from cpu registers, the best way to optimize compilation times is to combine all operations on data that could feasibly be performed into optimized compiled code. 

D2 - The paper notes the trade-off in logical complexity vs execution time between C++ and LLVM, which is essentially a intermediate language between C++ and assembly. The paper is unclear about how they went about optimizing between these languages, with the understandable justification that it is very complicated. In a sense, the compilation strategy is also part of this balance between easy of construction, understandability, and performance. The more specific something is, the more it can be optimized, but along with optimization is complexity. The authors do acknowledge this, looking at the code quality, measurining branches and misses. Branches in a sense can correspond to complexity, and misses to performance. Understandability is of course another dimension, but that can be subjective.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper seeks lay out a theoretical framework to improve the efficiency of query plans for in-memory DBMSs by implementing a data centric processing system, which will attempt to execute as many operations on data currently in the CPU registers as possible. The group also seeks to improve performance by generating code that is more amenable to the CPUs branch prediction (such code is either almost always evaluated true or almost always evaluated false). Finally, the group evaluated a real-life implementation of these optimizations, finding 1-2 orders of magnitude of improvement over a variety of commercial DB systems.</div>
  <div class="block">- S1: this paper takes both a theoretical and an applied approach - it lays out the groundwork well, and though it tests these theories, it keeps the testing precise.
- S2: this paper is very clear about which sorts of applications it will be useful for (in-memory rather than disk-heavy DBMSs). It eliminates much of the urge to ask, but what about...?
- S3: this paper illustrated its optimizations using specific code snippets and concrete examples rather than just theoretically describing what the system does, making it much easier to follow and compare the code generated by the compiler.</div>
  <div class="block">#NAME?</div>
  <div class="block">Traditionally, query plans have been optimized considering I/O to be the performance bottleneck. As a result, the iterator model is the most commonly-used model, as allows for pipelining. However, when I/O is not a bottleneck, such as for in-memory DBMSs - rather the CPU is. In order to provide efficiency at the CPU bottleneck, the authors of this paper evaluate the use of query plans that maximize the amount operations that will be done on any given data while it is on the CPU registers and avoid materializing data as long as possible. In order to do this, they must implement a somewhat more sophisticated query plan compiler that blurs the line between different operations (unlike the conceptually simple iterative model). 

The authors of the paper then test their model, achieving substantial (1-2 orders of magnitude) gains over previous iterations of their database as well as DB X but showing perhaps less substantial gains (given the radicalness of the model) over VectorWise and MonetDB (outside the last two cases).

The authors note that their method produces tighter loops that reduce he number of branch mispredictions and therefore claim that their algorithm produces better code.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The author identifies several deficiencies with the iterator model, specifically the large overhead related to iterating through tuples, and makes the observation that existing query evaluation schemes (e.g. "tuple at a time", "column at a time", "vector at a time") are suboptimal. To that end, the author proposed a query compilation strategy that improves on existing approaches. It includes emphasizing data centric processing, which is an approach that keeps data in CPU registers as long as possible, having data pushed towards operators to take advantage of code and data locality, as well as utilizing the established and well supported LLVM compiler framework to compile queries into native machine code. The author obtained results by integrating his strategy into the HyPer main-memory database management system and comparing performance results with other systems.</div>
  <div class="block">S1 - The author provides many concrete examples for how the improved system works, such as comparisons of code modification that leads to better branch prediction, how compiled queries work in his system, the sequence that queries are executed etc.
S2 - Chose to use LLVM, which is an established and well supported compiler framework and thus can enjoy future optimizations and improvements.</div>
  <div class="block">W1 - The author mentions that a single tuple might be too large for available CPU registers while his proposed improvement heavily relies on the ability to keep data in registers and swapping registers as infrequently as possible. He caveats this later by saying that using LLVM hides the problem by offering an unbounded number of registers. But the LLVM implementation also requires register swapping, just on a lower level.
W2 - The overall improvement is only at most factor of 10 faster, which as we discussed in class, is not significant enough for most users to pursue.</div>
  <div class="block">D1 - As much as S2 could be a strength, using LLVM involves low-level coding, which makes the codebase difficult to manage and manipulate. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper suggested a data-centric query compilation approach which is based on LLVM compiler framework to transform the query into machine code in order to achieve better performance than the classical iterator style query processing technique. The result is that this new approach could have a 50x win over the hand-written C++ code in terms of compiling time, and an around-10x win over other main-memory database in terms of execution time for OLAP. Besides, the generated LLVM code's locality is better than others as well.</div>
  <div class="block">S1
The idea of pipeline-breaker is good because in this way each fragment of code is strongly pipelining.

S2
The paper's work could have future impacts because it was based on a compiler framework.

S3
Although the evaluation section is not very long, it does hold water. </div>
  <div class="block">W1
My concern is a little bit same as for the previous OLTP paper, that is the senario is only about single-thread (that could be a future work I think).

W2
The pipeline-breaker might be too strict, would there be a better performance if it is relaxed?</div>
  <div class="block">For S1, this is one of the most important ideas in this paper. Since the code needs to have a good locality, the paper discarded pervious idea of pulling records up. Instead, it would keep pushing records towards consumer operators until the next pipeline-breaker is reached. Therefore, the paper ensured that data (records) could stay in CPU register as long as possible.

For S2, since the work was done based on LLVM compiler framework, it could be improved by the future updates of the framework (compiler updates, LLVM updates...).

For S3, the paper divided the whole evaluation process into two parts: compiling, executing time and code quality. For the evaluation of time, it got a win compared to almost all other database systems with different queries. I could infer that this new approach performances obviously much better when executing aggregations; for the evaluation of code quality, I think the method it used to evaluate is good, which is by counting branch mispredictions to visualize the degree of code's compact, but I think it would be better to use graph and charts instead of tables.

For W1, I learnt that this approach could handle SIMD well, but it didn't say whether it could have a better performance using multi-core (notice that the SUT is a dual-core CPU) and also handle MIMD.

For W2, I think the question is that how far the parsed tree that database could continue processing a tuple before the next tuple comes. Sometimes it might not be pushed until reaching the next breaker. I found a system called Peloton, which relaxed the pipeline breakers of HyPer to create mini-batches for operators that can be vectorized.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">
The hypotheses of the paper is that given query performance for in-memory databases are not bound by I/O anymore; it is necessary to take advantage of modern CPU capabilities to optimize performance further. HyPer proposed a solution that aims to organize the data in such a way that maximizes its presence in CPU registers and minimize the number of memory accesses. They achieve that through query compilation method, where they translate a query into a machine code dynamically and push the data between operators from one pipeline breaker into another pipeline breaker instead of pulling them. They demonstrate that using their method, they can achieve good query performance with minimal compilation time overhead.</div>
  <div class="block">S1. The writing was clear and managed to explain technical details in simple terms in addition to providing concrete examples to follow up with the conceptual explanation.
S2. The authors introduced a simple interface similar to the iterator model by modeling each operator conceptually into (produce() and consume()) functions to facilitate code generation.
S3. The authors addressed the problem of the difficulty of writing low-level code by mixing between a high-level language and a lower one in such a way that they still managed to achieve low query compilation times.</div>
  <div class="block">W1. They addressed scalability lightly.
W2. How easy would it be to extend the operators using low-level language or include new UDFs.
W3. Compiling query plan isnt a new idea and the authors didnt emphasize that.</div>
  <div class="block">D1. Regarding W3 I think the authors have failed to address that and discuss the challenges that made developers abandon this approach in the past and how HyPer addresses them.
D2. I found it unclear what Inter vs intra-query parallelism are about.
D3. In regards to W1 the authors didnt provide any evaluation in regards to the size of intermediate materialized data. How about when we have multiple concurrent running queries? Meta-data managing would be a challenge. Even though they do mention data partitioning, and that parallelism can be supported with minimal code changes, I found that wasnt enough and it would have been better if they provided further analysis on where their approach would lack behind.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The classical Volcano technique for query processing has become outdated as main memory grows and disk I/O becomes less pressing of an issue. Queries in modern, mostly in-memory databases can run faster than in existing systems when finely tuning the machine code for better branch prediction and more cache hits (data locality).</div>
  <div class="block">S1: The technique designed only majorly impacts one specific layer of the database system: query compilation into machine code. This is a relatively portable approach that does not require redesigning standard, relational database systems to implement. 
S2: Compilation to LLVM means that the optimizations made are not specific to a particular version of hardware. The query engine does not have to be redesigned with updates to the underlying architecture, and can still benefit from future machine/LLVM optimizations.
S3: The comparison between spilling into memory and spooling to disk is a convincing one: as the CPU-memory boundary emerges as a bottleneck, it makes sense to apply a similar approach that had been done to disk: minimize the number of fetches and try to stay in the faster portion of memory. </div>
  <div class="block">W1: The authors allude many times to hand-written query code being faster than all existing systems and claim that their system performs almost as well (and even better when LLVM optimization tricks can be used). But there is no concrete evaluation and comparison of hand-written execution plans versus the generated LLVM code. 
W2: There is no breakdown: it is unclear how much of the optimizations were actually a result of consciously avoiding branch mispredictions, cache misses, etc. and how much were a result of compiling down to LLVM instead of C++. </div>
  <div class="block">D1: Compiling queries down to LLVM did not introduce groundbreaking performance improvements, just 2-4x. Compiling down to C++ actually performs worse than existing systems. This indicates that there is a significant performance benefit just from switching to LLVM (in part due to code locality and LLVM-specific optimizations). Following up on W2, would the other systems enjoy similar improvements in performance if they compiled queries down to LLVM, even without applying the authors' techniques to avoid branch misprediction and data locality? And would those systems then perform better than this one? I didn't feel entirely sure about where the optimizations were coming from. 
D2: The design allows for simultaneous execution of consecutive operators that are not "pipeline-breaking", which allows for increased data and code locality, since we don't have to materialize results and switch operations as often. I found this a particularly convincing approach to accomplish S3, since the Volcano model encourages modularity and stricter separation between operators.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">With the growth of main-memory, CPU cost instead of disk I/O has become the bottleneck of query processing. In this paper, Neumann introduces a novel compilation strategy that translates a query into machine code through LLVM compiler framework to achieve data locality and branch predictability. He integrates the technique into Hyper and shows that it achieves competitive performance compared to the hand-written C++ code. </div>
  <div class="block">S1: Emphasis on data and code locality. Proposed the data-centric approach that keeps data in CPU registers as long as possible by using a push-based architecture.
S2: Use LLVM compiler framework to compile queries into native machine code. Use a mixed execution model that blends C++ code for complex data structures and LLVM assembly for connection to achieve simplicity and better performance.</div>
  <div class="block">W1: The implementation with LLVM compiler framework is too lower-level and permeates large parts of the engine code. It is possible to use simpler higher-level implementation.
W2: The proposed approach does not directly apply to query compilation in some other systems such as Spark with RDD abstraction and Java-based ecosystem.
W3: Would be better to have more detailed suggestions for future work.</div>
  <div class="block">With the growth of main-memory, CPU cost instead of disk I/O has become the bottleneck of query processing. In this paper, Neumann introduces a novel compilation strategy that translates a query into machine code through LLVM compiler framework to achieve data locality and branch predictability. He integrates the technique into Hyper and shows that it achieves competitive performance compared to the hand-written C++ code. 

D1: One thing that stands out in the paper is the emphasis on data and code locality such that data is kept in CPU registers as long as possible. To achieve this goal, Neumann proposes a data flow control that reverses the direction in that of classical iteration-style processing paradigms: data is not pulled by operators but pushed towards the operators. This allows data to be always pushed from one pipeline-breaker (an algebraic operator that takes an incoming tuple out of CPU registers) into another pipeline-breaker without leaving the CPU registers, thus a data-centric model. In addition, this query processing architecture has good code locality as small code fragments will work on large amounts of data in tight loops. These strategies greatly contribute to the query processing performance demonstrated in the evaluation section.

D2: Another significant idea proposed is the use of LLVM compiler framework to compile queries into native machine code. As higher-level languages provided easier access to data structures, Neumann performed several baseline experiments compiling queries by first generating C++ code. They ended up using LLVM because experiments showed that it is much faster and offers better control of the generated code. One thing that is promising is that not the entire query processing logic needs to be written in LLVM assembler. Instead, LLVM and C++ could be mixed in a way such that the complex data structure is written in C++ and connected via LLVM code to simplify the code structure and improve performance.

D3: Despite the great contribution this paper has brought to query compilation strategies, there is a nontrivial weakness to the proposed approach: the implementation with LLVM compiler framework is too lower-level and permeates large parts of the engine code. Tahboub et al. also points this out and devised the system LB2 [How to Architect a Query Compiler, Revisited] implemented in high-level language. They compared the performance of LB2 and Hyper and showed that LB2 exhibited rivaling performance with much shorted code (2k compared to 11k of Hyper). This implies that query compilation may be simpler and more straightforward to achieve similar if not better performance.

D4: Another potential limitation of the proposed strategy is the adaptability of the compilation approach to other systems other than Hyper. In the paper published in 2018 by Essertel et al. [Flare: Optimizing Apache Spark with Native Compilation for Scale-up Architectures and Medium-Size Data], the researchers presented Flare, an accelerator module for Spark inspired by query compilation techniques of Hyper. Experiments showed that direction application of Hyper strategy gives suboptimal results on Spark because Spark itself resides in a Java-based ecosystem and its RDD representation imposes boundaries between code generation regions thus causing much overhead. Thus, they argued that although the query compilation strategy works great in Hyper itself, direct implementation of the approach in Spark and similar systems will not work great due to the overhead of traversing traversing pipeline boundaries (Java vs C++, RDD overhead etc). Instead, they need to avoid this overhead by replacing the precompiled Spark runtime with one able to reason about the entire query. This of course does not invalidate the contribution of Neumann, but caution needs be taken when applying the compilation approach to different systems. 

D5: Finally, although the author points out possibility for future research with the proposed strategy, it would be nice to have a more detailed future work section in the paper. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper presents a query compilation strategy that determines control flow based on materialization points and keeps data in registers as long as possible. Their method achieves faster compile times on the TPC-C benchmark, fewer branch mispredicts, instruction/data cache misses.</div>
  <div class="block">S1: Using C++ for only complex data structures, and LLVM to implement tuple access and processing
S2: related to S1, extending the compilation from single-tuple to block-wise, using LLVM's robust type system of vector types to model SIMD.</div>
  <div class="block">W1: Performance tuning seems to require great coding effort, and would require frequent re-tuning as hardware technologies like branch predictors evolve.</div>
  <div class="block">The query compilation strategy combines pre-compiled C++ code for complex data structure handling with dynamically generated LLVM routines for tuple processing. The authors chose LLVM to be able to control low-level memory resources such as register usage (rather than letting the C++ compiler control this). They use a data-oriented control flow that is divided along pipeline boundaries: in other words, the compilation keeps the same set of tuples in registers for as along as possible, executing operators until the operator materializes the tuples. The control flow is designed to improve data locality and avoid passes across function boundaries, which would cause register contents to be replaced. The internal code generator uses a producer-consumer model to compile the program, but the strategy also reminds me of a greedy algorithm. The authors first present the query compilation for processing one tuple at a time, then show how the model can be extended to do MonetDB-like block-wise processing. 

(W1) The authors discuss performance tuning methods, for example, writing code that minimizes branch misprediction. While this is shown to be effective in experimental results, it seems impractical in the long-term to write code "predictor-friendly" code. It is difficult to infer what is actually predictor-friendly, and doing so breaks a software/hardware abstraction. (For example, a branch predictor may combine several pieces of information like global/local history such that it learns/predicts patterns just as a well as a 95%-taken branch). It seems like LLVM supports branch prediction hints via branch weight metadata fields and a __builtin_expect instruction--I wonder if the authors use these, as it is a well-defined interface between software and hardware. They argue that spending effort on the code generator pays off for subsequent queries, but if the branch predictor technology changes, the code-generation has to be re-tuned.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The problem is that compiling queries can be slow because it is an iterator style, thus the only queries that compile in a reasonable amount of time are hand written queries. Therefore, the authors propose compiling queries using LLVM rather than another language e. g. C++ or Java to have equivalent or superior performance to handwritten query compilations. I personally believe in their experimental results as it seems they have other related work and their results argue their point well.</div>
  <div class="block">S1- It is a strength that is uses LLVM as it is an open-source compiler framework that is consistently maintained.
S2- This paper makes references to other papers as well when arguing that obtaining the C++/Java code to compile the query is time consuming
S3  the metric of obtaining the number of branches and number of failed predictions is a good metric for evaluating their pipeline scheme,
</div>
  <div class="block">W1- For their evaluation, I am unsure if a query of only 30 tuples can show any massive performance boost. It may need a much larger sized query (e. g. 1,000+ tuples) to see a more obvious result that cant be challenged as measurement noise
W2- For their results, they should have also tested the related works the cited about C/C++/Java speed being too slow as well to use the Related Works to its fullest extent. 
</div>
  <div class="block">S1  Also, the authors took advantage of the fact it is open source to add their own code to LLVM to allow it to work better with query compilation, which can therefore be made publicly available for other database systems to implement.
S2  The paper shows the performance advantage of LLVM vs C++. But the fact they mention other papers showing the lackluster performance of using Java and C is useful because in research it is ideal to have other research backup your claim.
S3  Since the major improvement to their compilation is more effective pipe-lining, this is probably the best measure of show how well their code runs. Also, it shows the improvement LLVM has as well over all other methods
W1  This would make sure that they do not have a false positive in terms of results. Especially since they didnt make it clear if they repeated this experiment multiple times to ensure it wasnt just luck the had faster performance time
W2  As said in S2, it was good on their part to mention other words showing lack of speed using C or Java. But comparing the LLVM system directly with their systems as well would have made it more convincing LLVM is indeed the best.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper proposed a query compilation strategy that differs from existing approaches, explained its overall architecture, code generation details and different ways of integration.</div>
  <div class="block">S1: It gives clear explanation on some prerequisite concepts such as pipeline-breaker and code types in order to make its main points such as the query compiler and code generation more understandable.
S2: Clear figure illustration such as the Figure 2, 3 and 4 help to make examples more easy to understand, going a step further to help illustrate the main points such as query processing architecture.
S3: There are strong and phanic logical connection and good transition between each part such as it steps into the part of translation from algebraic execution plan into real code after introducing the execution plan naturally.
S4: Provided detailed appendix of code, figure and example for reference.</div>
  <div class="block">W1: The organization of the structure is a kind of weird, the related work part is in front of the main body and is more like a background introduction in the conventional sense other than a summary and extension. 
W2:  In the part of parallelization, their code generation framework can be supported with nearly no code changes, but the further problem of parallelizing decision is beyond the scope of this paper, so it seems like this paper has not much substantial contribution on this part of work.</div>
  <div class="block">D1: With the combination of example query, query execution plan, compiled query and also with the supplement of appendix and the method of analogy, the whole process of query is well illustrate, each one of these examples is very clear, simple and easy to understand enough, but without losing representative.
D2: Another impressive example of good illustration of this paper is in the part of performance tuning, where a simple but representative example of while loop and do while loop is being well used to illustrate the difficulty and complexity which may result from consideration that should be taken into in the code generation process such as the influence of coding style on branch prediction.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper talks about a new technique to compile a query plan which is data-centric and pushing tuples between each pipeline-breaker. In addition, it introduces the technique of using LLVM to generate the C++ code during query compilation which proves to have similar performance as hand-written cpp code. The hypothesis of the paper is that there is a better framework of query processing that is different from the original Volcano-style and requires less runtime.</div>
  <div class="block">S1: The LLVMs advantages in offering unlimited number of registers and compilation optimizer is well-explained. I am convinced that LLVM is better than writing assembler code manually.
S2: The appendix contains the actual code for understanding the codgen process and reimplementation.</div>
  <div class="block">W1: The evaluation section can have more testing. The paper has not pointed out other performance testing directions. It would be better if has some.</div>
  <div class="block">D1: The LLVM is good at generating lower-level code that is better optimized and is faster than higher level languages during run-time. The idea of combining compiler design tool with database query optimization is new to me. With LLVM, I am wondering if other compiler optimization technique could further speeds up the query process. The paper discusses the reason behind choosing cpp instead of Java and C because It is relatively light weight and allows iterator model, but cpp has drawbacks in dealing with complex query and lacks control over generated code. The study is inspiring that if better compiler tools and more suitable programming language emerge in the future, the result can be further improved. 
D2: As discussed in paper, if in a disk-based DBMS, the technique of data-centric query compilation works excellent. So, for the question of when would query compilation be useful, I think during the operators in CPU registers, the query compilation makes computation of query plan faster and cheaper.</div>

  </p>
<hr />
