---
layout: page
---


# SystemR Retrospective

## Overview

* Super ambitious project
* Lots of systems lessons
  * Rapid prototyping: throw out 1st version.  
    * avoided v2 system trap
  * unified representation of internals and data   
    * (system catalog, views for access control)
  * fast path
  * interpretation vs compilation vs IR
  * failures matter
  * interactions between abstraction layers (convoy)
  * Solving problems with levels of indirection
  * real users --> silences critics
* Notice points where logical and physical representatios are mixed up
* Relatively fixed storage/access mechanisms.  Why? (transactions)
* RSS vs RDS


Background/Setting

* Data is simple, implementations were complicated
  * Most data is records and relationships between records
  * But connections are called different names (links, sets, chains, parents, etc)
  * Show navigational example for "what is the lowest price for bolts?"
* Fundamental issue:
  * query_D(D): query formulation is dependent on database representation.
  * query(D): data independence means query is independent of the representation.

Goals (super ambitious)

* High level DECLARATIVE (nonprocedural) UI to maximize user productivity and data indep
  * note focus on the user!
* support variety of application use cases: programmed xacts (OLTP), adhoc, recurring reports (OLAP)
* Data Independence! support rapidly changing database env where table, indexes, etc can be added/removed
  * dApp/dt << dEnv/dt
  * DSL + compilers
