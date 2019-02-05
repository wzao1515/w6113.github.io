---
layout: page
---

## Comments on random reviews

Overall, the reviews are much better!  This paper is on the easier side to review because it's easy to read and the arguments pretty much make sense.



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

  <div class="block">This paper speculates on the DBMS performance through a detailed instruction-level breakdown of the major components involved in SHORE, a transactional processing database system.   Because of the advances in processors, memories, and networks, the author assumes that many OLTP databases can now fit into main memory and can be processed in milliseconds or less.  They modified SHORE by removing logging, locking, latching, buffer manager and found the system 20x faster in raw performance.
</div>
  <div class="block">S1 The paper is well explained and clear. 
S2 The main strength which is also the main contribution is that the work evaluates the separate overhead of each component. It is useful to help us understand which where time is spent in a modern database system.
</div>
  <div class="block">W1 Too strong assumptions have made, like the database can fit into the memory and no locking and latching needed.  Maybe it is true for 2008?
W2 The TPC-C seems too simple. </div>
  <div class="block">D1 About the weakness. "Another concern is that networks will become the new disks, but for many transaction applications, it is possible to
partition the workload to be single-sited. This is about the assumption. Is it possible now to partition the workload to be single sited? And would it introduce new memory redundancy?
D2 About the logging system. The paper mentions that "In our previous work we have claimed that this can be done for transaction systems as well [SMA+07]. If this is, in fact, the case, then the recovery code in legacy databases becomes also unnecessary overhead" The paper is not pretty sure about the point. 
D3 The TPC-C benchmark seems too simple? It only contains about 10 inserts or creates. I am not sure whether it would work for the real world system. What's most important, the data emerge compared to 2007, many assumptions are not reliable.</div>

  </p>
<hr />


<p class="review">
  <h3>---</h3>

  <div class="block">This paper examines how variants on conventional database systems - such as lockless, single-threaded, or transaction-less databases - can exploit modern hardware trends, especially the increased size and cheapness of RAM, to improve these database systems' speed by approximately 20x. The paper does this stripping out combinations of features such as logging, latching, and buffer management from an open-source database system and performing benchmarks on each variant. This paper makes the case that database paradigms need to be re-evaluated periodically to ensure that theyre still the optimal solutions and not just the optimal solutions of thirty years ago.</div>
  <div class="block">S1: The paper is very clearly sectioned, making it easy to understand the claim being made in each portion.

S2: The paper is very clear about *how* they went about removing the features they wanted to benchmark without and in what order as well as about how the authors chose to benchmark their database. This clarity increases the papers reproducibility.</div>
  <div class="block">W1: This paper does not address application crashes. While the authors point out that application crashes are more critical in some cases and less of a concern in others, it would still be helpful to have information on how this system handles application crashes so that those managing crash-critical applications could better appreciate the benefits and drawbacks of this pared-down database paradigm.

W2: The relevance of the bit on Weak Consistency and eventual consistency is poorly-explained. Its difficult to tell whether this  is a summary of existing work in the field or if this is part of their research. If this is part of the teams research, where did the data come from? How is it relevant to this paper? What is the point theyre trying to make?

W3: It would have been helpful if more generally the authors could have discussed the tradeoffs of removing these features somewhat more thoroughly - certainly for applications housing a vast store of data (such as a data warehouse), an in-memory solution wouldnt seem feasible or appropriate.</div>
  <div class="block">This paper claims that as the prototype for most modern DBMSs was put forth in the 70s and 80s, many features of these DBMSs will soon become obsolete due to recent innovations in computer architecture and hardware, such as cluster computing or RAM large enough to store the entire contents of the database.

The authors systematically remove components of the database in an attempt to increase performance. The authors break down how many instructions are spent on activities such as logging, latching, and buffer management.

The authors determine that the remaining kernel of database functionality is more cycle-heavy than instruction-heavy. The implications of this are not further discussed.

They also note that any one optimization doesnt perform substantial gains (only improving performance approximately thirty percent), but that stacking optimizations leads to much more impressive changes in performance.

