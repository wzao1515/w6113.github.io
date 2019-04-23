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

<h2 id="short-summaries">Short Summaries</h2>

<h3>--</h3>
<p class="block">provide two method bitwaving/V and bitweaving/H to scan data at bit level</p>

<h3>--</h3>
<p class="block">The paper provides a framework for fast(&lt;1s) lineage capture and optimizations</p>

<h3>--</h3>
<p class="block">This work provide a mechanism on how to track lineage in RDBMS with low overhead storage and capture as well as answer lineage queries efficiently.</p>

<h3>--</h3>
<p class="block">1. efficient lineage index representation 2. optimizations for lineage query processing</p>

<h3>--</h3>
<p class="block">Smoke uses read and write efficient index structures based on row ids to capture lineage information.</p>

<h3>--</h3>
<p class="block">Enables lineage capture to run at interactive speeds and outperform handwritten implementations by applying various instrumentation optimizations.</p>

<h3>--</h3>
<p class="block">The SMOKE engine which supports lower lineage capture overhead by introducing new physical algebra and pruning the lineage to speed up queries.</p>

<h3>--</h3>
<p class="block">SMOKE reduced lineage capture overhead and query processing costs by multiple orders of magnitude</p>

<h3>--</h3>
<p class="block">This paper describes SMOKE, an in-memory database engine that provides both fast lineage capture and lineage query processing.</p>

<h2 id="reviews">Reviews</h2>

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The background of this paper is the need to address the high demanding scanning in main memory database. 

In this background, this paper targets at intra-cycle parallelism for high performance which means that the bit weaving techniques can not only leverage the SIMD, but can also be implemented with full-word instructions. There are two methods proposed - bitweaving H and bit weaving V which are both based on columnar data in main memory data processing system.

The detailed method is below. 

For HBP method, it adds delimiter bits in the process word to represent different columnar code. Then it uses some conversion to compute the predicate and generate the result bit vector.

For VBP method, it is much easier. The data stores in the column in the processors' word. In this way, some optimization technique can be applied like early stopping. The paper also mentioned that the first group them, then do early stopping will be faster. 

Overall, the HBP is good for lookup and VBP is good for the scan. The experiments also show that is much faster than the state of art techniques. This could accelerate the main memory database. But the hater would be the overhead of constructing this memory layout overhead and in the HBP do the conversation. 
</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">Goal: accelarate query in analytical data processing. 

Denormalize complex query to small queries. And use some predesigned optimization to accelarate the small query.</p>
  <div>Predictions 2:</div>
  <p class="block">a vectorized implementation using AVX2 instructions with In-list predicate to accelerate the column-store database system.</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">Traditional provenance typically include answering questions on why a record come to being, which records contributed to it, and how it come into being. Lineage capture is a way to keep track of the relations between database operations (eg a graph), and can be used to answer provenance questions. One issue however is that traditional methods of instrumenting a database both physical and logical is slow, to the point where they are often not utilized.

Traditionally, logical lineage capture annotates the original table with symbols, resulting in annotated output graphs (eg semi-ring paper semantics). Querying these take a long time however due to joins required. Physical lineage capture builds data-structures of lineage as a query executes, which incurs overhead during query execution. 

Smoke descibes a row index based method of lineage capture that falls under physical lineage capture (index tables). However, it presents the option of partially instrumenting the data during base query optimization (Eg saving the hash table during hash based operations, fill in the index later). It recognize that lineage only needs to be captured for operators that are not 1 to 1 (eg different mapping of rids), reducing the amount of information captured. Other optimizations include pushdown based optimizations with select and group by to futher reduce what is captured. 

Results wise, delaying instrumentation does reduce latency compared to full smoke instrumentation, which does better than traditional instrumentation. 
</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">Data lineage generally tracks the history of data with varying purposes ranging from questions about the origin of the data, uncertainty of a specific result, and the process that led to that result. Lineage capture can induce large overhead in either the execution performance or data storage or both depending on the mechanism used. In this paper, the author introduced new methods that enable efficient fine-grained lineage tracking in in-memory databases with low overhead storage and capture as well as answer lineage queries efficiently. To achieve that, they take advantage of four principles 1) tight integration of lineage capture and query execution, 2) apriori knowledge of lineage queries which can inform on which lineage data need to be materialized as well as 3) push the logic of lineage consuming queries to lineage capture phase 4) data structure reuse to overlap capture and execution costs.
There are two types of data structures used to encode the lineage information based on the operator input to output relationship, the first is a 1-N relationship between input and output where ith index corresponds to the ith output id and points to an array that contains the set of input that corresponds to that output. The second is a 1-1 relationship between the input and output represented as a single array. To populate the indices there are two strategies used, defer and inject. Inject strategy incur the full cost of index generation during query execution. In the other hand, Defer strategy defer portions of lineage capture until after the operation execution.
As a demonstration of the capabilities of the system, the author provides two use cases that leverage lineage information 
and use them as constructs to build interactive applications where the interactivity of the application is contingent on the performance of lineage queries.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">This work requires proximity of the application to the database store, thus it is convenient when the database fits in-memory, otherwise other techniques to reduce the networking delay need to be taken into account in order not to compromise the application interactivity.</p>
  <div>Predictions 2:</div>
  <p class="block">Similar to Smoke, I would expect tight intergration of the lineage capture with the computation method and the use of efficient data structures to store the lineage metadata.</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">SMOKE is a database engine for lineage capture and lineage query processing in real time. To achieve this, several techniques are applied. SMOKE indexes rids based lineage representation because indexes are cheaper. Besides, to generate lineage indexes when executing operators, SMOKE uses two types of plan: DEFER and INJECT. DEFER would let operators run first and then capture the lineage. And the paper also talks about optimizations like pruning and push-down for some pre-defined interactions. As the future work said, I think SMOKE could combine some offline data structures such as data cubes and online lineage index structures to be more efficient.
