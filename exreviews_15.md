---
layout: page
---

## Comments

From the comments it sounds like people liked reading the paper!   That's awesome!  The roles are getting much better as well.

<!--
#### Problem

We talk a lot about characterizing the problem in this class.  There are many levels of "the problem" that people identified.


* low level: 
  * map reduce is slow.  let's make it faster
  * data flow graph is fixed
  * RDDs lack optimization -- does it preclude optimization, or jsut didn't get around to it, or it's "impossible"?
* high level
  * the user interface is hampered by the system design

        Users are responsible for the performance of the graph,
        which could be difficult. In some sense, this is analogous
        to requiring users to write their own query optimization,

  * the user interface is better than alternatives
    * mixing programming language and query/dataflow 
    * udfs
    * multiple interfaces to efficient 
-->

#### Applier

This role is tricky -- it's not identifying shortcomings of the paper, but cool things that even the authors didn't even think of!

This is a solid application, and in fact the databricks folks released a graphx engine as well, which also required a custom RDD layout

         would expect that Spark SQL would perform well with graph-related
         data as well: graph algorithms can lend well to parallelization
         and distribution,

This is another characteristic of a useful application.  It will be good to call back to how postgres addressed this and contrast against it!

        Another example is that the user can define complex data types and
        query relationships between those data objects. For example, a user
        defines different geometries and queries their spacial relationships.

The following are arguably really important features of spark/sparksql, since classic database systems don't handle either of these problems well at all.  Sure, databases support json and semi structured data, but loading and defining the schemas is a nightmare.  So, given flexible schemas and great PL integration, what applications _could_ they enable?  What problems would need to be addressed to get there?

        its extensibility makes it especially effective in areas with
        "semi-structured" data that deviates from the standard relational
        model.

        The flexibility of Spark SQL also has to do with its
        integration of relational queries with procedural programming
        in the underlying Spark framework, which allows for expression
        of more complex algorithms that may not be easy to express
        and fine-tune at the database level.

### State of the Art

The state of the art roles did a really great job!  This reviewer contrasts with prior papers we read (mapreduce), and identifies the contrast with hive and pig.   

        Early big data applications such as MapReduce give users a powerful
        but low-level procedural programming interface which makes optimization
        hard

        Hive and Pig relegate procedural code to UDFs and only use relational
        model internally. Spark SQL improves by directly querying data in
        user-defined classes and letting users mix procedural and relational
        APIs in the same languages.

The same reviewer also notes a key aspect of the spark ecosystem and this approach towards data processing: the user-friendly-ness

        Spark SQL mix relational and procedural processing in a more
        user-friendly fashion

