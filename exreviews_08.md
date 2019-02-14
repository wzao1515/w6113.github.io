---
layout: page
---

## Remarks

We will be transitioning from _how_ reviews are structured to the comments noted in the reviews.  

### Points

Imagining extremes of a parameter/spectrum is a good way of assessing decisions.

* If you imagine the extreme case of every tuple having the same join key, then every algorithm must take O(RS) time (at least for the final merge), which isn't accounted for at all in any of the performance analyses. In general, the effect of cardinality seems to be missing.  

A non-obvious observation.

* One thing that stands out particularly in this papers organization is to first present the algorithms with strict assumptions followed by adaptations to the algorithms with relaxation on these assumptions.jjjjjjjjkkkkkk

Clear summary, and an opinion.

* In response to the problem of All Real Memory Strategy assumption, Shapiro discusses the relaxation of the hot set + virtual memory model which I find interesting. The paper proposes that a certain number of pages be wired down in real memory and guaranteed to each process while the rest of the data space of each process (e.g. cache) will be assigned to virtual memory. It also recognizes the problem this model under LRU criterion and considers the effect of page aging on different join algorithms. Given the significant increase in the amount of data and parallel processing nowadays, it is almost impossible to allocate each join process a fixed amount of memory near the size of the smaller relation. Thus I find this **discussion of virtual memory rather important in today context**.


Many comments about

* poor partitioning function/partition overflow
* model assumptions may not hold
* Only theoretical analysis
* Does it hold for modern hardware/trends?


## The Reviews


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

  <div class="block">The paper proposes and examines 4 new types join equal join algorithms, namely sort-merge, simple-hashing, Grace and a hybrid of Grace and simple-hashing when memory is large enough to fit M^1/2 of the smallest table. </div>
  <div class="block">S1 - The paper does a careful breakdown of each algorithm, analyzing the required read to memory and disk and providing formulas</div>
  <div class="block">W1 - The flip side of the strength is that all plots are theoretical, and we have no idea if the hyper parameters such as accurate. They acknowledge  that the plots are optimistic since they presume there is no overflow, and there is no way to know how well their proposed solution to overflow would work. </div>
  <div class="block">D1 - This is a fairly old paper at a time when memory is small by todays standards, and merge sort joins are used universally. The authors argue that hash-joins perform much better than merge sort joins when there is sufficient memory for equal joins, which we take for granted today. Grace is an improvement on a simple hash join by partitioning the two tables so that there are multiple hash tables, with the disadvantage of writing to disk when buffers in memory fills. Hybrid doesn't use memory as a buffer on the first pass, building the hash table directly, saving some writes. The analysis of the complexities makes sense, as does the stimulated graphs which help to make sense of the formulas. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Their hypothesis is that they can design a fast execution of join using hash-maps rather than manually sorting the data to join them. The goal of their paper is to compare the commonly used sort-merge technique alongside current and the authors new hashing method to compute a data join. I agree with their hypothesis as their claim seems valid and has an unusual sense of clairvoyance as RAM would reach into the gigabyte level. </div>
  <div class="block">S1  It was brilliant that the authors compared their version of join with the 3 others most commonly forms of join.
S2  In their introduction and results, they show that hashing is superior to sort-merge.
S3  It is technically a weakness their algorithm relies on a certain amount of RAM being available, but it is good that they also place a decent amount of discussion into this scenario as well. 
</div>
  <div class="block">W1  Figure 7 shows that the hybrid system while it performs the best isnt performing dramatically better than GRACE. </div>
  <div class="block">S1  It was great that they also delved in detail about the steps for the algorithm, its run-time and requirements for memory. They also both theoretically and experimentally proved their model as correct. 
