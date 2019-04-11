---
layout: page
---

# Reviews

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

  <div>Response:</div>
  <p class="block">This paper proposes a new operator the data cube operator, which generalizes the SQL GROUP-BY operator with the goal to facilitate data analysis tasks such as constructing a histogram, roll-up, and drill-down. It introduces a new keyword ALL which represents the global sum of all items.
The CUBE operator introduced is motivated by the challenges faced during data analysis workflow to express tasks such as constructing a histogram using the standard SQL GROUP BY operator. 
The way the cube operator solves that issue is it aggregates over all the combination of the SELECT attributes and then uses the ALL notion for the aggregated columns. 
The author then argues that since CUBE generalizes the GROUP BY operator, the same techniques used to compute them would be applied to the CUBE operator as well, and they go over how the data cube can be computed. They claim that the cube is easy to compute, yet they didn't address other issues such as storage space as the materialized data can be prohibitively large, or how to maintain the data cube if an update occurs to the base data. 

As V. Harinarayan mentions in Implementing Data Cubes Efficiently, many issues with data cubes maps to materialized views. For example, when materializing the whole data cube is not possible given some space requirement, selecting which subset to materialize is similar to selecting which view to materialize. In addition, maintaining data cube which is a similar challenge to the view maintenance problem which S. Geffner et. al address in their paper The Dynamic Data Cube.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The paper addresses the cost of doing aggregation/UDFs over multi-dimensional data using sql. Traditionally, this would require users to perform a group by + aggregation/UDF subquery for each dimension (D) since they are of different scopes, which would end up involving D scans. Order matters too, which makes it even more expansive. Aggregation over computed categories are similarly complicated, for instance requiring the unions of subqueries for each month to compute a histogram for instance. This cost is an issue as OLAP analysis can involves large dimensions (the output of each aggregation/UDF is a dimension). 

State of the art at the time really is the groupby operator and complex queries, or writing custom programs for fast evaluations. 

The main issue in essence is a limitation of representation of classic SQL that results in inefficient expression and computation. The paper introduces a new CUBE operator to the API, which involves a new representation of multidimensional data, which the paper discusses. 

The CUBE operator computes the powerset of all possible aggregations fromed from groupby and aggregation, with the ALL term referring to everything contributing to an aggregation. This vastly simplifies queries involving multiple dimensions (aggregates) so that it can be computed in a single query instead of unions of subqueries. An alternate way to look at it is this: data cubes is all possible combination of dimension values computed in a pass, while the previous approach computes a single slice/ part of a slice each pass. The ROLLUP clause is added so that instead of computing the whole data cube, only the last dimension (aggregate of other aggregates, aka ALL) is computed. In terms of actual computation, the paper suggests reasonable evaluation strategies - eg, minimize data movement, hashing. No actual evaluation is done as the work is more of about the concept than implementation.

Overall, I would say that the idea is sound, though the special treatment of ALL, and the claim of the presence of additional rules not mentioned in the paper suggests perhaps excessive complexity. The feasibility of this API on a large scale is also not proven, since that requires actual empirical evaluation.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">Datacube is used in OLAP database. Data analysis applications typically aggregate data across many dimensions looking for unusual patterns. However, the problem here is that the sql standard does not have the multi-dimensional generalization of aggregate and group by, since the aggregate and group by operator produce only zero-dimensional and one-dimensional answer. There fore, this paper proposes a data cube operator. This operator can generalize the histogram, cross-tabulation, roll-up, drill-down, and sub-total. In the implementation level, to support the data cube operator, they extends the select-group by -having and add decorations.Then, allow decorations to remedy the pre-existing problems. The paper also considers how to easily access the elements of the data cube by making it recursive and allowing aggregates the reference sub-aggregates. Then, the paper show how to compute the data cube under different scenerios like distributive, algebraic and holistic. This part can be viewd as the "how": how data cube is implemented in the system. The future of data cube is to integrate into current database and make the data analysis convenient. The tradeoff may be there are limited type of analysis that data cubes can do and the memory and computation overhead of data cube. I guess these histogram, cross tabulation, roll up, drill-down and sub-total may be sufficient many years ago. But now, it seems far from sufficient for data analysis.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">In a nutshell, this paper proposed a new operator called data cube that unions different types of group-bys and aggregations. The hypothesis is to that by using the multi-dimensional cube and the operator, SQL could be more easy to use for visualizing/extracting data.

