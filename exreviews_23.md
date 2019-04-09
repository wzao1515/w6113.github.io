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

  <div class="block"><h5>Applier</h5></div>
  <div class="block">This monograph introduces materialized views. Materialized views are queries whose results are stored and maintained to facilitate access to data in their underlying base tables, And this monograph mainly focuses on three problems 1. view maintaining, 2. using a view, 3. view selection.</div>
  <div>Response:</div>
  <p class="block">The three main problems in this paper are 1. view maintaining, 2. using a view, 3. view selection. The view maintaining is similar to the data stream processing system. Runtime self-maintenance can be used here. And optimizing view maintenance queries can be naturally translated to various streaming join techniques. 

View materialization can also be considered as sharing common subexpressions. And the third part of this paper - how to select view maintenance can also be used as a multi-query optimization technique. These work could help us identify queries whose answers are affected by an event without examining all queries in a scalable continuous query processing scenario.

Also, this problem is related to caching. Caching also has the similar three questions -- how to maintain caching, how to use cache, and which to cache. 

The strategy in section 2.1.1 exploit some technique, or auxiliary information is like lineage. Lineage can be used here to delta update the maintained view. Reversely, view maintenance can also be used to help track lineage.</p>
  <div>Comments:</div>
  <p class="block">A view is like an intermediate representation. It is amazing to find that one problem in the database can connect to so many problems in the system. Essentially, they are all about how to use intermediate information and how to maintain it to be useful.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">This paper presents a survey on materialized views, which are defined as a set of queries whose results are materialized and maintained. There are different motivations to materialize a view, such as access control, logical data independence. The survey focused mainly on non-recursive SQL views and addressed problems such as how to maintain materialized views, how to use them and how to decide which view should be materialized.</div>
  <div>Response:</div>
  <p class="block">
Incremental view maintenance can follow algebraic or non-algebraic approach. The algebraic approach introduced by R. Paige et al. in 1984 is used to define delta tables which encode changes to base data as a pair of tables one of which represents the newly inserted rows and the other the new deleted rows. Using this approach, view maintenance is specified using a collection of change propagation equations. Due to various shortcomings of delta tables, summary-delta has been introduced which encode the net effect of insertions, deletions, and updates in a single table. 
An example of a non-algebraic approach is the counting algorithm developed by Gupta et al. in 1993 where for each materialized row the algorithm keeps track of the count of its derivations as extra information in the view. It is particularly useful for handling projection in relational algebra or duplicate elimination in bag algebra.
Another approach to view materialization takes a different route, instead of materializing each actual result row of the view, it materializes alternative structures or additional information such as ViewCache that is used in the ADMS project which stores pointers to base table rows or entries in other ViewCache corresponding to subexpressions that contributed to the result row.</p>
  <div>Comments:</div>
  <p class="block">-</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">This survey paper covers materialized views and their motivation, implementation and maintenance, and applications. Materialized views trade space for time by persisting (materializing) the results of a query (a view). The survey also covers the challenges of efficiently propagating updates between materialized views and their underlying tables (insertions and deletions as well as schema changes) and efficient timing of these updates.</div>
  <div>Response:</div>
  <p class="block">The paper itself acknowledges and outlines many weaknesses in the general concept of materialized views and presents attempts to remedy them, but certain costs are just inherent in the concept itself.

The obvious drawback of materialized views is that they trade space for speed, no matter what sort of optimizations one applies. With the rise of in-memory database systems, I wonder how well the concept of materialized views would hold up in memory-scarce applications. 

Also an inescapable reality of materialized views is that fact that view maintenance only provides performance improvements when the underlying query is costly (if it is cheap, theres no point in persisting it). View maintenance is generally costly, as well. It seems that materialized views are most appropriate for read-heavy applications or applications that perform bulk updates (such as data warehousing applications) - they are less appropriate for write-heavy applications, as the materialized view will have to be maintained more often. 