S2  This is critical as they have verified another publication that had the same finding. This is important for both verifying their related work and their own work as expected results.
S3  The author devotes Section IV to discussing about partitioning the data to fit into RAM if it is too large. They also devote some thought into how to deal with Virtual memory should there be not enough RAM to complete the join. The authors also address the reviews concern that such a memory manager would be very difficult to build. 
D1  In the introduction they mention that with 4 MB of data, they can join 325 MB relations. 81x efficiency is great, but does this hold for all sizes of RAM available? 
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper describes a new class of join algorithms that allow users to work off a close-enough paradigm of computing. Instead of blocking until the entire process is complete, the user will get periodic updates about the query result which each have corresponding confidence intervals. The user can choose to have the time between updates be longer, with the increase in confidence growing more per update, or vice versa, have more updates each of which has less overall effect on the confidence of the answer. </div>
  <div class="block">S1: This addresses a very common use case, online applications with a very large search space but where it is totally acceptable for the answer to be close enough. Even replacing a small fraction of queries with this model seems like potentially a huge computation reduction.

S2: In the worst case, the ripple join degrades into a nested loop join. That is, even in the worst case, its probably not that much worse than what you would otherwise have done anyway.

S3: Actively using statistics about data to decide how to process said data is a really perceptive direction to go in. Also, you could compile datasets of data-about-data to help decide when certain algorithms are applicable to try. </div>
  <div class="block">W1: Since tuples are seen in random order, it is possible to at some point(s) during your query see a tuple/set of tuples that drastically changes your perception of the shape of the data. For example, you could believe for most the processing that most values in some dataset are clustered around some value x, then subsequently by chance get several sets of outliers that disrupt that view. Then, even if temporarily, your confidence interval about your earlier result would have to go down. More computation time does not seem necessarily equivalent to higher levels of confidence in the results. In a non-negligible number of cases, earlier results might actually be closer to the real answer. </div>
  <div class="block">D1: The interpretation of ripple joins as creating families of algorithms is a very interesting one. Deciding which algorithms work best for ripple joins and determining which algorithms see the most benefit from allowing close-enough solutions is a very interesting problem theoretically.

D2: Another interesting potential line of work: knowing that ripple joins are an option, can you optimize your entire database so that ripple joins go even faster? This could include, for example, splitting up tables to improve aspect ratios between commonly joined tables.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper has proposed four join algorithms that take the advantage of main-memory based database systems: sort-merge-join, simple hash, GRACE hash and hybrid hash. They claim that the the hybrid hash algorithm could donimate the other algorithms in terms of efficiency, and since the cost for memory is lower and lower, people may prefer using the hash-based algorithms for doing join.</div>
  <div class="block">S1
Their approach could fit into some existing tools that could help increase the efficiency of join like filters and Babb arrays.</div>
  <div class="block">W1
For simple-hash join, the compare time is simply defined as 1.4*comp.</div>
  <div class="block">W1 For the simple-hash join, I think one thing is not good is that this method needs many passes because it is related to the amount of buckets, and buckets cannot be too many because that would require too many I/O, but if buckets are just a few, the probing (compare) times would increase. For the sort-merge-join algorithm, it requires only one or two passes. In the paper they simply defined F to be 1.4, but I think this is worth thinking about.

D1 I find one thing interesting in the GRACE join is the difference between this paper's method and the GRACE machine designed by [KITSUREGAWA, M., ET AL]. Firstly, this paper discarded the hardware sorter. They claim by doing this the subset of S could be size-flexible. But I am not sure whether they take the time cost for doing hash into consideration (maybe this does not make sense because doing simple hashing doesn't count as too expensive, but it would cost more than using hardware).

Overall I think this is a great paper because the four algorithm proposed is reasonable: each is given detailed workflow and analysis of its cost and improvement. Since this paper is too long and the algorithms need time to understand, I didn't finish Chap. 5.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper developed three hash-join algorithm base on and promoted from the simple hash-join algorithm which show advantages over the original one, and also proposed three useful tools that can be incorporated into algorithms.</div>
  <div class="block">S1: Clearly illustrated and compared the performance of four algorithms, is pretty comprehensive and integrated, with plenty of details one can look into.
