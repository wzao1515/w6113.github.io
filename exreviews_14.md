---
layout: page
---

## Comments

#### State of the Art

It's really good to identify the key comparison points from the state of the art!

		The MapReduce implementation leveraged several
		existing technologies to this end, including NOW-Sort
		(network-based sorting) for partitioning/sorting
		strategy, Charlotte System for scheduling, active
		disks for local disk storage, River for distributed
		communication/scheduling.

#### How

Recall that the point of this role is not to summarize every technical part of the paper.  It is to **prioritize** and identify the most important technical ideas used to achieve the paper's contributions?  Some are a matter of applying prior solutions, and some are trick and crucial to the system.  Identify and describe the crucial ones.


For example, the following review summarizes each part of the paper as sentences.  Although it does do a good job of listing a number of facts from the paper, it is better to pick specific points and go into more depth about **why** they are important technical details (e.g., whot could go wrong if they get the detail incorrect).  For instance, my opinion is that RDD's main technical contribution was building around an in-memory data flow system---this means that failures could easily lose data, thus the big technical question is how to efficiently deal with failures and recovery.  At this point, I would describe the main techniques and why they do a good job addressing the problem.

        The dependencies between RDDs can be wide or narrow. The
        narrow dependency is pipelined in each stage container,
        while the wide ones are materialized on the nodes holding
        parent partitions. For the interpreter, the two modifications
        on Spark are 1. to let worker nodes fetch classes over HTTP,
        and 2. to allow the code generation process to reference
        the instance of object directly. The memory management
        consists of three parts: the in-memory storage as Java
        objects, in-memory storage as serialized data, and on-disk
        storage. They also use LRU policy on RDDs to manage memory.

#### Haters

This is on point, and gets to the heart of the matter.  But why are there such vast overheads?  Is it inherent in their approach, or an artifact of the implementation?

		The hater of MapReduce is the vast overheads due to data
		replication, disk I/O, and serialization. Because in
		MapReduce, the only way to reuse data between computations
		is to write it to an external stable storage system, e.g.,
		a distributed file system.

This is also a good, strong statement, because it points out the primary points that State of the Art (SoTA) lacked.

		Both the mapreduce abstraction and its implementation were significant
		contributions to the state-of-the-art; existing technologies lacked
		both generality of programming model and large scale parallel
		processing and fault tolerance.

This is another Hater with good observations.  It is one of (many) major limitations from a performance perspective.  I like how it contrasts with the ideal:

        It only allows single data flow. Google Map/Reduce is useful only
        if there is single stage of map and reduce. If the job is consisted
        of multiple map/reduce stages, only way to do in map/reduce is to
        run each stage sequentially....  The ideal for data parallel
        applications is linear speedup, but we don't know how close MapReduce
        gets to this goal.

However, it is ambiguous when critiquing RDDs.  Specifically, what types of details would make it easier to appreciate the system?

        While if the author could provide some more details about the RDDs,
        it should be easier for the readers to appreciate the system itself.

## Reviews


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

  <div class="block"><h5>Future</h5></div>
  <div class="block">The paper introduced a programming framework that facilitates the processing of large data sets by hiding the heavy task of partitioning, distributing,  and scheduling jobs on the dataset using a set of commodity machines. The framework offers two functions programmed by the user, a map function that process and key/value pair to generate a set of intermediate k/v pairs, then reduce function that merges all intermediate values associated with the same intermediate key. </div>
  <div>Response:</div>
  <p class="block">This paper offers a step forward in the abstractions introduced that automate data-intensive processing and a step backward in that users are still required to program in a procedural language to specify the map and reduce tasks. Going forward, as can be seen with the RDD, and Spark papers, a lot of effort has been taken to leverage similar abstraction through using declarative language. 

