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


## Comments on random C-store Reviews

Weak points are those that tangibly weaken the hypothesis/argument in the paper.  This is why your summary is important.  It serves to show that you understand what the paper's contributions are!

The following summary lists a number of statements about the C-store system, and a hypothesis is lurking in there.  But is hidden from view.  In addition, it uses imprecise terms such as "for certain queries" that signal a lack of attention to detail.  

        Database systems that store columns (attributes) contiguously
        rather than rows (tuples) can have significant performance
        benefits in certain types of workloads, e.g. read-heavy.
        Some other features that C-Store allows include data
        compression, improvements to read-only transactions, and
        bitmap indices. By avoiding reads of unused columns, using
        better indexing and better compression, column stores perform
        much better than standard DBMSs for certain queries.

The following is a straightforward, clear summary where the hypothesis is in the first sentence.  The only change I might make is to also say "The paper also illustrates a scheme to support for writes and snapshot transactions without terrible loss in performance."   This acknowledges the support for modifications, but states that I don't believe it is the primary contribution.  

        C-Store is a framework for a read-optimized database, the
        basis for which is reading in data by column rather than
        by row, allowing for compression optimization and reading
        in less extraneous data. It has two stores, one which is
        writable and one which is read-optimized, which are connected
        by a tuple-mover. Read-only transactions use snapshot
        isolation of historical data.


#### Weaknesses

Given the above summary of the contributions (hypotheses), we can use it to guide the strengths and weaknesses of the paper.  Did they achieve their claims?  Does the evidence strongly support it?  In what ways?

Consider the following weaknesses in a review

        W1: The paper admits that "There is a tension between providing
        updates and optimizing data structures for reading." Because the
        system is read-optimized, the efficiency of writes suffers.

        W2: The separation of data into two components with different
        behaviors--Write Store and Read Store--complicates the design and
        makes certain problems (such as snapshot isolation and recovery)
        more complex.

        W3: C-Store uses strict 2PL for read-write transactions, which
        points to low, possibly unacceptable, performance in write-heavy
        environments.

* W1 is an easy complaint to make, since the paper alread admits that this weakness exists, and doesn't claim to innovate in this direction.  
* W2 is sort of fair.  I would go a bit deeper and think "is there an alternative sensible way to support writes?".  Did they miss an alternative opportunity, or miss related work that could have addressed this problem?  The paper even acknowledges that having two completely separate systems would be messy, and structure the WS to mirror the RS.
* W3 is also an easy complaint to make.  See 2PL and distributed transactions, and complain it is slow.  The real question is: does it cripple the primary argument?

Consider whether a weakness ultimately matters for the use cases.  In a column store, they expect that the workload is read mostly, and if the read performance is good enough, users may simply adopt a bulk loading process rather than individual insert transactions.  Thus, are some weaknesses truly weaknesses?


#### Details

Let's now look at a few detailed comments.

This one is great, because it takes a stand about whether something is a good idea or not, with respect to the contributions.  It gets straight to the point.

          D2: In addition, what I find interesting about the design
          is storage of overlapping projections, rather than the whole
          table. This replication allows for multiple ordering of
          columns, so that different queries could be optimized to
          use the most suitable sort order. Another idea that helps
          with performance is snapshot isolation. This rids C-Store
          of the trouble of using conventional locking, which may
          lead to substantial lock contention. Instead, it optimizes
          read performance by allowing read-only transactions to
          access the database as of some time in the recent past
          guaranteed to have no uncommitted transaction.


The following comment gets to the heart of why the experiments are lacking.  The paper shows great
RS-only performance -- and thus the potential of this approach -- but who knows whether the tuple mover
will be a bottleneck.  A caveat is that back-of-the-envelope suggests that it shouldn't.  

          D4: Another uncertainty of the paper comes with the performance
          evaluations. First, experiments have not been run on WS and tuple
          mover. Thus, the tests are limited to read-only queries and we
          have no clue how the addition of insertions and updates could
          affect the performance of the system. Although evaluation show
          significant improvement, the incorporation of OLTP transactions
          could result in high cost, probably due to the tuple mover.

