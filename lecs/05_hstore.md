# Overview

Administrivia

* Schedule online
  * Check your presentation slot
    * will want to read the required papers, and draw from any other related work
    * blackboard.  slides are a crutch
    * start with the basics and with examples
    * form an opinion
  * TBA are open for suggestions
* Project information is up
  * prospectus
  * timeline
  * CMT + mock PC
* Thurs for project related work?

Observations

* Part of the one-size-fits-all wave
* If you win by:
  * 100x, users will work around the limitations
  * 50x, some users will notice
  * 10x, may be good for new projects
* Exploit workload + hardware characteristics of the times.

## Some Background

Characterization of OLTP Settings

* Visa

        2B ppl, 5 xact/ppl / 3600 / 24.  / 2 for visa/mastercard ~= 57000 xacts/sec
* Simple transactions, ideally single site
  * remove 1 unit from product X (buy a product, swipe a card)
  * move 5 units between orgs (inventory, shipping)
  * What's a "site?"  coupled failures.
* New Order
  * Lookup district + tax rate
  * Lookup warehouse + tax rate
  * Lookup customer discount rate, credit, etc
  * Lookup item costs + names
  * Update stocking info
  * Create and log each line item of order, log order

Hardware trends change performance

* Memory is cheap, commodity clusters
* Operating systems and networking conferences are now full of proposals for “database-like” 
  storage systems with varying forms of consistency, reliability, concurrency, replication, and queryability 

Storage Manager Primer

* Buffer Pool
  * Hash table of page id --> page
    * Work with log manager to force pages to disk on commit
    * Mutex lock on per bucket for contention under concurrency
  * Xacts pin pages into buffer pool, unpin when done
  * Read/Write latch per page to avoid concurrent edits   
    Can be serialization point under high read contention for latch
    * Latch: like a lock, but no deadlock detection, no wait queue, manually managed
* Log Manager
  * Each page write --> log record
  * Log Sequence Number on every page
  * Flush on commit + group commit
* Shore 
  * Reasonably lightweight, minimal
  * Basically a key-val type store using System R-style design
  * Directly access records via indexes.  No optimizer/executor frontend
* Modifications
  * Logging:
    * Turn off I/O
    * group commit + infinit log buffer size = no flush
    * turn off log records
    * ignor LSNs
  * Locking
    * immediately grant all locks
    * remove code for handling access requests when don't have lock
  * latching
    * skip latch requests, immediately grant
    * remove latching code from b-tree.  hot spot
  * Buffer mng
    * directly use malloc
    * directly point to in-memory record objects, skip buffer pool
    * tight integration with rest of code, can't fully remove notion of buffer slots/frames

Life of a xact

* setup transaction (write log records)
* repeat
  * lookup location of record (latches)
  * lock record
  * load page.  if btree pages, get locks
  * load record into user space
  * read/write in app logic, write back to shore
  * write log entry
* commit 

    
### Looking Glass Experiment

Involved vs simple xacts

* New Order (a bit more involved)

        begin
        for 10 items
          lookup Item record, pin
        lookup District, Warehouse, Cust, pin them
        Increment order id in District record
        for 10 items
          Lookup stock info, pin
          update stock counts
          create lineitem - order record, add to btree
        create order summary info, add to btree
        create new order info (basically a copy), add to btree
        end

 * Payments
  
        Lookup District, Warehouse, customer, pin them
        Update them all
        Create and insert a payment hist record
        commit

Where does the cost go in a classic database for a simple query?

* Costs 
  * Indexes 10%
    * Specializing btree keys!  aka use in-mem index
  * Logs 20%
    * Commits + update logs
  * Locks 25%
    * Pin, tree locks, commit
  * Latches 15%
    * btree, buffer pool
  * BuffMgr 30%
    * big portion is due to small pages. bigger pages == fewer tree pages
    * indirection through hash table expensive
  * Else: 5%
  * OK, only 5% of time is for doing work
* Why these costs?
  * Logs, locks, latches for concurrency control
  * BufMgr for pages for disk
  * Indexes for _disk_ resident data
* A minor note that instructions ~= Cycles (wallclock).
  * doesn't account for read/write memory access, branch misprediction, function pointers, etc