S2: The logic is simple and easy to follow, and is organized in a straight forward structure, which is introducing four algorithms one by one and then compare the performance among them.</div>
  <div class="block">W1: The most part of this paper is a little bit tedious because its just a kind of stating the facts but without more interesting description.
W2: It implement sorting and joining sometimes based on hardware and sometimes based on software but tell little about the specific reason of these different treatment. </div>
  <div class="block">D1: It modified and optimized the simple hash-join algorithm in other three hash algorithms by taking the  application in virtual memory into consideration, which is a kind of important progress and breakthrough.
D2: The performance test results that show that the hybrid algorithm dominates other two algorithms is also very strong state and significant result, which shows the validation of that modification and its compelling advantages. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper champions the adoption of distributed database systems built on top of large collections of ordinary hardware rather than the adoption of specialized hardware systems. This is due to the fact that relational database operations are naturally (perhaps unusually) suited to parallel execution - they involve uniform operations (often aggregate operations) over large, uniformly organized sets of data. In order to achieve this parallelism, this paper argues that shared-nothing hardware design is more efficient because it minimizes the amount of overhead dedicated to resource sharing - only queries and reduced forms of the data are ever moved through the network, which leads to greater scalability.</div>
  <div class="block">S1: This paper provides two clear metrics by which we can measure the success of a parallel system: speedup and scaleup. It gives a level of nuance to the ways in which a database problem can grow - speedup being performance gains granted by increased hardware on a problem thats held constant, and scaleup being the change in performance caused by a problem and a system that vary in size (in equal measure). This is an important distinction to be made.

S2: Although this paper obviously provides a technical justification for the use of parallel systems in database problems, it also takes into account the state of the contemporary software ecosystem at large - the paper mentions how the fact that old software written for uniprocessors sees no performance gains when shifted to multiprocessors and how the necessity of rewriting this software may have impeded the adoption of multiprocessor systems as a whole.

S3: Offering opportunities for further research is a great way to end the paper - it acknowledges the papers limited scope and provides a space for the authors to discuss the implications of their work (and perhaps probe at possible solutions without having to write a whole other paper).</div>
  <div class="block">W1: The state of the art section was wordy and perhaps could have just been reduced to show the common traits the various distributed databases do and dont have, with a little space at the bottom dedicated to what makes each one particularly interesting. 

W2: Considering that many distributed database systems had already hit the market, this paper serves more as a survey of the field than of a research paper in and of itself - however, it seemed to really want to drive home the point about the benefit of shared-nothing systems, which seems out of place in a pure survey paper.</div>
  <div class="block">D1: Metrics of success - although most papers weve read generally perform benchmarking tests across different systems and then chart out the performance in a table for comparison (perhaps using tasks of varying sizes for the benchmarking), this paper concerns itself almost exclusively with the performance of tasks as they scale. This is a rather top-down approach that allows room for further small-scale performance tweaks (as mentioned in the further research section toward the end of the paper). 

D2: The idea of defining the ideal database machine was interesting - the paper defined an intentionally unattainable ideal and then described a way in which they could approximate this ideal using current technology. Framing an approach in terms of its closeness to a theoretical ideal is an interesting (although perhaps less tangible) metric.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">

The author proposed a hybrid hash based join algorithm which is based on two hash join algorithms to compute an In-memory equijoin of two relations efficiently and showed that it outperforms the state-of-the-art algorithms at that time.</div>
  <div class="block">S1. The author estimated the performance of the algorithm as a function of the relation size relative to the available memory space, thus not restricting it to the amount available when the paper was published foreseeing that the state of main memory size to its price in the future is increasing.
