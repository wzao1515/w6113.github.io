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

  <div class="block">This paper describes the design of the Gamma database which horizontally partition across multiple disks, use the hybrid hash join algorithm and dataflow scheduling.  With these techniques, the system enables large ralations to be processed by multiple processors concurrently without incurring any communication overhead. The paper also provides a thorough performance evaluation which demonstrates the good speedup and scale up.
</div>
  <div class="block">S1: Take full advantage of I/O.

S2: Add a split table to the Hybrid hash join.</div>
  <div class="block">W1: Not clear about how the split table performs.

W2: The aggregate operation is kind of simple</div>
  <div class="block">D1: The design takes full advantage of I/o. The method that uses hash and range hash to partition enables that some queries can execute in one partition. So many disks can read data at the same time.

D2: Gamma modified the centralized algorithm by adding split table. And send the partition to different processors to compute. 

D3: The paper states that there are three kinds of split tables. But the readers are not sure whether there is a guarantee how the split table works.

D4:The aggregate operation only supports the operator that could compute locally. Then gather the result later. This way is kind of restricted now.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The main contribution of the paper is the implementation of Gamma, a share nothing distributed system. The paper describes parallelized versions of common hash-based join algorithms, performing a fairly thorough evaluation of joins and brief description of other operators such as select, scan, aggregate etc. In addition, some paper also describes details of implementing distributed systems such as data partitioning, data flow, concurrency control and failure management.</div>
  <div class="block">S1 - Hashing on the join attribute to reduce communications cost is a smart way to take advantage of the particulars of an algorithm to reduce a bottleneck in the system (network communications), and it's great that speedup is analyzed with respect to both. Analyzing selects with non-indexed, indexed and clustered index tables is also very thorough. </div>
  <div class="block">W1 - I'm not convinced that tuple by tuple streaming is the most efficient method for data flow. As seem in previous main memory papers, processing data in blocks should be more efficient. It would also be great to see a more thorough treatment of different data partitioning methods (round robin vs range vs hashed).</div>
  <div class="block">D1 - The concept of a shared nothing architecture is first coined by Stonebreaker and has previously been implemented as mentioned by the authors. Grace, simple hash, hybrid and sort-merge are also existing algorithms. The main contribution of the paper however is that it undertook the difficult task of putting together such a distributed architecture and modified the algorithms for such a distributed architecture. I'm left with a deep impression of the pains and finicky details involved in distributed computing - concepts such as processor memory limitations, DMA and controller bandwidth must be taken into account, as well as network communications costs and actual data flow. In a previous paper by the same authors, a analysis of the impact of memory on the 4 join algorithm was done, while this paper explores the speedup and scaleup of these algorithms with respect to the number of cores. The paper shows that there is linear speedup and almost 1 to 1 scale up for the system as long as there is sufficient memory (with respect to the hybrid algorithm, which is expected)


D2 - The chained declustering method is fairly intriguing - though 2 phase clustering suffers from the usual limitations of 2 phase locking and can only deal with a limited amount of failure. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper is reminiscent of System R paper and Postgres, in that the authors provide a detailed description about their experience on designing and building a system, in this case a parallel database system on commodity hardware using a shared-nothing architecture, horizontal partitioning, and hash-based parallel algorithms that doesnt require centralized control.</div>
  <div class="block">S1. The authors provided an honest take on why either some design choice worked or not and what could have they done differently.
S2.  The authors identified and used different metrics suitable for evaluating multiprocessor database machines.  
</div>
  <div class="block">W1. The authors didnt justify the choice of aborting the transactions holding the fewest number of locks.</div>
  <div class="block">D1. Gamma takes the lessons learned from previous experience that showed the difficulty on scaling up architecture with shared memory and centralized control of execution; as a result, the authors have chosen to use a shared-nothing architecture where processors dont share disk and can only communicate by sending messages through an interconnection network.