Another reviewer did a great comparison with key related systems.  The whole review is worth a read, and I wish we discussed this in class.  Many of the points still go towards user friendliness.


        - Pig Latin, 2008: The thesis of Pig Latin as that most programmers
        who work to analyze big data are used to working with procedural
        programming and find SQL unnatural. Pig Latin aims to hit the middle
        ground between procedural and declarative programming. This seems
        like it does little to roll in existing programmers who work with
        SQL regularly (SQL having been widely used for decades at this
        point)... it provides a single language over the either/or approach
        that SparkSQL takes, meaning that anyone who wants to work with Pig
        has a steeper learning curve.

        Dremel, 2010: Dremel provides columnar storage representation
        on top of MapReduce. Unlike Pig and Hive, however, Dremel
        executes queries natively without translating them into
        MapReduce jobs. Dremel is used via a SQL-like query language,
        as well

        Impala, 2015: A general-purpose query engine built on top
        of Hadoop. Its execution does not run on MapReduce. Not
        very fault-tolerant - if any node fails, the whole query
        fails.



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

  <div class="block"><h5>Problem</h5></div>
  <div class="block">The RDD paper is a reaction to distributed computing frameworks like Mapreduce that do not allow access to intermediate results in memory, solving the issue of expansive in memory backups by limiting updates to transformations of  RDD instead of individual records, which allow intermediates to be determined by its lineage. Spark SQL improves on RDDs by adding the concept of data frames and declarative language over RDDs (spark), which allows for automatic optimizations and convinient access to relational processing. </div>
  <div>Response:</div>
  <p class="block">The issue with systems like Mapreduce is that they define a fixed series of computation that essentially reads from disk, compute intermediate values (key value pairs in this case) in memory, then combine (aka reduce) the intermediate values into the result that is written to disk. The fact that it is fixed means that if the intermediate values needs to be reused (Eg using same intermediate value like distance between points for each iteration of k-mean), intermediate values have to be recomputed. This is an issue because such intermediate values could be expansive to compute, but materializing all intermediate values and allowing access to it is clearly infeasible due to space and reliability constraints. 

 One issue in the RDD paper is that while it allows users to define custom computation graphs, there is no automatic optimization possible as the api is procedure. Users are responsible for the performance of the graph, which could be difficult. In some sense, this is analogous to requiring users to write their own query optimization, which is time consuming especially for repeated tasks. Spark SQL propose combining the RDD spark interface (procedure) with a declarative API so that for functions that fit in the declarative language, the system can perform optimizations. </p>
  <div>Comments:</div>
  <p class="block">The two papers are continuations of each other, and the second provides the concept of Dataframes, which is an alternate api to RDDs, providing similar underlying functionality but with different semantics. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Results</h5></div>
  <div class="block">Spark SQL is a new module in Apache Spark providing rich integration with relational processing which mixes relational and sophisticated analytics. It supports a wide range of features tailored to large-scale data analysis, including semi-structured data, query federation, and data types for machine learning. Spark SQL also has an extensible optimizer called Catalyst that makes it easy to add optimization rules, data sources, and data types by embedding into the Scala programming language.</div>
  <div>Response:</div>
  <p class="block">Spark SQL is a new module in Apache Spark providing rich integration with relational processing which mixes relational and sophisticated analytics. It supports a wide range of features tailored to large-scale data analysis, including semi-structured data, query federation, and data types for machine learning. Spark SQL also has an extensible optimizer called Catalyst that makes it easy to add optimization rules, data sources, and data types by embedding into the Scala programming language.



The expected experiment:
  1) overall performance and scalability comparison against existing systems
     a. different cluster size (for scalability)
     b. different applications (computing intensive, IO intensive, real-world application, ...)
     c. consider both throughput and latency
  2) how optimization strategies influence performance
     a. compare performance with different optimization rules

It evaluated:
   1) performance of Spark SQL against Shark and Impala
      a. using AMPLab big data benchmark which contains four types of queries
      b. 6 machines with 4 cores each
      result: Spark SQL is much faster than Shark and competitive with Impala.
              The largest gap due to Impala has a better optimizer for Join op.
   2) DataFrames v.s. Native Spark Code
      a. comparison based on a distributed aggregation task
      result: Spark SQL is 2x faster than Spark's Scala API and 12x faster than Python API
   3) Pipeline Performance
      Compare the performance of DataFrame+Spark pipeline against separate SQL query + Spark.
Result: the pipeline is 2x faster than separation.

cons: paper doesn't show evaluation for Spark SQL's scalability and latency.
pons: it is good to evaluate the integration of DataFrame API and Spark API.</p>
  <div>Comments:</div>
  <p class="block">Spark sql integrates the procedural analysis and relational computing. It optimizes the query and remains the spark functional models at the same time. It gives the person who do not know spark well but knows relational model a easy start.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">SparkSQL defines a new module for Apache Spark allowing users to perform mixed Scala/relational queries on top of the Spark system, which the authors claim simplifies expressing analytics operations on big data. The authors also create an optimizer generator built on top of the Scala programming language (rather than a domain specific programming language), which they claim lessens the learning curve for extending the optimizer generator.  The resultant module appears to have fairly wide community adoption (a point in favor of its usability) and show moderate improvement over their previous iteration, Shark. </div>
  <div>Response:</div>
  <p class="block">- Pig Latin, 2008: The thesis of Pig Latin as that most programmers who work to analyze big data are used to working with procedural programming and find SQL unnatural. Pig Latin aims to hit the middle ground between procedural and declarative programming. This seems like it does little to roll in existing programmers who work with SQL regularly (SQL having been widely used for decades at this point). However, it appears to be used to the point that theres an Animal Book about it, so its far from obscure. However, it provides a single language over the either/or approach that SparkSQL takes, meaning that anyone who wants to work with Pig has a steeper learning curve.