Finally, this review looked up related work/references to get to the bottom of things.  Good reviews will independently look through the literature to see if potential connections or evidence is missing.  (it turns out that there is a followup paper by Abadi et al that addresses the claims in Halverson et al)

          Halverson et al. [A Comparison of C-Store and Row-Store
          in a Common Framework] optimized the row-store for a
          read-mostly query workload using “super tuple” and “column
          abstraction”. They performed an “apples-to-apples”
          comparison with C-Store in a common framework and showed
          that the optimized row store actually provides better
          disk storage savings, reduced sequential scan times and
          lower additional CPU overheads.


#### Closing thoughts

* The paper is pretty sloppy and short on the experiments.  That is certainly a weak point.  It is worth gauging whether that deficiency makes or breaks the paper: have they proven what they sought out to prove?
* Finally, are there opportunities that the authors may have missed, that would be great to include?  Put those in the detailed comments!






# List of Reviews

<p class="review">
  <h3>-- </h3>

  <div class="block">The main hypothesis is that by separating updates and reads in a system, it is possible to highly optimize a column store read portion for fast adhoc analysis for large distributed storage. While the ideas are great, the evaluation is a little limited, done on a fixed sized dataset on a limited amount of queries. </div>
  <div class="block">S1- Great idea to take advantage of replicas for different projections of same data
S2- Great idea to separate update and reads</div>
  <div class="block">W1 - Weak evaluation
W2 - Questionable decision for snapshots</div>
  <div class="block">C-Store: A column-oriented DBSM is published in 2005, a time when data was beginning to get larger, storage cheaper and CPUs faster. Relational databases have become the established method of data storage, but traditional implementations do not meet the need of large data warehouses due to relatively slow read for ad-hoc queries. While column store databases existed, as well as data compression algorithm and distributed protocols, C-Store claims to be the first to combine these together to create a DBSM that has fast reads and writes, but at the cost of the timeliness of updates. The main hypothesis is that by separating the system responsible for reads and writes, taking advantage of the fact that physical storage in duplications need not be the same, it is possible to have both faster reads and writes.

The authors identified the presence of a conflict between fast reads and writes – sorting enables fast retrieval but drastically slows down inserts. To enable both, the authors divided the database physical storage design into two part, a write optimized store and read optimized store, with a tuple mover to move updates from the write to read store. This, combined with a desire to reduce locks on the read store, lead the authors to decrease the timeliness of updates to the read store by imposing the requirement that reads are done on snapshots, resulting in new updates not being reflected. Dividing the database to optimize for reads (dense compression of information, b tree on columns) and writes with storage keys seems like a good idea. From today’s standards, the delay is not ideal, but it is progress from updates that require the system to be taken offline before updates. 

To evaluate the system, the paper compares C-Store with commercial column and row store databases over 7 queries, showing orders of magnitudes improvements over both. This however is hardly an exhaustive test, and does not show how well it scales, which defeats the point a little since the goal was for good performance with large datasets. 
</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">This paper by Michael Stonebraker presents the design of a read-optimized relational DBMS. In a traditional database, the data stores in rows and their native data format and the databases are write-optimized. However, many databases like e customer relationship need a lot of ad-hoc queries. Also, the CPU is getting much faster than disk bandwidth. We can trade CPU cycle for disk bandwidth. Due to these, this paper presents a read-optimized database which stored in the column.</div>
  <div class="block">For this paper, the main contribution is the column based storage. </div>
  <div class="block">The drawback for the design is the reconstruction cost and introducing additional join indexes. The drawback of the paper is the incompleteness of the work. </div>
  <div class="block">This paper by Michael Stonebraker presents the design of a read-optimized relational DBMS. In a traditional database, the data stores in rows and their native data format and the databases are write-optimized. However, many databases like e customer relationship need a lot of ad-hoc queries. Also, the CPU is getting much faster than disk bandwidth. We can trade CPU cycle for disk bandwidth. Due to these, this paper presents a read-optimized database which stored in the column.