D2. Throughout the paper, the authors acknowledge sub-optimal design decisions they made such as choosing to decluster all relations across all nodes with disks, and they explain the better approach that they would take if they had the chance, in this case, use the heat of a relation to determine the degree to which the relation is declustered.
D3. Gamma assigns different tasks to different processes as follow: background process for the catalog manager that overlook all the conceptual and internal schema information for each database, one query manager process per user, a set of scheduler processes which control the execution of each multisite query, a set of operator processes, deadlock detection process, and a recovery process.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The Gamma database machine is a shared-nothing architecture for a distributed database that splits tuples to be processed through horizontal partitioning among disk drives and uses hash-based parallel algorithms for computation.  A hypercube serves as the communications network between the individual processors, whose partial outputs are synthesized by a single process at the end. This allows the creation of a potentially infinitely wide distributed system.</div>
  <div class="block">S1: The Gamma machine is scalable and non-dependent on processors because it is a shared-nothing system. This is the system's main goal and it appears to have been convincingly accomplished, as shown by the figures illustrating runtimes and the tuple numbers and hardware specs the authors cite.
S2: The notion of chained declustering failure management, though only a proposal and not an implementation, is an elegant and well-thought-through recovery system for a shared-nothing architecture.</div>
  <div class="block">W1: The reasoning for breaking down Gamma into the processes they did could be a lot better explained. Given the level of detail in which the authors explained other elements of their design process, from the page size to the algorithm design, this seems like a fundamental part of the paper to gloss over the design of.</div>
  <div class="block">D1: For further exploration: the operating system in Gamma does not currently provide virtual memory. It would be interesting to see if using virtual memory for computation provides much cost reduction.
D2: The authors approach in discussing problems they encountered while designing, for example that dereferencing null points on VAX version didnt throw any errors, or that increasing the page size to more than a disk improved performance, helps make their final case much more convincing and appear much more optimal than if they had not explained these prior missteps.
D3: All Gammas relational operator algorithms are written as if they would be run on a single processor. Then all the partial results are sent to a single process which does the work of combining. A potential interesting next exploration would be to create tiers of recombining, and perhaps design algorithms that are optimized for different orders of magnitude.
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">GAMMA uses a shared-nothing design to implement a distributed database system. GAMMA minimizes coordination among nodes, which removes the bottleneck of a centralized coordinator. GAMMA also takes advantage of the distributed architecture to design operator implementations that are more parallelizable. </div>
  <div class="block">S1: The paper is very introspective, aware of its own weaknesses and areas of improvement that could be made, such as over-partitioning relations that don't need much partitioning and constraints due to interfacing with the underlying hardware.
S2: The speedup and scaleup experiments demonstrate the effectiveness of not only GAMMA specifically, but of applying a distributed solution to massive amounts of data.</div>
  <div class="block">W1: While some operators are insensitive to partitioning approach (e.g. selection), the default hybrid hash-join is dependent a specific partitioning approach. 
W2: In a similar vein to W1, users can only choose one partitioning approach for a specific relation. This limits parallelizability to operators that play well with the partition model. 
W2: The paper does not address the fact that a centralized logging system is a potential bottleneck, since transaction commits depend on successful writes to the log.</div>
  <div class="block">D1: To moderate W2, it is worth pointing out that for consistency purposes, it is necessary for some form of centralized structure to exist. However, logging is still an aspect of the system that would not scale well because of its centralization.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper introduced the implementation of a scalable parallel database system called Gamma database in details, this work try to reduce the gap between the growth of CPU and disk access speed and solve the bottleneck caused by I/O.</div>
  <div class="block">S1: Relations are partitioned across nodes in the cluster, allowing for parallel data scans without any specialized hardware.
S2: Gamma popularized hash-based parallel algorithms for join and aggregate operators.</div>
  <div class="block">W1: The shared-nothing architecture continues to be the dominant form of scaling databases.
W2: Not surprising that a balanced system would require accounting for the high startup costs of disk operations. Aggregate disk bandwidth is not the problem: initiation and similar latencies are the problem. Surprising that the authors didn't deal with this up front.
W3: As authors acknowledge, it's not necessarily always a good idea to horizontally split relations across disks.</div>
  <div class="block">D1: On the organizational structure of storage, this part of the problem is how to divide the data into different machines through a strategy, which are used a lot in current system, whether it is the architecture of those large sites or those NoSQL database, there are their shadow. The major methods of round robin, hashing and range partition.