Currently, data cube is commonly used in visualizaing geo data like sptial and temporial data in the field of OLAP. By pre-computing and storing the cube in data warehouse, people could visualize and interact with the data efficiently. Since data cubes are often too large to fit into memory and query in real time, data cube decomposition techniques are used to decompose cubes into data tiles. For example, high dimensional cubes could be projected into 2D or 3D dimensional cubes. There are many appliers for data cubes. For example, immens and Nanocubes take advantage of data cube to achieve real-time interaction of large datasets, and for immens, it projected the data cube into only 4D and 3D cubes.

To compute the core of the cube, a 2 to N algorithm is used. An iterator function is applied to every tuple lengths N, and for each element in the tuple, it will decide whether it is ALL or the original value, where ALL is a dummy value. However, this algorithm would be less efficient if it is used for all types of aggregations. To solve this, they categoried aggregate functions into three types: distributed, algebraic and holistic. Except computing super aggregates of holistic functions, the number of calls of the algorithm could be reduced.

In the future, I think there could be more works on the algorithm since it is still exponential. Martin-nevot et. al published C-Idea: A Fast Algorithm for Computing Emerging Closed Datacubes several weeks ago, in the book, they proposed a faster algorithm to compute reduced and lossless representations of the emerging cube by using the concept of cube closure.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">Introduction of common statistical analysis pattern: formulate, extract, visualize, analyze. Explanation of need to reduce dimensions by summarizing along certain dimensions. Sample analysis problem, limitations of analysis via existing SQL constructs (mainly GROUP BY). GROUP BY extensions provided in some databases to overcome these limitations in certain cases. CUBE and ROLLUP operators introduced along with ALL value. Algebraic combinations of CUBE, ROLLUP and GROUP. Syntax proposed for new operators. Implications of addition of ALL value. Alternative to use of ALL value. Decoration columns, their utility in GROUP BY queries and their interaction with ALL. Star and snowflake schema/queries and use in capturing hierarchical attribute dimensions. Proposal for extracting values from a CUBE result. An approach to computing CUBE. Aggregate functions classified as distributed, algebraic or holistic; each types computability via proposed approach discussed. Maintaining CUBE and ROLLUP results as data changes inspired by SQL Server customer behavior.</p>
  <div>Comments:</div>
  <p class="block">The CUBE operator seems very handy for certain data analysis tasks, but would also seem to be on the fuzzy edge of what is broadly useful and obviously belongs in a relational database and what should probably be left to special purpose data analysis tools. Not surprisingly, it only seems to be implemented in commercial databases where they ran out of ways to differentiate themselves and had to start tackling specialized markets. These days one would probably start with Hadoop and Sawzall rather than an RDBMS. With this sort of data processing, the strengths of an RDBMS  transactions, indexes, sophisticated query optimizers  either dont help at all or just get in the way.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The Data Cube paper discusses a cube operator that acts as an N-dimensional generalization of the SQL aggregation functions. This is very novel because it provides a new mental model for how to work with data that takes it from an abstraction to a more concrete representation. It is also novel in comparison to star and snowflake schemas in that it is possible to embed a notion of time or some other linear scale in the data model itself.

In order to accomplish this, the paper describes the different types of aggregate functions (distributive, algebraic, and holistic) and categorizes the standard aggregation functions including MIN, MAX, AVERAGE among them. Arrays or hashing are used to organize the aggregation columns in memory, storing one aggregate in each array or hash entry.
In gaining insights from data, cross-tabulation is usually very important, and data cubes make this kind of multivariate analysis much more conceptually accessible. 
While the data cube model can be a literal cube, the framework extends to N dimensions; the core can be N-dimensional and can produce N-1 dimensional slabs through projections. 

Drawbacks include implementation complexity at scale, including that if the data cube does not fit into memory, array techniques do not work, therefore the cube must be partitioned with a hash function or sorted, which could be expensive. Also, actually materializing the cube is quite expensive. In order to explore the cube and make it actually usable for a dataset of nontrivial size, there should be indexing of some kind within the cube itself, which the paper does not get into much detail about. Additionally, there must be some kind of strategy to materialize intelligently, which the paper also does not really describe.