The C-Store system design contains three parts: WS, RS, and Tuple Mover. The column store can bring many advantages that it can process the queries only reading just a few related columns and compression technique can be applied since the format of a column is uniform. The WS and RS are both column-oriented. The tuple moves tuple from WS to RS in bulk by an efficient method of merging ordered WS data objects with larger RS blocks. In RS,  the author introduces projection. A column may be in many projections. So it has K -safety property. C-Store avoids conventional locking by providing snapshot isolation. The update is implemented by inserting and deleting. There are many timestamps: HWM for epochs, LWM for tuple mover to help control the version and recovery for the database system. This paper also introduced query operators and query optimization.
In the evaluation part, this paper compares C-Store with the commercial row-store database and commercial column-store database. The C-Store is not complete, and they only have a storage engine and executor for RS,  but the results show that C-Store occupies less space and is much faster. The result meets expectations. Space is less mainly because the compression techniques and the speed is faster mainly because of the column representation. 
For this paper, the main contribution is the column based storage. The drawback for the design is the reconstruction cost and introducing additional join indexes. The drawback of the paper is the incompleteness of the work. For the experiment, the result is reasonable, but the implementation for column stored commercial database is not clear from the paper.
</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">C-Store system is more efficient than other commercial products, which are row stored when querying large amount of data, and also could address the tension between updates and optimization. In terms of querying performance, C-Store does win, but since they did not really implement a tuple mover and WS, whether the could demonstrate the second hypothesis could be a question.</div>
  <div class="block">S1
The assumption that CPU is faster than disk is very important.
S1
For RS's encoding schemes, they could cover all the value cases, which could convince me that for every column, it could be fitted into one encoding scheme.
S2
The data model they built (projections) could cover all the tuples in the origin data, and it gives enough evidence to prove that the storage is greatly less than other products.</div>
  <div class="block">W1
They did not get WS and tuple mover running, so they cannot demonstrate they could balance updates and optimizations with data, also cannot be compared with other products in real-world use.
W2
What if the tuple mover find many "worthy" segment pairs at the time? And how did they define "worthy"?</div>
  <div class="block">D1
For the introduction of segments, I want to ask for clarification about "horizontally" because I get confused about segments and Sid, I think it is just like one row, thus make me unable to fully understand the SK and join indices.
D2
The idea of LWM and HWM could be helpful to fix a potential problem: since the read-only queries only run in the history mode, if a most recent transaction is related to the query, the query would not return the correct result. Also, I think if they gave their definition of "correctness", their work could be better strengthened because there is no need to give LWM and HWM if the correctness is defined in another way.
D3
For S2, I think they did convince me that by storing only projections could save the resource as well as cut disk I/O, because DB will not read large amount of repeated data.
D4
For W2, I think tuple mover is very interesting because I think it is like an on-command operating . I think it could work when the number of "worthy" segment pairs is relatively small, but if the number is great, I think it would be better to rebuild the index rather than conducting MOP.</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">The paper proposes that row-store models are not ideal for read-heavy database applications. It proposes an alternative column-store data model it claims optimize both space usage and read speed. They then implement a rudimentary test of their hypothesis and benchmark it against existing models.</div>
  <div class="block">S1: this paper is deeply rooted in contemporary changes in computer hardware. One of the driving motivations for exploring C-Store’s compacted data model  is that “CPUs are getting faster at a much greater rate than disk bandwidth is increasing” and that therefore “it makes sense to trade CPU cycles…for disk bandwidth” (553). The paper also notes that “‘grid’ computers will be the cheapest hardware architecture for computing and storage intensive applications such as DBMSs” and that “grid computers in the near future may have tens to hundreds of nodes, and any new system should be architected for grids of this size” (554). The prevalence of grid computing motivates the paper to explore its highly distributed, high-redundancy approach. 

S2: The data warehouse and library card examples seem like believable and common use cases - so it seems like this idea has its applications.</div>
  <div class="block">W1: The epoch model and the fact that it handles updates as delete/inserts in particular seems like it could cause storage bloating if improperly applied to a system that handles a lot of updates to its data. Thus, even if speed performance of writes is irrelevant, the space occupied by frequently-updated data could quickly spin out of control.

W2: The diagram on page 554 didn’t shed any new light on what was being describe, so perhaps it wasn’t the most efficient use of space in this paper.

W3: The evaluation is highly theoretical; the authors note that the prototype is not yet fleshed out enough to do extensive testing and only perform benchmarking on read-only queries. While read-only queries are the primary focus of this DBMS, and while the DBMS appears to perform well on these benchmarks, lack of a prototype means that we don’t have as much insight as we might like into how this system performs under less-than-optimal conditions - does performance degrade quickly or slowly as we stray from the ideal?</div>
  <div class="block">D1: This paper seeks to sketch out the design for a read-optimized relational DBMS. The paper notes that row-store DBMSs are the most prevalently studied and implemented type of database; however, it notes that these types of databases are optimized for reads rather than writes and that they include a certain amount of padding to enable their row-store data model, which means that row-store databases are not ideal for read-heavy applications such as data warehouses.

