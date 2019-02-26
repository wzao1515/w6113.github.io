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

  <div class="block">Eddie describes a new method of data flow control in query processing, where instead of optimizing to generate a fixed physical plan for all data, tuples move between operators freely (push based) in an order dependent of the current estimated best order. </div>
  <div class="block">S1 - This idea is very different compared to past strategies that aims to find the best optimization, acknowledging and dealing with the fact that complexities can change. </div>
  <div class="block">W1 - Given that this is about online processing, it might make sense that there was no comparison against block based execution systems. However, it wasn't clear to me how much overhead the eddy operator incurs vs saved due to better ordering when inputs/selectivities change since time is only reported for overall queries. </div>
  <div class="block">D1 - Eddies is a interesting algorithm where each operator lives in its own thread, processing tuples 1 at a time. Firstly, this is a push based system, where tuples are pushed to operators whenever they are marked as available. This by itself translates to gains over traditional pull based systems due to back pressure of slow operators (Eg fast operators are typically done first and do not have to wait for slow/blocking ones). Operator order have a fixed initialization order, but order can be changed between blocks based on heuristics collected and scheduling policy (lottery scheduling, named since the operator that receives a processed tuple is determined by a weighted lottery). To make the process adaptable, the hueristics are reset after a certain interval (interval is not really examined in the paper). The algorithm is evaluated over selects and ripple joins in terms of time for execution, adaptability and correctness vs optimal order. Authors point out that hash joins, with its blocking operator is not suitable for eddies. 

D2 - The concept of Eddies is that it sacrifices optimality for adaptability. A scenerio in which it would be bad against fixed optimizations is for OLTP queries where tuples and selectivity does not change much, but is frequently run. Window interval is also not fully explored in the paper, and a imagined nightmare scenario is if the interval coincides with some kind of cyclic tuple/selectivity change such that the order is always wrong. In the best case scenario, changes are constant across multiple windows, allowing Eddies to always perform better than any fixed optimization plan. </div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The Hypothesis of this paper is that using Eddies that can optimize queries during runtime through techniques like switching the order of operations can improve performance. I agree with the results of the experiment seeming reasonable that eddies should be studied further as a means to optimize query performance is a distributed system</div>
  <div class="block">S1 - I like how the paper offers an interesting alternative to optimizing queries, in particular changing the order of which joins occur first. It seems reasonable that if you start joining with smaller sets, the overhead of the whole join will decrease.</div>
  <div class="block">W1 - I was hoping for more details on proving the communicative property in certain circumstances in Section 2.2.2. Is commutative property based only on the size of the tables? </div>
  <div class="block">In terms of which queries would the system work best, it would work best with data is highly structured as they would have primary keys and most likely be in Third normal form preventing duplicate tuples that could add overhead in Merge Join. An example of such data would be like a warehouse inventory data. In terms of queries that would it perform worst with, well if the data isn't normalized it will cause much worst overhead. The queries that would cause the most trouble for this system would be data where the size of the selects will usually not be the same. Also, if the data is highly unstructured and uncorrelated could add more overhead in the join as the data could be in other parts of the share-nothing system.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper presents Eddies, a query processing mechanism that allow fine-grained, adaptive, online optimization. Eddies use the Ripple join and continuously reorder the pipelined joins to adapt the unpredictable query environment prevalent in massive-scale systems.</div>
  <div class="block">S1: Using a window scheme to the lottery. In this way, eddies  reacts adaptively to the changes.
S2:  Eddies has flexibility in reordering operators.
</div>
  <div class="block">There is no example of a complete workflow and no demonstration of scalability.</div>
  <div class="block">D1:  Eddies can flexibily reorder operators. There is no requirement that all operators in the eddy be at moment of symmetry except the operator fetching new tuples.
D2:  Eddies acts better than other systems when there are big changes to operators.
D3:  And the worst case can be either 1: run the same query for many times (eg require best optimization) or 2: there is small fluctuation on the optimization border.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Eddies are modules that greatly optimize query plans by reordering in-flight operators while a query runs. In the system, the eddy sits between the data sources and the operators and routes tuples to operators on a tuple-by-tuple basis. This can greatly improve runtime by not getting stuck at long operators and holding selective operators until the end.</div>
  <div class="block">S1:

The simplicity of the concept of the eddy is very convenient. The notion that it can either be the sole optimizer or work in concert with another optimizer allows it to integrate well into systems and makes it easy to use. 

S2:

The conceptual contribution of the paper in recognizing and defining barriers and moments of symmetry is very important in further work in dynamic query plan reorganization. </div>
  <div class="block">W1: A lot of the performance of the eddy is dependent on its lottery scheduling and its forgetting scheme, for which the authors do a lot of trial and error but do not end up defining cutoffs.

W2: The test cases shown and discussed are very short and simple queries, but the use of eddies is probably most applicable when query plans are very long and have lots of operators to route to. It would be useful to posit, if not actually experiment with, what happens when you run a very complex, many-staged query.</div>
  <div class="block">
D1:

Good showcase of the eddy: If you had a very long query plan, with a series of many join operations on a small set of tables that changed cost dramatically, and had low selectivity, the eddy would make your runtime dramatically faster because it could adjust the order when operator costs switch, and each wrong routing would cost less. 

D2
Bad situation for the eddy: Suppose you have a very large result set that you are doing an equi-join on, so you use a hybrid hash join.  If you have equi-joins with very high selectivity and you are using hash joins, then eddies dont help much. The tuples will be absorbed into hash tables and you wont be able to re-route them. Then, too, the jeopardy for erroneously routed queries will be high, and reordering doesnt help much either.  If you further added a delayed input, the ticketing scheme of the eddy, which does not capture that growing selectivity, will further detract from the use of the eddy. Also, if the field you are joining on is federated data that you dont have statistics on, and/or complex objects, it will be even harder for the eddy to predict. 

Also, if you could create a situation where you had to route to the most selective relation first, that would also dramatically decrease performance.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">

Static optimization techniques are not effective in a dynamic workload were the assumptions used to compute plans cost are not stable, in this paper the authors introduced 'eddy' which is a query processing mechanism that continuously adapts a query plan at a fine-grain on tuples according to changing costs of operators; their selectivity, and tuples arrival rate from inputs</div>
  <div class="block">S1. The introduction of a general framework for adaptive query processing
S2. Defining synchronization barriers and moments of symmetry of operators</div>
  <div class="block">W1. Intertwining both the experiments setup and results with algorithm description wasn't ideal and made it hard to follow.
W2. Eddy's operator may become a bottleneck if all tuples have to go through it.</div>
  <div class="block">D1.  Come up with the most interesting query/workload for which such an engine would really shine. What are its properties?  adaptive query processing is most beneficial for systems with fluctuating behavior that affect query processing cost such as running a continuous query over data streams from multiple sources that reside in a wide-area network. Thus, any static optimization plans can easily become suboptimal over time.

D2. selectivity fluctuations arise: 1) due to correlations between predicates and the order of tuple delivery, 2)  harware performance fluctuations since relations are often horizontally partitioned, one disk can perform better than another

D3.     Eddy's tuple routing strategy selects operators based on consumption (cost) and production (cost and selectivity) rate and uses lottery scheduling , where each time the eddy gives an operator a tuple it credits it with one ticket, each time the operator return s one ticket is debited from the eddy's running count for that operator. when an eddy is ready to send a tuple to be processed, it holds a lottery among the operators eligible for receiving the tuple, then the chance of winning corresponds to the count of tickets for that operator.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper introduced a new query operator called an eddy. This is a query processing mechanism that continuously reorders pipelined operators in a query plan on a tuple-by-tuple basis. This allows the system to adapt to fluctuations in computing resources, data characteristics and user preferences.</div>
  <div class="block">S1: Lots of independent threads working on pulling tuples through query plan trees is complex and no so efficient and simple as routing them. The idea of routing tuples is intuitive and easier to deal with.
S2: Continuously reordering pipelined operators in a query plan is a clever concept.
S3: The concept of moment of symmetry is very interesting as it allows to swap the order of processing without losing work.</div>
  <div class="block">W1: The time taken to adapt to changes is not presented.
W2: The lottery-based algorithm is not described enough.
W3: The author dont provide results about a complete query workload. One may wonder if the eddy will scale when the number of queries to process will increase.</div>
  <div class="block">D1: It would be nice to see how moments of symmetry can be utilized in other adaptive query processing engines. There are systems that do this by inserting a check-statistics operators.