The point this paper drives home is that its important to re-evaluate the state of the art every so often - just because a paradigm was the most efficient one thirty years ago doesnt mean that its still the best approach today. We also learn that while individual optimizations may provide modest gains, we should also pay attention to the performance improvements that can be made by combining the results of multiple prior research concepts.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper hypotheses is that given the current hardware advancement, and that OLTP databases have been optimized for different era of technology which with modern technology disk I/O can be avoided in its entirety; the paper explores and gives a detailed evaluation of how well different stripped down database architecture design variants perform and whats the implications this will lead to future OLTP databases designs. It reports that to achieve a substantial performance improvement to OLTP standard design, multiple optimizations need to be applied and stripping a single component isnt enough. </div>
  <div class="block">S1. The paper gave a detailed explanation of the experiment setup and performance measurements including the system they modified and the procedure they followed to disable the different components of the system.
S2. The paper gave not only an empirical study but also their implications on future OLTP designs and speculate on their performance.</div>
  <div class="block">I found the paper well written and easy to read and couldn't identify an apparent weakness.</div>
  <div class="block">D1. The performance measurements reported in this paper gives insight into which direction future research can follow and which components need further analysis to be optimized. In addition, the authors report on potential bottlenecks that can be reached once a database system high-level components have been optimized such as cache misses in the B-tree.

D2. The paper discusses the disadvantages and overhead of maintaining a log with an active-passive replication scheme and suggests active-active scheme as a better approach for replication where transactions run at each replica. This, in turn, will lead to a new set of challenges with respect to concurrency design to maintain the replicas, since resorting to two-phase commit will simply introduce large latency.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Computer technology (processors, memory, networks) has evolved since the late 1970s, but DBMSs have not evolved to the same degree. A shift towards in-memory databases means less disk bottleneck, a motivation for many "classic" features such as multithreading, logging, and buffering: now, these add more overhead than they are worth in OLTP workloads.</div>
  <div class="block">S1: Very thorough presentation of performance metrics. The results give very specific numbers for the performance improvements caused by each feature. This allows the authors to identify which features are more effective (e.g. the removal of the buffer manager) while also identifying the types of operations/instructions that are reduced. It is convincing that the authors did indeed make significant improvements to SHORE.
S2: Clear, simple improvements were made that demonstrate principles of architecture design rather than specific, minute optimizations. Many of the changes that the authors made to the system were quite straightforward: to implement logless database, simply get rid of code paths processing logs: reading/writing logs and dealing with LSNs. Modifications to SHORE were mostly modular, showing that an already-developed DBMS can be "deconstructed" without too much manipulation to produce performance benefits.
S3: The authors give very clear avenues of potential further work that would build upon their own work--there are promising possibilities for in-memory database design that the authors acknowledge. </div>
  <div class="block">W1: Using SHORE had its benefits (including simplifying the project overall and avoiding building a database system from scratch), but also restricted the amount of experimentation that could be done. The paper spent a lot of time describing the specifics of SHORE. 
W2: The overall improvements to the database system were significant (20X), but not groundbreaking or necessarily unexpected. It was pretty obvious that the the removal of each layer would produce modest performance improvements. The system was not improved to the point that commercial products would be motivated to revolutionize and strip down their products. 
W3: The authors did not really evaluate the detriments of removing the layers of logging, locking, etc. For example, there is some discussion regarding the lack of support for parallelism, but no exploration into whether alternatives are viable, e.g. database partitioning, which is mentioned.</div>
  <div class="block">D1: In regards to W1, I felt that more effort could have been made to deviate from the SHORE architecture. The authors were unable to completely remove the buffer manager layer because it permeated the codebase for SHORE.  They relied on the existing SHORE B-trees instead of creating cache-conscious pages, which they admit would have been an interesting and important area of research. I was left wondering how specific the performance improvements were to SHORE, and how reproducible the results would be in other database architectures, e.g. large-scale commercial systems.