The selection of materialized views also poses a problem: DBAs can either hand-tune them, or the system can select the views automatically. Hand-tuning requires man-hours, while automatic selection becomes an optimization/search problem - both expensive solutions that require tradeoffs to perform.</p>
  <div>Comments:</div>
  <p class="block">This was a fairly thorough and clearly written overview of the field of materialized views. This field being mature as it is, it obviously has a great deal of breadth, and that shows in the depth of this paper - the authors generally give only a few details and leave the reader to check out the citations if interested. This being a survey piece, however, that seems appropriate. The citations are incredibly rich and serve as a helpful tool for research in this domain!</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Results</h5></div>
  <div class="block">This paper provided an introduction and reference to the topic of materialized views, which are essentially queries whose results are materialized and maintained to facilitate access to base tables.</div>
  <div>Response:</div>
  <p class="block">Instantiate the performance measure that one wants to optimize as query-processing costs for a given query workload and instantiate the system resources to be constrained as, or view-maintenance costs for a given update workload.</p>
  <div>Comments:</div>
  <p class="block">It would be nice for the framework to also consider scalability to a large number of views and triggers, management of synopsis in support of both query optimization and approximate query answering, unifying materialized views and caching.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">Materialized views is a survey paper that attempts to identify the most salient issues involved in materialized views and past work that address these issues. The main topics of discussion includes knowing when materialized views can speed up a query, how to selection views to materialize and how to maintain views. The survey covers delta tables, weaknesses and inefficiencies of algebric methods, list of improvements, timing, duplications, and search space definitions for materialized view selection. Overall, the survey provides a list of work under each relevant section.</div>
  <div>Response:</div>
  <p class="block">The greatest weakness of the paper is its presentation. The purpose of a survey is to identify the core ideas presented in a field and clearly explain the core contributions in a convincing and progressive manner. The survey instead reads like a fairly disjointed group of subtopics, where a large list of work is provided under each subtopic without explanations of the overall importance of each work, and weak emphasis on the importance of each subtopic.

One example of disjointness is the following: In the introduction, the paper mentions that it would not consider recursive queries for simplicity. Immediately in section 2, one of the examples involve a recursive query, and there were mentions of how to detect irrelevant updates with datalog. The paper should either decide to cover a topic in depth, or not mention it at all.

Further examples of lack of clarity includes not defining the difference between procedure and algebraic maintenance, and why it matters. Why is derivation counting considered procedure? Why is it more efficient? Why are summary tables considered a cross between procedure and algebric? It really isn't convincing to say that something is better or worse than another, especially when another section ends with assertions that everything is subjective, dependent of the database instance (argument for why optimizer should decide). 

More care should also be taken when organizing sections - there should be more explanation and evidence on the different types of updates. Eg analyze how irrelevant updates, self maintenance and runtime self maintenance affect performance. The paper also did not make clear the link between that section and the next, which discuss materializing alternative data structures to tables and auxiliary data. In essence, I would argue that more sections should be organized like the section on timing, with a proper motivation section followed by literature review. There is also no sense in grouping concurrency together with commercial implementations. Commercial implementation really should be in a section on its own, with subsections for techniques mentioned above, or interspaced at the end of each technique section where relevant. In the section for materialized view selection, no explanation is given for why OLAP queries is treated differently from general selection (Eg. why is having a lot of aggregation special?) 

</p>
  <div>Comments:</div>
  <p class="block">Overall, I feel like in its pursuit to cover all grounds and topic, the survey paper loses a lot of cohesion in terms of the topics it covers. The topic of view materialization is definitely very interesting with many nuances and optimzations possible depending on the use case, which could explain a little of the disjointedness. If I already knew of a topic I would like to explore in detail, this would be a useful reference due to the many many papers it cites in each section. It does not however do a good job of motivating, analyzing and summarizing the topic as a whole - not the most helpful paper for someone who does not plan on reading through the 30 pages of reference it cites.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">The paper proposed a method to speed up queries: materializing views. By doing this, results could be derived directly from the materialized views rather than running the query. The major barrier for this is the maintenance of the view because if the database changed (data or schema), the view needs to be changed as well. To address this problem, they introduced algebraic incremental view maintenance, which is to apply some small changes to the view rather than recomputing the view.</div>
  <div>Response:</div>
  <p class="block">The paper chose algebraic approach to maintain the view. One important concept to understand the method is propagation equations. Starting with delta (insertion, deletion) of base tables, we can progressively derive the the query. For example, one equation in selection is select(R difference deltaR) = select(R) difference select(deltaR). This is similar to the reverse of factorization. However, this is not efficient if only this is applied directly because there are other informations that could take advantages on, such as count. For example you want to delete a row from table, it is non-determintistic if you just apply the equation on the row, therefore you have to recomputing the view. On the other hand, with the count, if the count is 1 before deletion, you could just delete the related information in the view. Besides, this could also achieve making views (together with the auxiliary data) self-maintainable. 

Another problem is to select appropriate views to matrialize. One approach is to select manually. However, this is not scalable or trustworthy (could improve the performance). The approach is different in OLAP and common systems since most of OLAP queries involve aggregations and groupings. In the database system, the general approach is to restrict the search space of candidate views to materialize.</p>
  <div>Comments:</div>
  <p class="block">-</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">This survey paper provides an introduction and reference to materialized views, which are queries whose results are stored and maintained in order to facilitate access to data in their underlying base tables. It addresses three fundamental problems: view maintenance, answering queries using views and view selection. For each of these problems, the paper also discusses its connections to other areas of database research and proposes directions for future research.</div>
  <div>Response:</div>
  <p class="block">This survey paper provides an introduction and reference to materialized views. It aims to address the three fundamental problems encountered in its study and applications: (1) how to maintain the materialized views efficiently when the base table changes, (2) how to use materialized views effectively to improve performance and availability (which is discussed in Chapter 3), and (3) how to select which views to materialize wisely. 

