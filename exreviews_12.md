---
layout: page
---


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

  <div class="block"><h5>Hater</h5></div>
  <div class="block">This paper provides new insights into the tradeoff between hashing and sorting for unary operations, studies the problem of avoiding redundant method invocation during query processing, and present a number of techniques for handling multiple methods in a query.</div>
  <div class="block">The technical work: As the author states in the paper, Each algorithm that we tested caused the method to be computed exactly once per value, maybe they can make the method to be computed multiple times per value and get the average to make the result more convincing.
The presentation of the paper: As the author states in the paper, they raised some new optimization problems in choosing caching techniques but didnt shed light on how to solve them, maybe its better for them to give some consideration on any hint or clues of how to optimizing choosing caching techniques.</div>
  <div class="block">This paper as a whole is very excellent and heuristic on query execution techniques, and the way that they design their experiments on varying the columns is also very interesting.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">The main aim of the paper is to minimize the time spent in computing User-defined methods by avoiding redundant calls to the same method. The paper proposes the use of a combination of hybrid-hashing and memoization (hybrid-caching) and demonstrates which cases it outperforms sorting and memorization techniques.</div>
  <div class="block">One way to address the issue of UDFs that have large outputs sizes is that, if the materialized data is larger than some threshold, we store it in a  separate buffer and in the hash table we only store a pointer to it. Once the buffer size reaches a limit, it gets paged out to disk so that we write in batches instead of writing to the disk many times. </div>
  <div class="block">-</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">The authors found that when users invoke time-consuming methods in their queries, a lot of time is wasted redundantly computing methods on the same value. They realized that they could cache the result of the method to improve performance and memory use. To that end, they propose an alternative to existing solutions (memoization and sorting): a variant of unary hybrid hashing called Hybrid Cache. After running experiments, they found that the memoization technique is extremely inefficient, Hybrid Cache is superior when it comes to caching Boolean predicate methods, whereas there are tradeoffs between Hybrid Cache and sorting for methods that return large output. </div>
  <div class="block">How should the technique be evaluated?

Based on the reading, my intuition is that HC should be compared with memoization and sorting in terms of time taken and memory used during processing. It should be tested with queries that contain time-consuming methods and simple methods on a large enough data set with varying amounts of duplicate values. 

How it was evaluated:

The authors ran two experiments to evaluate the technique. For the first experiment, they examine the behavior of HC alongside memoization and sorting as the percentage of duplicate values in the input is increased. They passed in an expensive method that always returns false (Boolean type), where no tuples would satisfy the selection predicate. They ran the query 7 times and varied the column specified in the method.

For the second experiment, they compare the performance of HC and sorting, using methods that produce outputs of various sizes.

For both experiments, they examine the time it took to run the query and the number of temp-file I/Os  that sorting and hashing require to stage and rescan tuples.</div>
  <div class="block">Results:  

For the first experiment, from 100 duplicates onward, both HC and memoization behaved identically. Performance of sorting is not competitive with HC due to the number of temp file I/Os that sorting and hashing require to stage and rescan tuples. Sorting has consistent I/O cost no matter the number of duplicates, while HC has 0 I/O cost starting at 100 duples. This is because the behavior of sorting is a function of the number of tuples in the input, while I/O performance of HC is a function of the number of values in the input.

For the second experiment, they found that HC is not always better. It performs worse for inputs with less than 1,000 duplicates and it's I/O cost is significantly higher for inputs with no duplicates. This is because HC shares main memory among both inputs and outputs. As output size grows, HC can fit fewer entries into main memory, and more partitions are needed to partition the inputs. Additionally, as output size increases, the number of values per page becomes smaller and approaches the limit of the square of the size of memory. When limit is exceeded, HC performs recursive partitioning (which requires extra I/O) to avoid memory overflow. 

Finally, even though HC and sorting have same number of temp file I/Os for 10 and 100 copies per value, sorting still outperforms HC in running time. The time difference is due to the cost incurred by HC to maintain its temp files, where there is significant overhead of closing and opening temporary files.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">The hypothesis of this paper is that you can cache the results of using an expensive method call in query using a version of hash-join to improve performance. It has proven to be superior to memorization and as long as the output is small, it is superior to sorting.</div>
  <div class="block">Since this is a caching problem, there is always the question on when to remove expired files. The end of Section 5.2 describes how Hybrid Cache gets slowed down by temporary files. A good research experiment could be to look into which caching system works best (FIFO, LRU, Second Chance LRU, etc.) for the files in the temporary folder and setting a cap on how large the temporary file can grow. The hypothesis can be that Second Chance LRU would perform best as it is the caching system used in CPU caches. </div>
  <div class="block">#NAME?</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">The paper proposed a new algorithm for optimizing predicates called hybrid caching, and the result is they could show that hybrid caching outperforms memorization and sorting in terms of boolean predicates, besides hybrid caching could dominate memorization since it is basically an advanced version of memorization.</div>
  <div class="block">Flaws in Memorization Window