</p>
  <div>Comments:</div>
  <p class="block">-</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">The map reduce paper aims to create a paradigm (API interface) suitable for distributed computing (take advantage of parallelism) that hides the details of distributed networking and coordinations for resiliency. The Resilient Distributed Database paper aims to  create a distributed memory  abstraction that perform better than existing systems like mapduce in terms of iterative algorithms and ad-hoc analysis based on transformation of data and keeping track of its lineage. </div>
  <div>Response:</div>
  <p class="block">The way map reduce works is that it divides workloads into M map and R reduce tasks. The programs starts on multiple machines, with one machine being the master, which keeps track of the status of tasks, data locality and assigns tasks to workers (rest of the machines). The paradigm is that each M produce a set of intermediate values, and reduce combines the intermediate values into the desired results. The RDD paper pointed out a issue with the above paradigm, namely that it doesn't allow intermediate results to be reused in multiple other functions without writing to disk, as is common with iterative algorithms like page rank. Instead of tasks and intermediate results, RDD (Resilient Distributed Datasets) are read only partitions (originally from disk). Each transformation results in a new RDD in memory, but RDDs need not be materialized and can instead be comprised of the lineage that leads to it. RDDs can then be passed into actions that results in values being returned to application. Lineage provides fault tolerance for RDDs since any materialized RDD that fails can be computed from its lineage. In map reduce, resilience is provided by the master checking on workers, and restarting a task if it doesn't hear back. The Spark interface is both more restrictive and flexible compared to map reduce. The transformations seem to be a subset of map in map reduce while actions overlaps with reduce. It is more flexible in that it gives users power over what to do with intermediate results, instead of map reduce that have a static map -&gt; intermediate -&gt; result execution graph. </p>
  <div>Comments:</div>
  <p class="block">I enjoyed the readings, especially the comparison between RDDs and distributed shared memory. It's interesting how both paradigms guarantee consistency (map reduce it doesn't matter is job is computed twice since reduce is key based, with RDDs its read only so extra copies can't hurt). </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">MapReduce -  a programming model allows users to write map and reduce functions to process a vast dataset with automatically parallelization and execution.</div>
  <div>Response:</div>
  <p class="block">The hater of MapReduce is the vast overheads due to data replication, disk I/O, and serialization. Because in MapReduce, the only way to reuse data between computations is to write it to an external stable storage system, e.g., a distributed file system.

The non-faulting sequential execution of the non-deterministic operators is not clear to me.

The paper mentions how to choose R by saying "we make R a small multiple of the number of the worker machines". It is not clear. Is there an optimizer to choose that?</p>
  <div>Comments:</div>
  <p class="block">Although it is not a complicated idea to come up with, it is great work (from the citations)! I guess MapReduce model laid the foundations for current parallel systems.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">MapReduce is a computation model that abstracts away the complexities of parallelization and distribution in large-scale data analytics. RDDs is another abstraction that leverages shared memory so that intermediate results can be reused.</div>
  <div>Response:</div>
  <p class="block">MapReduce and RDDs both address the same general issue: providing a framework for large-scale data analytics that takes advantage of distributed systems for efficient computation. They both provide an abstraction that allows users to focus on expressing the details of their data processing algorithms without being mired in the complexities of distributed computing: fault tolerance, partitioning/parallelization, load-balancing, etc. Large-scale data analytics very widespread, and the popularity of both MapReduce and Spark can attest to it. There are widespread applications for both systems (the MapReduce paper lists many problems to which it can be applied, such as distributed grep, url access frequency count, etc.). 

Both systems deal with distributing and handling intermediate results of some kind, but their techniques are different. MapReduce requires that the user specify a "map" algorithm that can be applied to partitions of the input data, which produces intermediate output on each cluster's local disks. The "reduce" workers perform remote reads to obtain the data and complete the computation. For problems that can be expressed in terms of a map and reduce function, MapReduce allows for intuitive (and massive) scale-up of computational resources for parallelization--Google uses MapReduce to process data on thousands of machines, as the paper states. This simplifies the setup and maintenance of a distributed system, since it is abstracted away. 