</p>
  <div>Comments:</div>
  <p class="block">Besides, fine-grained lineage could also be used for the diagnosis of distributed machine learning pipelines. Zhang et.al at Diagnosing Machine Learning Pipelines with Fine-grained Lineage proposed a system that records the input datasets, the output datasets and the cell-level mapping between them. Therefore, many common ML operations such as featuriza-tion and labeling could be mapped to the lineage capturing interface.</p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">Consider selection: both forward and backward lineage capture use row-id arrays, with the forward one pre-allocated based on the cardinality of the input relation. While iterating over the relation evaluating the predicate, the inject strategy adds two counters to track the row ids of the current input and output and uses these to update the indices when an output row is emitted. There is no defer strategy for selection, because it is strictly inferior to inject.
As another example consider hash joins. Smoke will generate both backward row id arrays, and forward row-id indexes. Under the inject strategy a build phase augments each hash table entry with a row id array containing the input row ids for that entrys join key. The probe phase tracks the row id for each output record and populates the forward and backward indexes. One drawback of this strategy is that we might trigger multiple re-allocations (growing the size) of the forward indexes if an input record has many matches.
The defer strategy for hash joins takes advantage of the fact that we know the size of the forward indexes after the probe phase. The build phase maintains an additional output row id list, storing the first output record for each match (output records are emitted contiguously). After the probe phase, the forward and backward indexes can be pre-allocated and populated in a final scan of the hash table.</p>
  <div>Comments:</div>
  <p class="block">Instrumentation pruning disables lineage capture for lineage indexes that will not be used by the workload. Lineage queries that apply summarisation / aggregation before presenting results provide an opportunity to push-down optimisations. Selection push-down when using a static predicate, and partitioning of index arrays by predicate attributes for dynamic selections. Group-by aggregation can also be pushed down into lineage capture.</p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">In all, this paper is pretty thoroughly written and the concept is thoroughly tested. The applications of lineage to usages such as debugging and auditing are fairly clear to grasp, and the use case of a data visualization well motivates the need for interactive speeds, since data visualizations is very much an area where one would expect a user to need to debug queries. 

However, I am curious as to how lineage would be expressed to the user in these circumstances - although I understand the concept of "using lineage to debug," how to integrate lineage into a highly visual environment is an interesting product design question. It may be, however, somewhat out of the scope of this paper, considering that this paper seems to mostly be concerned with Smoke's speed and low overhead wins over the state-of-the-art.

Speaking of Smoke's speed/overhead, these are the axes along which the reader would naturally expect to see smoke tested, and the authors follow through on this. 