D2: The paper proposes a column-store model, which they claim can eliminate much of the padding allocated in the row-store model by coding data elements into more compact forms and by “densepacking” the values in storage. Additionally, C-Store will store data in such a way that read operations will be significantly faster, but write operations will not be optimized.

D3: The authors perform some tests of their hypothesis on a rudimentary prototype. This prototype, however, is only able to perform read-only queries. The authors benchmark their prototype against a commercial row-store DBMS and against a commercial column-store DBMS, and their prototype appears to outperform both by a substantial margin in terms of both storage space and speed. These results appear to validate the authors’ claim that under particular circumstances, a read-optimized, non-write-optimized database may be significantly more efficient.

D4: It should be noted that because these design decisions are rooted in the authors’ assessments of hardware trends, the efficacy of their design decisions is tied to the accuracy of their predictions for the future of hardware - this approach may not always be the best one if the trend in question is volatile or difficult to assess.</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">This paper by Stonebraker et al. proposed a design of a read-optimized relational DBMS C-Store which is oriented toward ad-hoc querying of large amounts of data. It makes the contribution of designing a novel column-oriented architecture to improve reads efficiency since it avoids reading irrelevant attributes and allows for better data compression. Although performance comparison shows significant improvement in read queries, the evaluation has limitations as it does not consider insertion and update queries, and the other commercial row-store and column-store are not optimized in the comparison. </div>
  <div class="block">S1: Novel idea of column store that trades CPU cycles for disk bandwidth. This takes advantages of data compression.
S2: The projection data model allows different sort order for the same column, so different queries can take advantage of different ordering.
S3: Snapshot isolation gets rid the trouble of lock contention in conventional concurrency control with locks.</div>
  <div class="block">W1: System optimized for a "shared nothing" architecture, which might not always be a good assumption.
W2: No test on WS and tuple mover. That is, no evaluation on insertion and updates.
W3: Comparison with row-store and column-store is questionable. Other commercial DBMS is not optimized, so they are not compared under the same framework.</div>
  <div class="block">At the time of the writing, most traditional database systems were row-store, where attributes of a record are store are placed contiguously in storage. These systems are wrote-optimized as a single write simply pushes all fields of a record to disk. This paper by Stonebraker et al. proposed a design of a read-optimized relational DBMS C-Store which is oriented toward ad-hoc querying of large amounts of data. This system, in contrast with the traditional one stores data by column rather than by row. In a read-mostly environment, it should greatly improve efficiency as claimed since it avoids reading irrelevant attributes and allows for better data compression. 

The authors recognized that it is still essential to perform transactional updates even in a read-mostly environment, so C-store is designed to be a hybrid architecture with a large read-optimized component (RS) for handling ad-hoc queries and a write-optimized component (WS) for updates. A tuple mover sits in between to copy data from WS to RS. The paper also details the data model, that is projections, of C-Store and explains how it works with RS and WS. In terms of features key to any DBMS, the paper tries to present how C-Store meets the requirements of concurrency control, recovery, query optimization etc. It ends with a performance evaluation of the system, comparing it to commercial column-store and row-store DBMS.

D1:
This paper indeed makes significant contribution in proposing a new way of designing a DBMS, and the implementation of C-Store sets the ground for many later column-oriented databases. It brings about the novel idea of trading CPU cycles for disk bandwidth, which tackles the bottleneck in read-mostly environment. Based on this idea, the DBMS could take advantage of the coding and compressibility of a column-store and have the query executor operate on the compressed representation. The paper actually provides several encoding schemes for data of different ordering and different proportion of distinct values. Later research [Integrating Compression and Execution in Column-Oriented Database Systems] continues with the idea and was able to extend C-store with a compression sub-system to further improve performance. New compression schemes such as bit-vector encoding and run-length encoding are suggested.

D2: 
In addition, what I find interesting about the design is storage of overlapping projections, rather than the whole table. This replication allows for multiple ordering of columns, so that different queries could be optimized to use the most suitable sort order. Another idea that helps with performance is snapshot isolation. This rids C-Store of the trouble of using conventional locking, which may lead to substantial lock contention. Instead, it optimizes read performance by allowing read-only transactions to access the database as of some time in the recent past guaranteed to have no uncommitted transaction. 