D2: I felt that the experiment results were informative, but also not extremely strong. Although the numbers were very detailed and demonstrated the effectiveness of each improvement the authors made, the overall impact was only 20X, and it's also hard to say whether these improvements are more SHORE-specific or would carry over to other systems. 
D3: I understand that it was not necessary to build a database system from scratch to prove the points about in-memory databases that the authors put forward, but with the constraints of using SHORE, I wonder if more involvement in modifying SHORE or building certain components from scratch would have made their experiments more comprehensive.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Modern general purpose online transaction processing (OLTP) database systems support a suite of features, including log-based recovery, efficient buffer manager, and support for concurrent queries via locking-based concurrency control, that were developed in the 1970s and 80s. With the advent of fast processors and rising interest in database-like applications without the full suite of standard database features, the authors sought to find out if recently proposed alternative systems (logless, single threaded and transaction-less) were worth building.  Based on results obtained by stripping components from Shore (a modern open source database system), which yielded a factor of 20 improvement to throughput, the authors concluded that there may be cause to put more effort into investigating the merits of stripped down systems.</div>
  <div class="block">S1: The authors provided adequate context by giving a detailed description of Shore architecture before showing how they removed each of the proposed functionalities (logging, locking, latching, buffer manager calls). 

S2: Provided specific setup and measurement methodology in addition to open-source code to allow others to replicate their experiments.

S3: Detailed breakdown of speed improvements for each feature removed to support their hypothesis.</div>
  <div class="block">W1: Benchmark results only done on two types of transactions.

W2: Used CPU instruction count (vs cycle count) to measure results, which makes it potentially artifically faster and not actually faster.

W3: No mention of actual uses cases of such a stripped down DBMS.</div>
  <div class="block">D1: The paper only includes results from two types of transactions (New Order and Payment) when TPC-C involves a mix of five concurrent transactions. The authors mentioned that the five transactions have "different complexity" but failed to clarify relative complexity of New Order and Payment transactions to the transactions that were excluded, so the reader cannot extrapolate if the results would be similar if all five transactions were included. Moreover, it is not a given that significant performance advantages on two types of transactions would mean the same with other types of transactions.

D2: The authors make no mention of actual use cases of such a stripped down DBMS, only that there has been proposals advocating for them. It is easy to believe that making a system simpler would make it faster, but there are no examples or evidence of domains that would want to use such a DBMS. If no one wants to use such a DBMS, then there's no point pursuing a design. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Traditional OLTP engines should be redesigned to support modern hardware for order-of-magnitude performance gains. Large amounts of RAM means that datasets can fit in memory (removing disk latency and I/O waits), while cluster computing allows one to remove logging mechanisms in favor of high-availability replication.</div>
  <div class="block">S1. I like the performance analysis, where they broke down which changes gained the most performance. The comparison of a surgically-modified existing system to hard-coded optimized version (for a performance ceiling) and the unmodified system (for a performance floor) was also quite convincing that these techniques could be applied to existing storage engines.

S2. I found the theoretical argument about replacing logging with cluster replication particularly neat -- it's true that most production databases that I'm aware of do some kind of cluster replication for high-availability, so reusing that capability for durability makes a lot of sense.</div>
  <div class="block">W1. The authors kind of waive away network latency as an issue -- although they argue that "for  many  transaction  applications,  it is possible to partition the  workload  to  be  'single-sited', such that all transactions can be run entirely on a single node", this seems to go directly against their argument that cluster replication can obviate logging. If you need to replicate transactions across nodes for durability/recovery, won't you still need to take into account network latencies? This gets especially bad if you replicate across data centers and/or regions if you want to be resistant to, for example, AWS us-east-1 going down.