D2: Since the eddy operator is purely and execution time evaluation, none of the changes it sees are reported to the query optimizer. It would be nice to have the eddy update the optimizers statistics whenever it sees a huge change. Then, maybe we could change the join algorithm on the next run, not just the argument order.
D3: The notion of a moment of symmetry is very intuitive and clean. It can probably be applied to a variety of query operators. The experimental evaluation section suffers from not providing an evaluation of a full workload.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper claims that because hardware and data layout between any two particular machines in a large distributed system can vary so drastically, query optimization is no longer a useful form of queries processing. Eddies outlines a method for dynamically optimizing the querys execution strategy as it runs using an operator called an eddy, which is capable of reordering tables among joins. The authors claim that this model is able to drastically improve the performance of dynamic query plans while still handling static query plans with minimal performance overhead.</div>
  <div class="block">S1: The naming schemes used in this paper helps the reader grasp how the engine works on a more intuitive level.</div>
  <div class="block">W1: The terminology and concepts in this paper are a bit hard to visualize and would have been greatly enhanced by a diagram.
W2: In general, I just really struggled to understand what was going on in this paper beyond a high-level overview of the operators adaptability. Im not sure if this is a problem with the paper itself or with my own understanding of the paper, but its difficult to parse in a way thats not easy to rectify by shoring up on background topics.</div>
  <div class="block">D1: Perhaps one interesting application for this engine would be a torrenting-like application that relies on a variety of different hosts - each with different hardware and network connections - to piece together data. Any host may suddenly stop sharing with the network. Eddies adaptability is ideal for such an application because 1. it can learn the dataflow for this network without any prior knowledge about the individual hosts in the network, and it can adapt if new hosts join or disconnect via its lottery system.

D2: A query this engine would suffer somewhat on would be any query that has to be executed in a static order that can easily be optimized using query optimization, as the Eddies techniques overhead would be allowed the greatest chance to take over in that case.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Instead of investing resources in finding optimal plans, eddies adaptively arrange the order of operations at moments of symmetry during query evaluation. The flexibility and adaptivity of eddies lead to query performance that compares (or exceeds) that of static optimizers. </div>
  <div class="block">S1: Eddies provide benefits to join operations, which is an especially expensive part of query execution. The authors identified join algorithms that lent well to eddies due to many moments of symmetry, such as ripple join.
S2: It was cool that even naive eddies perform well under operations with similar selectivities, simply because of the "back-pressure" that naturally develops. 
S3: I appreciated the boldness of doing away with static optimizers completely and replacing them with a dynamic approach. Eddies provide a new paradigm for thinking about query optimization in terms of data flow instead of static analysis.</div>
  <div class="block">W1: S3 is also a potential weakness for eddies. They are not really compatible with standard query optimization, so a switch to eddies would only be motivated by performance gains that reach beyond the decades of work to improve standard, static query optimizers.
W2: In some cases, finding the optimal query plan is important--eddies disallow this possibility. For example, shorter queries that are run long and often would benefit greatly from having a pre-determined optimal query plan. </div>
  <div class="block">D1: Adding onto S3, eddies can optimize query execution in some ways that are impractical for standard, static optimizers. One example is selectivities that are not evenly distributed in a dataset: e.g. if an attribute is correlated with another attribute on which the relation is clustered. 
D2: I would expect that eddies perform well with user-defined functions, the selectivities of which are hard to determine ahead of time. It would be an interesting area of further evaluation.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Eddy is a new query processing mechanism that dynamically reorders operators in a query plan while it runs. This paper presents the idea and explores its viability, with the aim of doing away traditional (static) optimizers by providing run-time adaptivity. The authors show that eddies can successfully account for differences in cost among operators as well as differences in selectivity, while also react adaptively over time to changes in performances and data characteristics. However, it is not able to handle selectivity changes in delayed input.</div>
  <div class="block">S1 - Moments of symmetry, where the scheduling dependency between two inputs ends and order of inputs can be changed without disturbing state, sounds really interesting. Being able to switch processes without losing previous work seems to be a valuable concept.

S2 - Ready and Done bits preserve ordering constraints while maximizing opportunities for tuples to follow different possible ordering of operators.</div>
  <div class="block">W1 - Experiments done on relatively simple queries, small set of tuples. Does not show if eddies would be suitable for a large, complex workload.</div>
  <div class="block">D1 - A query that fully takes advantage of the ability to utilize unused resources. The query should have delays that vary a lot across runs.

D2 - A workload that would cripple the eddy engine would be one that is join-heavy and stores all its tuples inside the first join that is executed. This prevents the tuples from being utilized in other possible joins. Another possible property it should have is having behavior that changes during every sliding window so the learning algorithm.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">This paper proposes a query processing mechanism Eddy, which is able to reorder operators dynamically. Eddy takes advantages of moments of symmetry, that is the order of the inputs to the join can often be changed without modifying any state in the join, to do reordering. The result is that Eddy could performance well in dynamic environments except one scenario that considered delayed delivery.</div>
  <div class="block">S1