1. When the M pages are filled and there are incoming tuples whose hash value is not in those pages, then the algorithm would do the staging. The question is where to save the result of the hashed values of the selected b pages that are going to be partitioned since M pages are already all filled?

The way to address the problem:
I think it is written to disk, but not sure that would be the overhead. Or they could be sorted in memory?

2. How to choose the isolated b pages? Randomly or arbitrarily? Because if it is not well chosen, lets say it choose the first b pages, and many the incoming inputs have the same hashed key with the first b pages value, the performance would not be good because it needs to put into buffer and write to disk, and then be rescanned.

The way to address the problem:
It didnt say but I think it should just isolate the last b pages, because in that case it will perform at least as well as the original algorithm.

Flaw in Experiment
In figure 5, the running time of Hybrid caching goes up and down from 10k to 100k to 1m, it didnt say why. I'm not sure why but this is noticeable.</div>
  <div class="block">I think this is overall a good paper to appreciate because it gives comprehensive analysis and illustrate its advantages and disadvantages compared with sorting, which is another commonly used algorithm. Then, it gives ideas for addressing multiple methods, and proposes a future work that combines sorting and hybrid caching, which greatly broaden the work.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">The paper outlines a new algorithm for caching called hybrid cache which leverages hybrid hash join and has better performance by about a factor of two than the existing standard technique, System-R sorting, and is vastly better than the other standard technique, memoization, for expensive predicate methods where there are many duplicates. By setting its first hashtable to exactly fit in memory, hybrid caching avoids steep paging costs.</div>
  <div class="block">The key insight is that redundant method invocations during query processing can be avoided more often by using caches strategically. The hybrid cache is based on memoization, which is building a hash table in memory. Memoizations weakness is that when there are many values, the main-memory hash table becomes very large and must be paged to disk. Hybrid cache avoids this by staging tuples with previously unseen input values to disk, making the insight that the cost of paging should be avoided if at all possible. Sending all hash partitions to disk and keeping the first hash table exactly the size of main memory is the key optimization from memoization.

Good cases:
  1) When there are many duplicates in the input relation, hybrid caching performs much better than sorting, which is the traditional choice as of this paper.
  2) As in section 6.3, when chaining method caches in a query, the performance of hybrid cache can be improved by first projecting the input to the caches so the caches only store the necessary columns. 
bad cases:
  1) As in section 4.2, if the input relation exactly fills the memoization hash table, hybrid cache sends some values to partitions on disk and incurs extra cost for   its output buffers.
  2) Hybrid cache shares main memory among both input and outputs of the       method and therefore does not handle large outputs well. As output size         increases, the number of pages required for hashing reaches the cutoff of       sqrt(MEMSIZE) and extra I/O is needed to recursively partition.
  3) If there are a small number of copies per value, there is little point in using       hybrid caching and sorting far outperforms it.</div>
  <div class="block">The example with the thumbnail image is very applicable to the multimedia database problems we have discussed!</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">For potentially expensive user-defined functions, we want to aovid evaluating the functions as much as possible. We can do this by caching the outputs of a function, with a similar algorithm to the hybrid hash join.</div>
  <div class="block">The state-of-the-art here doesn't look super sophisticated, at least in the database world. It seems there was a lot more effort put into integrating the user-defined functions into a cost estimator (to see whether predicates should be pushed down or not).

The paper notes that the most established technique is just memoization, while SystemR uses a sort-based approach for caching UDFs. Postgres seems to have implemented caching by creating a temporary table, indexed on the arguments of the function for quick lookups (which is similar to memoization, but using a B-tree index instead of a hash table).

In general, I think that CPU caching was already a thing (LRU caching with cache lines), and buffer pool caching for paging was looking into LFU caching. </div>
  <div class="block">- I thought it was interesting that this didn't benchmark against the (IMO) simplest obvious solution which is an LRU cache for memoization.
- In practice, it feels like this is something that needs to be configurable to tradeoff the CPU cost of the UDF with the I/O cost of the additional paging here.
- The idea of using a *different* hash for partitioning than the hash for caching was really interesting and non-obvious. I feel this was arguably the key insight.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Results</h5></div>
  <div class="block">This paper presents the problem of expensive user-defined function calls and first explores the two already-proposed solutions, memoization and sorting, before presenting a third, called Hybrid Hashing. They claim that Hybrid Hashing performs as least as quickly as memoization, and they further claim that Hybrid Hashing scales better to inputs with large numbers of tuples than sorting. The authors comparison tests of the three algorithms partially validates their hypothesis - for expensive predicate functions, Hybrid Hashing outperforms for every level of input variation, but for predicate functions that produce large outputs, Hybrid Hashing loses out to sorting until the number of copies per value becomes very high.</div>
  <div class="block">The ideal way to test this algorithm would be to compare it against memoization and sorting in a highly controlled fashion - to study the performance of the three methods as a single variable changes and then to interpret the meaning of these changes.