S2. The author has explained in details their assumptions and how they run the evaluation process.
S3. The author has addressed their unrealistic assumption about how memory is pre-allocated to each process and explored how the algorithms would work in virtual memory model and what are the challenges faced and the minimal requirement for their algorithm to perform well.</div>
  <div class="block">W1. Partitions size depends on data distribution and quality of the hash functions used.</div>
  <div class="block">D1. The author has evaluated four different algorithms for computing an equijoin (1. modified sort merge, 2. Hash join, 3. GRACE, 4. Hybrid hash) where each algorithm is designed to take advantage of the trend of increased available main memory space relative to their cost.
D2. The three hash-based algorithms are extended in such a way as to account for the case when a hash table wouldn't fit in main memory. In that scenario, they use partitioning, where each relation is partitioned into disjoint subsets that can fit in main memory in such a way that it would be sufficient to check for tuples equality between two relations by inspecting their corresponding partitions.
D3. Each algorithm has two phases, a preprocessing phase, then the actual join computation algorithm.
D4. The author has discussed the possible ways to take advantage of available tools in the database systems such as selection filters, semijoin strategies, and Babb arrays and how their addition can improve the algorithm's performance.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The author proposed an especially efficient hash-based algorithm for computing the equijoin of two relations, given sufficient main memory (aka available main memory is significant fraction of the size of one of the relations to be joined). Shapiro demonstrated that the hybrid hash join algorithm outperforms than the simple hash algorithm, the GRACE hash join algorithm and the traditional sort-merge join algorithm on the same dataset, which he accomplishes on the assumption that a specific amount of real memory is allocated to the process. </div>
  <div class="block">S1 - Shapiro presents a convincing argument that the hybrid hash outperforms the other two hash-based algorithms and sort-merge join algorithms by extensively providing proofs and performance stats. </div>
  <div class="block">W1 - Shapiro makes a lot of assumptions regarding the environment in which his algorithm was tested, such as a hash table for R can fit in main memory, and that the memory manager is able to allocate a fixed amount of real memory to each join process. This is a key weakness because the hybrid join algorithm's performance is predicated on having a certain amount of real memory allocated and available.</div>
  <div class="block">D1 - Shapiro does acknowledge that he makes assumptions about his memory management strategy and details the weaknesses as such. I appreciate that the author managed to cover such ground in a short paper and extensively address (and provide solutions to) the inherent weaknesses of the paper's ideas.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper aims to study algorithms for computing equijoin of two relations with the availability of large amount of main memory. Shapiro defined and analyzed three hash-based equijoin algorithms (simple hash-join, GRACE hash-join and hybrid hash-join) and a version of sort-merge join that takes advantage of significant amount of main memory. He showed that the hybrid hash-join algorithms performance dominates the others under most situations and concluded in the end that hash-based algorithms will become the preferred strategy for joining large relations with decreasing main memory costs.</div>
  <div class="block">S1: Detailed analysis and comparison of four join algorithms with large main memory. 
S2: Careful with assumptions. First present the algorithms with strict assumptions followed by adaptations to the algorithms with relaxation on these assumptions.
S3: Discussion of hot set + virtual memory model significant in today's context given the increasing amount of data and application of parallel processing.</div>
  <div class="block">W1: Partition overflow is not quantified. With skewed data and inappropriate partition hash function (an appropriate one is not easy to find), overflow could occur often and severely influence performance.
W2: All analysis and conclusion are based on the derived formulas, there is no implementation and measurements of the algorithm. So analysis are susceptible to false assumptions and derivations.</div>
  <div class="block">D1: The major contribution of this paper is its detailed presentation and comparisons of the four join-algorithms. This accounts for its convincing arguments for the use of hash-based join processing in modern database systems. The paper uses detailed cost derivations with mathematical formulas and shows which algorithms dominate which under different conditions with varying memory sizes and other factors.

D2: Shapiro is careful with the assumptions behind the algorithms, which clarifies lot of the following derivations. For example, he makes the assumption that each tuple from S joins with at most one block of tuples from R. This justifies the partition of R with sqrt(FR) blocks of memory in the hash-join partition phase. One thing that stands out particularly in this papers organization is to first present the algorithms with strict assumptions followed by adaptations to the algorithms with relaxation on these assumptions. The paper starts by presenting four algorithms under the assumption that partitions in hash-joins will not overflow and that a fixed amount of real memory is allocated to each join process throughout its lifetime. Then, it admits that even with a good hash function and good statistics, partition overflow will still occur and there are even more problems with fixed memory assumption. Thus in later sections, it relaxes the assumption and proposed solutions to these problems. This approach to the problem simplifies the algorithm construction while remaining rigorous.
In response to the problem of All Real Memory Strategy assumption, Shapiro discusses the relaxation of the hot set + virtual memory model which I find interesting. The paper proposes that a certain number of pages be wired down in real memory and guaranteed to each process while the rest of the data space of each process (e.g. cache) will be assigned to virtual memory. It also recognizes the problem this model under LRU criterion and considers the effect of page aging on different join algorithms. Given the significant increase in the amount of data and parallel processing nowadays, it is almost impossible to allocate each join process a fixed amount of memory near the size of the smaller relation. Thus I find this discussion of virtual memory rather important in today context.

D3: In general, this paper does a really good job in analyzing the four join algorithms with large main memory and makes a strong argument in favor of hash-joins. One major flaw I find with the discussion is that the paper is a little hand-waving in its discussion of partition overflows. Although this paper gives detailed cost derivations with mathematical formulas for all algorithms and even virtual memory cases, partition overflow is not quantified. The proposed solutions to overflow on disk and memory are both along the lines of modifying the hash function, rehashing and reassigning some new buckets. But this involves nontrivial disk I/Os and may result in bad performance on the hash join algorithms. In addition, the paper naively assumes a uniform distribution and just says to the hash function h accordingly. Later research show that such a split function is not easy to find [Hash-Partitioned Join Method Using Dynamic Destaging Strategy]. If the distribution is skewed on the join keys and the hash function is not suited, the performance of the hash joins is unclear. This discussion is kind of overlooked by the author.

D4: Another weakness of the paper is that although the analysis of the algorithms are clear and convincing, there is no measurement done on any implementation of these algorithms. As all the graphs and according conclusions are based on the calculations from the mathematical formulas, any false assumption or error in the cost derivation will result in incorrect conclusions. Thus, future work on algorithm implementation and performance testing is preferred.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper discusses 4 different join algorithms, showing how to use comparatively "large" amounts of RAM with "small" relations. The 4 algorithms are a sorted-merge join, a simple hash join, a grace hash join, and a hybrid hash join.</div>
  <div class="block">S1. I thought the hybrid hash join was a very neat (and in retrospect obvious) way to combine the simple and the GRACE hash join algorithms.

S2. I liked the fact that they came up with actual numbers for their constants (are they reasonable though?) and made some plots of the different performance costs. The graphs made it very easy to see the relationship and relative costs of the hashing algorithms (especially the hybrid hash compared to the GRACE hash at low-memory values and the simple hash at high-memory values). </div>
  <div class="block">W1. It would have been nice to see at least some kind of empirical result showing that the predicted I/O and CPU costs match up to reality (and to get some idea of what all the constants (especially the fudge factor F) look like in a real system). Where did all the constants from Figure 6 come from?

W2. It seems like a lot of this analysis assumes that the distribution of join keys is at least somewhat distributed. But all of tuples in R share a handful of join keys, then you might need to do cross-partition joins for the GRACE and hybrid join algorithms. If you imagine the extreme case of every tuple having the same join key, then every algorithm must take O(RS) time (at least for the final merge), which isn't accounted for at all in any of the performance analyses. In general, the effect of cardinality seems to be missing.</div>
  <div class="block">D1. I thought the discussion of the sort-merge algorithm a little lacking. The paper makes it seem that it's always dominated by the hybrid hash join algorithm (since it's dominated by the grace hash join in large cases and the simple hash join when R fits in memory), which is probably true in general. But if you have a B-tree index for R or for S, then you could skip the sorting (saving I/O and CPU costs) and do a semijoin directly on the index without a projection (saving disk I/O), which makes sort-merge look a lot more attractive.

