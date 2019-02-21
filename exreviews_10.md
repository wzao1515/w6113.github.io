---
layout: page
---


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

  <div class="block">The volcano is a query evaluation system which encapsulated parallel query evaluation in a single module. It demonstrates extensibility for the uniform interface between operators. It demonstrates parallelism by including an exchange operator that allows intra-operator parallelism on partitioned datasets like Gamma, and both vertical and horizontal inter-operator parallelism. The encapsulated allows for the query algorithms to run on a single machine and run in a parallel environment with too many modifications. </div>
  <div class="block">S1: The exchange module  implements three forms of parallel
processing within a single module, that it makes parallel query processing entirely self-scheduling, and that it did not require any changes in the existing query processing modules, thus leveraging significantly the time and effort spent on them and allowing easy parallel implementation of new algorithms.

S2: This paper is in an objective tone and fairly discuss the overhead of the system. The summary and comparison with Gamma is very clear to readers. </div>
  <div class="block">W1: The parallelism is all based on inter-processing communication. They do not consider parallelling using threads.
</div>
  <div class="block">D1: For S1,  the exchange module implements vertical parallelism, bushy parallelism, and intra-operator parallelism. Vertical parallelism is implemented by the exchange operator, the consumer and produce model. Bushy parallelism means concurrently execute different subtrees of a query tree. And intra-operator parallelism is computing the same subtree at the same time. This involves partitions.

D2: The paper proposed a fork method to create processes which demonstrate to be faster than before. But the paper does not consider
thread parallelism.

D3: This paper notes that spin-locks are quite effective in a multi-processor environment which is an interesting finding.  It demonstrates the context switch is more expensive than hundreds of the spinlock. 

D4: From 4.5, the paper discusses the file system modification. It adds device busy/map busy locks and buffer locks. And it proposed a two-level scheme for buffer locks.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper describes the design and implementation of Volcano, a new dataflow query processing system that effectively combines extensibility and parallelism. It introduces an exchange operator that encapsulates all parallel processing issues in the system, which allows developing operators and query processing algorithms in a single-process environment but executing them in parallel. File System modifications and a brief performance analysis are also addressed in the paper.</div>
  <div class="block">S1: Volcano is built with great extensibility via support functions and streams, which allows for easy introduction of new operator and operator implementations.
S2: The exchange operator combines extensibility and parallelism by allowing each operator to be designed and implemented in a single-process environment.
S3:  it designs its system with future considerations (e.g. shared-nothing architecture) in mind.</div>
  <div class="block">W1: The introduction of the exchange operator in various scenarios such as vertical and horizontal parallelism lacks concrete examples and graphical illustrations.  
W2: Quantification and experimental analysis is lacking when it comes to tradeoffs between different design decisions (e.g. concurrency control, a separate scheduler process vs self-scheduling). This makes a lot of arguments weak.</div>
  <div class="block">D1: One feature specifically emphasized by the Volcano System but ignored by previous systems we have read about is extensibility. A standard interface between algebra operators allows easy addition of new operators and operator implementations. Quite a few mechanisms together contribute to this extensibility. First, all manipulation of data objects such as comparison and hashing is passed to the iterators by means of pointer to appropriate support functions. This split into iterator shells and support function s separates iteration and data manipulation so that new manipulations could be added to the algorithm shell easily. Secondly, the anonymous inputs or so called streams execution model provides a powerful abstraction so that an operator does not need to know what operators produces its input. This again greatly contributes to Volcanos extensibility because it allows for combining any number and any kind of operators to evaluate a complex query.

D2: Based on the above system design with great extensibility, the paper introduces the exchange operator that combines extensibility with parallelism. Previous systems such as Gamma achieves parallelism through hash-based partitioning. As each disk is accessibly only locally, algorithms such as joins and aggregations are implemented in a way that the input stream for the operator is partitioned into disjoint segments and executed on multiple processors. A modification to the scheduler algorithm is thus need for controlling and organizing a multi-site execution. This introduces difficulty in introducing new operators and operator implementations to the system. Volcano however, encapsulates all these parallelism issues by introducing a new operator exchange. It supports all three types of parallelism by simply inserting it into a complex query tree where parallelism is desired. And all operators could be designed and implemented in a single-process environment. In this way, it combines extensibility with parallelism by making data manipulation and parallelism orthogonal.