* concurrency control and a notion of integrity (lock-based transactions.  It'll take a while to really define consistency levels!)
* explicity dealing with failures with notion of correct state (HW,OS,SW,xact)
* views as access control and (query) modularity.  An elegant use of data independence
* reasonable performance (notice this is last)

Rapid prototyping via the waterfall approach

* V1:
  * test SQL interface
  * Wizard of OZ: fake it using XRM to see potential performance trade-offs
* V2:
  * full implementation of ambitions
  * user evaluation
  * amazing that V2 actually worked and did'nt become bloated!

## V1 System

SQL

* Single table results, subqueries to filter records wrt other tables
  * essentially defining the execution order to sidestep the hard problem
* The note about user study on usability is actually lame
  * evaluating SQL vs a mathier version called SQUARE (predicate calculus)
  * proves that SQL > SQUARE.  Not that SQL is good
  * See Date's criticisms of SQL: A critique of the SQL Database Language
  * SQL sux, see datalog, predicate calculus, QUEL, etc
* Catalog as relations
  * super elegant!!
  * in later user studies, users point out how much they like this
  * if the DBMS manages data, and the catalog (stats, table, schemas, views) is data, then this is obvious

XRM details.  

* Highlights how system is tied to a single physical implementation 
  * Surprisingly akin to modern column-store designs (except for compressed exec)


        [ pid,tid  | age  | state   ]
                     |        |
            v---------        ----v
           [1                    [CA
            2                     TX  -----> [ tid1, tid10, tid13 ]
            3                     NY]
            10]

  * Data values for each domain (attr) stored in a single place
  * Inversion (inverted index) is cross table
    * tid1, 10, 13 could be from different tables
    * akin to database-wide search.  Not a terrible idea
* Makes reasonable sense for the subset of SQL (single table + subquery filters)
  * Inversions let you fetch TID lists based on attr value 
  * TID intersection to compute conjunctions
  * Late materialization!
  * Wasn't really used until rise of fast OLAP engines
* Optimizer 
  * idea good: use estimation.  Specific estimator very naive
  * focuses on NUMBER of tuples fetched
  * ignores access patterns, locality, etc
  * ignores intermediate records, computation costs, etc
* Cons
  * Bad for SELECT * type queries

Lessons

* Better estimators that take more features into account
  * TID list manipulation
  * Chasing tuple pointers
  * Disk IO
  * Clustering and locality 
* They argue using TID lists is a big drawback
  * it's simply a tradeoff.  Good idea for large scale aggregates
* Bottleneck is CPU.  Rather unexpected, would expect IO.
* User studies convinced them that joins are useful
* Fast Path: Make simple/common queries fast. 


## System R Version 2

Overview

* Frontend-backend clean separation.  RDS vs RSS. 
* Locking to support isolation and atomicity for concurrency
* Authorization via views
* DB-OS interactions
* user evaluation!?

Views

* Users granted access to tables and views 
* If want to limit a user to a subset of data, define view and grant access to view
* View definitions stored in catalog, replace references to view with view def's parse tree
* View updates is ambiguous in most cases beyond simple single table settings
  * Insert into T JOIN S
    * what should the join key's value be?
  * Update on aggregation
  * Delete from T Join S
    * delete both T and S records?  What if multiple ways to derive deleted record?
* Views are super powerful use of independence
  * Materialized views are access methods
  * Maintaining materalized views is akin to streaming
  * Provides access control
* Views and tables named [user].[tablename].
  * made passing ownership hard
  * now named [schema].[tablename]
* Reloading tables adds complication
  * Want to drop table, reload, and build indexes
  * That would cause view to be dropped as well
  * This is the point of xacts


PL/DB interface

* Embedded DB into PL/I syntax.  
* During program execution/compilation, needs to
  * Identify SQL queries
  * Compile query into an "access module" aka function q() on the server
  * Replace query with q() call
* Today, interaction is still clunky
  * DB library that connects to server
  * LINQ and data frames as library
  * UDFs put PL runtime into DB
    * opaque, how to cost estimate and index UDFs?
* Still an open problem.  Compilation to mix PL and Query is not a bad idea

Compilation

* How a program (query) is executed is a tradeoff
  * interpreted? Postgres
  * compiled to low level language? Hyper
  * compiled to IR/opcodes? SQLite
  * When to compile?  JIT?
  * What if program changes?
* Arguments for compilation rooted in complaints about overhead of "prepping" queries
  * Parsing, validation, optimization is expensive
  * That's the argument for stored procedures, not Compilation!
* What if access methods/tables change?
  * Compiled programs (access modules) stored and accesed via catalog
  * Invalidate in catalog, will automatically recompile next time
  * This is roughly how query caching works too
* Separates compilation decisions (RDS) from the storage system (RSS)
* evaluation
  * even in adhoc cases, perf win >> compilation costs
    * cost(interp) >> cost(compilation) even for small tables
  * C++ compilation overhead is in multi-second territory
  * Dataset needs to be large enough to justify high fixed cost
  * why use interpretation?
    * when flexibility and app features are still in flux
    * once app requiremenst frozen, move towards raw perf (currently SQL is in this phase)

Access Paths

* Removing tuple points means varlength records --> bookkeeping overhead + redundant data values
* Vestiges of IMS/CODYSL wars: pointers in a record to connect to related records (links)
  * can become a referential integrity nightmare to maintain
  * not really used
* Saw that XRM inversions are just indexes
* Sort
  * Used for sort-merge join.
  * So important, there are major sorting benchmarks

Optimizer (Selinger)

* Rule-based heuristics
* Evaluated bunch of join implementations, found the two classic ones
  * index nested loops.  See block nested loops for memory-aware optimization
  * sort-merge.  See external sort
  * Goetz has a great survey

Optimizer evaluation 

* not great.  the DB satisfies selinger's assumptions!
  * uniform distributions 
  * attr independence
* estimation is still one of the hardest problems in query optimization
* note that actual estimates don't matter, only relative ordering
* See https://wp.sigmod.org/?p=1075 for why query opt is still hard

Recovery

* Failures as first class citizen.  Uncommon in systems at that time
* Recovering is not good enough. Needs to be in a "correct" state.  
  * Defined ala transactional correctness!
* Checkpointing + dual logs
  * Checkpointing is stop the world
* Logs store old/new copies ala shadow copy method.  Confusing
  * On write, create shadow page on disk that gets the write
  * Current page is "old"
  * Periodically flush shadow page changes to disk
  * At this point, shadow page is now "current"/"old" copy
* Describes WAL, the intergalactic standard, as an aside!!

Locking - Transactions

* predicate locks too complicated
  * does a=1 and b=2 overlap?
* phantom problem
  * physical locking to enable logical constraints
* Hierarchical locking is gold standard for locking-based CC
  * Intention locks vs actual locks  (why?)
  * trading many small locks for large lock
  * upgrading
* Semantics
  * Level 1, 2, 3 are read uncommited, unrepeatable read, repeatable read
  * Defined ala locking protocl rather than consistency semantics
    * misses OCC, MVCC, etc
  * See Adya and Liskov ICDE 2000 and Natacha Crooks PODC 17
* Eval
  * See that perf for level 2 worse than for level 3, and argue against level 2
  * modern DBs use level 2 often for performance
  * level of concurrency in exps not high enough?


Eval notes

* Mentions drawback of normalization forcing joins
  * Normalization is logical.  Nothing says DB can't denormalize underneath

## Convoy problem

* described poorly.  Classically an issue with FIFO scheduling
  * if jobs take variable amounts of time (100, 10, 10)
  * then jobs 2 and 3 need to wait 100 steps before they can quickly run
  * the "convoy" waiting behind the slow leader
* issues
  * poor interaction between DB lock manager and OS process scheduler
  * FIFO lock allocation
* How the locking mechanism appears to work
  * Process 1 acquires lock
  * Process 2, 3 wait in a FIFO queue
  * Process 1 releases lock (for whatever reason), 
  * lock mgr immediately gives to Process 2
  * Process 1 wants the lock again (in the same quanta), and needs to go to end of line
  * Unlikely that queue will drain faster than processes can complete work
* Exacerbated by OS scheduler interactions
  * DB processes can be preempted into "long wait time" by OS scheduler
  * Bad case is if preempted process holding popular lock,  
    * processes that want lock get quanta and waste it in line

Solution

* Ideally want control/way to signal to OS scheduler
  * See Stonebraker's treates about OS support for databases
* Solve part of problem by modifying lock manager
  * don't immediately give lock to head of queue
  * let process in queue acquire lock (if they are allowed to)


Conclusions

* The age old question: 
  * declarative language + optimizing system vs non-declarative programming
  * How much to widen the dev API to give access to performance/systems concerns?
* Pros
  * flexibility, "unanticipated applications", simpler APi
* Cons
  * monolithic architecture (perhaps)
  * slower than hand-tuned
* Big data stack saw rise in procedural querying
  * MapReduce, key-value stores (dynamo, bigtable, etc), distributed infrastructure


# Misc (not discussed)

## Storage hierarchies 

Numbers to know


## The importance of simple equations

5 minute rule

## General principles 

Semantics and Correctness

Failures and Recovery

Buffers/Caches/Prefetching

* Client caches
* Buffer pool
  * disk page prefetching
* C-store's write buffer
* LSM trees for buffered writes
* Prefetching
  * instruction prefetching
  * page prefetching (hardware and in DB via access patterns)
  * pipeline prefetching with CPU

Temporal/Spatial Locality

Trading CPU for Memory (Compression)

Latency vs Throughput (Batching)

Replication for recovery and performance

* Log and DB state are replicas 
* Distributed file systems store replicas for handle failure
  * faster reads as well
* Read vs write trade-off

Skew/Fast paths/common cases

* Skew in queries/access patterns
* Skew in daa access --> locality
* Skew in data values

Communication

* messaging, shared memory/space