D2. This might just be a terminology thing, but is the Babb array not just a bitmap? Why the need for the special terminology? (Also -- would it be useful to extend this idea to use a 1-hash Bloom Filter instead?)

D3. I found the choices of the page size ($\sqrt{FR}$ and $\sqrt S$) to come out of nowhere. I originally assumed that they were chosen to minimize the cost, but I'm not quite sure how. I think a better exposition about how these values were chosen would be helpful (as-is, the performance cost seems to totally ignore them, except for "IO savings for extra memory").

D4. This is more of a style thing, but I found this paper hard to read and actually understand. There are a lot of different variables to keep track of (not all of which I felt were really that important) and I felt the algorithmic explanations weren't very well done (I almost wish they just had psuedo-code here).</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper introduces different ways of JOIN processing: sort-merge join, simple hash-join, GRACE hash-join, and hybrid hash-join. In addition, it shows the performance tests with those algorithms, the partition methods used in hash, and the memory management strategies: with all-real memory or virtual memory. The hypothesis is that though join is the most costly operation in database system, the hash-based algorithm is the most efficient way to implement with sufficient main memory given. </div>
  <div class="block">S1: The explanations on four algorithms are very straightforward. The cost analysis and figures help readers understand the algorithms better.
S2: The results of the paper is impressive that with 40MB memory, it can hash more than 1k times relations which is useful in the 80s. </div>
  <div class="block">W1: The paper was published in the 80s when the real memory is more expensive and limited than today. The assumption that mentioned in the beginning is that the algorithm works on a uniprocessor machine to avoid the problems of synchronizations, which is not the case now when multi-threading and distributed systems are prevail. </div>
  <div class="block">D1: The idea of partitioning and hashing in doing join operations to speed up computation is fundamental because I found it was cited by 475 papers in the 90s. It also gives future directions to improve the performance: applying filters, semi joins, and babb array. The Babb array is unfamiliar to me which uses an array of bits to indicate whether the hash bucket has R tuples. Another thing I do not understand is that the paper mentioned it is difficult to predict the amount of memory of single process or to coordinate multiple memory requests, the solution it provides is to give process the memory it requests in virtual memory, so why it does not work in real memory?</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper discusses 3 existed methods for computing the equijoin of relations in a standard system with large main memory: sort merge join algorithm, hashing algorithms, Grace algorithms. The authors also present a new hybrid algorithm of two hash-based methods. They conduct experiments to prove that their new algorithm dominates other algorithms, even in a virtual memory environment.
</div>
  <div class="block">
S1: The hybrid method gracefully combines the advantage of two methods and achieves better performance.

S2:  This paper clearly describes three kinds of join methods for large memory. It can serve as a good survey for research.

S3: The paper contains a very solid theoretical analysis of memory and performance. </div>
  <div class="block">
W1: The memory is much larger these days compared to tens of megabytes in the paper. The paper is kind of out-of-date. 

W2: The partition overflow does not have experimental support.
</div>
  <div class="block">
D1:  From the figure, we can see the hybrid algorithms always needs less time than the other algorithms along with the memory enlarging. 

D2: This paper explained sort-merge join,  single hash join, and Grace. These three algorithms gradually enhanced performance.  In the single hash join, we need to go over S again and again. In grace, we can reduce the number of accesses to S. Also, the paper gives the precise computing of the cost of different algorithms which is super clear.

D3: In this paper, the memory is about tens of megabytes which is far different from today's memory. Now, most of the relations can be stored in the memory which makes the paper less meaningful.

D4: In the part of partition overflow, the author gives many ways to handle different situations. But there are no experiments to support it. For example, the readers may be curious about how much effect if we do not handle it and after we handle it, how much will it improve. 
</div>

  </p>
<hr />