- Hive, 2010: This appears to be fairly similar to Spark SQL in that it provides a SQL-like declarative interface to big data clusters (in this case, Hadoop), and it appears to be performant on large, real-life use cases (it was in live use at Facebook at the time the paper was written), but it still doesnt provide the hybrid interface - it essentially resembles SQL, which, as the writers of the Pig Latin paper note, as a declarative interface may not appeal to procedural programmers.
- Dremel, 2010: Dremel provides columnar storage representation on top of MapReduce. Unlike Pig and Hive, however, Dremel executes queries natively without translating them into MapReduce jobs. Dremel is used via a SQL-like query language, as well. It is used in the field at Google to handle queries against their massive data stores. 
- Shark, 2013: Shark focuses on fine-grained fault tolerance and failure recovery and also leverages in-memory columnar storage. Shark supports the dual model of using a SQL-like declarative interface alongside Sparks procedural interface. Shark, however, is an earlier iteration of Spark SQL, which lags behind in code generation performance and therefore doesnt stack up well against the C++ and LLVM-based Impala engine. 
- Impala, 2015:  A general-purpose query engine built on top of Hadoop. Its execution does not run on MapReduce. Not very fault-tolerant - if any node fails, the whole query fails. Impala provides impressive speedups against Hive. Generates code in C++ instead of Java. 
- SparkSQL appears to be endorsed by the Apache Spark website as Apache Sparks module for working with structured data, meaning that it has become and remains a standard - this is a mark in favor of its success as an intuitive, usable extension to Spark.</p>
  <div>Comments:</div>
  <p class="block">#NAME?</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Hater</h5></div>
  <div class="block">This paper introduces RDDs, an abstraction framework over distributed memory to empower programmers to have control over memory usage that would enable them to reuse intermediate results across multiple computations by explicitly performing operations such as load a dataset in-memory, control data partitioning, and manipulate them through a predefined set of operators. The key idea behind the paper that enables efficient fault-tolerant support is that they log transformation rather than the actual data.</div>
  <div>Response:</div>
  <p class="block">Although the introduced abstraction framework is powerful, it is lacking in terms of the missing opportunities to optimize over transformations graph, for example, RDDs enable join and cross product transformations, thus, running a certain combination of these transformations may yield suboptimal result as opposed to running the same transformation in a different order.</p>
  <div>Comments:</div>
  <p class="block">-</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">Spark SQL offers a relational-database framework on top of Apache Spark with a focus on large-scale data analysis and extensibility into nontraditional data types.</div>
  <div>Response:</div>
  <p class="block">Spark SQL goes beyond the abstraction layers of RDDs and MapReduce, which allowed for easier distributed computing, but were not as expressively rich as actual database systems. Spark SQL seems easy to apply to problems involving large-scale databases in general, and its extensibility makes it especially effective in areas with "semi-structured" data that deviates from the standard relational model. One example mentioned a few times in the paper is machine learning applications, and I would expect that Spark SQL would perform well with graph-related data as well: graph algorithms can lend well to parallelization and distribution, and also benefit from the extensibility of Spark SQL instead of being expressed/constrained to a strict relational model. The flexibility of Spark SQL also has to do with its integration of relational queries with procedural programming in the underlying Spark framework, which allows for expression of more complex algorithms that may not be easy to express and fine-tune at the database level. </p>
  <div>Comments:</div>
  <p class="block">I found it interesting that Spark SQL extends RDDs to support actual databases, which was a limitation of MapReduce/RDDs: we had discussed last class how the many of the data processing problems that they could address could also be expressed as a subset of relational queries. Spark SQL is a natural extension of the abstraction that Spark offers, making it even easier to manage data in the context of a database without worrying about the complexities of distribution/federation.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Problem</h5></div>
  <div class="block">RDD: RDD is a data abstraction which used in Spark. The properity of RDD is that it is read-only and could only be generated by the presisted records. It works well for parallel and distributed data processing.</div>
  <div>Response:</div>
  <p class="block">RDD:  The main problem needs to be fixed is data reuse. For some systems like MapReduce, they are not efficient because they are repetely writing result into files on disk, thus is not able to be used in data reuse. RDD is a data structure that lets users explicitly persist intermediate results in memory. 