W2. I found the argument for single-threading unconvincing (especially since it was left out of the performance evaluation). While a single transaction at a time processing lets you ignore synchronization costs (except perhaps to queue requests up), it also doesn't let you use multiple cores in a CPU. The authors argue that one can treat each core as a separate node via virtualization, but it's unclear to me that that would be any faster or simpler than just using shared-memory techniques for multicore programming. Nodes already not cores (because durability requires replication across nodes -- replicating data across cores just wastes RAM), so it seems like we shouldn't pretend like they are. </div>
  <div class="block">D1. I'm curious to know how the authors would've responded to SSDs, which dramatically lower the latency of disk access compared to hard drives. I suspect the answer is "not much" (the core argument that RAM is faster than disk and that RAM is big enough is still true with SSDs), but I don't have a good sense of whether the order-of-magnitude differences between SSDs vs HDDs vs RAM make the economics work differently.

D2. In "The End of an Architectural Era" paper, the authors detail several classes of transactions that can be fast-pathed to avoid synchronization and/or undo logging overhead. AFAIK, the system in that paper (H-Store) was commercialized as VoltDB, but I can't find references to "sterile" or "two-phase" transactions in the VoltDB documentation. Are these transactions called something else now, or has the concept been mostly abandoned?

D3. The authors really only discuss the case where all data fits in memory of a single node. How would you do cross-node transactions? The H-store paper briefly mentions a kind of sharding scheme and some principles of schema design to avoid cross-node transactions, but what if the workload can't be sharded?</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper introduced and performed performance study on the architecture of Scalable Heterogeneous Object Repository by using a method of stripping, and it also isolated the components overhead in the term study method.</div>
  <div class="block">S1: Clearly illustrated the specific way of old order and new order of optimization and discovered two most significant contributors to system overhead, which are buffer management and locking operations.
S2: Illustrated the implementation of stripping out components of Shore that produced a 20 times faster benchmark judging system.
S3: Discovered interesting and meaningful observation of stripping down system that are heuristic for future work.</div>
  <div class="block">W1: It says that buffer management and locking operations are the most significant contributors to system overhead, this is true, but actually the disparity of influence between buffer management and locking is also pretty large which is nearly twice as much in most cases, but seems be ignored for discussion.
W2: Logging and latching operations are also significant, in this sense, actually there is no unimportant factors, which reveal no big difference and in some extent indicating that this part of work doesnt make too much sense or contributions.
W3: Actually B tree Keys also plays an important role in system overhead but only it was omitted in the conclusion part, which may be possible to cause some misunderstanding.</div>
  <div class="block">D1: The statement of buffer management and locking operations are the most significant contributors to system overhead, but that logging and latching operations are also significant is neither outstanding nor comprehensive. 
D2: The hypothesis that unless one strips out all of these components, the performance of a main memory-optimized database is unlikely to be much better than a conventional database where most of the data fit into RAM means it a kind of extreme way to optimize the performance of a database system by stripping its components.
D3: The hypothesis that when one does produce a fully stripped down system, the performance is orders of magnitude better than an unmodified system, implicated a totally new and exciting ideas of improving the performance of database system, and also indicated several possible developing directions such as single threaded, implements recovery via copying state from other nodes in the network, fits in memory, and uses reduced functionality transactions.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">With the possibility of in-memory databases thanks to cheaper main memory and faster processors, Harizopoulos et al. performed a performance analysis of stripped-down variants of Shore by removing progressively removing its functionality to identify the overheads of different features (e.g. logging, locking, buffer management) of a modern database. They used these measurements to speculate on performance of different DBMS and provided directions for future work in main-memory databases. Despite these contributions, insufficiency remains with the paper such as validity of claims regarding how much of an overhead specific features should pose on the system and further research is needed to have them fully justified.</div>
  <div class="block">S1: Dissected overheads of various features in modern RDBMS by stripping down Shore progressively.
S2: Made speculation on different DBMS based on the measurements and suggestions for future research.</div>
  <div class="block">W1: Validity of claims: doubts are raised regarding whether specific features such as concurrency control are really causing as much of an overhead as claimed and whether these overheads only occur in certain occasions and could be reduced. 