However, the Data Cube paper does provide a theoretical foundation for further exploration into these matters, which is done in later related works such as Implementing Data Cubes Efficiently, which discusses a non-naive implementation of when to materialize each view in the cube.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">This paper by Jim Gray et al. introduces the design and implementation algorithms of the new Data Cube relation aggregation operator, which generalizes the histogram, cross-tabulation, roll-up, drill-down and sub-total constructs. It goes over the motivation, creation and the syntax for SQL integration of this operator and uses examples to illustrates this usage. It ends the paper with cube computation and techniques for adding aggregation techniques. 
At the time of writing, there was an increasing demand for OLAP queries where maximum information needs to be extracted from data and analyzed across multiple dimensions. The paper is motivated by the inadequacy of GROUPBY in a series of aggregations such as roll-ups and drill-downs because aggregation is expected at different levels so that a lot of unions over GROUPBY is required. Thus, the authors proposed a generalized aggregation operator Data Cube, or cube, which generalizes simple aggregation to N-dimensions and builds a table with all aggregate values for different grouping attributes. The introduction of this new operator is the papers major contribution. By introducing the ALL value, they are able to store aggregations at different dimensions. The authors also admit the complexities of ALL value introduction and gives techniques for optimizing cube computations. 
One strength that makes this paper very readable is the example queries and sample outputs the paper provides. By actually listing out the cube output and always providing sample tables for the concepts such as roll-ups and cross-tabs, I clearly understand what it is referring to and why there is a great need for the cube operator.
One weakness I find with the paper is that it does not discuss the impact of null-values in the tables clearly. While the paper admits the presence of null-values, it is not taken into account when calculating the cardinality of CUBE and GROUPBY. Thus, its claim that when Ci is large (tens or hundreds), CUBE will be only a little larger than GROUPBY is questionable and should be made more precise.
Potential improvements of the paper that could make it more suitable for todays context is more discussion of Data Cube in the distributed environment. The paper briefly touches on the case where source data spans many disks or nodes, and proposes using parallelism to aggregate each partition followed by coalescing the aggregates. However, more details could be given today on how we wisely partition and store the data, how to fit this operator in a larger distributed workflow and how to optimize it. In addition, motivated by the survey paper on materialized view, is it possible to materialize and maintain some cubes locally for efficient processing? What aggregates should it materialize and how should the system maintain it?</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The CUBE operator allows for N-dimensional generalization of SQL aggregate functions and the GROUP BY operator to allow for easier expression of histogram, roll-up, drill-down and cross-tab queries, which were daunting in conventional SQL. The purpose of the CUBE operator was to make data visualization and report writing easier.

The authors built CUBE by extending a few aspects of SQL. They (1) Extended SQL's SELECT-GROUP-BY-HAVING syntax, (2) Allowed decorations, which are columns that do not appear in GROUP BY (so neither an aggregation column or an aggregate), as long as they are functionally dependent on the aggregation columns, and (3) created the ALL() function that generates the set over which the aggregate was computed (e.g. Model.ALL = ALL(Model = {Chevy, Ford})). CUBE has become widely used in data mining and multidimensional information systems, which suggests that CUBE filled a crucial gap in the language. 

The main weakness I found from the paper is that the ALL token greatly complicates existing SQL code (in fact, the authors suggest that it may be simpler not to implement the ALL function at all). It is also not clear if there is a way to efficiently calculate the power set of the aggregation columns, which CUBE relies upon. The section discussing algebraic, holistic and distributive aggregates didn't specify the exact cost of computing the power set.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The Data Cube paper generalizes SQL Aggregation functions to an N-Dimensional "cube", whose points are aggregates along each attribute. Super-aggregates are computed by aggregating over a set of attributes, so that the result is in a lower dimension. Most importantly, the paper provides the semantics for CUBE and ROLLUP operations, and practical ways of computing the cube.