D3:
Despite all the achievements C-store has attained, it still maintains several limitations and uncertainties. First, C-Store is optimized and based-off a “shared nothing” architecture. In a distributed situation, simply sharding the database and have each partition responsible for its data definitely provides better scalability and concurrency. However, there are still cases where “shared nothing” is not the best premise for a data mode. When the data of a smaller scale or the difficulty of finding a smart partition strategy, the higher cost of accessing different partitions (e.g. joins over different partitions) in a “shared nothing” architecture could make the system architect to choose a “shared disk” or a hybrid design.

D4:
Another uncertainty of the paper comes with the performance evaluations. First, experiments have not been run on WS and tuple mover. Thus, the tests are limited to read-only queries and we have no clue how the addition of insertions and updates could affect the performance of the system. Although evaluation show significant improvement, the incorporation of OLTP transactions could result in high cost, probably due to the tuple mover. Secondly, even read-only queries are the only concern, questions are raised by multiple researchers claiming that C-Store outperforms column-store and row-store because they are tested in different frameworks, because the other two are not optimized. Halverson et al. [A Comparison of C-Store and Row-Store in a Common Framework] optimized the row-store for a read-mostly query workload using “super tuple” and “column abstraction”. They performed an “apples-to-apples” comparison with C-Store in a common framework and showed that the optimized row store actually provides better disk storage savings, reduced sequential scan times and lower additional CPU overheads. Thus, it remains a question whether C-Store out performs the other DBMS architectures.</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">The paper designed a unique read-optimized DBMS sysetem. Instead of storaging data by row as most relational DBMS, the system storage of data by column. In order to utilized CPU cycles to save disk bandwidth, the system carefully compresses objects into storage including main memory during query processing. It also has a non-traditional implementation of transactions which “includes high availability and snapshot isolation for read-only transactions”.</div>
  <div class="block">S1: C-store is a read-optimized DBMS, which is suitable for read-mostly applications such as CRM and electronic library systems. The feature of these system is “periodically a bulk load of new data is performed, followed by a relatively long period of ad-hoc queries”.
S2: Comparing with the warehouses which maintain two copies of data, C-store doesn’t store multiple objects in the same way. C-Store allows redundant objects to be stored in different sort orders, providing higher retrieval performance in addition to high availability. 
S3: C-Store tried to solve the dilemma of providing updates and optimizing data structures for reading. It has two components Writeable Store(WS) and Read-optimized Store(RS). WS supports high performance inserts and updates and RS supports large amounts of data storage. </div>
  <div class="block">W1: C-store only supports running read-only queries in historical mode, which require a careful implementation of snapshot isolation. Otherwise the system would present elements that are not existed in certain period of time. 
W2: The article only has an early implementation of WS component, so the performance analysis is limited to query. The improvements in insert or update performance is still not proved.</div>
  <div class="block">D1: In order to maintain the HWM, C-store define a timestamp authority (TA) to allocate timestamps to other sites. TA periodically move the system forward one epoch while it receives all epoch complete message from other sites. It also use some reliable data transmission protocal like TCP to reuse timestamps. I think the article should get more into the detail about how they “wrap” the timestamps.</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">The author hypothesised that the column-oriented DBMS would outperform the traditional commercial DBMS in the aspects of performance and storage by a complete overlapping projection storage and a separated implementation of RS and WS. The results prove that extensive use of bitmap indexes and specialized compression did improve on read-only query. However, the possibility to maintain an efficient tuple mover and WS remains doubtful. </div>
  <div class="block">S1: Very efficient compression mechanism: compression specialized for the four different types of columns as well as the extensive use of bitmaps and densepacking. These contributes to the performance benefits against the commercial DBMS.
S2: The idea of snapshot isolation: This resolves the lock problems in a different ways. However, this introduces another level of complexity.</div>
  <div class="block">W1: The complexity in the implementation of WS and tuple mover. I think it is very ambiguous about what a worthy segment pair is. Essentially checking over all (WS, RS) pair could be expensive already. 
W2: The lack of experiments. There are several aspects in the paper that wasn't illustrated clearly in the experiments. For example, how C-store ensures K-safety and how tuple mover checks for updates.</div>
  <div class="block">D1. I think this paper really explains how a column--oriented DBMS could potentially benefits from specialized compression and optimization and does a good job in proving such statements.  It also convinces me the importance of the extensive use of bitmaps and densepacking.