D3: Another strength I with this paper is that it designs its system with future considerations in mind. Although Volcano runs on a shared-memory multiprocessor, when deciding on the paradigm of a data-driven dataflow, the paper directly addressed that even though a scheme with request messages (demand-driven) would probably perform well on a shared-memory machine, it chooses a paradigm for data exchange that performs well on a shared-nothing machines. This is based on their belief that high degrees of parallelism and true high-performance query evaluation requires a closely network. And indeed in a 1994 paper [Volcano  An Extensible and Parallel Query Evaluation System] Graefe points out the future work of extending Volcano to distributed-memory systems.

D4: This paper does a great job in proposing the novel query processing technique that encapsulates parallelism while maintaining extensibility. However, there are a few flaws with the paper that could be more carefully dealt with. First, The introduction of the exchange operator in various scenarios such as vertical and horizontal parallelism lacks concrete examples and graphical illustrations.  Although an example is shown in section 4.3, it is too abstract using operators A, B, C and D and exchange operator X and Y. It would be much helpful to make these concepts concrete by giving an example query that achieves parallelism with the exchange operator inserted between operators such as scans and joins in a bushy query tree.

D5: Another flaw is that the author makes weak arguments when it comes to the comparisons between previous systems and Volcano and when justifying a lot of the design decisions. Admittedly this is due to the scope of the paper, but the fact that a lot of the tradeoffs are not carefully dealt and quantified makes the design not as strong. For example, no quantification of the tradeoff of using a pool lock vs locking each search bucket is provided. Also, although theoretically Volcano show much better extensibility than Gamma, the performance in parallelism is unclear. Graefe also admits that the advantages and disadvantages of a separate scheduler process in a distributed-memory query processing system like Gamma versus a self-scheduling system like Volcano should be evaluated. 
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper introduced the volcano query processing system and query parallelism mechanism within it, and is promising on parallel algorithm development and evaluation.</div>
  <div class="block">S1: Good comparison between Volcano and Gamma database system, based on deep understanding on both systems, point out differences but not claim superior is excellent.
S2: Did good job in high performance and low overhead by transferring data quickly with the exchange operator, and also guaranteeing pipelined multi-process execution.</div>
  <div class="block">W1: The way that Volcano introduces parallelism is a kind of too simple: just introduce a new operator, called the exchange operator at any desired point in a query tree.
W2: The structure organization of the content is a kind of unbalanced, too much on the Multi-Processor Query Evaluation part and too little on other parts.</div>
  <div class="block">D1: The iterators support a simple open-next-close protocol. An iterator can hold internal state, so that one algorithm can be used multiple times in a query.
D2: The exchange operator can be used to implement pipelined parallelism called vertical parallelism in the paper, bushy parallelism which processing different subtrees of a complex query tree in parallel, and intra-operator parallelism which partitioning the dataset and processing partitions in parallel for a single operator.
D3: The key benefit of the exchange operator technique is that is allows query processing algorithms to be coded for single-process execution but run in a highly parallel environment without modifications. Its a small step from the single host, multiple process model described in the paper to a multiple node parallel processing engine.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper describes the Volcano query processing system, a system of abstraction for logical query plans that turns each operation in the system into a iterator, where data passes through the iterators in a pull fashion with the exception of the newly introduced exchange operator, which is pushed based and used to coordinate multi-processing.</div>
  <div class="block">S1 - The abstraction is very clean and easy to follow. 
S2 - Makes it very simple to incorporate multiprocessing without complex scheduling policies.</div>
  <div class="block">W1 - overall the paper is very strong in terms content originality, but lacks a little in conciseness when summarizing its contributions, which is not a major issue since the originality of the abstraction framework is obvious compared to other database systems. 

W2 - With hindsight (as mentioned by later column store paper) iterator based physical processing is not optimal in terms of communication overhead. </div>
  <div class="block">D1 - The paper was framed as a query processing system that doesn't claim superiority to existing techniques, merely pointing out the difference briefly, with a small section devoted to Gamma in particular. However, these comparisons highlights a need and strength that the paper does not explicitly point out - the need for a clean abstraction for a flexible query plan between complex algebra and physical plan. Gamma's query plan is fairly complex, involving numerous details and alternative managed by a scheduler, which make it difficult to understand and reason about. Volcano's paradigm is very simple, comprised of a graph of operators with open, next, close. This is easy to follow as well as reason about.