The paper motivates data cube by first examining the ways in which aggregation is used for data analysis or graphical representation. The concrete examples are given in traditional SQL constructs. The authors show that histograms, roll-up totals, and cross tabulations are difficult or not possible using SQL constructs. The CUBE operation is explained very clearly with examples. The authors introduce the ALL value to represent sets over which aggregates were computed. The ALL keyword is like a stand-in for what would normally be a single attribute name, so if a sum was computed over all possible Colors, the Color attribute would have value ALL. The row with (ALL, ALL, ...,ALL, f(*) is the total-aggregate, a 1-D point.

The computation methods for the data cube were really interesting. The authors provided a classification of aggregate functions, defined each class in terms of function properties. The properties also led to practical methods for computation. Actually, the Distributive class of functions seems like a traditional accumulator/fold. The algebraic aggregate seems like a map-reduce where there are many mappers (handles) that pass intermediate results to fewer reducers which output lower-dimension data.

The paper is really cool and seems like a very self-contained idea/implementation, so related work and experiments are not discussed and not that necessary.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">This paper introduces the notion of a multi-dimensional aggregation operator, called a "data cube." They implement this as an extension of the GROUP BY operator that computes progressively computes all combinations of super-aggregates among the columns involved in the GROUP BY.

This functionality mainly seems to target data mining and business intelligence operations. 

I really just don't get histogram example - the author's "intuitive" version is hardly any different from from the SQL92 version! I'm not really sure why this was necessary to include in the paper.

I also don't get why decorations are necessary - if the decoration's value is functionally dependent on something in the GROUP BY statement... why not just add it to the group-by statement, since it won't have any effect? It seems that the purpose of introducing this issue is that extra fields in the GROUP BY expression would add additional super-aggregates in the cube, but I would have preferred the authors stated this more explicitly, since I'm not sure it comes across as an inherently necessary feature.

"It is not clear where to draw the line between the reporting/visualization tool and the query tool." I also wonder if this feature, given its complexity, is really necessary to add do SQL or if this is best left to the data visualization space - I feel like there's a certain point where complex aggregations would be much easier to understand in terms of a visualization.

"It is convenient to know when a column value is an aggregate." I would have liked to have seen an example here, since it's not immediately obvious when it would be convenient to do this.

The paper also didn't do much to motivate or evaluate the proposal - is this functionality that business analysts actually want? Would they like it enough to learn it over existing applications such as Excel? I also maybe would have liked to have seen more examples of how queries can be simplified using CUBE (the reduction of multiple unions was one of the strong points of the paper, but I wonder if there are other examples they could have tested)</p>
  <div>Comments:</div>
  <p class="block">This paper wasn't very well-proofread - there were a variety of grammatical errors throughout the paper.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div>Response:</div>
  <p class="block">The problem that this paper wants to address is to generalize the orderby, cross-tabulation, and aggregations into CUBE operator that is more useful in data analyst settings. The CUBE operator reduces the complexity to express global aggregations and fit better with data visualization tools. The main problem with current SQL is the difficulty of constructing histograms, roll-up totals and sub totals for drill-downs and cross tabulation. It is not clear how the state-of-art at tried to generalize those operations, but the Red Brick systems added some other aggregate functions to enhance the groupby mechanism. This includes Rank, N_tile, Ratio_to_total, and running_sum/average. Though these functions extend the usability, it still cannot solve the problems that been said. The CUBE operator is an extension to the groupby function in SQL, so the authors said that the techniques for computing groupby can also be used in CUBE such as using hashing to organize in memory computation and use parallelism to aggregate each partitions. The current algorithm is used to allocate a handle for each cube cell. So whenever a new tuple arrives, one step requires to handle for each cell matched the value in the cube. Thus, the ITER function will be called 2^N times which is very expensive. With the CUBE operator, it will generate one more ALL row for each of the attributes in the select list. The key result is that now users are able to generate the histogram with the enhanced groupby operator and the ROLLUP function can be use a simpler method instead of computing all the cube. The result of the paper sounds not to optimize the query time, but to extend the SQL language to support more functionalities. So a reasonable testing is to compare the query statement of the same results which already has many examples in the paper such as the global aggregate case. The future might be to compute the CUBE operator in a more efficient way. A problem that might occur, even though the possibility is low, is that the data cube might not be able to fit into memory when the result is too large. If that is the case, there must need a technique to make sure if the memory is limited, it still can compute the CUBE operators. Also, the CUBE operator sounds very computational expensive. If the user computes different CUBE operators that generate similar results, it can be a good idea to cache some of the results or find lineage between them without recomputing everything. In the paper, the author mentioned a case when the core of the cube is sparse and suggested to use B-tree to index the data, it is also a future research direction to test how the indexing of CUBE operators improves the performance. The CUBE operators would have broad application in data analyst areas because it provides more data aggregation functions for data mining. The CUBE operators is still an extension on the relational query language. However, some systems such as graph and file databases with non-relational query languages are widely used now. The paper was in the mid 90s, so the idea of CUBE operators may be useful for traditional relation database systems, but its application in other query languages are in doubt.</p>
  <div>Comments:</div>
  <p class="block"></p>
  </p>
<hr />

