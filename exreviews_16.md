---
layout: page
---


### Reviews

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

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">The paper reduces the latency of arbitrary functions (that fits its interface) to approach that of streaming systems (and surpassing Kineograph) by relaxing state backups (reliability) and clever messaging. It provides a high level api of existing workflows systems (mapreduce, pregal etc) as well as lower level operations to allow flexibility and convenience without requiring users to access system code</div>
  <div>Response:</div>
  <p class="block">Naiad's main claims of contribution are the following: First, it combines aspects of low latency streaming data flow systems and more general (in that operations are based on process graphs) batch update data flow systems to get a single system with low latency that allows iterative batch updates. For batch processing, Naiad claims to do much better (10-100x) in terms of latency compared to Parallel Data Warehouse (PDW), DryadLINQ and SHS. PWD is a comercial distributed database system by Microsoft, and might not be state of the art at the time. DryadLINQ is a parallel processing system that models workflows as a DAG and includes numerous optimization. Naiad actually allows loops. SHS is a in memory distributed database store. The paper likely compares performance against these systems due to the fact that they run on Microsoft. More general data flow systems mentioned include CIEL, Spark, Spark Streaming and Optimus. The paper argues that they allow dynamic updating of the process graph, but is slow. Meanwhile, systems like Storm, MillWheel provides fast computation but requires a fixed 1 directional computation without states. 
</p>
  <div>Comments:</div>
  <p class="block">Systems that allow batch updates usually requires stage storage, as seen in the form of RDDs for Spark. This requires backups of state in terms of reliability, which could be expansive if there are too many of them, which is likely why previous systems are fairly slow. Streaming systems work fast precisely because state is not stored and the only overhead is communication. Naiad's technical contribution seems to be the method of storing only occasional state backups and minimizing the overhead involved in messages to update workflow to allow streaming like performance. However, it's replication process of stopping all processes to checkpoint seem a little unnecessary and cause scalability issues at a large enough scale. Fundamentally it's an interesting read, though the Microsoft specific techniques to mitigate a serious issue (stragglers) seem like a cheap workaround. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">Naiad is a general-purpose system that fulfills the requirements for batching processing, streaming processing and graph processing system. At the same time, Naiad achieves the same performance.</div>
  <div>Response:</div>
  <p class="block"> Every time, a new distributed programming model is introduced, it is tightly coupled with the underlying execution model. And this leads to a proliferation in vertically integrated systems that struggled to interoperate with each other. For example, we have batch processing systems, like MapReduce, spark. They are based on stateless deterministic and synchronous task execution model. And we have stream processing systems, like Storm. They use asynchronous streaming models. We also have a graph processing model like Pregel, GraphLab that are specialized to use a vertex oriented execution model. If you want to combine these models, you can only use the low-level processing workload which is batch processing. Some of the tasks are fine. But what if you say you want to feed your stream into a graph processing algorithm or combine the results of a graph-based machine learning algorithm with the results of a batched job. Naiad, a timely dataflow is trying to solve this problem by using a common operator  to support all of these problems in one system. </p>
  <div>Comments:</div>
  <p class="block">Question1 : Not clearly about the could result in relation in the paper
Question2 : Not very clearly about the distributed progress tracking. How the nodes know whether there is more input. 

Pros: I like the writing in the paper. When they discuss the optimization,  they will point out where the related experiments are. 
Pros: The author are knowledgeable. The Infiniband is used in the system which I have never heard before.
Cons: The experiments in the real world application is not convinced to me. The batch processing system they choose to compare are all about graph. I expected they compare with spark. But they do not. In the meantime, I am not super sure the systems they choose are better then spark?</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">This paper introduced timely dataflows and Naiad, its counterpart distributed system implementation. Timely dataflow modeled as a directed graph with structured loops that allows information to propagates along the graph's vertices while allowing feedback within a loop in a constrained manner.  </div>
  <div>Response:</div>
  <p class="block">Naiad provides a set of low-level abstractions following the timely dataflow computational model which enables programmers to build higher-level abstractions that map to a variety of different applications that are characterized by iterative and incremental computations in an efficient manner. In addition to the set examples of different applications they implemented on top of Naiad, the timely dataflow model would enable data control support since information is propagated along the graph, it can enable better access control over the data.</p>
  <div>Comments:</div>
  <p class="block">-</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">This paper propose a prototype implementation of an environment for running programs on big datasets, which is a timely dataflow for data-parallel computation in a distributed cluster. It provides low-latency access, iterative sub-computation, and a consistent intermediate outputs so that sub-computations can be nested and composed. Based on some results taken from real-world applications, Naiad shows a considerable performance increase in iterative and streaming computation.</div>
  <div>Response:</div>
  <p class="block">Maybe they can further investigate the use of other specialized and modified protocols, such as RDMA, or maybe TCP-based protocols that in spite of reliability provided by TCP, they could suffer less from the overhead. Some performance evaluation maybe accompanied by some QoS metrics could be nice.</p>
  <div>Comments:</div>
  <p class="block">It would have been very useful if the paper used different words for edges/vertices in logical data flow graph versus those in the input graph that workers compute and modify. This gets very confusing at places.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">Naiad is a distributed dataflow execution program where data is handled by vertices passing around timestamped messages. To use Naiad, a programmer defines a dataflow graph consisting of input stages, computational stages, and output stages and then repeatedly supplies the input stages with data. Naiad has two separate components: processing updates to the data and doing low-latency querying of the application state. </div>
  <div>Response:</div>
  <p class="block">