W2: Due to scope of research, not much insight is provided for potential bottleneck of the system after being stripped down to minimal. e.g. Data structure of storage itself might become an overhead due to cache misses in B-trees.</div>
  <div class="block">Disk I/Os has been the primary performance bottleneck for most online transaction processing (OLTP) databases in the 1970s and 1980s. However, with the development of modern technologies, the prices of main memory have dropped significantly such that it is then sufficient to keep many OLTP databases in RAM. Thus, it is worthwhile to investigate the set of features most suited for modern in-memory OLTP databases. Harizopoulos et al. started from an existing database with traditional OLTP database features and progressively removed functionality that might not be necessary. They performed a performance analysis of stripped-down variants of modern DBMS through an instruction-level breakdown and provided directions for future work in main-memory databases, which are the main contributions for this work.

D1: First, the paper identifies four major components whose removal substantially improved the system throughput: logging, locking, latching and buffer management. They were able to verify their hypothesis by removing these features progressively from Shore, an open source database with the common functionalities such as full concurrency control and recovery. They compared the performances of the system with the bottom line after each modification and demonstrated a factor of 20 improvement in overall throughput with the code residue which is only about 5% of the initial instruction count. These results shed light on the possible overheads that limits traditional OLTP databases and pointed out the limitations to be overcome in future research.

D2: Another interesting section in the paper is the implication for future OLTP engines where they suggested research in aspects such as concurrency control, multi-core support and consistency semantics. This section details the limitations of the proposed design of the stripped-down database, and gives options for future work (though some are kind of vague, discussed below in the weakness section). For example, the paper admits that although dynamic locking is a large overhead, it is essential for a lot of DBMS applications to have some form of concurrency control. Thus it is worthwhile to explore different concurrency control protocols other than locking, such as optimistic concurrency control. This idea is implemented in many later database systems such as Hekaton [Hekaton: SQL Servers Memory-Optimized OLTP Engine]. Hekaton does not use any latches or spinlocks as they are proved here to be large overheads, and instead adopted optimistic multi-version concurrency control to provide transaction isolation semantics. 

D3: Despite the many contributions that this paper has made, there are still some doubts and insufficiency with the paper. First, doubts are raised regarding whether specific features are really causing as much of an overhead as claimed and whether these overheads only occur in certain occasions and could be reduced. For example, this paper claims that locking, latching and concurrency control pose a large overhead on the database system, and combined with the short-running nature of most OLTP queries, suggested a single-threaded implementation. However, Jones [Low Overhead Concurrency Control for Partitioned Main Memory Databases] extends this paper by showing that some form of concurrency control can be beneficial in highly contended workloads and even locking and multi-threading can prove to be beneficial. In addition, Pandis et al. [Data-Oriented Transaction Execution] claimed that lock contention is mainly due to the central nature of the lock-manager, thus they devised DORA where a chunk of data is assigned to each thread instead of assigning a specific transaction to each thread. This approach reduces contention and thus the overhead from the lock-manager. 