It seems that the paper does this. The authors tested the different algorithms as the number of duplicate values varied, while everything else - the size and number of tuples, the predicate, and the hardware remained constant. Number of duplicate values seems like an appropriate choice of variable to test because both optimizations are most appropriately applied in situations with high numbers of duplicate values. The authors then follow their tests over the number of duplicate value with the same series of test over the same variable but a non-predicate function, which generates a very large output. This is a good choice of scenario to test because it acknowledges and explains a significant weakness in the Hybrid Hashing algorithm and states that Hybrid Hashing is more appropriately applied to scenarios where the output of the expensive function is small or where the number of duplicate values is extremely high, lest the user lose out on performance.

Other variables, especially the number of tuples, may have been worth exploring, as the authors mention that one major improvement of Hybrid Hashing over sorting is that the amount of memory consumed by Hybrid Hashing varies with the number of unique values whereas the amount of memory consumed by sorting varies with the number of input tuples. It would have been enlightening to see the performance change as the number of input tuples changes and to evaluate whether this growth is linear, quadratic, exponential, etc..

However, the authors did still test the most important variable - number of duplicate values - and so their testing framework seems very reasonable.</div>
  <div class="block">In general, this paper seemed to very simply and clearly present its findings. One notable strength of this paper is that it acknowledged its major weakness - functions with large outputs - in a very head-on fashion so that the reader could clearly understand that in spite of this weakness, the algorithm still had many useful applications. The paper was also careful to clarify that hashing is only useful in situations where a lack of duplicates cannot be guaranteed. 

This paper would have perhaps benefitted from more robust testing, but it ultimately appears to have tested the most important aspect of its algorithm (execution time with respect to number of duplicate values).</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">The paper introduces hybrid cache, an optimization for processing duplicate tuples with expensive user-defined functions. The hybrid cache hashes input tuples and materializes distinct input and outputs, and uses a second hash function to partition a number of buffers that are written to disk, rescanned, and processed using naive memoization. The hybrid cache can produce superior results in cases where output values are small, and can be used in Object-relational databases in conjunction with sorting.</div>
  <div class="block">The paper tackles the problem of expensive UDFs being computed on the same input value more than once, which significantly hurts efficiency. This issue is most relevant in object-oriented or object-relational databases, where the complex data structures stored would require UDFs to interact with. UDF performance is part of a larger problem: how query optimizers generate query plans for queries that use costly UDFs. Even when optimizers try to place the expensive UDFs in optimal parts of the query plan, the UDF may still be costly. Optimization may also cause predicate methods to be postponed until after joins, creating more duplicates. There are several factors that cause slowdown in processing UDFs: number of tuples, number of values, size of output. Any optimization like the ones mentioned (sorting, memoization, and hybrid cache) incur slowdown from I/O and paging. </div>
  <div class="block">One interesting issue the paper mentions as future work is the question of integrating hybrid cache with query optimizers. The performance of hybrid cache may depend on its placement in the query plan, due to the different characteristics of input tuples. For example, the proportion of duplicates, average size of output. </div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">The paper demonstrates why Hybrid Cache is better than memorization and sorting in expensive queries. Their experiment results show that Hybrid Cache dominates other method for expensive predicate methods, but there is a tradeoff between sorting when the size of method outputs is large. </div>
  <div class="block">Hybrid Cache can definitely be applied to query optimizations in large database systems. In paper, it only did experiments on single synthetic table, so a more general user case would be applying Hybrid Cache when querying many tables with different tuple sizes such as JOINs. In this case, in the growing phase of Hybrid Cache, multiple hash tables might be needed to map each tuple. Modifications are needed when querying different tables at the same time. Another case is the multimedia database. Querying a multimedia database can be very expensive, so the idea of hybrid cache to avoid redundant method invocation can be appl</div>
  <div class="block">The paper gives less impressive results than expected. Based on the experiment results, the only case Hybrid Cache dominates is when facing expensive predicate methods. However, the paper states that Hybrid Cache is always superior than hashing and sorting in many instances. For other methods such as ORDERBY and GROUPBY, users still have to identify whether to use sorting or Hybrid Cache.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">This paper considers three caching algorithms, memorization, sorting and Hybrid Hash for the problem of avoiding redundant method invocation during query processing. It presents a performance study which shows that Hybrid Cache is the most efficient for expensive predicate methods while sorting is often preferable for non-predicate expensive methods with large outputs. The paper also introduces additional techniques for caching multiple methods and suggests directions for future work.</div>
  <div class="block">As suggested in the paper, in addition to avoiding redundant method invocation, hybrid caching techniques could be applied to a lot of other applications of unary hybrid hashing such as grouping for aggregation and duplicate elimination. This becomes even more significant when it comes to online settings. Although sorting also works for grouping and duplicate elimination, but it has obvious problems in online query processing as sorting is blocking: outputs can only start being produced after the entire input has been processed. Thus it is preferable to use hashing algorithms for online aggregation and duplicate elimination so that outputs could be produced as tuples are read in. Since traditional hashing algorithms has the problem of thrashing when the number of values exceeds the size of main memory, hybrid caching is a great improvement for such scenarios that scales gracefully.</div>
  <div class="block">I find the experimental study section of the paper thorough and detailed. It not only considers the performance of hybrid cache vs sorting for different number of duplicates, but also for cases of expensive methods of different output sizes. The discussion of effects of output size is significant as the prior experiments are performed on expensive predicate methods where the outputs are small Booleans. When it comes of scenarios of large output sizes, the authors admit that sorting should outperform hybrid hash since hybrid hash share main memory among both inputs and outputs. Fewer entries can fit into main memory as output size increases and thus require more partitions. This tradeoff is justified with experimental results and leads to the discussion of future work. The optimizer should be able to choose which caching algorithm to use based on method types in a object-relational database.</div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">The key idea presented by the paper is an adaptation of the hybrid hash join algorithm named hybrid cache for the purpose of caching instead of joins. The paper tackles the issue of preventing unnecessary re-computation of expansive operators in a database system, and propose using hybrid hashing as an alternative to traditional memorization, and caching single methods after sorting them. It essentially solves the issue of hash tables of method and outputs being too big to fit in memory. </div>
  <div class="block">