An interesting further direction of research would be to make timestamps in Naiad work similarly to the way that RDD uses lineage information. Naiads stateful vertices already have CHECKPOINT and RESTORE interfaces to which data can be reverted in the event of a failure. 
However, RDDs dont have checkpointing overhead because they use lineage. Naiad seems like it could feasibly do the same since the messages are effectively already lineage. The fact that Naiad is doing this on a graph of arbitrarily deep loops complicates this decidedly, but that is also what makes it an interesting research question. </p>
  <div>Comments:</div>
  <p class="block">Naiad leaves a lot of low-level management to the developer which seems like it could get cumbersome in practice. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">The paper introduces Naiad, a distributed system for executing data parallel cyclic data programs which is claimed to offer high throughput batch processing, low latency stream processing and the ability to perform iterative and incremental computations all in this one framework. The paper starts by introducing and giving great details of a new computational model, timely dataflow, which uses logically timestamped messages to support low-latency concurrent execution of different epochs and iterations. It then explains how this timely dataflow model is implemented on Naiad and provides extensive performance evaluation on the system applying on different applications based on different metrics.</div>
  <div>Response:</div>
  <p class="block">First, the timely dataflow model is proposed. This abstraction is based on a directed graph with input and output vertices with logically timestamped messages flowing along the edges. Each vertex implements two callbacks ONRECV and ONNOTIFY, and together with the partial ordering constraint on the timestamps make the model capable of concurrent execution of different epochs and iterations and make sure that vertex notification is issued only after all messages with a specific timestamp have been delivered. The paper provides much more detail on how the vertices adjust those timestamps and how scheduler keeps track of and delivers events in section 2.

Then the paper goes to introduce Naiad, the high-performance distributed implementation of timely dataflow. Each worker hosts several stages of the timely dataflow graph and is responsible for delivering messages and notifications to vertices in its partition of the graph. For distributed progress tracking, the workers use occurrence counting for each active pointstamp to make sure that there are no outstanding events in the system with a pointstamp that could-result-in the pointstamp of the notification before delivering a notification. This protocol makes sure that no local frontier moves ahead of the global frontier considering all outstanding events in the system. The paper also gives some information on optimizing broadcast updates, achieving fault tolerance and preventing micro-straggler.

The paper also illustrates how developers actually write programs with Naiad by providing a prototype where developers are required to define a dataflow graph and repeatedly supply input stages with data. Libraries are also available data parallel patterns for data parallel patterns and graph construction patterns so that it is it is handier for the programmers.
</p>
  <div>Comments:</div>
  <p class="block">First, this paper is indeed not easy to read. A lot of information is packed in the 16-page long paper explaining technical details from the computational model to its distributed implementation. Specifically, I find the part on timely dataflow extremely hard to follow, with all its notation on vertices, edges and timestamps. And similar for the Naiad implementation part when it tries to explain how workers communicate with each other. Some graphical illustration may help clear things a bit.
Secondly, it all comes to is it worthwhile to have the whole new system of Naiad despite its better experimental performance. Although Naiad achieves high throughout, low latency and iterative computation in one single framework and claimed to significantly outperform Spark in latency performance, it comes with a lot of complexities. Spark comes with a much simpler user interface while Naiad requires users to interact with a dataflow graph which is complicatedly stateful and timestamped. In addition, some fundamental features such as fault tolerance is only able to be achieved with much more user intervention where checkpointing and restoring logic needs to be provided. This is much more complicated than other systems such as Spark. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">With the timely data flow computational model, Naiad is a distributed system for executing iterative and cyclic computation tasks. The paper claimed that Naiad can be applied on many higher level programming model such as PageRank, WCC, ASP, etc, and optimized on latency and runtime. </div>
  <div>Response:</div>
  <p class="block">As stated in paper, the current systems lack the ability to provide low-latency results for non-iterative algorithms and batch system. Those systems are specialized and also cannot guarantee consistency for iteration tasks. The Naiad is developed to be general-purpose meaning that it is extensible to other systems and is able to satisfy both low-latency and high consistency results for iteration tasks. The performance of Naiad should be no worse than the current systems as well. I think it is important because it applies a new computational model, the timely data flow, that uses stateful vertices and edges to track iteration process. It explores the possibility of using this new model on iterative system design. The results of Naiad also proves its importance. The batch iterative graph computation costs of general-use systems are higher than the Naiad. </p>
  <div>Comments:</div>
  <p class="block">The paper provides a lot of analysis on Naiad and how it works. It would be nicer to have more details about how to apply Naiad on a widely-used application such as the WordCount described in performance evaluation. The paper is hard to read for me especially the fault tolerance and preventing micro-stragglers parts. I am unfamiliar with computer networks, so how the Naiad utilizes TCP to address latency issues is ambiguous to me.   </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">Lots of data processing tasks need low-latency, consistent intermediate output and iterative processing, but no existing system satisfies all three requirements. Naiad is a general-purpose system that fulfills all these requirements by supporting three features:  1. structured loops allowing feedback 2. stateful dataflow vertices that consumes and produces records without global coordination 3. notifications for vertices once they have received all records for a given loop iteration</div>
  <div>Response:</div>
  <p class="block">Fault tolerance in Naiad cannot be simple if it relies on preventing multiple sources of micro-stragglers at the same time. Naiad has to deal with network protocol optimization, vertex state contention and .NET garbage collection. While each individual mitigation may work, it may not be effective with many machines and a large pool of users.

The authors refer to a lot of different edges and vertices in various sections, and it's hard to follow which one they're referring to. </p>
  <div>Comments:</div>
  <p class="block">The description of the looping structure is unclear and doesn't illuminate the reader on how it works.</p>
  </p>
<hr />