D4: Another insufficiency is that due to the scope of research, some vague claims are made without experimental support. For example, the paper claims that it appears to be more important to optimize concurrency control and recovery than the data structure of the storage itself. Although the paper later admits that once the system is stripped down to very basic kernel, the cache misses in B-tree structure may become a new bottleneck. In fact, Stonebraker et al. [The end of an Architectural Era] already showed with the H-store database that the simple optimization of representing read-only tables as arrays offers great improvement in throughput. The paper made suggestion for future research to try out other indexing structures such as hash tables, but fail to give much insight on the trade-off.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The main contribution of the paper is its acknowledgement that many architectural designs in classical RDMS are based on computation resource limitations that no longer hold true, and a detailed analysis of the cost of such components to support the claim. In particular, the paper notes that users of OLTP systems can accept a more relaxed form of consistency (eventual consistency), have alternative mechanisms for recovery and could fit in memory. By stripping logging, locking, latching, the buffer manager and performing optimizations, the paper claims to give an estimate of the cost of each component, which corresponds to potential gain. </div>
  <div class="block">S1  Succinctly identified knowledge gap of known issue, and filled it. It is well known that popular database architecture is old and likely not optimal. However, this is the first work that provides a detailed breakdown of the areas cost. </div>
  <div class="block">W1  Given that the paper aims to provide a breakdown of the cost of areas DBSM components, analyzing a single system seem to be overly specific since it could be drastically for different systems with different architecture. The order in which components are stripped from Shore could also potentially impact the instruction number. Lastly, the workload seem to be a little limited, with only 2 transactions. </div>
  <div class="block">D1  The paper has a nice survey of database components, potential optimizations and detailed analysis of cost. The recognition that not all OLTP systems require ACID properties provided by traditional DBSMs, and the need for greater understanding of the cost of providing guarantees is definitely a guide for creators of future systems. There is naturally a high degree of uncertainty with regards to the actual cost of a database feature, as combinations of factors could compound and the cost for one system is highly unlikely to be identical to another. This point is not fully addressed, but in a sense acknowledged by pointing out the different potential factors, such as the network, cores, memory. I like that they note and address the difference between instruction count and cpu cycles. Different transactions also can have very different distributions of cpu cycles, as is evident in the differing breakdown graphs of the two transactions. More transactions, or a breakdown of transaction types would be part of an ideal analysis. I am also slightly confused by the insertion of switches that correspond to different parts of the system, and was wondering if they could have more experimentation with turning off different combinations of swtiches. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper measures the performance of Online Transaction Processing databases, and was able to attribute performance overheads to buffer management, logging, and concurrency mechanisms (latching and locking). In order to isolate the contributing factors to performance overheads, the authors removed features from Shore and ran the TPC-C benchmark on those pared-down versions of Shore. They found that buffer management and locking operations were the most significant contributors. </div>
  <div class="block">S1: In addition to removing costly features, the authors also hand-coded some optimizations. Increasing page size was an effective optimization that reduced page allocations and decreased B-tree depth.
S2: Their interest in various DBMS components like logging, locking, buffer manager is solidly motivated by actual advancements in architecture. 
S3: Their method of decoupling certain features from Shore was detailed clearly.</div>
  <div class="block">W1: Their scope was limited to single-threaded applications because Shore only uses user-level threads. 
W2: They conclude that significant gains would require multiple optimizations, which is realistic but makes the future implications less clear.</div>
  <div class="block">While the scope of the paper is to measure performance, it's also important that these measurements could lead to actual alternative DBMS architectures. For example, the paper cites Bugnion, Devine, and Rosenblum's Disco paper when discussing single-threaded database architecture. The result that locking and latching creates significant overhead is only useful if there is a feasible architecture that wouldn't require locking--The paper shows that such an architecture is made feasible by advances in virtualization. Similarly, they motivated their interest in memory resident databases, citing the increase in RAM sizes.

Still, although virtualization presents one alternative architecture, it would have been interesting to see the same benchmark on a multi-threaded DBMS. Even though the only effect of measuring on single-threaded is lower # of CPU instructions, I wonder if things like buffer management would disproportionately affect total # of instructions.

Ultimately they conclude that a DBMS can achieve significant gains only when multiple of the contributing factors are eliminated. While this is a realistic conclusion, they do not discuss the feasibility of a stripped-down architecture (maybe it is out of the scope of the paper). The authors only discuss feasibility for the individual factors, for example, recovery by copying state from other nodes as a replacement for logging. They also mention transactional semantics as a replacement for full transactions support.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The goal of the paper is to model the impact of various simplifications on database systems that take advantage of hardware gains of the past 30 years. The authors identify log-less databases, single-threading, and transaction-less databases as potential alternative DBMS architectures, and propose to model them with variations on the Shore system. Each of the three alternative architectures is modeled and benchmarked by sequentially removing the most expensive subsystems in Shore: logging, locking, latching, and the buffer manager, and significant gains are demonstrated (20X).</div>
  <div class="block">  S1: The authors provide very clear specifications of their testing system, both with respect to its hardware and software. They also acknowledge very specific   limitations on their measurements, including that cycle counts should be taken only relatively. This makes their tests much more reproducible and verifiable.

  S2: Providing quantified ways to evaluate blue-sky proposals from the field is certainly helpful in scoping out further research directions. 

  S3: The paper proposes feasible ways to avoid two of the most difficult         problems in RDMSs, locking and recovery. 
    locking: authors claim it accounts for up to 30% of query time 
      usually relaxed consistency is feasible and hence locking is             unnecessary 
    recovery:
      by assuming multiple replicas exist, avoid logging and assume you       can fill in memory from other replicas instead of creating logs </div>
  <div class="block">W1: It is unclear from the paper how valid the fully modular notion of a database is, and whether this is too much of an oversimplification. A main difficulty the authors faced was that the different subsystems in Shore were too integrated to treat as fully modular when removing them. Are the gains of breaking down the parts really as large as the authors claim, or do the original cross-subsystem integrations provide some optimization in the original Shore that is not accounted for in measuring the gains?</div>
  <div class="block">D1:  The graphics illustrate the system gains very well, showing both the proportional costs of the various subsystems and their instruction counts (in particular Fig. 5 and Fig. 6)