D2. I think many implementation in C-store are still theoretical or inefficient. Such as the concurrency locking system and the recovery of WS, which becomes much more complicated due to the complex structure of the frameworks. 
D3. I think the work could be strengthened by completing a slightly larger portion of the DBMS since WS and the tuple-mover are also essential in the implementation of C-store. With a single experiment with simple query, it is unclear whether C-store would perform on real-world cases. </div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">C-Store is a framework for a read-optimized database, the basis for which is reading in data by column rather than by row, allowing for compression optimization and reading in less extraneous data.  It has two stores, one which is writable and one which is read-optimized, which are connected by a tuple-mover. Read-only transactions use snapshot isolation of historical data. </div>
  <div class="block">S1: Column-store can increase query performance by reducing scanning of unwanted rows; only load relevant attributes 

S2: Has writable store and read-optimized store

S3: compression techniques can be used when loading columns because they are all the same type (C-Store has a custom optimizer to demonstrate this)</div>
  <div class="block">W1: Needs a tuple-mover between writable and read-optimized store 
W2: read-only queries have to be done on historical data within this framework 
W3: updates-in-place are not possible; instead must turn into two operations: insert and then delete
</div>
  <div class="block">D1: Since the majority of database interactions are reads, it makes a lot of sense to optimize for reads. This understanding of user behavior is a good opportunity for major optimization.

D2: Snapshot isolation in C-Store: read-only queries work with coarse-grained historical intervals of several seconds where there are guaranteed to be no uncommitted transactions.</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">The paper on C-Store by Stonebraker et al presents the design of C-store, a departure from the architecture of current database management systems. The authors argue that a column store architecture, in which values for each column are stored contiguously, has a huge performance advantage over row store architectures, especially for read-mostly applications. As such, C-Store is aimed at the "read-mostly" market in that it optimizes for reads while still providing functionality for writes. Based on the performance of the early implementation, C-Store seems to be a promising alternative to some existing commercial products.</div>
  <div class="block">S1 - Hybrid architecture with separate components for inserts and updates (Writeable Store) and query performance (Read-optimized Store)
S2 - Providing snapshot isolation to providing mechanisms for avoiding deadlock</div>
  <div class="block">W1 - No description of the two systems used to measure performance against C-store
W2 - The scenario for the Type 4 encoding scheme (foreign-order, many distinct values) is essentially not solved
W3 - Will need multiple complicated join indexes for tables with many attributes</div>
  <div class="block">D1 - I would like clarification on how the join index might work for a complicated projection (multiple attributes), especially when the data for one attribute does not correspond neatly to the same amount of rows as the first table. 
D2 - Similarly to D1, it's not clear how the join indexes take advantage of the sort order of a projection if two tables don't have the same amount of rows. Example 1 in pg. 556 is also confusing because it doesn't seem like EMP2 has an attribute that maps to a primary key of another table (surely age isn't a primary key?). Also, what if we try to join two projects where the sort orders conflict, which is the case with EMP1 and EMP2?
D3 - Columns in the RS are compressed using one of four encodings, which depends on its ordering. The authors describe the type 4 encoding scheme as "foreign-order, with many distinct values," but neglects to suggest a valid compression technique if that scenario arises. I wonder if the performance of the early implementation of C-store detailed in part 9 of the paper takes into account this particular encoding scheme. </div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">Database systems that store columns (attributes) contiguously rather than rows (tuples) can have significant performance benefits in certain types of workloads, e.g. read-heavy. Some other features that C-Store allows include data compression, improvements to read-only transactions, and bitmap indices. By avoiding reads of unused columns, using better indexing and better compression, column stores perform much better than standard DBMSs for certain queries.</div>
  <div class="block">S1: Great in read-mostly environments (e.g. data analysis) where often we do not need the entire row of each tuple for queries. 
S2: Multiple overlapping projections, i.e. the same column(s) but sorted in different ways, allows for the optimizer to choose the best projection for processing each query. This is kind of like having multiple indexes, but not necessarily as large. 
S3: Better compression and no word padding can allow for more compact data.
S4: Snapshot isolation allows for queries to be run at varying timestamps. </div>
  <div class="block">W1: The paper admits that "There is a tension between providing updates and