SparkSQL: The main problem is that user needs a declarative language to interact with the system, and also the relational system is not so good to perform data analysis. For some early systems like MapReduce, they require user to write onerous codes like the map and reduce function. So it is a good idea to let user have the declarative language like SQL. Besides, relational systems are weak to express more advanced analysis and graph processing, so they need procedural systems. SparkSQL is like a bridge to connect these two systems.</p>
  <div>Comments:</div>
  <p class="block">-</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">Spark SQL is a module in Apache Spark that attempts to be a best of both worlds between relational and procedural systems. It allows users to use a declarative API to employ traditional relational queries in the same system that they use to run UDFs and call complex analytic libraries. Spark implements this through DataFrames, a table like abstraction embedded in a programming language, and an extensible optimizer called Catalyst.</div>
  <div>Response:</div>
  <p class="block">DataFrames are generalized abstractions of tables that are integrated into a programming language. DataFrames can be essentially the same as a relational table, or constructed against RDDs of objects native to the programming language.
Putting all objects into DataFrames standardizes the operations against them. 

These operations are optimized by the Catalyst optimizer. 
Internally, each RDD already represents a logical plan to compute a dataset. Spark SQL creates a logical data scan operator pointing to the RDD which is then compiled into a physical operator, but Spark does not launch a computation until an output operation. Depending on the output operation, the AST can be  optimized using rules, which are functions from a tree to another tree. Rules are grouped into batches, which are executed until some fixed point, until a minimized tree for that rule is reached. 

Arbitrary user-defined types can also be optimized with Catalyst; to register a type, users provide a mapping from their object to a Catalyst row of built-ins and an inverse mapping back. The API analyzes logical plans eagerly even though query results are computed lazily, so errors can be reported as soon as incorrect code is typed.</p>
  <div>Comments:</div>
  <p class="block">pretty groundbreaking!</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>How</h5></div>
  <div class="block">This paper describes Spark SQL, which is an extension of Spark that allows for easy processing of relational data. One of the disadvantages of Spark is that it requires a decent amount of coding to run properly; Spark SQL rectifies that by introducing a DataFrame object, which allows the user to run standard relational operations, and its own optimizer, Catalyst, which processes these operations. In addition, Spark SQL supports easy methods for users to customize their own extensions to the program.</div>
  <div>Response:</div>
  <p class="block">Spark SQL uses a nested data model based on Hive and supports all major SQL data types along with complex like array and map, etc, and user-defined data types. It ships with a schema inference algorithm for JSON and other semistructured data. The algorithm attempts to infer a static tree structure of STRUCT types which in turn may contain basic types, arrays etc, in one pass over the data. The algorithm starts by finding the most specific Spark SQL type for each record and then merges them using an associative most specific super type function that generalizes the types of each field.</p>
  <div>Comments:</div>
  <p class="block">There are several technical contributions of Spark SQL. First of all, it achieves ETL on various data sources through the DataFrame API which is able to perform relational operations on both external data sources and Sparks build-in RDDs. Second, it supports advanced analytics by using Catalyst Catalyst that utilizes the feature of Scala to add composable rules, control code generation and define extension points. </p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Applier</h5></div>
  <div class="block">The RDD paper introduces its application on the Spark system that provides the abstraction on distributed memory. The Spark SQL paper explains a new functional programming language built on Spark machine. The Spark SQL utilizes the benefits of both relational processing and procedural API, and it also introduces DataFrame API that is compatible with increasing need of complex analysis in ML.</div>
  <div>Response:</div>
  <p class="block">I found the idea of mixing the use of relational language and functional programming is very interesting and applicable in many cases. The relational language is a common query language in most database systems, but the paper claims that it insufficient to use it in big data applications. It makes sense because relational language is not an ideal representation of graph or complex relationships especially when the data size is very large. However, functional programming language and it pattern matching technique are very powerful in building recursion tree (AST) and adding/modifying rules. The Catalyst optimizer can be extended by users to fit their needs such as self-defined types, data source, analyzing tools. From a user point of view, the Spark SQL can be used in doing complicated algorithm ML data query efficiently, such as applying complex cost or convolutional functions on a very large dataset. Another example is that the user can define complex data types and query relationships between those data objects. For example, a user defines different geometries and queries their spacial relationships. These features of Spark SQL demonstrates its application in big data analysis which is useful in OLAP areas.</p>
  <div>Comments:</div>
  <p class="block">I enjoyed reading this paper because it is an exciting data processing model that fits to current large-scale data analysis needs. The experiment results described in paper is not very impressive that the performance of Spark SQL is not like a 20x improvement, but I think the its extensibility and idea of using functional programming designed optimizers are valuable.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>State-of-the-art</h5></div>
  <div class="block">This paper describes the design and implementation of Spark SQl, a new module in Apache Spark that integrates relational processing with Sparks functional programming API. Compared to previous systems, Spark SQL makes two major additions to bridge the gap between relational and procedural programming: a declarative DataFrame API and a highly extensible optimizer Catalyst. The paper also evaluates Spark SQL on SQL query processing performance and Spark program performance and demonstrates that Spark SQL makes it much easier and efficient to write data pipelines that mix relational and procedural processing.</div>
  <div>Response:</div>
  <p class="block">Section 8 of this paper offers a thorough analysis of state-of-the-art and how Spark SQl attempts to advances it from different aspects including programming model, extensible optimizer and complex analytics.