MapReduce mainly relies on persistent storage on disk for communication across machines. RDD, on the other hand, focuses on the management of in-memory data in a distributed environment. It addresses the central problem of fault tolerance, which can be cumbersome in distributed systems. Especially since it relies on sharing in-memory information, fault tolerance is essential. RDD uses coarser data operations ("transformations") that express larger changes to the dataset rather than fine-grain manipulations (e.g. tuple-by-tuple), so the size of logging information is much smaller than the data being manipulated (rather than being proportional). As a result, logging can be done efficiently, and very little data needs to be transferred from machine to machine to express state. </p>
  <div>Comments:</div>
  <p class="block">How are MapReduce and RDD both able to simplify distributed computation? I think a big part is the simplification of the computational model: MapReduce constrains the user to specifying two easily parallelization operations to perform on the entire dataset, which RDDs are best applied to "batch applications that apply the same operation to all elements of a dataset," due to the coarse granularity of its operations. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Results</h5></div>
  <div class="block">By restricting the programming model appropriately (e.g. map - reduce, or the RDD "coarse-grained" parallelism), you can get a flexible programming model with automatic fault tolerance and distribution.</div>
  <div>Response:</div>
  <p class="block">What we're most interested in evaluating is probably raw performance for a couple of key benchmark tasks, performance overhead of failures, and reassurance that this is an expressive enough programming model. The benchmark tasks would ideally be either simple/standard tasks (e.g. sorting numbers) or would stress different parts of the program (e.g. different data locality distributions for partitioning, etc). Ideally, we would also compare these frameworks against "hand-coded"/optimized solutions to each task, to get a sense of how much performance is still on the table (for example, we could compare these distributed frameworks on "small" problems that fit on a single machine, to see what the distribution overhead is).

MapReduce:
The "expressive-enough" questions was pretty well answered by some of their anecdotes from Google (which implies they were using this paradigm extensively), although a little detail on what was hard to express would've been good. Performance-wise, the paper focused on two tasks: grep and sort (mostly the sort), which are both nice standard problems. They presented the total execution time, as well as graphs of input, output, and shuffle bandwidths. They also measured how fault-injected machine failure affected these graphs.

Overall, this was a pretty good comparison, although I would've loved to see:
- Multi-stage map/reduce jobs (can they be pipelined)?
- Map-Reduce jobs with a super-skewed key distribution (which should mess with the shuffle / reduce phase)