D2 - The exchange operator simplifies the logic of parallelization enormously compared to systems such as Gamma by essentially hiding the different types of interprocess communities. Pipelining between processes is named vertical parallelism, concurrent processing of data partitions as intra-operation horizontal parallelism, and concurrent processing of operators as inter-operation horizontal parallelism. However, a potential downside I can imagine is that it would be harder to debug issues since the system acts differently for this operator depending on the type due to different concerns (processor threshing for instance for horizontal parallelism). It also seems like the author ended up modifying it (changing number of processes, locks, whether to broadcast etc.) based on the algorithm its used in, which makes me question if its appropriate to hide these all under one operator. 

D3 - The point that communications cost could be greater than process idle time is interesting. Eg. It's important to optimize for bottlenecks, and to balance optimization time vs time saved. 

D4 - I like that the experiment highlights the value of the exchange operator, and investigates variables that could influence it. It's expected that throughput would be much better with parallelism given that all else is the same. The exponential slowdown due to smaller packet sizes highlights communications overhead. I think for this paper, it's acceptable that it's light in terms of performance comparisons with other systems since the point was to show that this system works, not that it's faster. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The hypothesis is that given it is easier to parallelize database algorithms using exchange operators to handle intra-operator parallelization (using multiple processes to handle one operation e. g. ORDER BY) and inter-operator parallelization (using multiple processes to handle more than one operation. I currently do believe their results as their system architecture seems simple, however it does fail to have performance tests against its competitors such as Gamma.</div>
  <div class="block">S1  Used obvious techniques to improve query compilations
S2  It is a simple architecture
</div>
  <div class="block">W1  The authors using threads to handle some of the issues such as intra-operator parallelization.
W2  In Figure 2b, it seems that there is no obvious optimum value for number of records in a packet
W3  There is no performance comparison between Gamma. </div>
  <div class="block">S1 - It is a good idea on performance to pre-compile the support functions (e. g. sorting, comparisons, etc.) which means the whole query which will rely on these operations will require less compilation time.
S2 - The system architecture seems very straight forward as it seems all the records are essentially a linked list with a result id integer and a next pointer for the next record. Also, the flow of the iterators from open, next until End of Stream and close has an easy work flow as shown in the figures.
W2  The authors could have tested the bucket size with values like 100, 120, 140, 160, etc. The reason is, judging from the graph it seems starting at 83 records per bucket it starts to plateau. Does it really plateau here? Is there a point where there are too many records in a bucket as well? 
W3  In Section 4.6 and in Section 4.2 they mention they have work in progress on using process pools to save time with some operations. It would have been better if they finished the results before publishing as they can have started a debate on given a certain number of CPUs to use Volcano and in another case to use Gamma in Section 4.6.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The authors of this paper claim to have created a highly modular relational database system which encapsulates the functionality of each operator, as well as each component of the filesystem an its parallel processing functionality, into separate modules, providing better extensibility for those who want to may want to perform research involving only a particular portion of a relational database. This system, Volcano, also features a data flow paradigm that allows for parallelism to be added to a system without having to modify the internal code of the operators themselves. It it implements its dataflow mechanism using two primary techniques: pipelining and an IPC-based producer/consumer model. </div>
  <div class="block">S1: The illustrations on pages 9 and 10 help clarify how an exchange operator might be used via a detailed example. 

S2: The authors note that this is more an existence proof sort of paper than the measurable metric proof kind. However, that doesnt mean that the paper is devoid of metrics. The authors compare the systems performance against itself under varied packet sizes.

S3: The authors describe how the system design evolved as they tested performance and as their needs changed (for example, they decided to halve the number of processes running for the exchange operator and changed how the filesystem worked to handle concurrent processes). This provides excellent insight into the justification for their decisions. </div>
  <div class="block">W1: The authors claim that the future of high-performance query evaluation lies in shared-memory machines, but considering the modern push towards distributed systems, this claim doesnt seem to ring true. (see D1)

W2: Volcano only provides mechanisms for single-user query evaluation. While, of course, Volcano is intended for research purposes, the fact that this is a fairly common feature in other systems. However, implementing such a feature might break Volcanos neat self-scheduling paradigm.

W3: Once again, as Volcano was designed for research and educational purposes, its performance isnt terribly central to its success. However, if performance is the end goal of research performed using the Volcano code, researchers could feasibly be motivated to disregard the modular architecture in order to cut overhead and thus squeeze even more performance out of the system - thus defeating its purpose.</div>
  <div class="block">D1: The authors do note that their paradigm for data exchange has been proven to perform well in a shared-nothing database machine, so while this approach would certainly still work, this paper may not have been optimized for a shared-nothing machine.

D2: The need for this system wasnt particularly well-established - while one might imagine how this systems modularity might be used to perform more granular research on database systems, it was somewhat vague in addressing how widespread or urgent this problem was. 

D3: I'm a little conflicted about how I feel about tracking the evolution of a system's design. On the one hand, it helps justify the authors' decisions; on the other, sometimes it just adds bulk to the paper where the focus should really be on the finished product.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Parallelism in the Volcano query iterator model can be abstracted behind a single exchange operator while leaving the rest of the system intact.</div>
  <div class="block">S1. The overall idea of the exchange operator is really elegant -- I especially like how they adapted it to fit both vertical and horizontal parallelism (and bushy parallelism, although I think that's just a superset of vertical parallelism where you partition a DAG instead of a chain of operators)

</div>
  <div class="block">W1. The exchange-as-fork operator seems to encourage a proliferation of different processes, which could result in excessive context switching overhead. They briefly mention that they have plans to switch to a "primed process" architecture (presumably some kind of work queue / thread pool -like system), but don't go into detail about how that would works while making sure that the `exchange` operator is still composable with any other operator.</div>
  <div class="block">D1. One thing that they don't go in this paper (but seems crucial for the usefulness of parallelism) is how the system actually decides what to parallelize. The flexibility of the `exchange` operator means that there are lots of different options to chose, which increases the difficulty on the query planner/optimizer.

D2. I thought it was interesting that to parallelize sorting, they had to modify the exchange operator (by tagging the output with which exchange operator it came from, so that merging is easy). It makes me a little suspicious that other operators might require more modifications the `exchange` interface to work well.

D3. They mention that "backpressure" can be added pretty easily with a semaphore and show that the performance effect is small. Earlier, they rejected a demand-based parent-child interface because it'd have too much overhead. I'd want to see a benchmark showing that a demand-based interface is slower than the push-based interface plus the backpressure semaphore (i.e. is request packet + response packet slower than response packet + backpressure check?). This is especially true, because then the demand-based interface wouldn't require modifications for sort-merging (for example).

D4. The overhead of the non-parallelized exchange operator (25.73 microseconds per record, which I think is like ~400 clock cycles on their 16 Mhz CPU) is surprisingly large for 3 function calls that basically don't do anything. Is my intuition here just really off?</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This work presented an abstraction model for query execution that provides an extensible unified interface between operators, and of which can take advantage of multiprocessor systems through parallel execution of operators with the same abstraction model by using an exchange iterator with the goal to facilitate the implementation of parallel database algorithms.  </div>
  <div class="block">S1. The introduction of a simple and efficient abstraction model that can be generalized to support multiprocessor systems.
S2. Encapsulating parallelism issues through the exchange operator remove the overhead on the developer of managing communication between and within processes.

</div>
  <div class="block">W1. The paper lacked the presence of sufficient examples to explain their workflow.
W2. System resources are limited (number of ports can be open and processes it can create) thus could lead operators to compete for those resources.

</div>
  <div class="block">D1. Volcano represents queries as algebraic expressions and maps the operators of this algebra to a query processing algorithms which are implemented as iterators.
D2 Volcano uses an abstraction called the exchange iterator that is responsible for extending the iterator model for parallel execution and synchronization. It supports two schemes: vertical and horizontal parallelism. In the Vertical parallelism, a new process is created through the open procedure, it sets the parent as the producer and the child process as the consumer. The main difference with the single operator scheme is that the consumer receives its input via inter-process communication. The horizontal parallelism is supported by two means: bushy parallelism and intra-operator parallelism,
In the first, the operators are run in parallel, in the later, several processors perform the same operator on different subsets of data partitions.
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The main contribution of the Volcano paper is the idea of encapsulating  parallelism in the exchange operator. Past approaches implemented parallelism as part of the architecture rather than as an operator, e.g. scheduling components or data partitioning (inter-operator parallelism).</div>
  <div class="block">S1: the exchange operator allows for extensibility.
S2: successfully combines lots of past work, such as the Produce-consume model, and data partitioning for inter-operator parallelism</div>
  <div class="block">W1: Not a ton of performance analysis. As Graefe acknowledges, the performance section sought to demonstrate that overhead associated with the exchange operator was reasonable, and was not meant to be a complete analysis.</div>
  <div class="block">The main goal of the Volcano paper was to maintain extensibility without sacrificing efficiency. The solution was to use the exchange operator, which can be inserted into any normal query plan. The exchange operator then represents a "process boundary" and is an iterator with open, next, close procedures. When the query is executed, each child process acts as a consumer and each parent process acts as a producer, effectively pipelining the execution. Specifically, each exchange operator's next() call pushes the packet into the lower process. The Volcano system was designed to run on a shared-nothing architecture, which seems to be ideal for parallelism as we saw in Gamma. It is hard to find weaknesses in the paper because it introduces a new idea that is quite successful. As part of related work, the author discusses issues with the bracket model of parallelism, for example, needing to code a separate scheduler process.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Volcano is a database system built for efficient query processing over large quantities of data. Efficiency is the primary goal, and Volcano is designed to enable parallelism, both inter- and intra-operator.</div>
  <div class="block">S1: The design of Volcano includes small but detailed optimizations aiming to improve overall performance, such as flow control and the propagation tree scheme. 
S2: The exchange operator greatly facilitates parallelism, as interprocess communication allows multiple processors to work simultaneously on the same query without the expensive network overhead found in GAMMA.</div>
  <div class="block">W1: The heavy use of terminology (support functions, virtual devices, vertical, horizontal, bushy parallelism) make it difficult to follow the nuances of the paper, whereas more diagrams and code samples would aid with clarity. I found that even the example, which came with an illustration, difficult to follow.
W2: Flow control does not seem to make an impact on performance. In the experiments, there was an improvement of 0.05 out of ~16 seconds when flow control was enabled.</div>
  <div class="block">D1: I wish there had been more conversation about the pros and cons of shared-memory vs. shared-nothing. There was a detailed section comparing Volcano to GAMMA, but mostly in terms of design details rather than performance specifics. It is hard to say that this is a weakness of the paper, since shared-nothing is not the focus, but it was a little unclear what the relationship Volcano had to shared-nothing databases is: the authors say, "we decided to use a paradigm for data exchange that has has been proven to perform well in a shared-nothing database machine." How does Volcano work with a shared-nothing architecture?</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper proposed a new query processing system Volcano, which was able to encapsulate parallelism in one operator. They introduced a new operator: exchange operator. The key idea behind exchange operator is that an operator does not need to know what kind of operator produces its input, and whether its input comes from a complex query or from a simple file scan. The result is that they show that using exchange operator could be approximately 40% faster than not using the operator.</div>
  <div class="block">S1
Compared with Gamma, Gamma gave each operator control within its process and using flow control mechansims to synchronize those operators. Multiple operators would need threads to be realized inside a single process. Therefore, Volcano is better for both sequential and parallel query execution.
S2
The exchange operator uses data-driven dataflow, which means the communication overhead between processors could be reduced.</div>
  <div class="block">W1
I think one weakness might be this share-memory architecture is limited, which Gamma could be scalable for parallel execution, but since Volcano is only responsible for single machine query execution, this might not count.</div>
  <div class="block">D1
Operators in Volcano are activated using a tree iteration method, which means it is a top-down way. Like what it said in the given example, the first opeartor would call next process' 'open' procedure and it does not know it is a process-cross call, this is refered as anonymous input. The next operator, if it is an exchange operator, will create a port for data transfer. And finally there will be an end-of-stream sign passed back to the first operator.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper introduces a new Query processing system, Volcano, which is a shared-memory parallel database system. It contrasts with the GAMMA system, the shared-nothing system, talked about in the last paper. The hypothesis of this paper is that the idea of shared-nothing system, GAMMA, is cool but there are issues in performance and generality, so the Volcano system strived for better efficiency and symmetry in design.</div>
  <div class="block">S1: The idea of using exchange module to achieve both horizontal and vertical parallelism is interesting. It also includes some variants of exchange operator to broadcast all consumers and add merge and sort features. </div>
  <div class="block">W1: The paper argues the difference between Volcano and Gamma that includes shared-memory vs shared nothing, the completeness of Gamma system, the existence of scheduler process, and etc. It also argues that these design decisions of Volcano should not be compared with those in GAMMA. It is  unclear what lessons the Volcano has learnt from GAMMA, as mentioned in Section 2.
W2: The paper points out the potential problem: the convoy issue. They expected the problem to evaporate quickly because of their scheduling policy, but had not provided a solution yet.</div>
  <div class="block">D1: I think the paper is interesting after reading the GAMMA paper because it introduce a shared-memory system design. The key improvement is the idea of exchange operator. Based on the performance experiment, the exchange operator reduces operation time from 28s to 16s. However, I am not sure why there are no experiments between the Volcano and the GAMMA or others, so there is no clear reason why users should switch to use Volcano.
D2: Some questions I have: 1. How is the convoy problem solved in multi-processors system? 2. Why each record in the buffer is owned by exactly one operator during the fixing and unfixing? 3. Does the idea of exchange operator being applied on other future systems as well?</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Volcano Query Processing System is built on a shared-memory multi-processor with the intention of allowing parallel processing on a single module. The key tenets of the system is that it should boast high performance and be flexible without sacrificing efficiency.</div>
  <div class="block">S1 - Description of the system is clear and easy to understand. Appreciated the example in 4.3 to visualize how parallelism works in Volcano.</div>
  <div class="block">W1 - high performance overhead due to forking. Did authors consider using other parallel processing mechanisms (such as threads)? It seems like the whole point of creating Volcano was to optimize for high performance.
W2 - One of the key goals of Volcano is "flexible without sacrificing efficiency". It's not quite clear what the authors meant by flexible (can the code be easily modified to suit others? Is it because different number of processes will automatically be forked based on different needs? etc.). It's difficult to review the paper without understanding the goal.
W3 - Convoy problem not addressed! </div>
  <div class="block">D1 - With the tree structure, it seems like work grows exponentially and a lot of work is replicated. An example is on page 10, where the Y operator in each D process has to send an end-of-stream indicator to each BC process and be completed received before they can be closed. As the number of processes go up, the amount of work and waiting will go up.
D2 - Performance wasn't compared against other query evaluation systems, so it's not clear whether Volcano pushed the envelope or is merely another query evaluation system one could choose. Without a benchmark, it's not clear whether this system achieves "high performance." The authors did note that they only wanted to "point out differences" and that they "do not claim that the design decisions in Volcano are superior to those in GAMMA, but what do they claim their system to be then?</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Volcano is a query processing system that includes a novel exchange operator to allow parallel query processing. Three types of parallelism are supported, all of which use the same operator. Operators in Volcano use anonymous streams and can therefore be recombined to create any level of depth in query plans.</div>
  <div class="block">S1: Using anonymous inputs allows operators to be much simpler internally, which makes them easier to create complex query plans with. The uniform open-next-close paradigm of each operator makes their behavior consistent and easy to program new algorithms or queries against. The structure NEXT_RECORD as the return value of next that allows fixing records to the buffer also helps encapsulate the behavior of each operator. This also allows operators to call each other across, and without knowledge of, process boundaries, which is highly convenient from a user standpoint.

S2: Volcanos lack of a scheduler process makes the workflow of the system clearer and cleaner. The fact that data flow techniques are utilized both within and between processes makes it possible to examine this system at many layers of operation, and the modularization of the system also makes future incremental improvements easier to phase in. </div>
  <div class="block">W1: Why are there two algorithms each of natural join, semi-join, etc? It is not clearly explained why and when alternate versions would be called for.</div>
  <div class="block">D1: It was useful to see the authors design process around how to distribute work among parallel processes. They began with a centralized operating scheme where a master process in a group spawns child processes, and direct children of the master become masters of subgroups. Their transition from this scheme to a flat one where every master and every child fork proved to be a big improvement, and showing their earlier, poorer design choice strengthens the conclusion they end up with.

D2: While it breaks the consistency of the system to have eager evaluation rather than lazy evaluation for only the exchange operator, because the open-next-close behavior still mirrors the other operators, and because it allows using the same exchange operator for many types of parallelization, this seems like a fair tradeoff. Since the exchange operator is in some sense a parallelization operator, at different points in a query plan causes different effects. This being the case, it would be an intriguing further direction of work to write an optimizer for where in the query plan it is best to place this operator.</div>

  </p>
<hr />