* Baseline is a fast in-memory btree with nothing else.

## H-store


Original H-store proposal was before looking glass paper

* Keep database simple, push complexity outside of db xacts
* aggressively optimize for single site or non-coordination xacts
  * really really want deterministic execution without coordination
* no knobs (didn't happen, customers want control)
* Didn't want a centralized coordinator, assume bounded network delays.  
  Each site waits for a "short period" to ensure it sees all inflight xacts, locally orders them
* Like high level programming API that integrates data model into application (ala ruby on rails)

Skeleton wase same, some things changed

* High level
  * No parsing -- stored procedures

        Procedure:
        p1 = SELECT cost FROM data WHERE id = ?

        Query:
        p1(10)

  * No concurrency.  Serial execution
  * BufMgr not needded, just tuples in memory (scary?  failure?)
  * Memory-oriented indexes.
  * Multi node.  2PC?  No, that's for global xact ordering.  Just do it from a xact coordinator.


Recovery (replication options)

* Log shipping (store logs, ship updated values to backups)

                  Primary            Secondary
        qureies --> DBa  ---------->    DBb
                           logs     (replay logs)
  * extra work for primary, traditional logs are inefficient

* Active-active

                 /  DBa  -----> write query log (just params)
       queries --
                 \  DBb



Costs are now in distributed Transactions

* 2 phase transactions
  * Read-only phase to determine transaction order and abort/commit
* eventual consistency -- avoid coordination
  * Amazon's dynamo was 2007 (one year before looking glass paper)
  * see CALM, CRDT, Lattices, Anna, etc

### Hekaton Sketch

Integrated with SQLServer, language keywords to create in-mem tables

* Improving 10x means run 90% less instructions.  100x means 99% fewer!
* Doesn't rely on partitioning due to non-partitionable queries
* Latch-free memory-designed indexes for concurrency
* Optimistic MVCC
* Single node, many cores --> shared memory over cores rather than h-store's shared nothing

Index is interesting.  

* Essentially postgres-like tuple versioning using linked list tuple chains, with some diffs
  * As many version pointer slots in tuple header as indexes over the tuple.  
  * pointers actually connect all tuples in bucket/leaf node _and_ their versions
* OK because versions all in memory (vs on-disk writes in postgres)

Transactions

* Serializability: read and write times are logically identical
* In practice, read earlier in xact, writes later, commit at end
  * need to ensure earlier reads would be _identical_ to reads at commit time (ala OCC)
* Commit validation
  * Maintain read set as pointers to read tuples
  * Ensure tuples are still visible at commit time (begin <= commit <= end)
  * rerun index scans to check that no phantoms appeared (newly inserted tuples)
* If T1 starts during T2's validation, T1's commit depends on T2's commit decision
* Maintain write sets to update END timestamp for modified tuples


## So what does this mean

Modern databases contain multiple specialized databases

        (SQLServer
          [hekaton OLTP database])

        (DB2) -- (BLU)


        (Spark
          (sparksql OLAP database)
          (spark graph))   --- Impala



# Tiny bit of distributed transactions

Remind about Time scales

* IPC costs: 5 microseconds = 0.05 ms
* Network costs
  * ping RTT -- 3-4ms
  * State of the art: 0.2ms
  * WAN: 200ms

Simple query

* a = b + c
  * a,b,c on different machines
  * Coordination is needed
    * read locks on b,c
    * write lock on a
  * _also_ make sure replicas of a, b, c are consistent
* 2PC requires 2 round trips
  * IPC: 20k xacts/sec
  * Datacenter: 5k xacts/sec
  * WAN (megastore): 5 xacts/sec
* If replication, then consistency plays a part as well
  * wait for replicas for each value to say OK

When can you avoid coordination?

* blind inserts
* 2 increment statements (commutative -- CRDT)

Clarify Consistency vs Coordination

* Coordination
  * Concurrency and locking, you don't want to write/read the wrong results
  * ACID
* Consistency
  * When you have copies for replication, ideally any time someone reads a value from all replicas, they should be the same value
  * Can be expensive
  * Eventual consistency -- screw it, don't care