The paper spent pages explaining synchronization barrier and moments of symmetry and how operators could be reordered based on that, which is helpful to understand the background the Eddy depends on.
S2
The way that this paper conducts experiments is very rigorous. The overall idea is control variable. For example, in selection, it considered control selectivity and delay units respectively and analyzed both naive and fast Eddy.</div>
  <div class="block">W1
I think the lottery scheduling algorithm used in fast Eddy is not a fair algorithm. In other real world use of lottery scheduling, each process, or operator is given different initial numbers of tickets according to its frequency. By doing this, each operator could have different chances for being selected, and thus makes scheduler fair. However, in Eddy, its uncleared about the initial scenario. Thus, I am not sure how the fairness is ensured and I believe that's the reason that Eddy performances not so well when the delayed delivery is taken into consideration.

W2
Since both Telegraph and River are about parallel, I cannot see how Eddy is related to, or could be fitted into this, which also described as a chanllege in the conclusion.</div>
  <div class="block">For W1, I think one possible solution is to get Eddy trained (maybe combine with machine learning) so that it could correctly set the initial value for each operator, and thus be able to favor the correct join, like given less tickets to RS at the first time.</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">Static, ahead-of-time optimization of queries is both too complicated and not good enough at optimizing. Instead, you should adaptively optimize your queries while you are executing them. We can do this with eddies, which take any query subtree and routes tuples to different parts of the tree one-at-a-time.</div>
  <div class="block">S1. The idea of using the backpressure behavior of River's queues as a way to implicitly estimate the cost of an operator is pretty nice (and seem to work quite well with their experiments).

S2. In the experimentation, I liked how they gave themselves precise control of the query characteristics (e.g. the selectivity of different operators), which let them examine a variety of different behaviors. It was a very good way to really demonstrate the advantages of adaptive query processing.</div>
  <div class="block">W1. One thing that the paper didn't benchmark is the overhead of the eddy implementation (in their benchmarks, they compare implement the static query plan using a "fixed" eddy). Making routing decisions on a per-tuple basis sounds like it could have substantial overhead -- getting a good sense of how much computation to spend on routing versus just executing the query would've been nice.</div>
  <div class="block">D1. The system they describe pretty heavily uses River's parallel processing, which lets it use backpressure as a way to implicitly estimate operator costs (and this presumably makes the eddy implementation almost free, since the tuple routing is parallelized with the actual tuple processing). What happens when these aren't parallelizable? What kind of adaptive cost estimation works best?</div>

  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block">The paper talks about Eddy, a query processing engine that can continuously reorder the query plan as it runs. The hypothesis is that as the large shared nothing databases developed, the traditional static query may not be able to optimize the query plan efficiently because the assumptions are made before the query executed. Eddies are proved to be better than traditional approach when combined with JOINs, but still needs improvement when dealing with delayed queries.</div>
  <div class="block">S1: The experiments shows that Eddies can be applied to SELECT and different JOINs, and especially an impressive result in the 3-table equijoin case. It is convincing to apply Eddies in current systems to make great improvements.  
S2: The Lottery Scheduling scheme is interesting. To prevent cases when unequally distribution of tickets can affect past and present lottery, the paper introduces a window scheme that uses two counts to keep track of the number of banked tickets and the efficiency in each window time. The mechanism is simple, but is effective to maintain the efficiency of lottery in each window time.</div>
  <div class="block">W1: As paper stated, the Eddies still poor performance when the inputs are delayed, which will cause performance issues during a multithreading case. </div>
  <div class="block">D1: Based on the experiments described in the paper, the Naive Eddy is improved to be able to adapt variable selectivities by introducing the Lottery Scheduling Policy. The paper points out that the Lottery Scheduling policy has issues in cases when the variable selectivity does not correlate to the number of tickets it gets. In addition, there is no proof of whether the lottery scheme converge to optimal execution in static cases. The paper was issued in the 90s when machine learning did not attract too many attentions. Is there a more accurate way to predict the selectivity of each variable or correlate it with more than the count of tickets? I am wondering if there were other papers that improved the Lottery scheme or enabled a different scheme similar to Eddies.</div>

  </p>
<hr />
