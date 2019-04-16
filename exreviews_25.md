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

<h3></h3>
<p class="block">BlinkDB does an optimization problem on differently biased, multi-columnar stratified samples to arrive at approximate query results on big data</p>

<h3></h3>
<p class="block">BlinkDB chooses the multi-dimension stratified sample methods via ELP over runtime and builds multi-dimension stratifies samples via MILP planning.</p>

<h3></h3>
<p class="block">defining a multi-dimensional sampling method that builds and maintains a set of samples, and sample selection strategy to satisfy a set of constraints</p>

<h3></h3>
<p class="block">The main contribution of this paper is the creation of BlinkDB, which reduces query response time on large data by using statistical sampling methods</p>

<h3></h3>
<p class="block">The paper introduces BlinkDB, a system for fast approximate queries. They introduce a novel system of stratified sampling to answer queries.</p>

<h3></h3>
<p class="block">BlinkDB uses stratified random sampling over a subset of frequently used columns achieve low latency estimated response</p>

<h3></h3>
<p class="block">It proposes BlinkDB, a parallel approximate query engine that processes queries with error and time constraints by novel sample creation and selection</p>

<h3></h3>
<p class="block">BlinkDB gives a framework that is able to query large dataset with bounded error and time.</p>

<h3></h3>
<p class="block">A system that to bridge the gap between overly general query engines and overly assumptive query engines. Allowing user-specified constraints.</p>

<h3></h3>
<p class="block">1. built a approximate query processing framework to speed up big data interaction. 2. a stratified sample for better representation of small groups.</p>

<h3></h3>
<p class="block">BlinkDB is an approximate query engine, made a key assumption about large-data workloads (QCS model), and used sampling/multi-dim aggregation methods.</p>

<h2 id="reviews">Reviews</h2>

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">BlinkDB is a system that does an optimization problem to choose among stratified samples to give approximate query results for large datasets. In comparison to related works it is novel in that it has differently biased samples built on multiple columns, as well as guarantees on error and runtime. BlinkDB attempts to be an intermediate, which offers both efficiency and generality, between the existing solutions, which have big tradeoffs between making strong assumptions about workload and having good performance. In BlinkDBs sample creation and sample selection modules, a set of stratified samples are built and the sample to base a querys results on are selected based upon the users response time and accuracy requirements. Blinks optimization approach is column-set based and assumes the frequencies of group and filter predicates will remain relatively stable across time.

The papers walkthrough of how a user inputs a query greatly clarifies the workflow, as does the implementation stack diagram. The timeline of running a query, including edge cases such as that where the workload changes dramatically, seem to be addressed. 

The evaluation methods are sound: BlinkDB uses the standard TPC-H benchmarks, by which it performs reasonably well. It also uses industry standard workloads from Facebook and Conviva, which is highly convincing in terms of its practical feasibility. The authors state BlinkDBs performance over running on the full dataset is 10-200x faster on a 1% error bound. The fact that this ran on a distributed system makes it extremely useful in practice but does not seem to be among the major theoretical contributions of the paper.

However, the M and K constants in the optimization are not clearly described and seem to be magic numbers. Although they are confirmed to be reasonable successful by the experiments, more reasoning about the choice of these values would strengthen the paper.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">
BlinkDB support queries to have a shorter response time with the sacrifice of some little accuracy. The main idea is using the sampling function to include the predicted distribution of workload to achieve the balance of efficiency and generality.

The most important contribution is different from former research, BlinkDB allows choosing the multi-dimension stratified sample methods in the runtime. The choice is made based on balancing the error and response time. Another important contribution is that BlinkDB builds and maintain multi-dimension stratifies samples via MILP planning. 

BlinkDB introduces error latency profile to give heuristic information on how to satisfy the response time requirement, which is the most novel. 

One thing not quite about the paper is that the paper is named bounded error. However, it is not 100% confidence in the error rate. And I am not pretty sure about how the confidence rate comes out from the paper. One thing I like is they use ILP to model which dimension to choose. About the scale out part, I am not clear.