I do wonder whether hand-implemented lineage tracking is as common as the paper makes it out to be - I'm not sure how necessary beating handwritten code was in the first place if implementing the handwritten code is cumbersome and not terribly to begin with. Is/would anyone actually argue for this code to be handwritten when faced with a reasonably fast lineage capture implementation?</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The problem that paper wants to solve is that currently the lineage engines cannot have both negligible lineage capture overhead and fast query execution. The SMOKE engine is built to make both possible. The other database systems that supports lineage such as Trio and Subzero only differs based on how and when the lineage is materialized. The SMOKE solves the problem in a different way that designed based on four design principals. I think they all make sense to reduce the lineage overhead because it introduces new physical algebra to represent the relationship between lineage and execution and optimize by reusing data or avoiding unnecessary materializations. I think the key implementation point is the two main rid-based lineage representation and the use of DEFER and INJECT diagram to decompose each type of query. By having two types of query plan, it is clear that how projection, selection, group-by, and joins can be represented in both plans and their advantages in reducing capture overhead. The future optimizations in joins are also interesting which saves space for hash table and preallocating rid indexes to speed up allocation process. The result shows that SMOKE outperforms other alternatives in many magnitudes in lineage capture, lineage query performance, and query evaluations. The experiment results part is very detailed and convincing to believe SMOKE optimizes the lineage process greatly. As mentioned in 2.3 and 6.5, the SMOKE has many applications in debugging tools, data manipulations, and visualizations. The paper explores two use cases: Crossfilter and Data Profiling. By adding SMOKE to the existing system, it is clear the SMOKE outperforms LAZY and data cube in space or time. In a debugging setting, it would be interesting to see how to use SMOKE to debug in DISC systems such as Hadoop and Hyracks. One thing to question the technical part of the paper could be in the approach of the SMOKE, it mentions the plans are translated into machine code, so it would be better to show what machine codes are generated and could there be further optimizations on this side.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block"></p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The SMOKE paper posits that the longstanding problem lineage systems have is how to quickly capture lineage across a workflow in order to speed up future queries. To reduce overhead and execution costs, the authors: (1) introduced a physical algebra that tightly integrates lineage apture logic into query execution, (2) stored lineage in write-efficient data structures, (3) when lineage queries are known upfront, use apriori knowledge to avoid materializing lineage that will not be queries in the future, and (4) reuse data structures constructed during normal query execution. SMOKE should be compared against state-of-the-art lineage capture techniques to prove that they have successfully reduced overhead and sped up queries. The authors do so using a combination of microbenchmarks, TPC-H queries and two real-world applications. Their experiments show that SMOKE reduces lineage capture overhead and query processing costs by multiple orders of magnitude, and it's effective for using with data profiling and visualization applications.
</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">The Titian paper suggests that debugging is time consuming because of no tools provided  programmers resort to trial and error debugging through log files. Current approaches that support data lineage: (1) use external storage to retain lineage information, (2) have to make data provenance queries through separate interface, (3) have no access to intermediate data. Based on the Titian paper, SMOKE should allow programmers to easily access lineage information of a certain input or output, and provide data on intermediate transformations. Lineage queries should be bundled into the platform.</p>
  <div>Predictions 2:</div>
  <p class="block">The Interactive Visualizations paper suggests that we would need to speed up visualization interactions or database queries to readily visualize data. The role of fine-grained provenance in interactive visualization has been less explored because the overhead to track fine-grained provenance can slow fast query processing engines by multiple orders of magnitude and cripple interaction response times. Based on reading this paper, SMOKE should reduce overhead to track fine-grained provenance so that queries can be faster.</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">This paper by Psallidas and Wu describes SMOKE, an in-memory database engine that provides both fast lineage capture and lineage query processing. It presents the design principles, system implementation, run-time optimizations of the database and details the experimental setup and results. In its experiments, Smoke is compared to state-of-the-art logical and physical lineage capture and query processing approaches and outforms these systems by multiple orders of magnitude under microbenchmarks, TPC-H queries and real-world applications.

The major contribution of the paper is its description of the database engine SMOKE which avoids the drawbacks of the traditional logical and physical approaches in lineage capture. It tightly integrates the lineage capture logic into physical database operators and store lineage in read- and write- efficient lineage index representations to enable fast lineage capture. DEFER and INJECT strategies are combined wisely to minimize operator instrumentation costs. It also employs effective optimizations that uses apriori knowledge of future lineage consuming queries to speed up query processing. These techniques allow SMOKE to reduce lineage capture overhead and lineage query processing costs by up to multiple orders of magnitude compared to state-of-art approaches. One thing that I find the paper strong at is its experimental evaluation sections. The evaluation is thorough and convincing as it is done systematically along multiple dimensions. It starts by comparing lineage capture techniques on single operator followed by multi-operator, and then on the workload-aware optimizations. The workloads are also diverse ranging from microbenchmarks, TPC-H queries to two real-world applications. 

The other strength of the paper I find is that it is organized well around the four design principles: Tight integration, Apriori knowledge, Lineage consumption and Reuse brought up in the Introduction section. All following sections refer to these principles before going into the details of the system implementation. This helps concepts well motivated and clearly explained. In addition, in the conclusion section the authors assert that these design principles are broadly applicable beyond the design of the engine. This is a very interesting observation and I hope the paper could give a little bit more detail pointing out how these principles could help with future research in database systems.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">This paper examines the connections between data provenance and interactive visualizations and explores how interactive visualizations could be expressed and implemented in terms of provenance. The introduction of the paper does much give a lot of clues regarding how this is implemented, but based on the paper on SMOKE we could expect some underlying index structure for lineage representation that captures the relationship between input and output tuples. This provenance could be used to answer interactive queries on the data visualizations such as interactive selections, joins and aggregations.</p>
  <div>Predictions 2:</div>
  <p class="block">This paper describes Titian, a library that enables data provenance in Apache Spark so that users using this extension could quickly identify the input data that causes a potential bug or outlier result. According to the papers abstract and introduction, different from SMOKE which features a physical algebra that tightly integrates the lineage capture logic, Titian is a separate library that captures data lineage using agents in the Spark programming interface. Titian enhances Spark RDDs with a LineageRDD reference that allows for data tracing.</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />
