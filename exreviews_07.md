---
layout: page
---

## Remarks

We will be transitioning from _how_ reviews are structured to the comments noted in the reviews.  In particular:

* What is the singluar intellectual concept behind the paper?
  * Instead of space (aka dense) partitioning (quad, kd), use a sparse partitioning (bounding boxes).
* What is the crux that determines the paper's success?  Which did the paper mention/address?  Which were not?
  * In the R-tree case, it is the ability to speed up lookups in a shape database
* How _should_ the paper be evaluated?  How far is that from what was actually evaluated?
* Specifically for the Guttman paper, are there insert workloads where the index could regress into becoming useless?
* Good exercise to reformulate concerns that arise as research questions, and see extent it is answered.  


#### The good

Good point.  An index should be better than a sequential scan (including prefetching benefits of scans).  R-tree cannot guarantee this for even a theoretical selectivity of ~0

        The upper bound of scan is actually O(N) is overlap between
        rectangles is large and the algorithm end up traversing all
        branches.

The crux is how nodes are split!    The division of labor, where splits happen on inserts, but affect reads is a great observation.

        D2: The authors outright conclude that the linear-cost
        algorithm is the superior approach - I question this, since
        the cost of node-splitting is incurred with writes, while
        the cost of suboptimal node-splitting is borne with reads.
        Thus, a more read-heavy application is more likely to feel
        the cost of the cheaper node-splitting algorithm.

A great corner case!

        W3: Objects with little area but have a large span over
        multiple dimensions (e.g. a diagonal sliver in 2D) would
        be quite poorly represented because of wasted space.

If the evaluation is limited to 2D, then why can't other structures work?

* quad tree may oversplit, but what about KD-tree?
* is back of the envelop sufficient to convince the reader than quad-trees/kdtrees are bad for R-tree use cases?
  * they are designed for points.  When to stop splitting for boxes/polygons tuples?

        W1. Even though the index data structure was designed for
        multi-dimensional spatial data, the paper provided evaluation
        only on two-dimensional data with the assumption that the
        performance would follow a similar trend.