</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">BlickDB aims to balance between efficiency and generality compared to previous work by assuming that the previous set of columns Query Column Sets are stable over time without making assumptions about the query attribute values.
They achieve that through the use of multi-dimensional stratified samples on the most frequent QCSs. The authors model the problem as an optimization problem, where the goal is to select the column sets from the possible QCSs that can answer the queries while satisfying the storage space limit and query constraints.
The authors discuss the online sample selection problem and the challenges encountered during query execution and their approach of solving it by running queries on sub-samples and collecting statistics that facilitates query evaluations using Error Latency Profile heuristic.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">BlinkDB achieves the reduction in query response time by creating a set of representative samples, allowing users to trade off query accuracy for response time. Since BlinkDB relies on a set of samples, it's important that the samples are proven to be representative of the data set and a good predictor for future workload. We also want to verify that BlinkDB can answer queries much faster than existing benchmarks. The authors do a good job in showing that past history is a good predictor for future workload by measuring the number of unique Query Control Sets (aka sets of columns that are stable over time) over a percentage of queries. Their results also show that they managed to achieve up to 200x speedup for queries with a 1% error bound at 95% confidence compared to standard Hive on Spark. One question that I would like addressed is how to determine when the size or type of samples need to be recalibrated, since the distribution of data may change over time.</p>
  <div>Comments:</div>
  <p class="block">BlinkDB would be great for applications that require making decisions really quickly based on a huge volume of data, such as day trading or serving ads to Facebook customers. It can also be effectively used to display aggregate counters on websites such as Twitter, where accuracy isn't as important as being able to display the result in as little time as possible.</p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">S1. The paper presented a fairly clear argument for why targeting "predictable query column set" queries was a good target application, which was nice.
S2. The use of stratified sampling is a good application of statistical sampling techniques, and I liked the formulation of *which* samples to create as an optimization problem (although I admittedly found the details of the problem hard to follow)

W1. One complaint I had about the optimization problem was how they treated storage costs -- the storage costs is in the number of rows, which doesn't seem that meaningful. Shouldn't we care much more about the # of bytes? (It's also unclear whether the constraint should be a "soft penalty", given how easy it is to trade money for storage space if you use S3 or some other elastic store)
W2. At the end of the paper, the compare this method to data cubes (or other pre-aggregation techniques, as opposed to sampling techniques), but aren't fully 
convincing about why their technique is better than these "synopses". They basically argue that synopses can't express the queries we're interested in, but a specific example of such a query would be helpful (especially because BlinkDB seems to only support `GROUP BY ... HAVING`, aggregations, and filters).
W3. The experiments were overall fairly thorough and well-done, but I think there were two experiments that would've really improved the paper: (1) it would've been interesting to examine Error-Latency Profiles in more-depth: how accurate are the empirical estimates? How sensitive is the system's performance to ELP accuracy? Are there big gains to be had here, or is the current approach good enough? (2) The briefly mention a problem with correlated queries coming from re-using samples, but it would've been nice to get some kind of analysis how big this problem is (e.g. how big do the samples need to be and how fast do you need to replace samples for this not to be a problem)?

</p>
  <div>Comments:</div>
  <p class="block">#NAME?</p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">Interactive queries requires fast response times, which give rise to a numerous methods to provide fast estimates. However, traditional sampling methods with error bounds either overly restricts query types, while online aggregation require long computation for rare tuples. BlinkDB is an improvement on traditional sampling which relaxes the distribution assumption by assuming that future queries involve similar columns with no limits on the exact queries. The relaxation of the assumption expands the space, and would typically introduce more sampling error for edge case. BlinkDB deals with this with a tradeoff with sampling time - by constructing stratified sampling over the columns instead of uniform sampling, it ensures a tighter error bound for edge cases intruding up to 30 mins of delay in sampling. 

BlinkDB essentially provides a balance between previous state of the art methods. Stratefied sampling had been previously introduced, but is too expansive; BlinkDB considers past queries by limiting the column space. Sampling estimation have long existed, as have subsampling, but BlinkDB combines all these techniques to achieve a balance. 

At its heart, BlinkDB is about optimizing tradeoffs. Sample size, column space, subsampling, storage and data selectivity etc are all interrelated, and the goal is to find the best set of columns and sampling strategy to satisfy the users error bounds. It uses MILP optimization to compute the column that covers the most distinct values constrained by storage, then estimates time needed to evaluate a query by precomputing a subsample of the stratified samples, where each subsample can be computed in a distributed manner.