D2: On the process structure in Gamma, the catalog manager and query manager processes are placed on a host machine that accepts user requests. The query manager is responsible for compiling the SQL query, optimizing the query, and ultimately generating an operation tree. The scheduler process, which accepts the operation tree generated by the query manager and executes it on the various machines distributed by the operation nodes in the tree, should also be responsible for coordinating synchronization between the nodes. The operator process is responsible for processing an operation, which is sent to it by the scheduler process. There are also processes dedicated to deadlock detection and logging.
D3: On the operation of the implementation algorithm:
For selection operations, Gamma takes three optimization approaches: 1. Indexing attributes; 2. Compiling predicates into machine code to improve the efficiency of filtering tuples; 3. Using prefetch strategy.
For join operations, the main focus here is on the implementation of the hashtable overflow handling method, which is less detailed and should be more complex, preferably with an adaptive strategy.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The hypothesis is that it is possible that given a database that can have multiple processors and data distributed among multiple hard disks, it is possible to build a system that parallelize the database operations (join, read, aggregate, etc.). Currently, while the results seem promising, there are a few other metrics they could have included in their results which may have permitted for easier improvements to their system. </div>
  <div class="block">S1  It is good they mentioned the earlier versions of Gamma and the flaws of their earlier versions
S2  Building a personalized query manager that can compile the queries for the users and cache their queries is a smart idea to keep the cost of operations low. It also attempts to avoid multi-processing if possible, to avoid the overheard of managing multiple processors to handle the query as stated in Section III scheduler section.
</div>
  <div class="block">W1  In the paragraph of Section II says, In addition, the CPU is interrupted 13 times during the transfer of one 8 Kbyte block partially because a SCSI disk controller is used and partially because of the FIFO between the disk controller and memory. </div>
  <div class="block">S1  In the case of Gamma 1.0, they went into a detailed discussion on how the lack of virtual memory, and how adding protocol headers to managing this multi-process database operating system ended up causing more issues. At least with this information, researchers would know it was not worth putting in the effort to building new protocols for this kind of system.
W1 - Considering interrupts are extremely costly, is there a way that the interrupts can be further minimized? They could have argued that this is best possible number of interrupts because of the system architecture. This concern of interrupts effecting performance is not mentioned in the Results as well, where it could be critical to improve on this system.
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The authors proposed a new design and implementation of a highly parallel database machine, and proved that they can achieve a nearly constant response time for both selection and join queries as the workload is increased by adding a proportional number of processors and disks. With the benefit of hindsight now, one can see that the success of this paper contributed to the rise in use and designs of parallel database systems.</div>
  <div class="block">S1 - Authors showed how they took the lessons they learned from DIRECT and Gamma Version 1.0 to build Gamma Version 2.0. It is impressive that they scrapped the progress they obtained with Gamma Version 1.0 and iterated on Gamma Version 2.0 for five years to achieve what they finally built. </div>
  <div class="block">W1 - I cannot think of a weakness of the paper. I think the authors are convincing regarding the viability and success (in terms of the ability to speed up processing by increasing the amount of processors) of a parallel database system. </div>
  <div class="block">D1 - It is an impressive paper filled with many concepts, such as how relations could be partitioned, how query processing works on the partitioned tuples effectively, how the query optimizer minimizes queries across nodes, and much more. The authors also show conclusive results that increasing number of processors contribute to speedup and response time.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The authors of this paper claim that network communication is the speed bottleneck of distributed joins and therefore seek to improve speed by reducing network communication. The authors reduce network communication by tracking the number of matches and the locations of each tuple involved in the join and analyze these tracked statistics to broadcast the matched tuples such that the least amount of data is transmitted over the network. Finally, the authors further reduce network communication (and thus further reduce speed) by testing various compression techniques on the join statistics.</div>
  <div class="block">S1: This paper features extensive details on how the authors implemented their algorithms and their extensive tests of their algorithm, right down to phase-by-phase testing of the algorithm and specifications o the hardware they ran it on. The results of this paper seem highly reproducible, which lends a lot of credibility to the authors results.

S2: Although the breakdown of the algorithm into three incrementally improved algorithms was somewhat confusing at first (as the intermediate steps didnt seem entirely necessary), when the authors discussed the tests they ran, it was highly illustrative to have statistics for each different phase of the algorithm - this proved that each improvement was actually useful (well, for the most part - see W1)

S3: In the introduction, the authors noted that they tested a number of queries and found that the most expensive five queries spent 65-70% of their time transferring tuples on the network, which would account for 14.7% of the total time required to execute the entire analytical workload with more than 1500 queries. It was extremely helpful in understanding the relevance of the problem that the authors were able to quantify their motivating problem in this way. </div>
  <div class="block">W1: The statistics dont seem to show much, if any improvement between 3TJ and 4TJ. Although 4TJ shows a few-ms speedup in figs. 5 and 6, I question whether the increased complexity created by an additional layer to the algorithm is an example of prematurely over-optimizing (and thus overcomplicating) what seems like a fairly novel paradigm for distributed join processing.

W2: It took a few read-throughs to gain a working understanding of what the algorithm was actually doing - although fig.1 did a great job illustrating the differences between the algorithms, perhaps a more in-depth illustration or verbal explanation of what was actually happening would help the reader to understand which tuples were moving where (since there are two tables involved, sometimes its hard to figure out which table the authors are referring to in their explanations).

(all in all, I felt this was a pretty strong paper, even if I struggled a bit to understand it at first)</div>
  <div class="block">D1: I appreciate the fact that the authors noted that additional gains could be realized by stacking compression on top of their algorithm - this highlights potential means for further work on this subject.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper describes the Gamma database, a distributed database with a shared-nothing architecture. Tables are horizontally partitioned across nodes (which enables parallel scans). Query operators are automatically parallelized using split tables, which demultiplexes a stream of tuples into multiple streams of tuples (one per node).</div>
  <div class="block">S1. I liked how the paper wrote about some of the engineering tradeoffs that they made -- from how the original hardware had bottlenecks to the how changing all for-loops over pointers to use something network-agnostic, there were a lot of nice tidbits about actually building the system.

S2. Using hashing for shuffling data across nodes is a neat and elegant idea. It gets a lot of uses today for similar tasks (e.g. shuffling data in MapReduce). </div>
  <div class="block">W1. I found the discussion on failure management was a little lacking -- it mostly focused on replicating data across disks for recovery. But what happens during query processing? If a node fails in the middle of a query, how does the query planning adapt?

W2. One thing the paper didn't mention was rebalancing across the horizontal partitions. What happens if a node gets significantly more data than other nodes? If you had a skewed data distribution, this would almost completely kill your parallelization wins (by Amdahl's Law). There also seems to be a pretty simple answer -- split your relationship into many more partitions than nodes (P &gt;&gt; N), and then maintain the same partition -&gt; node split table. That way, if any partition gets significantly larger than another, you can move partition onto other nodes. This keeps the overall architecture mostly intact, with a small tweak to the split table.</div>
  <div class="block">D1. I feel like in most modern distributed databases (even for a simple sharded database), there's a lot of emphasis on keeping queries shard-local to avoid communication overhead, and I didn't really get that priority is shared in Gamma. Were the special Intel Hypercube hardware fast enough and reliable enough to make cross-node communication a non-issue?

D2. In a lot of ways, I feel like this architecture is more suited to a shared-memory multicore architecture (presumably using some form of RAID for disk parallelism)? It's true that the design is technically shared-nothing, but that's really only because you have a central catalog manager / scheduler that seems to do a lot of heavy-lifting and would probably be better as a shared-memory structure.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The authors designed, implemented, and tested a highly parallel/scalable database machine called the Gamma database machine. The shared-nothing architecture on which Gamma is based is highly scalable, so Gamma software is  (quite successfully) designed and optimized to scale as well. The major software techniques are horizontal partitioning of relations across disk drives, hash-based parallel algorithms, and dataflow scheduling. </div>
  <div class="block">S1: Testing demonstrates that parallel hybrid-hash join and dataflow scheduling are effective. (I originally thought the centralized dispatcher process would be a major bottleneck, but testing shows reasonably small overhead). </div>
  <div class="block">W1: would have been nice to see the performance of three declustering strategies (round robin, hash, and range partition) on different workloads, to show that the different strategies allowed for better performance on different workloads. the partitioning method was also taken into account by the query optimizer, so partitioning method could potentially have a significant effect on performance. As they acknowledge, a better approach would be to use the "heat" of a relation to determine the amount of declustering--this also suggests the success of declustering is highly dependent on data properties.

W2: I think chained declustering still has some problems. If two adjacent nodes fail, we suffer loss in data availability. As the authors acknowledge, the dynamic reassignment of workload requires accessing a fragment of data, which may be indexed and partitioned in various ways. Even though this is addressed in another paper, it's not implemented in Gamma.</div>
  <div class="block">The paper presented two iterations of the Gamma design, as well as future work probably leading to a third iteration. In the first version, the authors discovered majors issues with running on VAX processors. The token ring network over which processors communicated limited the disk page size. Unibus (bus in VAX) was slower than disk reads, creating another bottleneck during join query processing. Finally, the processors only had 2 MB of memory, limiting the space for join hash tables, stack space, and buffer pool. In the second iteration, the hypercube + NOSE OS worked better. The horizontal partioning allowed for parallel selection/scan operations. The success of partioning depended on the properties of the data, so Gamma supported round robin, hash, and range partition methods for different kinds of data.

The paper adapts Shapiro's Hybrid Hash-Join algorithm to be parallel using two split tables, one which partitions the data and the other which aggregates them by routing tuples to appropriate processors. Split tables are useful for processing other operators as well. Performance testing shows the distributed join scales nicely (linearly) with input relation size, as long as there is sufficient hashtable space to hold the inner relation, to avoid disk write.

The paper also describes concurrency mechanisms, logging, and failure management through chained declustering.
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper describes the design and implementation of the Gamma database machine, a parallel relational database machine based on shared-nothing architecture. Gamma is highly scalable and achieves almost perfect linear speedup and scaleup performance by horizontally partitioning all relations across multiple disks, applying hash-based parallel algorithms and employing dataflow scheduling techniques. The paper also describes the mechanisms for concurrency control and failure management of Gamma and provides directions for future research.</div>
  <div class="block">S1: Gamma uses a shared-nothing architecture that contributes to its scalability.
S2: Gamma uses a dataflow scheduling technique that minimizes the overhead of control messages sent between each operator process and the scheduler process. Compared to previous shared-memory designs like DIRECT, no complex centralized disk control is needed.</div>
  <div class="block">W1: No implementation of recovery architecture and logging system. The control message between scheduler process, operator process and log manager might affect the performance of the Gamma database machine.
W2: Implementation of the parallel join/aggregate operations with hash-based partitions in Gamma does not try to minimize data transfer, which may cause much network traffic overhead. The characteristics of the relations (e.g. size, payload) and data distribution could be taken advantage of in designing algorithms that avoid redundant transfers of tuples across the network.</div>
  <div class="block">D1: One major contribution of the Gamma database machine is the use of a shared-nothing architecture. This is the major improvement from the earlier multi-processor database machine prototype DIRECT, which uses a shared-memory architecture that leads to multiple major deficiencies. DIRECTs performance is severely constrained by its limited I/O bandwidth as it is using shared disk and CPU resources. In addition, since all data is stored on the same disk, it is hard to take advantage of data locality and incurs a lot of overhead doing random seek. Gamma on the other hand, partitions its data horizontally across multiple disks so that the I/O bandwidth could be expanded incrementally. Also, by associating one or more processors with each disk it maximizes the amount of processing done locally and thus requires smaller bandwidth for the interconnection network.

D2: Another strength of the Gamma database design is that it does not need complex centralized control for query processing. DIRECT, with a shared-memory architecture, requires complex algorithms for disk controllers to coordinate parallel processes. For complex relational operations, the number of control messages is proportional to the product of the sizes of the two input relations (at least one control messages for each data page), which creates a huge overhead. Gamma on the other hand, adopts a dataflow scheduling technique to coordinate the execution of multi-operator queries. Each multisite query is controlled by a scheduler process which starts operator processes at different processors. The algorithms run by those operator processes are written as if they were to be run on a single processor. Their execution is completely self-scheduling except for the three control messages to initiate and terminate the operation. This significantly reduces the overhead of controller overhead of a shared-memory database like DIRECT.

D3: In general, this paper does a really good job in presenting the design and implementation of the Gamma database machine. However, to strengthen its argument and design, there are a few more things to consider. First, the recovery architecture for Gamma has not been implemented yet. Although Dewitt et al. proposed the use of WAL for logging and recovery, it has not been added to the system. Thus it lacks experimental evidence how this addition would impact the performance of the database. Because operator processes generate log records upon update, query processors send these records to log managers and then written to disk, and scheduler processes send commit or abort records to log managers, these messages all take time and could become new overhead to the system.

D4: Another flaw of the proposed Gamma database is that all four join algorithms (sort-merge, Grace, Simple and Hybrid) supported by the system are implemented with hash-based partitioning. While these join approaches is good at splitting the workload and process multi-operator queries in parallel, it does not take advantage of characteristics of the relations (e.g. size, payload) and data locality to minimize data transfer. This generates a lot of unnecessary network latencies which is especially unpromising today when network latencies typically dominates I/O bandwidth. For example, if one relation is significantly smaller than the other, it would be easier to broadcast the smaller relation to all nodes rather than hash partition both relations across all nodes. In other cases, relations are co-partitioned on the join attribute, so there is no need to take the pains to redistribute all the tuples. Instead, appropriate algorithms could be applied to generate the optimal transfer schedule for each distinct join key to migrate the matching tuples of each key to a single node [Track join: Distributed Joins with Minimal Network Traffic]. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper introduces a new multiprocessor database architecture called Gamma which employs a shared-nothing feature. The hypothesis is that the DIRECT machine has several deficiencies such as use of shared-memory and centralized control for the execution algorithms. The Gamma machine was created to address the difficulties of scaling database system to hundreds of processors. </div>
  <div class="block">S1: The performance tests are thorough. They shows a linear speedup of increasing processors and the less response time when using cluster B-tree index.  
S2: The Gamma machine implements the JOIN algorithm describes in the paper Join Processing in Database with Large Main Memories. It applies the original hybrid hash-join algorithm to a parallel system which is novel. </div>
  <div class="block">W1: In the introduction, the paper talks about several other database machine similar to Gamma such as Teradata, Bubba, and Tandem. In the paper, it is unclear how Gamma is better or worse than other parallel database systems. </div>
  <div class="block">D1: Some questions I have: 1. why a cluster size of 4-8 is optimal in the trade off between cluster size and the probablilty of data be unavailable during disk failure? 2. The Gamma is built on top of NOSE specialized database management system which uses semaphore to protect critical sections, can the Gamma also be built on other operating system as well such as UNIX?
D2: I think the idea of chained declustering mechanism is very interesting. It dynamically balances the workload of processors based on the entire system. Consider a system with hundreds of processors, the overall increase in workload because of the failure of few processors will become negligible. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper proposed a shared-nothing architectured and horizontally scalable Gamma, which is unlike the previous suggested scalable database system DIRECT, and the biggest difference between Gamma and DIRECT is the way of nodes' communication. The result is that Gamma could break some bottlenecks of DIRECT such as the scalability, and could have a better performance in terms of doing joins and aggregations by its parallel algorithms.</div>
  <div class="block">S1
The evaluation is quiet detailed and make sense. First of all, they did take network latency into consideration. To avoid that influence, they avoid returning data to the host, instead, they just store the result in the machine; the latency between nodes is also not a problem in this scenario. Secondly, in the speedup evaluation of selection, they categorized clearly (divide the selection into non-indexed and indexed, indexed also has clustered-indexed and non-clustered-indexed).
S2
The join operator uses hybrid hash-join algorithm for the parallel version, I think this is a good implementation of the last paper we learnt.</div>
  <div class="block">W1
I think there is one weakness using hashed partition method for data storage. According to the paper, one node is like one bucket that contains all tuples of the same hash value. If one more node is added into the network or one node is removed, all of the disks need to be rewrite.</div>
  <div class="block">D1
For S1, I think one issue that might potentially weak the evaluation is about the scalability. The experiment used 30 processors at maximum, but it said the network could be extended as 100 processors.</div>

  </p>
<hr />