RDDs:
The expressive enough part here was answered by the commercial applications of Spark which they referenced. The benchmark tasks Spark used were (1) iterative machine learning (logistic regression and k-means) and PageRank, which really stressed the iterative nature of Spark (especially compared to Hadoop's map reduce paradigm).</p>
  <div>Comments:</div>
  <p class="block">#NAME?</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">MapReduce: The authors of this paper specify and implement a two-pronged model for processing large raw data stores in parallel. The authors claim that native parallel execution over a number of commodity machines will allow for improved fault tolerance, which they successfully demonstrate an example of in their experiments.   Spark: The authors of this paper present an abstraction for allowing users to persist intermediate query results and implement lineage tracking in case of data loss. The authors claim that query result persistence will provide speed improvements across similar queries and that lineage tracking will allow for faster recover in case of data loss. The authors then compare Sparks speed against that of the state of the art (Hadoop) and realize up to 10x speedups.</div>
  <div>Response:</div>
  <p class="block">MapReduce: The authors of the MapReduce paper observe that what should be simple computations over large, distributed datasets become unnecessarily complex due to the need to handle parallelization and fault tolerance by hand. This problem appears to have substantial relevance, as they note that MapReduce is being used to perform various data aggregation tasks within Googles production systems.

Spark: The authors of the Spark paper identify that data reuse is widespread across many machine learning and graph-related algorithms, which requires substantial overhead due to factors such as data replication and disk I/O. The authors of Spark propose to solve this problem by allowing users to keep data in memory in order to yield speed-performance gains. The authors also claim that logging transformations is costly and that tracking lineage will be a more efficient way to recover data in case of loss. The authors then enumerate a number of cases in which Spark has been commercially applied, such as analytics and Twitter spam classification.</p>
  <div>Comments:</div>
  <p class="block">The MapReduce paper didnt seem particularly thoroughly-tested - the authors did not compare it to anyone the systems.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">MapReduce is a programming model that provides map and reduce abstractions for parallel computing, along with an implementation that provides work distribution and fault tolerance. RDDs are a distributed memory abstraction for data sharing, data reuse, data mining, and implements distributed storage and fault tolerance for the intermediate data. </div>
  <div>Response:</div>
  <p class="block">Both MapReduce and RDDs altered the landscape of the state-of-the-art; Each defined a new problem and provided an apt abstraction for that problem, rather than providing a technical solution to existing problems. MapReduce targeted applications that processed large amounts of raw data to compute derived data. Their observation was that while computations were simple, parallelization, fault-tolerance, load balancing, and data distribution were difficult. The map reduce abstraction was general enough to be useful to many applications/computations, and the implementation exploited local disk storage for (materialized) intermediate key/value pairs. The MapReduce implementation leveraged several existing technologies to this end, including NOW-Sort (network-based sorting) for partitioning/sorting strategy, Charlotte System for scheduling, active disks for local disk storage, River for distributed communication/scheduling. 
Both the mapreduce abstraction and its implementation were significant contributions to the state-of-the-art; existing technologies lacked both generality of programming model and large scale parallel processing and fault tolerance. 
RDDs built on the work of MapReduce to solve an adjacent subclass of computations; Data processing that required keeping data in memory and performed the same operation to elements of a dataset. While MapReduce avoided network bandwidth costs using local disks to store materialized key/value pairs, RDDs required that intermediate data be materialized and persistently stored. The key insight was that a bulk-write workload could be leveraged to provide fault tolerance through lineage, a record of coarse-grained transformations. RDDs use some similar techniques to MapReduce, for example, they employ backup execution of slow tasks("stragglers"). But the RDD framework can express MapReduce as well as other models like SQL, Pregel, so it is a meta-framework in some sense. To implement data sharing for the general abstraction of RDDs, the paper used existing technologies such as high-level dataflow programming languages, lineage, materialized views in RDBMS.</p>
  <div>Comments:</div>
  <p class="block">It was really cool!</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">MapReduce: Simplified Data Processing on Large Clusters: This paper describes a parallel batch job programming model called Map Reduce. Map Reduce is mildly similar to Condor where users submit jobs, a supplied Map and Reduce function to be applied to a large data set, and the system handles all fault tolerance, scheduling, and distribution. Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing: This paper presents a distributed memory abstraction called RDDs used for in-memory computations on large clusters. Also,they provide their own programming model using RDDs. When dealing with fault tolerance,RDDs with narrow dependencies only need to recompute the lost parent partitions instead of full computation.</div>
  <div>Response:</div>
  <p class="block">MapReduce: Simplified Data Processing on Large Clusters:
The technical work:
It only allows single data flow. Google Map/Reduce is useful only if there is single stage of map and reduce. If the job is consisted of multiple map/reduce stages, only way to do in map/reduce is to run each stage sequentially.
The presentation of the paper:
The authors do not compare the performance of their MapReduce programs to the equivalent single-machine implementations. Although it is certainly clear, from the descriptions of their implementations, that the MapReduce version would be much faster than the serial version of grep and sort, the authors do not give speedup or efficiency results, so we do not know. The ideal for data parallel applications is linear speedup, but we don't know how close MapReduce gets to this goal.
Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing:
The technical work:
About the programming API, there is reasons for them to use Scala, but I wonder if there is a way for the Spark to provide a pure Java API, and make the Scala only used inside the system, so that the users don't need to adopt a relatively new language, although it's true that Scala is based on Java. 
The presentation of the paper:
There is no deny that this paper did a great work on most of the aspects. While if the author could provide some more details about the RDDs, it should be easier for the readers to appreciate the system itself. </p>
  <div>Comments:</div>
  <p class="block">MapReduce: Simplified Data Processing on Large Clusters:
MapReduce started a flurry of activities in this area and many other systems followed MapReduce to provide a simple interface for writing programs to run on large clusters. Compared to systems such as distributed/parallel databases, MapReduce is a comparatively lightweight and is a better choice when the problem is relatively simple. But, for complicated operations such as joins, the onus is on the programmer to implement the logic and he may find it easier to use a more powerful interface, in this case a parallel database.
Resilient Distributed Datasets: A Fault-Tolerant Abstraction for In-Memory Cluster Computing:
The description of the programming model is clear, also because of the example applications. The evaluation has clear goals, and I believe they make a case for the speedups obtained in the LR and K-means. They make the reasons of the speedup very clear. Since they rely on memory locality, they also show a graceful degradation of the iteration times as the memory is used.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">MapReduce is a programming model that is used for distributed system in parallel computation. It is developed by Google and used in many Google products, but the idea of MapReduce impacts on the design of other distributed model such as in the other paper, the RDD (Resilient Distributed Datasets). The RDD improves on MapReduce that it also provides the abstraction on distributed memory, while other distributed system such as MR and  Dryad only provides the abstraction on distributed computational resources.</div>
  <div>Response:</div>
  <p class="block">The idea of MapReduce is to build a simple interface that enables automatic parallelization, distribution of large scale computation and high performance on a large cluster of machines. The computation model is simple that only consists of two functions: map and reduce. The Map function is to generate all intermediate key/value pairs for a key/value pair, while the Reduce function is to merge all the intermediate key/value pairs to a single one. The mechanism of MapReduce function is that the input data is partitioned into M pieces into Map workers (the machines) and one master copy. The Master copy is used to forward buffered locations to the Reduce workers. When the Reduce workers are notified, they will read intermediate data, sort them and pass those corresponding values to Reduce function. After all Map and Reduce function complete, the user function will then be awaken. The key insights are that workers and master are fault tolerant that if a worker fails, the master can simply re-executed the work and continue. If a Master fails, the system can write checkpoints to help it recover. Another interesting point is the use of Master to backup in-progress tasks to avoid the straggler problems.
As stated in the RDD paper, the main difference between existing distributed memory system is based on fine-grained updates, the implementation of RDD is based on a coarse-grained transformation (map, filter, join) that apply the same operation to many data items. The implementation of RDD is shown on the Spark system. The Job scheduler is modified to examine the RDD lineage graph to build a DAG of stages for execution. The dependencies between RDDs can be wide or narrow. The narrow dependency is pipelined in each stage container, while the wide ones are materialized on the nodes holding parent partitions. For the interpreter, the two modifications on Spark are 1. to let worker nodes fetch classes over HTTP, and 2. to allow the code generation process to reference the instance of object directly. The memory management consists of three parts: the in-memory storage as Java objects, in-memory storage as serialized data, and on-disk storage. They also use LRU policy on RDDs to manage memory. Finally, they enables checkpointing on Spark because they claimed that event though lineage of RDD is enough for recovery, the checkpoint technique will save a lot of time when lineage chains are too long. </p>
  <div>Comments:</div>
  <p class="block">The difference of MR and RDD is on their purpose: the MR is designed to enable a simple way of utilizing distributed computation resources, while RDD is designed to leverage distributed memory. Also, MR is a library, but RDD is built and improved on the Spark, but its idea can be applied on other systems as well.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Results</h5></div>
  <div class="block">The MapReduce paper presents the MapReduce programming model, which supports parallel execution of a computation task on a cluster of commodity machines. The RDD paper describes the Resilient Distributed Datasets (RDDs), a distributed memory abstraction that lets programmers perform in-memory computations on large clusters in a fault-tolerant manner. Both papers present the architecture, strengths and application of its model and gives experimental analysis on different benchmarks comparing to other exiting solutions. </div>
  <div>Response:</div>
  <p class="block">The MapReduce programming model is evaluated on two computations running on a large cluster of machines which are representative of a large subset of the real programs written using MapReduce: searching for a particular pattern and sorting on one terabyte of data. Both computations are evaluated by examining the data transfer rate overtime. Specifically for sorting, experiments are conducted on normal execution, with no backup tasks and with 200 worker processes killed. The resulting graph fits well with the theoretical analysis. In the no backup case, there is a very long tail where hardly any write occurs. In the machine failure cases, the cluster scheduler restarts new worker processes for the tasks so that a temporary negative input rate occurs. The good experimental results manifest MapReduce a strong candidate for parallel data processing and it has been applied to a lot of fields such as large-scale indexing and graph computations.
The RDDs and its application software Spark are evaluated through a series of experiments on Amazon EC2 as well as benchmarks of user applications. Performance experiments include two iterative machine learning applications, logistic regression and k-means, and Page Rank. Results show that Spark outperforms Hadoop by up to 20x because it avoids I/O and deserialization costs by storing data in memory as Java objects. Experiments are also conducted to evaluate the fault tolerance of the system and results shows that Spark can efficiently recover by building only the lost RDD partitions. 
The evaluation on both systems focus on the performance (either iteration time or data transfer rate) and fault tolerance. But we could see that the performance experiments are conducted on a set of applications that the system is specifically good at. For example, Spark focuses on iterative algorithms which obviously benefits from its in-memory storage and MapReduce emphasizes its capability of processing large datasets quickly. This kind of choice is natural for the paper but it will be also interesting to see the performance of both systems on other kinds of task.
</p>
  <div>Comments:</div>
  <p class="block">Both papers advertise their systems as efficient and fault tolerant in parallel computing, which are significant and demonstrated in their experimental analysis. In addition, RDD tries to provide a general-purpose framework instead of a specialized programming model while MapReduce uses a restricted programming model to make parallelizing computation easier. It is kind of hard to say if Sparks generality beats Hadoop, but they do both have a set of applications that they are suitable for. Spark is undoubtedly dominant when it comes to iterative processing and when real-time processing is expected. However, although its in-memory feature makes it efficient in iterative workloads, it faces memory issues when data sets are huge. Thus, for applications where there are more linear processing of huge datasets and when no immediate results are expected, MapReduce may outperform Spark.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">MapReduce is a framework for distributed computation tasks that allows a task to be split up into multiple threads of parallel execution, which are scheduled by a master node. RDDs are a read-only collection of objects that are partitioned across a set of machines so they can be rebuilt if a partition is lost.</div>
  <div>Response:</div>
  <p class="block">MapReduce is suited for batched data where computation happens offline on historical data, such as finding page hits, doing text indexing, or search engine functionality. It is good for doing a series of different operations on the same data. MapReduce doesnt work well for multiple parallel operations because it doesnt cache or store intermediate data or raw input data, so for something like training a model it is not optimal. 

RDDs are best suited for performing the same operation to all elements of a dataset. A user can checkpoint RDDs to avoid having to go through and redo all of the lineage.
RDDs are most suitable for cases where data is generated and must be checked in streams, such as sensors.Two cases given in the paper, traffic modeling and detection of spam in Twitter messages, demonstrate that RDDs, unlike MapReduce, allow graph processing.</p>
  <div>Comments:</div>
  <p class="block">Is Hadoop just open-source MapReduce or it is a different thing altogether?</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">MapReduce: MapReduce is a model that processes large datasets. It has basically two phases: map and reduce, which are all user defined function that take pairs of key and value as input and output pairs of key and value. The result is that it could efficiently work on parallel and distributed systems and deal with large dataset. RDD: RDD is a data abstraction which used in Spark. The properity of RDD is that it is read-only and could only be generated by the presisted records. It works well for parallel and distributed data processing.</div>
  <div>Response:</div>
  <p class="block">I believe the future work for MapReduce to do is to improve its performance. The reason is that MapReduce's efficiency is greatly weaken by its disk I/O. The intermediate result of Map will be written into disk and read by Reduce while Spark (uses RDD as its data structure) takes advantages of cheap RAM access and does in-memory computation, which means storing intermediate result in distributed RAM. Since the limitation of I/O cannot be addressed in MapReduce, there leaves little future for MapReduce. When it comes to RDD, I think there could be works to handle structured data since now RDD doesn't have schema view of data.</p>
  <div>Comments:</div>
  <p class="block">Although MapReduce seems faded compared with Spark, I really appreciate the paper of MapReduce itself. In its evaluation part, it takes two common usecases to illustrate the performance, which makes sense and could convince me, plus the improvement of indexing system in Google search engine by using MapReduce.</p>
  </p>
<hr />