The basic hybrid cache algorithm first builds a hash table with method and inputs as key and output as value in memory. When memory reaches a certain size, the key idea is to partition the rest of the functions so that they can be evaluated/memorized based on the partitions where the hash table for the partition fits on disk. The rest of the input tuples are partitioned based on a new hash function. Each partition has a buffer in the remaining space in memory, and the buffers can overflow to disk if full. After forming partitions, each partition is read into memory, evaluated, then deallocated. A minor tweak is that instead of saving some space for the buffers, the original hash table is allowed to fill up the whole space, then parts of it is freed and sent to buffer if necessary. 

The design decisions really mirrors that of hybrid join. Eg. Since its hashing based, it operates better when there are many repeats in inputs since costs grows with value instead of total records as with sorting. A key difference from hybrid hash join is that instead of pre-allocating partition 0 and materializing the hash table, input is added to fit memory. Basically partition 0 becomes a adjustable partition. This is necessary since there can be great disparity between output sizes of functions, causing memory overflows for predetermined partitions. 
</div>
  <div class="block">The paper is very thorough in its evaluations of the advantages and disadvantages compared to sorting and memorization. There is one part that I'm not very sure of though, which is whether access patterns for methods match that of caching. The hybrid join algorithm operates per method, with the assumption being that the cache will exist as long as necessary. However, the  hash tables for a method only exists for a certain amount of time, after which it is deallocated. What if the same method is required for a different part of the query that is evaluated later? This is a key difference between joins and caching generic methods, where we can be sure that we are done with hash table of the former after joining the tuples involved, but method access can be arbitrary. </div>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">This paper is focusd on the problem of redundant method invocation during query processing. Since the standard method to address this problem is memoization which is not efficient for many values, the author proposed the hybrid join.  The hybrid hash join dominates the memoization method and have a trade off between the sorting method. </div>
  <div class="block">Problem: Redundant method invocation during query processing is wasteful. 

It is important, because in object-relational database, people are allowed to write expensive methods and the method may run on the same data for several time which is time-consuming. The current standard method in PL is memoization which is extremely inefficient dealing with large values and sorting is also not efficient for moderate dataset. So we need a new way to address the problem.</div>
  <div class="block">1. The main idea is like a hybrid join as it has mentioned. The first table is stored in memory, and the other data are partitioned to different disks.
2. Hybrid everything sounds like a boring incremental paper. 
3. I would like to say this paper is very clearly written and comprehensively analyzed, such as the discussion about how many partitions and how many pages for hashtables. 
4. The conclusion is clear and includes the recommendation for industry: "recommend that both sorting and Hybrid Cache be implemented in extensible Object-Relation and Object-Oriented
Database systems. "</div>
  </p>
<hr />