It is always good to stepback from the paper's settings and ask what the real use cases are/should be!
It turns out that trajoctry data is highly diagonal, and R-trees are terrible.   See [Trajstore paper](http://db.csail.mit.edu/pubs/TrajStore.pdf).

        is that a realistic assumption though? I assume that a lot
        of geospatial queries require paths between things (e.g.
        taxicab rides in NYC) which probably don't work at all with
        R-trees.

Good to appreciate the context of a paper and when it is being proposed.

        D2: I dont see any major flaw to the paper itself as I find it
        pretty impressive for the author to present a novel working data
        structure clearly and convincingly in a short paper.


Call out nonsense cases!  In what applications would this problem arise?  This exact observation motivated a cottage industry of papers.

        D3: The data structure is split by rectangle. But in this
        scenario, when all the shapes are non-overlapped and the
        minimal bounding rectangle is the same, the tree would be
        nonsense.

#### Needs Improvement


This is not bad, but ambiguous.  What properties of the VSLI data make you feel like it would not generalize?

        it's unclear whether these results really generalize to
        more real-world settings.

Why?  What could happen under higher dimensionality?

        W2: Have not consider the cases when dimension becoming higher, for example, more than 5.

This is an unqualified statement.

        S3:  Very innovative and contributive.

Proof of which algorithm?  Proof of what?

        W1: The paper will better off if it could provide an Appendix of proof of the algorithm mentioned


At least do some basic literature to suggest what these alternatives would be!

        If the researcher could do an experiment between R-Tree
        and other range search algorithms (I assume there exists
        some)




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

  <div class="block">The paper propose a new method of indexing spatial data named a R tree which allows for on average O(log N)  search over multidimensional spacial coordinates. </div>
  <div class="block">S1 - This seems like a very reasonable adaptation of the B tree to Muti-dimensional data suited for indexing to search for spatial objects that naturally take up area in high dimensional space. </div>
  <div class="block">W1 - claims to do better than many existing approaches, but claim is difficult to evaluate since no experiments are done. For instance, we don't know how big the impact of secondary memory paging is with Quad tress and K-d trees. We don't know if it performs better than grid files

W2 - The upper bound of scan is actually O(N) is overlap between rectangles is large and the algorithm end up traversing all branches. </div>
  <div class="block">D1 - The paper contains a easy to understand data structure and algorithm for inserting, deleting and searching through objects that spans high dimensions. They claim that a good use case for this would be searching for geolocations within an area. The structure is very similar to a B tree, with the main difference in that instead of a scalar value with well defined concept of difference and distance, the R tree compares ranges in high dimension. Like the B tree, split is expansive. However, the quality of split can determine the complexity of search, and finding an optimal partition is NP hard and very expansive. Alternative quadratic and linear algorithms for splits are provided and evaluated. 

D2 - It's interesting that they experimentally try different values m and M bounds for entires in a node. I do wish a more thorough exploration of different M values and total time it takes to compute each operation is more thoroughly explored.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper presents a new type of data structure, the R-tree, for querying multi-dimensional spatial  data. The R-tree is organized as a hierarchy of increasingly small bounded regions, which can be used to reduce the search space when executing a query. The R-tree is not very thoroughly tested, but as it is a new kind of data structure that provides sheer functionality not offered by the various other options, it is an improvement over the current approach (which is to simply search through all the data programmatically).</div>
  <div class="block">S1: The images and the simplified 2D example help to clearly explain how the data structure operates.
S2: The paper provides a number of different node-splitting algorithms and compares the strengths and weaknesses of each approach (see D1 for details).
S3: The algorithms laid out in the paper are very explicitly defined and provide pseudocode, which makes it easy to understand the steps involved.</div>
  <div class="block">W1: The performance tests werent particularly surprising, but they occupied a fairly substantial amount of real estate - it seems like the performance tests were more proof of concept that this new data structure functioned and so perhaps could have been condensed.

D2: The authors outright conclude that the linear-cost algorithm is the superior approach - I question this, since the cost of node-splitting is incurred with writes, while the cost of suboptimal node-splitting is borne with reads. Thus, a more read-heavy application is more likely to feel the cost of the cheaper node-splitting algorithm.

W3: The paper claims that the various approaches already suggested for this application are insufficient for various reasons, but it does not include these data structures in its performance tests for comparison, and it does not include any citations proving this point</div>
  <div class="block">D1: The different node-splitting algorithms are as follows: the EXHAUSTIVE ALGORITHM guarantees correctness but is unworkably slow. The QUADRATIC-COST ALGORITHM is not guaranteed to find the correct solution, but it provides a reasonable approximation. The LINEAR-COST ALGORITHM is a less-granular approximation with an even faster runtime. Thus, different tradeoffs may be made depending on how often a node is expected to be split vs. how often it is to be queried. 

D2: Perhaps it would have been more illustrative to use an applied example, such as geographic data, to better illustrate how this data structure could be used on real data. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">
In this paper, the author has proposed a dynamic hierarchical index structure that is meant to efficiently store and retrieve multidimensional data objects of non-zero size according to their spatial location with the goal to minimize number of visited nodes and disk access in a search queries by grouping data objects into disk pages using notion of objects proximity in space.
</div>
  <div class="block">S1. The author has explored and evaluated different algorithms to achieve a good split when a nodes entries exceed M.
S2. The data structure is designed to minimize the number of visited nodes in a spatial search.
</div>
  <div class="block">W1. Even though the index data structure was designed for multi-dimensional spatial data, the paper provided evaluation only on two-dimensional data with the assumption that the performance would follow a similar trend.
W2. The choice of the splitting algorithm affects the performance of R-trees which determines how many nodes are expanded during a query search thus incurs extra overhead.
</div>
  <div class="block">D1. R-tree inspired its design from B + -tree and extends it to a multi-dimensional space where index records in its leaf node contain pointers to data objects with their enclosed area, and none-leaf nodes include entries that point to a child node and the minimum bounding box that encloses all entries within the child node.
D2. R-tree puts an upper bound on the space utilization to be at least 50%  by restricting the number of entries in each node, except the root, to be between [m, M] where m &lt;= M/2 and M is the maximum number of entries to be inserted in a node.
D3. R-trees aims to increase the branching factor to produce shorter depth thus fewer fetches with the downside that you would have more work within each node.
D4. Splitting happens when a nodes entries exceed M. The author has proposed different strategies to handle it with the goal to minimize the total area and the overlapping of the two nodes since this increase the pruning of irrelevant nodes when performing search queries.
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">There is a lack of effective data structures/algorithms for indexing multidimensional data, which is central to applications such as visual design and geo-data. R-trees are a structure that make possible the indexing, updating, and searching of such data.</div>
  <div class="block">S1: The algorithms are very clearly laid out in pseudocode, demonstrating a relatively straightforward but powerful implementation. It is difficult to argue against a written-out implementation.
S2: The authors give a large set of operations and algorithms, including several different search functionalities (overlap search, range search, specific item search), demonstrating R-trees' versatility and flexibility.</div>
  <div class="block">W1: There is little evaluation of R-trees compared to other existing data structures, which would provide a stronger motivation for adopting R-trees.
W2: The authors give techniques for maintaining R-tree structures (e.g. re-inserting when redistributing entries) but do not convince through proof or experimentation whether they are able to successfully maintain the tree. Furthermore, I wasn't sure how consistent R-tree performance would be: the authors don't go into much detail about the worst-case scenarios
W3: Objects with little area but have a large span over multiple dimensions (e.g. a diagonal sliver in 2D) would be quite poorly represented because of wasted space. </div>
  <div class="block">D1: In the introduction, the paper lists various other approaches for handling multidimensional data and briefly mentions shortcomings. It would have been more thorough to choose one or two of the most viable alternatives and use them on the same datasets (to compare ease of use, performance, memory usage, etc). I would be more convinced of the power of R-trees if its strengths versus other data structures were quantified more concretely.
D2: Related to W2, the authors mention that "re-insertion incrementally refines the spatial structure of the tree, and prevents gradual deterioration" but don't really specify how the tree might deteriorate. And is re-inserting adequate enough to ensure that R-trees don't deteriorate? Could performance/capacity still become unacceptable over time? I wish the authors had further explained the situations where R-trees would perform worst: what sequence of inserts/deletes/updates would produce a poor tree? How often/quickly does an R-tree degrade? </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper describe a dynamic index structure called an R-tree which that help to retrieve data items quickly according to their spatial locations, and illustrate its insert searching and updating algorithms as well as performance test. </div>
  <div class="block">S1: Specific and clear regulation on R trees properties: how R tree is constructed and how to apply it.
S2: Illustrative figure such as Figure 3.1 help to understand the principle and structure of R tree.
S3: Very innovative and contributive.</div>
  <div class="block">W1: Has not rule out the possibility of overlapping between spatial area of nodes.
W2: Have not consider the cases when dimension becoming higher, for example, more than 5.
W3: Have not consider the overlapping of indexing space.</div>
  <div class="block">D1: Detailed comparison among exhaustive algorithm, quadratic-cost algorithm and linear-cost algorithm.
D2: Performance tests show advantages of R tree in linear node-split algorithm and is useful for indexing spatial data objects.
D3: Give clear and specific steps of each algorithm with detailed explanation.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The idea of the paper is to define a new indexing technique called R-Tree which is better than B-Tree in indexing multidimensional data. The hypothesis is that as spatial and high dimensional data become desirable, a new data structure that is better than B-Tree could optimize range search/delete/update queries.</div>
  <div class="block">S1: The paper introduces the impulse, method, and results of designing R-Tree very straightforward. The pseudocode provided makes reimplementation of the algorithm easily.
S2: The performance of linear algorithm approach is strong enough for users to apply R-Tree in real system. </div>
  <div class="block">W1: The paper will better off if it could provide an Appendix of proof of the algorithm mentioned. 
W2: The experiments only consist of comparisons between R-Tree algorithms. If the researcher could do an experiment between R-Tree and other range search algorithms (I assume there exists some) in general, the paper will be stronger.
W3: In the insertion experiment, it measures the last 10% of insertions. The reason behind might be when the tree is more complete, the performance becomes more stable. However, in real settings, an incomplete or sparse tree is sometimes common.</div>
  <div class="block">D1: The paper pioneered the indexing technique of Muti-data which is impressive since it was published many years ago. The R-tree improves based on the existing data structure: B-Tree, Quad tree, and kd-tree, and proves to be a very efficient way (linear time!) of indexing. There are a lot of applications of R-tree until today. I found there is saying that R-tree does not have a good worst case bound, but it has a good practical application. How does that work? </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper posits a method, R-trees (R for rectangle), to index over spatial data of n-dimensions, which is not adequately addressable by existing one-dimensional tree structures including kdb trees and b-trees. The root node of an R-tree represents the total area covered and each level down narrows the bounding box of all nodes below it. R-trees are height-balanced and insert nodes based on where they will require the least expansion of the bounding box.</div>
  <div class="block">S1: There is a reduction of data redundancy by having overlap among nodes of the tree.

S2: The organization of the paper into different algorithms which can be performed on the tree, together with their comparative computational costs, is helpful to think through more potential use cases. However, the lack of detail about the nature of the dataset on which the tests were performed makes the runtimes less helpful. Running on trees of different spreads rather than running tests of more algorithms might have been more illustrative of the actual capabilities of R-trees.</div>
  <div class="block">W1: There is a lot of redundancy in coverage because of overlap, as shown in Figure 3.1.
This means that there are multiple correct places for each leaf to go, that is, in Fig. 3.1 R9 and R10 could belong in either R3 or R4. This means that you have to search in both, increasing computation time.

W2: What is the nature of a spatial-data tuple? While for the purposes of a survey paper it might be fair to simply posit that one could exist, since this paper involves runtime comparisons and the creation of a spatial tuple as opposed to an ordinary tuple is non-obvious, especially an N-dimensional spatial tuple, this could use more elaboration.

W3: In most use cases, you likely need an R-tree in addition to some other indexing mechanisms because not all of your data will be spatial. This means that you need to create multiple access methods for different subsets of your data, and perhaps keep multiple representations of spatial tuples. This increases overhead of database management.</div>
  <div class="block">D1: In the Introduction, Guttman says R-trees are meant to address areas in multi-dimensional spaces. We can extrapolate that more complex objects are still reducible to rectangles, but the paper is not clear about how extensible this is in practice to N-dimensional spaces. The testing phase mentions that all tests used two dimensional data but the structure and algorithms work for any number of dimensions. While theoretically this may be true, in order for this to be a solid claim there should be some test results on higher-dimensional data.

D2: W1 and S1 are direct tradeoffs. This is a classic computation tradeoff. The R-tree paper does not clearly enough make the case for why they found this overlap a better choice than creating more redundancy. This is addressed by the later R+ tree which adds objects to everywhere they could belong instead, which indeed is shown to be much faster.

D3: Regarding W3: this difficulty is addressed well in the "Generalized Search Trees" paper, which creates a way to sidestep the need for a new codebase for the same underlying dataset.

D4: Guttman states that it is not possible to guarantee good worst-case performance. However, it is not clear exactly what might trigger the worst-case runtime nor how common this is likely to be. More detail about this would strengthen the case for adopting this data structure in real databases.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">We need a new data structure to efficiently index multi-dimensional data. An R-tree is a tree that works using bounding boxes -- each node is attached to the smallest bounding box that contains all elements in the node's subtree.</div>
  <div class="block">S1. The core idea of the R-tree was well-presented and quite intuitive. I liked how the algorithms were explicitly laid out, with both a short intuitive definition and a more detailed psuedocode-like description.

S2. During the performance evaluations, I like how they looked a lot of different measures of performance (CPU time, pages touched, etc) with different tuning parameters. It helped give a sense of which parameters mattered for which metrics.</div>
  <div class="block">W1. There don't seem to be any theoretical bounds on R-tree performance, which I found concerning. Obviously, if for every node only 1 subtree needs to be searched, than you get O(lg n) time for searching. But if every nodes has 2 subtrees that need to be searched (because their bounding boxes overlap enough), then you suddenly require O(n) time for searching, which is a big difference. In what kind of situations can you guarantee that bounding boxes are 'distinct' enough to get logarithmic time?

W2. Related to W1, I found the performance evaluation a little lacking. They used only used VLSI layout chips and did random queries on, and it's unclear whether these results really generalize to more real-world settings. I wished they had explored more real-world applications (with both real-world datasets and quasi-realistic query patterns). </div>
  <div class="block">D1. The paper only really talks about 'find all ___ that are contained in ___ area' queries. Are these the only geospatial queries worth mentioning? I think it would've helped a lot to go over some real-world applications to get a sense of what kind of queries are actually important.

D2. The fundamental assumption behind R-trees is that geospatial objects are 'box-y' -- is that a realistic assumption though? I assume that a lot of geospatial queries require paths between things (e.g. taxicab rides in NYC) which probably don't work at all with R-trees.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Guttman's paper on R-Trees introduces a new data structure indexing objects based on their spatial location, allowing spatial data to be quickly accessed. The R-Tree maintains a node invariant that the bounding box I of a node contains the bounding boxes of its children. Using this invariant, the paper implements search, insert, delete, condense, node-splitting methods to maintain the R-Tree.</div>
  <div class="block">S1: The paper implements R-Trees and is able to measure performance of various node operations using metrics such as CPU cost, Pages touched, space efficiency.

S2: the linear node-split algorithm uses greedy techniques to find a close-to-minimum area node split. The algorithm's performance gain outweighs the small tradeoff in accuracy.</div>
  <div class="block">It is hard to find weaknesses, considering the paper was written in 1984, and R-trees were the first of many proposed data structures to successfully handle multi-dimensional point data and be introduced to RDBMS.</div>
  <div class="block">D1: the scope of the paper is to present the implementation of R-tree data structure, so it does not does not discuss integration into RDBMS at length. Presumably, any RDBMS using B-trees could be adapted to use R-trees without changing the query optimizer, for example. But there may be benefit in tuning components like the query optimizer to take advantage of R-trees, for example by performing cost estimation. Still, the paper is not targeted towards a specific RDBMS so this is out of scope.
D2: From work following the Guttman paper, it becomes clear that R-tree is useful for CAD programs and geo-spatial data. In a 1994 paper Hellerstein et al proposed an indexing method that behave flexibly depending on the kind of data to be stored, e.g., like B+-tree, R-tree, RD-tree for data with set-valued attributes. The target application for their Generalized Search Trees were even broader than R-tree "sets, terms, images, sequences, graphs, video and sound clips, fingerprints, molecular structures, etc". Interestingly the GiST insertion algorithms are based on the R-Tree ones. But the GiST paper also deals with other issues such as compression, user-defined functions, cost estimation in query optimization.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Motivated by the need to handle spatial data efficiently in tasks such as computer aided design and geo-data applications, this paper proposed a data structure, R-tree that is an indexing method well suited for data objects of non-zero size in multi-dimensional spaces. Guttman et al. performed several tests on the R-tree and concluded that it is a powerful structure for such operations and is simple to added current relational database systems for spatial augmentation. Although there are multiple following papers describing variants and improvements of this data structure, this paper offers great basis and general idea for multidimensional search trees</div>
  <div class="block">S1: Describes a novel dynamic access method that organizes multi-dimensional data objects via a hierarchical organization of rectangles.
S2: The use of heuristics to minimize the total area of the two covering rectangles.</div>
  <div class="block">W1: Queries may lead to searches in multiple paths from the root to the leaves due to overlaps of the bounding rectangles thus inefficient in case of large overlapping area and empty space.
W2: Only total area of the bounding triangle is considered in the heuristic. Other factors such as margins and overlap is not optimized.
W3: R-tree is more domain-specific and is not flexible enough to support arbitrary data types. This is not necessarily a weakness considering the scope of the paper, but a possibility for future research which is addressed in the GiST paper.</div>
  <div class="block">D1: The major contribution of the paper is that it describes a novel dynamic access method that organizes multi-dimensional data objects via a hierarchical organization of rectangles. Similar to B-trees, R-tree is a height-balanced tree with index records in its leaf nodes containing pointers to data objects. The number of entries that each non-leaf node can hold is between m and M, and each node points to a child such that its minimum bounding rectangle contains all the rectangles in its child. It supports searching, insertions, deletions and other operations on multi-dimensional data in an efficient manner. One of the great innovations of this data structure is the use of heuristics to minimize the total area of the two covering rectangles in node splitting. Guttman proposed the Quadratic Split and Linear Split algorithms which uses this heuristic to improve the performance to query processing and was able to show that the linear node-split algorithm gives performance almost as good as more expensive techniques. 

D2: I dont see any major flaw to the paper itself as I find it pretty impressive for the author to present a novel working data structure clearly and convincingly in a short paper. However, there are some weaknesses to the R-tree data structure, which motivates many following studies of more efficient variants. First, one major disadvantage of the structure is that queries may lead to searches in multiple paths from the root to the leaves due to overlaps of the bounding rectangles. Thus, in case of large overlapping area and empty space, these could lead to many vain searches that deteriorate performance. To handle this issue, Sellis et al. proposed a variant of R-tree, the R+-tree [The R+-Tree: A Dynamic Index for Multi-dimensional Objects] that avoids overlapping rectangles in intermediate nodes by allowing partitions to split those rectangles. Their experiments shows up to 50% savings in disk accesses when doing searches, but the clipping technique results in duplicate copies of objects to be stored. This redundancy makes the updates of the tree structure to be more complicated (e.g. upward propagation is required in splitting) despite the improve in search performance.

D3: Another flaw in the R-tree design is that although it tries to minimize total area of the two covering rectangles during node splitting, it is a little nave that it does not consider other factors that also affect the quantity of splits such as overlaps and margins. Thus, when the data rectangles have very different sizes and shapes, optimizing just one parameter may influence the others in a bad way and deteriorate the overall performance. Beckmann et al. noticed this weakness and considered these criteria other than minimizing area which includes minimization of overlaps and margins, as well as maximization of storage utilization [The R*-tree: An Efficient and Robust Access Method for Points and Rectangles]. They proposed another variant of the R-tree data structure, the R*-tree that uses a combination of heuristics of reduction of area, margin and overlap of the rectangles so that it is robust against ugly data distributions. This structure has also been proved successful in applications and commercial systems. 

Overall, the R-tree structure provides a great foundation for multi-dimensional search trees and has led to all the forthcoming variants of the dynamic structures for spatial searching.
</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The problem the author is addressing is that it can be difficult to managing multi-dimensional data e.g. a Cartesian plane in a data structure that can support CRUD operations in polynomial time. Therefore, the author proposes to use an R-tree which is based on B-Trees (Balanced Trees) structures by having multiple indexes to support n dimensional data and new algorithms to manage the new data structure to be compatible with databases. I personally agree with the authors attempt to deal with multi-dimensional data, as it is reasonable to attempt to not invent a new data structure from scratch to address this problem.</div>
  <div class="block">S1  The author is attempting to use modified B-trees which is a reasonable starting point as B-trees are already often used to store database information. 
S2  The explanations are clear on how R-trees work, especially figure 3.1 made it clear especially when you notice that M = 3 and m = 1. 
</div>
  <div class="block">W1  The author doesnt compare his R-trees with current data structures he mentioned in the introduction meant to tackle multi-dimensional data.
W2  From the paper, Section 3.5 seemed critical as the maximum number of nodes is a tuning parameter that can seriously impact the performance of R-trees, but the author does seem to gloss over this a lot. 
W3  There are optimized B-Trees that the nodes store the number of nodes in the subtree and sum of keys.
W4  The authors could have attempted to simplify the problem further. 
</div>
  <div class="block">S1 and S2 - Although, I wish there was a clear example on how this could have been used to say represent vector space. The algorithms for CRUD operations were also simple and didnt stray much from their B-tree origins, so at least that is a strong point.
W1  While the results seem pretty good but, the best way to prove your system is superior is to compare it with other systems in the same niche. Honestly, I may have considered rejecting the paper for this reason.
W2- The authors have mentioned in Section 3.5.2 that the cost of rebalancing the tree O(M^2). However, their plots didnt put all their M parameters they said they tested or plotted performance against M. Is there a point where M can be too high as well that it can negatively impact R-trees?
W3 - This might have been used to attempt to pre-emptively correct subtrees that can have M + 1 leaves. I am not sure, but it may be less costly to change a subtree than the whole R-tree.
W4 - What if for example you know all that values will be higher or lower than everything currently on the tree. Could this be used to optimize R-tree performance? Some data can be easily predicted, which can perhaps help minimize the number of rearrangements the R-tree needs.
</div>

  </p>
<hr />



<p class="review">
  <h3>---</h3>

  <div class="block">This paper proposed a new data structure, R-tree, in order to support spatial database storage (multi-dimensional point data). R-tree shares some common properties with B-tree, and it could split space with hierarchically nested, and possibly overlapping boxes. In the test it shows that R-tree could have a good performance, although there isn't too much mathmatical analysis. I believe the R-tree could have a really bright future because of its good compatibility and the cheap split method.</div>
  <div class="block">S1
The linear-cost algorithm is useful and cheap, and it is an important point for supporting R-tree.</div>
  <div class="block">W1 
The heavy overlap of each node's minimal bounding rectangle would result in low efficiency (O(N) rather than ideally O(logm(N))) of searching.

W2
Initialize a R-tree index structure is time-consuming (O(Nlogm(N))).</div>
  <div class="block">For S1, the algorithm is based on the previously suggested quadratic split method, and only PickSeeds and PickNext are modified. For PickNext, the linear-cost algorithm simply chooses any one of the entries rather than choosing the greatest preference entry. For PickSeeds, it will firstly normalize the separation and then pick the greatest separation (the distance between leftmost right side and rightmost left side and the distance between rightmost right side and leftmost left side).

For W1, it is because for one searching rectangle, all intersected rectangles must be visited, therefore the worst time would be O(N).

For W2, to build a B-tree structure, there could be efficient ways like bulk loading, but for R-tree, according to the paper the only way to build the structure is to repeatedly insert objects, so the time complexity would be O(Nlogm(N)) without counting disk I/O.

Personal idea for this paper: For this paper, I believe it is more like an inspiration because it only gave simple ways to implement each operation. Therefore I could hardly find any weakness in terms of the idea that is suggested, and the weaknesses for the algorithm (operation) are not very reasonable. Besides, for the evaluation, it was just based on some experimental data rather than offering theoretical analysis. Thus more works were done after this paper (like the R*) to improve its performance, and R-tree is more applicable in real life (IBM Informix system)</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Classical one-dimensional database indexing structures are not appropriate to multi-dimensional spatial searching becuase they would need to support a multi-dimensional search space along with range search. To address the deficiencies in existing structures (such as the inability to support multiple dimensions and inefficient random searches), Guttman proposed an alternative structure called an R-tree that represents data objects by intervals in several dimensions. An R-tree is height-balanced with index records in its leaf nodes containing pointers to data objects. Index records in the R-tree are either in the form of (I, tuple-identifier), where I is an n-dimensional rectangle representing the bounding box of the spatial object; or (I, child-pointer), where I covers all rectangles in the lower node's entries.</div>
  <div class="block">S1 - I appreciated the illustration of the R-tree structure in figures 2.1a and 2.1b. It makes it much easier to visualize how it works and how objects may overlap.
S2 - The provision of extensive pseudocode to illustrate how each of the R-tree methods works is helpful, especially since the author suggests that other search algorithms could be implemented (presumably by the user) in the future
S3 - The R-tree is height-balanced, which means the proposed algorithms (e.g. delete) wouldn't have to propagate up by too many levels</div>
  <div class="block">W1 - Overlapping structure contributes to non-optimal search algorithm.
W2 - The proposed algorithms require making a lot of adjustments (on every level) to the tree.
W3 - Author provided performance analysis between algorithms but not against other multi-dimensional structures (or even different types of multi-dimensional data). It seems obvious that a linear algorithm would beat an exhaustive or quadratic algorithm, so much of the extensive performance analysis seems to not have a point.</div>
  <div class="block">D1 - (expanding W2) In Section 2, the author said that "no periodic reorganization is required." However, looking through the algorithms in Section 3, Insert invokes AdjustTree and Delete invokes CondenseTree, both of which adjusts the index and involves relocating entries in the tree. Furthermore, performing node updates means its index record must be deleted, updated and re-inserted. His earlier statement seems like a direct contradiction to his description of the design. As for the design of the data structure itself, each insert and delete potentially affects multiple nodes, so one can imagine the overhead to build up quickly when using the data structure. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper presents R-trees which handle spatial data efficiently. R-tree is an index structure which uses (I, tuple-identifier) as index record entries. I am the minimal rectangle bounding box. It has delete, update, insert strategies as B tree and is optimized for the spatial data. For the node splitting part, the paper put forward 3 algorithms: exhaustive algorithm, quadratic-cost algorithm, and linear-cost algorithm. The author also conducts several experiments on inserting, updating, deleting. To conclude, the linear node-split algorithm proved to be good.
</div>
  <div class="block">S1:
This data structure is suited to spatial data.
S2:
The data structure similar to the B tree needs fewer disk accesses.
S3:
When the record number becomes larger, the performance is not worse. 
S4:
The linear algorithm proved to be good and save cost on inserting and deleting.
</div>
  <div class="block">W1:
The data structure only supports the rectangle store. When it comes to irregular shape. it could result in the imprecise query.
</div>
  <div class="block">
D1: The R-tree is a multi-way tree. Even if the index is disk-resident, the structure is designed so that a spatial search requires visiting only a small number of nodes.

D2: From figure 4.7, with the number of records larger, the CPU per insert and delete is not going to be larger. Thus, the structure is good to deal with a huge amount of data.

D3: The data structure is split by rectangle. But in this scenario, when all the shapes are non-overlapped and the minimal bounding rectangle is the same,  the tree would be nonsense.

Question: These papers extend indexes to consider multi-dimensional datasets. Do they address the needs for modern data types (e.g., videos, images, books) and all the things we want to use this data for??

Answer to the question: Video has too many dimensions. I think we need the metadata to search the videos. For images, all the images can be modeled as (length, width, channel). The pixel channel is all ranged from 0 to 255. So, it can not address the need for images too.
</div>

  </p>
<hr />