Early big data applications such as MapReduce give users a powerful but low-level procedural programming interface which makes optimization hard. Thus, new systems are proposed to provide a relational interface to the data so that systems could take advantage of declarative queries to do automatic optimizations. Among these systems, Shark is the most similar to Spark SQL and also provides relational queries as well as advanced analytics. However, although it is a relational interface built on Spark, it could only be used to query external data stored in the Hive Catalog and a SQL string has to be constructed in order to call Shark from Spark. Spark SQL advances this state-of-art by creating the concept of DataFrames with which queries can be combined using constructs in host programming language, and it also supports a wider range of data sources. Other systems like Hive and Pig relegate procedural code to UDFs and only use relational model internally. Spark SQL improves by directly querying data in user-defined classes and letting users mix procedural and relational APIs in the same languages. From this aspect, Spark SQL mix relational and procedural processing in a more user-friendly fashion.

The other aspect that Spark SQL improves from the state-of-the-art is the highly extensible optimizer Catalyst based on Scalas functional programming constructs. At the time of writing, there has already been extensible optimizers such as EXODUS and Cascades, but they require a domain-specific language to specify rules. Embedded in the Scala programming language which has handy features such as pattern matching, Catalyst makes it easy to add optimization rules, data sources and data types.</p>
  <div>Comments:</div>
  <p class="block">I find it pretty interesting to see how the database systems are developing by trying to get the best of both worlds. In this paper, Spark SQL, specifically the DataFrame API manages to integrate relational and procedural processing to benefit from complex procedural algorithms and automatic optimizations. In other aspects of DBMS such as data layouts, researchers are developing hybrid systems from row and column-stores to have them optimized for both OLTP and OLAP workload. In terms of algorithms, hybrid hash join is developed that takes advantage of extra main memory. Many more examples to follow.</p>
  </p>
<hr />

<p class="review">
  <h3>---</h3>

  <div class="block"><h5>Future</h5></div>
  <div class="block">The paper introduces Spark SQL, which is a module in Apache Spark that integrates relational processing and complex analytics libraries. Spark provided a functional programming API, and Spark SQL introduces the DataFrame API. Spark SQL also uses an extensivlbe optimizer called Catalyst, which can be extended with data sources, optimization rules, and data types. Their target workload/application is big data/machine learning.</div>
  <div>Response:</div>
  <p class="block">The Spark SQL module added support for complex analytics features, specifically for big data workloads. The authors see a lot of opportunity for future work in extending these analytics features. For example, Spark SQL can automatically infer a schema from a set of records in JSON format. They authors plan to add inference for CSV and XML, which allows developers to skip some parsing step, and immediately perform queries. Another possibility for future work is MLlib, Spark's machine learning library. MLlib is based on the concept of pipelines for execution and DataFrames for storage. Using DataFrames allows exposing algorithms with a lambda-like UDF, and the authors mentioned exploring APIs to expose pipeline construction in SQL. The authors introduce query federation, which allows queries from multiple (potentially external) sources. To handle the expensiveness of query from multiple sources, Catalyst, pushes down predicates from data sources. They want to add predicate push down for key value stores such as HBase and Cassandra.</p>
  <div>Comments:</div>
  <p class="block">Not as clearly written as the RDD paper I think... I was confused because there was no architecture overview, but I guess it assumes knowledge of spark</p>
  </p>
<hr />