These are definitely important problems to solve for adopting materialized view. Concerning problem (1), maintaining view efficiently is important because as base table changes, materialized views become stale and must be updated for further usage. Since naively recomputing the definition query over the new tables is unnecessarily costly given that the changes are often small compared to the entire database, incremental view maintenance saves time and computation power. Example queries are used to demonstrate the advantage of having incremental maintenance. The paper also talks about materialization strategies such as maintaining auxiliary data that may benefit the maintenance. 

Problem (3) selecting views to materialize is important because one cannot materialize all possible views in a real-life system due to the limits of the storage spaces for the views and the system costs. Thus, it is important to specify the constraints of view materialization and select views wisely. The paper discusses scalable automated view selection and survey the view selection algorithms in commercial databases.</p>
  <div>Comments:</div>
  <p class="block">I find this survey comprehensive and organized. I like its direct shout-out of the three problems it wants to address in the abstract and its brief overview of the topic in the introduction. Each chapter is also organized effectively by first discussing algorithms to solve the specific problem, followed by some interesting alternatives to the direct solution to the problem. For view maintenance, the paper considers storing data structures instead of view contents and maintaining auxiliary data. Also, the paper considers relevant issues such as concurrency control and distributed settings to make the argument adaptable to commercial applications.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">The book introduces the materialized views which are used as a pre-computed and cached table to facilitate the querying process. There are a lot of concepts been talked about in this book including how to maintain the updates/deletes of views in algebraic, information, and timing dimensions and how to select a specific view to materialize to speed up queries.</div>
  <div>Response:</div>
  <p class="block">There are a lot of applications of different techniques mentioned in the textbook. The algebraic approach such as bag algebra can be efficiently expressed in SQL database which enables the summary-delta implemented using stored procedures with cursors. For the optimizer, it can also use the algebraic approach to generate the change propagation equation and optimize them in a query to recompute the overall maintenance cost. In an information sense, most of the discussions are about achieving self-maintainable or maintainable with partial information to the view. The book said these problems are hard to verify because testings are actually co-NP-complete. In practice, ADMS uses ViewCache to store the rows that contributed to the view and reuse these information in future queries. Another approach is to use auxiliary data in additional data to achieve self-maintainable. Optimization technique pioneered by Ross et al. can also be applied in this process to further minimize the maintenance cost. The idea of timing technique is to delay the processing of some non-urgent views. An asymmetric batch incremental maintenance technique can be used on JOIN views. There are already many applications of those optimization methods in commercial databases such as Microsoft SQL, Oracle, and IBM DB2, so I think beyond this book those methods can be applied to OLAP, OLTP as well.</p>
  <div>Comments:</div>
  <p class="block">After reading Ive had a general understand of materialized view and how the research are related, but to fully understand those methods it will take a lot of time since there are so many reference papers mentioned in those chapters.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">The paper is a survey of materialized views. The chapters we read discussed solutions for view maintenance and view selection.</div>
  <div>Response:</div>
  <p class="block">The key problems of materialized views are 1) how to answer queries using views, when queries are not written in terms of the materialized views, 2) view selection: how to choose which views to materialize 3) view maintenance: how to maintain materialized views as base tables change. The chapters we read focus on the last two. The authors limit the scope of the survey to nonrecursive SQL views; They exclude data models that require specialized implementations of materialized views, e.g. probabilistic, streaming, spatiotemporal, semistructured, object-oriented data models.

The view maintenance problem requires considering language/data model used to define views as well as the run-time/storage of implementing view maintenance. The language is important because the expressiveness of a language is tied to the ability to do incremental query computation. Furthermore, the support for integrity constraints in a DBMS also plays into the problem, since constraints can be useful information for view maintenance.

In addition to the main problem of view maintenance (across base table changes), there is a problem of view adaption, which is using previous materialized result to compute the result of a different query/view definition. Another problem is updating view definitions so that they remain valid after schema changes. Finally, the authors mention the problem of allowing base tables to be modified as a result of modifying view contents.

The view selection problem is directly related to query processing time, as well as other resource requirements, e.g. storage. The survey considers two scenarios where view selection is useful, OLAP queries and single base table. Improving view selection is considered important by commercial DBMS's, and has been tied in with designing system architectures with automatic tuning.</p>
  <div>Comments:</div>
  <p class="block">N/A</p>
  </p>
<hr />