optimizing data structures for reading." Because the system is read-optimized, the efficiency of writes suffers. 
W2: The separation of data into two components with different behaviors--Write Store and Read Store--complicates the design and makes certain problems (such as snapshot isolation and recovery) more complex. 
W3: C-Store uses strict 2PL for read-write transactions, which points to low, possibly unacceptable, performance in write-heavy environments. </div>
  <div class="block">Strengths S1 and S2 are innovative and convincingly effective. They were the most compelling features of C-Store, although it must be said that they can only be utilized in certain workloads. The paper provides examples of queries that are drastically more efficient in C-Store than standard commercial products. However, the paper made a less convincing argument for S3, because it did not go into too much detail about how the data was compressed in C-Store, and why similar techniques could not be used in row-stores. Some numbers were given in terms of net impact on storage (2.5 times more compact), but not much explanation was given to what "superior compression" techniques were used. A question I had was whether omitting padding is something that can be done with row stores--is it a unique feature of column stores, or is it a separate feature that can be universally enjoyed?
W1 seems to be a major weakness of C-Store, even making it unsuitable in environments with mostly writes and few reads (e.g. logging). The authors did try to mitigate the effects by processing updates in batches and keeping a writable store buffer. Updates are inevitably implemented as a read-and-delete, which seems like a lot of moving around of entries in memory using the Tuple Mover. The query performance shown is very impressive, but an evaluation of insert/update performance that is as thorough is missing. Overall, the storage of data appears to be quite complex, as there are multiple locations where entries can be found: Write Store, Read Store, Deleted Record Vector, Insertion Vector.</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">The authors designed a column oriented databases to optimize for a read-heavy workload. Additionally, they used encoding/packing to exploit two trends in computer architecture: faster CPUs (making disk access the performance bottleneck), and cheaper disk space. Because they did not want to sacrifice performance on transactional updates, they proposed a hybrid architecture with a smaller writeable store sitting on top of a read-optimized store.</div>
  <div class="block">S1. eliminating padding and using compression
S2. using column orientation to optimize read-heavy workload (TPC-H benchmark) on single node</div>
  <div class="block">S1. not fully implemented, so hybrid architecture, K-safety not provably successful
S2. want clarification on how query optimizer chooses to perform the Mask, what kind of statistics it would keep on projections</div>
  <div class="block">
D1. That the hybrid architecture works is not fully convincing because they had not yet integrated the writeable store, and had not measured its performance. The overhead of the hybrid architecture is non-trivial, because updates need to be batch processed and deletes in WS need to be marked dirty in RS. Still the performance measurements show that C-store is faster than row store and commercial column store and more space efficient because of compression and absence of padding.
D2. Because C-Store is row oriented, the query optimizer differs from traditional ones. The query plan uses traditional Selinger-style estimation, but the query optimizer instead optimizes for choosing the set of projections to build the query plan on. While the measurements on a simplified version of the TPC-H benchmark were promising, the full benchmark may better show the ability of the query optimizer to successfully choose among projections. One clarification I request is what statistics are kept for optimization.
D3. Another ambitious part of the design is having K-safety, tolerance of failures of K nodes in the grid. The reconstruction of tables relies on join indices to specify sort order and propagate the sort order through a collection of tables. But K-safety in an N-node system seems to only be practical when K &lt;&lt; N, otherwise there are N-choose-K sets whose failure the database must tolerate, which would seem to require that the join indexes would be quite dense, to have many redundant paths. One assumption the authors make is that the common case of crash is that WS is damaged but RS is intact.</div>

  </p>
<hr />

<p class="review">
  <h3>-- </h3>

  <div class="block">The paper illustrated the design of a kind of column store architecture database called C-store which combined several innovative features, this database is oriented majorly for read rather than for write, which is different from most modern commercial database.</div>
  <div class="block">S1: Well organized and have good hierarchy.
S2: Have good and ample examples.
S3: Unique and innovative.</div>
  <div class="block">W1: There is too little figure to illustrate the structure.
W2: Few low level details about the implementation.
W3: Didn’t go into deeper analyzing and explanation after simply display the performance results. </div>
  <div class="block">D1: It is well arranged in a hierarchical way that it first introduced the constitution of C-store model and followed with the discussion of each component parts of C-store such as RS, WS and Tuple Mover in succeeding sections respectively.
D2: In the Performance Comparison part, each system that used for representing the corresponding storage category may not necessarily be representative enough, so the result may not be that compelling.
D3: It may not be a sensible way to just display preliminary experimental result without fully illustration and further discussion, since each query statement has different characteristics, they can make some more explanation on the relationship between the corresponding results and them.</div>

  </p>
<hr />