D2:  Other work following up after Looking Glass confirms the necessity of moving   on from the disk-based System-R-derived model. 
  It was unclear from just the Looking Glass tests what the effect of the         optimizations would be for less intensive databases than the TPC-C benchmark. 
  Hekaton (2013) provides a good perspective on this, applying many "Through the Looking Glass ideas to single tables. Hekaton extends the Looking Glass ideas by not even being a DBMS at all, but rather an integration into SQL Server. 
  The H-Store implementation in The End of an Architectural Era uses of many of the   ideas from OLTP but gets 80-fold gains rather than the 20-fold discussed in Looking Glass. H-Store keeps at least two replicas of each table, which, as the Looking Glass paper predicts, allows for the removal of an undo-log from the system. Overall, the results from "Looking Glass" have proven both true and highly extensible.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper decomposed the memory-resident database systems for OLTP by graduately removing logging, locking, latching and buffer management in order to measure the overhead that each variant could cause. Besides, it showed that all-stripped-down systems could have a great performance (orders of magnitude) than the origin database system, while partly-stripped-down systems would not have a better performance than the origin database system. Therefore, they claimed that some proposals for stripped down systems could have a potential value.</div>
  <div class="block">S1
The paper does convince me why that four parts could be removed while OLTP is still able to work on the new database.

S2
The evaluation part is detailed enough and I'll explain in detailed review.</div>
  <div class="block">W1
For the removing of logging, I don't see why allowing group commit could avoid disk I/O request.

W2
Figure 4, Calls to Shores methods for New Order and Payment transactions is not very clear and I got confusing with the variables "I, D, W, C..."

W3
Using malloc could have potential issues.</div>
  <div class="block">For S1, the paper explains why logging, locking, latching and buffer manager could be removed. For logging, it could be removed because OLTP allows a logless architecture; for locking and latching, since the model only process one transaction at one time, those two are unnecessary; for the buffer manager, the model would have very limited number of disk I/Os because it is a main-memory database, therefore the functionality of buffer is rarely used.

For S2, the paper firstly introduced the work flow of its benchmark, which is a simplified TPC-C. Then, it focused on New Order &amp; Payment, and gave the relationship of IC and each step of transaction break down. Finally it compared cycles with IC. I think this part could convince me that their hardcoded B-tree optimization is useful because the fraction of time and IC ratios for B-tree keys are obviously different.

For W1, as far as I know, what group commit would do is firstly records current LSN into thread, and when it enters the flush stage, leader would search the queue and compute the greatest LSN. Since they have already increased the buffer size, even though the group commit could decrease the request, not allowing group commit will not cause disk I/O as well.

For W3, in the removing functionality of the buffer manager part,  instead of using Shore's page mechanism, they called malloc for each record. I'm not sure whether I am right about this, but accoding to my experience, reusing the pointer to memory which is freed could cause a crash. There is a risk that the internal structure of the heap will become corrupted. Besides, there would also be a risk of memory leak if pointers are not handled correctly.</div>

  </p>
<hr />