To prove that it is better, the algorithm would need to compare its performance against all other similar sampling based query estimation systems. The emphasis however is on the novelty of the algorithm/approach, and the paper very thoroughly evaluated the algorithm, with storage, scaling, runtime considered under both realistic datasets and extreme cases. However, the only comparison is against base hive without sampling (200x better), which is perhaps too naive of a dataset. 

Overall, the paper reads as an incremental algorithmic improvement to exisiting estimation algorithms, where there would soon be a new variation of it used to address specific limitation. A possible improvements would be an heuristic alternative to MILP which is exponential.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">This paper by Agarwal et al. introduces the design and implementation of BlinkDB, a parallel, sampling-based approximate query engine built on top of the Hive Query Engine to provide support for ad-hoc queries with error and response time constraints. In order to achieve better balance between accuracy and response time, BlinkDB is based on two key ideas: a multi-dimensional sampling strategy and a run-time dynamic sample selection strategy. The paper goes over both strategies in great detail and provides a thorough evaluation of the system which uses both the well-known TPC-H benchmark and the Conviva real-world workload. The authors evaluated the system on query response times, accuracy and convergence properties of the sampling approaches and sample selection as well as its ability to scale. Experiments on BlinkDB show promising results along all these dimensions.

The major contribution of the paper lies in the two key ideas of the system: sample creation and sample selection. It uses column-set based optimization framework to compute stratified samples based on the assumption that future queries will be similar to historical ones. The paper does a good job in its clear classification of the predictability of the workloads in the background section. This well motivates the use of QCSs in sample creation. The other novel idea involves the dynamic sample selection algorithm that uses error-latency profile to decide the sample to run the query which best meets the querys response time or accuracy requirements. The authors illustrate this idea with an example and carefully accounts for the statistical bias that might result from the stratified samples. Although correction is proposed, more details on how the correction reweight different subgroups would be preferred. 

In general, I find this to be an excellent paper which proposes significant sample creation and selection ideas for approximate query processing. The system augments Hive to support for ad-hoc queries with bounded errors and bounded response time. The paper gives thorough evaluation of the system on different metrics with different workloads and the plots are pretty clear in demonstrating the capabilities of the proposed system. However, there are just a few places where the authors could potentially improve. 

First, Although the paper gives a lot of derivations and intuition for the value of a set of parameters such as M and K, I think the ultimate value assignment of these parameters are arbitrary and lack justification. For example, the paper only states that in practice it sets M = K = 100000 and the experimental results show that the system performs quite well on the datasets using these parameter values, but no explanation is given regarding how the values are picked and how the performance changes as the values change. Maybe there is a better choice of value?

Another potential weakness of the system lies in the validity of the papers assumption that the data and query workloads are relatively stationary over time. Although the paper empirically tests the validity of the predictable QCSs model with real-world queries, it is questionable how adaptable this model is. First, if the workloads changes overtime, then the QCSs will not be as predictable, making the pre-computed samples not as useful. Secondly, if the data itself changes constantly, the samples becomes stale. I appreciate that the paper recognizes this concern and states that BlinkDB keeps track of statistical properties of underlying data and periodically runs the sample creation module to recompute these properties and decide whether the set of samples needs to be changed. Nevertheless, I find this important issue should receive more attention and be discussed in more detail. How many samples could change at a time? How are the statistical properties gathered and maintained? 

Nevertheless, this paper is significant and proposes a framework that proves to be efficient for approximately processing ad-hoc queries with error and response time constraints, especially for relative stable data and workloads.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The paper introduces BlinkDB which is a parallel approximate query engine for running queries on massive dataset. The issue that paper wants to solve is that current framework is a bad fit to the big data analytics workload. The online aggregation method is inefficient, and sampling and sketch makes strong assumption about the predictability of the workloads. Two sets of ideas exist in current work. One is to use additional computational resource to decrease query time. They have drawbacks when computational resources does not grow with new data size. The BlinkDB built on top of Spark, so it improves on it by requiring less resources. Another idea is to use Approximate Query Processing (AQP) to provide approximate answer for the query plan. Two key strategies that enable BlinkDB to be efficient are the multi-dimensional sampling strategy which is an offline sampling module and dynamic sample selection strategy, a run-time module that creates Error-Latency Profile for queries. The implementation of BlinkDB is on top of Hive framework, so it basically extending the original framework by the two modules. The main idea to create offline samples is by constructing a sample to target queries using QCS and selecting a subset of the columns to build the sample families. The paper claims that selecting columns to maximize the weighted sum of the coverage of the QCSs of query is an optimization problem. I think that part is very interesting because it formalizes the problem into a linear optimization problem. There are also many techniques such as narrow down the problem scope to fasten the optimization process. There are a lot of features evaluated in the paper: the tradeoff between accuracy and response time, convergence properties, effectiveness and error projections, and scalability. The results show that BlinkDB is x10-x100 faster than other frameworks with the expense of 2%-10% errors. Users are able to set error constraints or time constraints to bound the performance which also has very practical use when users have different needs on the query process. The BlinkDB only supports simple queries, so in the future the BlinkDB can extend to nested queries and joins as well. In paper, it does not explicitly talk about the algorithm to solve the optimization, though it does talk about considering only column sets that actually occurred in the past queries as a simple optimization. A possible research direction is to find algorithm efficiently solve this optimization problem. The BlinkDB has many applications such as querying an extremely large dataset that does not require zero error rate. I do not find major drawbacks of the paper because the background well-explained, the system makes sense to me, and the experiments are well-rounded. Haters might say the tradeoff between accuracy and run-time is not impressive, and there could be better framework that achieve same run-time with much lower error rate.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">There are some existing methods for approximation in DBMS like sampling, sketches and online aggregation (OLA), however, none of the previous solutions is a good fit for todays big data analytics workloads. OLA provides relatively poor performance for queries on rare tuples while sampling and sketches make strong assumptions about the predictability of workloads or substantially limit the types of queries they can execute. BlinkDB is come to rescue, it is a distributed sampling based approximate query processing system that strives to achieve a better balance between efficiency and generality for analytics workloads. BlinkDB consists of two main modules: Sample Creation and Sample Selection. The sample creation module creates stratified samples on the most frequently used QCSs to ensure efficient execution for queries on rare values. These samples are designed to efficiently answer queries with the same QCSs as past queries and to provide good coverage for future queries over similar QCS.
I think BlinkDB has many advantages when compared to other methods. First of all, they adopted an adaptive optimization framework that builds and maintains a set of multi-dimensional stratified samples from the original data over time. This method makes less assumption compared to traditional sampling methods and obtains more valuable information by solving the optimization problem. Second, they utilized a dynamic sample selection strategy that selects an appropriately sized sample based on a querys accuracy or response time requirements. Next, BlinkDB supports more general queries as it makes no assumptions about the attribute values in the WHERE, GROUP BY, and HAVING clause, or the distribution of the values used by aggregation functions. From an engineering perspective, BlinkDB provide a flexible query interface for end users, this flexibility enables end users to configure their system according to their current workloads and change dynamically, it is a really cool idea and definitely useful in industry. 
One weakness I think the paper has is that it makes overly conclusive statements about the stableness of QCSs. Real-life workloads from Facebook and Conviva are analyzed and it was determined that QCSs were stable in those workloads, which I think definitely means that QCSs can be stable, but I dont think that this necessarily means that we can assume this is true for all or even most workloadsI think this is a topic that might need its own research paper to prove. Also, another minor weakness is that any system that allows for more user-specification (time &amp; accuracy requirements) is inherently more complicated to use than one that does notthis is by nature and many times is not an issue, but is still a trade-off.</p>
  <div>Comments:</div>
  <p class="block">Generally, its a nice paper describing a novel approximation method in handling large data. The downsides of this paper are minor. Although they made less assumption about the complete knowledge of the tuples, as they said in their paper, if the distribution of QCSs is stable over time, their approach create samples are neither over- nor under- specialized for the workload, however, if the distribution of QCSs are variant, I think this prediction may not be very well. Similarly, due to this reason, the BlinkDB is not so good for the database where the data changes very frequently.</p>
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The main problem is to address online aggregation for big data. It would take a long time to scan the whole tuples. Instead, we could only look at parts of data to speed up the query. And that's why we need samples. However, uniformed sample sometimes is weak to represent small groups of data because it assumes a
