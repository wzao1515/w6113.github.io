# Reviews

Linke to [annotated reviews and student reviews](../exreviews_04)


* summary covers breadth
* details, strengths, weaknesses cover depth: picking a few things and doing deep on the details and comments
* comments on the ideas and design choices, not necessarily the writing
* be able to explain the details of the main idea to someone else
* there are always weaknesses in a paper (or series of papers)
  * not all weaknesses break the argument
  * is the evidence in the paper sufficient?
* ensure what you write is correct and PRECISE

        "...which is different from most modern commercial database."

        "A column may be in many projections. So it has K -safety property. "

  * in what way does it differ?  
  * is the point of comparison meaningful?
  * are commercial databases the state of the art?  
  * does differing from commerical databases imply research improvements?

# Column Stores (OLAP)

Background

* OLAP is fundamentally about throughput
  * disk scans, memory throughput, CPU throughput (instructions/tuple)
* Hardware trends since 70s
  * CPUs >10000x faster
  * Disk bandwidth / Disk capacity reduced by 100x
  * Seq / random acces increased by 10x
  * Memory latency vs CPU cycle increased 100x
* Two major systems
  * C-store addresses memory and disk bottlenecks
    * Better utilize work per byte of bandwidth from disk
    * storage format, compression, query execution, shared nothing
  * MonetDB/X100 addresses CPU and memory bottlenecks
    * Better utilize each byte sent to CPU 
    * vectorized operators, compiler optimizations (simd, loop pipelining, avoid branch mispredictions)
    

The bag of tricks

* virtual IDs
* block/vectorized processing
* late materialization (keep columns rather than multi-col tuples)
* Column compression + compressed execution
* Specialized joins
* Replicate cols on different sort orders
* Adaptive column sorting (cracking)
* Bulk loading

Row vs Col page structure

* Row pages (NSM Nary storage model) data top down, and pointers to rows bottom up

        [[header] [row 1], [row 2], ...

                [row 2 ptr] [row 1 ptr]]

* Col pages (DSM decomposition storage model)

        [[header] [rid1], [val1], ... ] Subrelation for Col 1

* PAX: hybrid.  Each page contains K rows, internally columnar (for CPU)


### C-Store

Motivation

* Logging

        10k machines * 100hz = 1,000,000 
        * # Sensors * # data centers 
  * bulk writes, few updates, read-mostly

* Star schema
  * unnormalized retailer schema 

        store_id, store_name, store_owner, store_addr1, ....,
        emp_id, emp_name, emp_age, emp_salary, ...
        prod_id, prod_name, prod_id, prod_price, ...
        ...

  * Redundancy, big issuse, it's bad
    * update/delete anomalies
  * normalized "star" schema
    
        fact(store_id, emp_id, prod_id, cust_id, ...)
        store(store_id, name, owner, addr1, ...)
        ...

  * Why do this?
    * read less data since only care about a few attributes
    * avoid anomalies

Overview

* supports the logical relational model
* So much data that indexes don't help and in fact _hurt_ in most cases 
  * good if reading a few records/pages (we'll get to that for OLTP)
* performance bottlenecked by read throughput 
* want 100x speed up compared to existing solutions
  * data cubes 
    * expensive -- OLAP queries access different sets of attributes
    * restrictive -- for constrained queries (algebraic, distributive)
    * but can still be built (faster) on column stores
* Notice:
  * WS vs RS: write buffer vs read optimized storage
  * Most wins come from compressed execution, yet big chunk of paper discusses correctness.  Why?
  * There's a story for multi-node, but kind of an aside here


#### Physical layout

Projection: Multiple columns

* each column in project is stored separately
* sorted on subset of the columns (sort key).  Index in sort order is storage key for row.

      EMP1(name, age | age)  # sorted on age

* The columns for EMP1:

      (name, storage_key)
      (age, storage_key)

      // storage key not stored, just its order number in the column
      // for joining between projections
* Each projection horizontally partitioned by value on sort key into segments
  * segment = key range.  Can use segment header to store other metadata
  * thus name, age are identically partitioned


Join Indexes match the same records across projections

* Maps rows from segment k in projection C1 to corresponding storage keys in C2
* Suppose I have the following query and projections

        SELECT a, b FROM data

        C1(a | a)  # M partitions
        C2(b | b)  # N partitions

        C1_i is i'th partition of C1
        J_i(sid, storage_key)  

        * has same number of rows as C1_i.
        * sid, storage_key of matching row in C2

* Expensive to maintain under updates!  Try to avoid by having each col in many projections
        


* Example query + projection
* overlapping projects
* Picking projections is an optimization problem limited by storage space
  * if don't have a projection for query, gets expensive

Compression

* few distinct values, sorted on this col

      (val, startidx, endidx)

* bitmaps

      (val, bitmap) e.g., (99, 0111100010100)

* delta compression

      10, 12, 13, 14 --> 10,2,1,1
      reduce bits per val, then compress again

* What data types/use cases are these good for?
* So fast that CPU decompression is often bottleneck!


#### Write Store (WS)

Tries to avoid building two engines.

* also column store
* same physical design as RS (projections, join indexes, horizontal partitioning) 
* columns store the storage key explicitly
* storage key > key in largest RS segment.  
  * **Thus (sid, storage key) is globally unique**
  * join indexes naturally index into WS
* uncompressed, b-tree indexed, likely in mem
  * two indexes: one on sk, the other on sort key

        EMP(age,name|age)

        btree index on sk --> age
        btree index on sk --> name
        btree index on age --> smallest sk of the run


Implementation

* BerkeleyDB + lots of memory

* Low and high watermarks
  * High: latest time big reads can run in snapshot isolation
  * Low: earliest time big reads ta run (why? to avoid keeping arbitrary timetravel state)
* Run in epochs.    Central epoch provider (timestamp authority TA)
  * Each projection segment in WS keeps insert vector (insert epoch for each row) and deleted vector
  * TA sends new epoch messages to nodes
  * nodes wait until all active xacts commit locally, then reply OK
  * TA sets new HWM, sends to nodes
  * HWM is safe to read
* Tuple Mover (TM) 
  * Look for pairs of segments from WS, RS
  * Merge all inserts/deletes into a new RS' segment where timestamps < LWM
  * Thus, can do time travel between LWM - HWM.


#### Why not implement in a rowstore

* vertical partition the row store  
  * tuple overhead and storing pkeys substantial
    * 17 col table is 4GB compressed
    * single attr is 1GB compressed
      * 4 bytes for data, 8 bytes for header, 4 bytes for pkey
  * C-store: 240MB/col
* Mat views do well, but suffer the overheads, and limited to recurring queries (not adhoc)

Isn't storing all these projections blowing up disk costs by several X?

* Compression such a big deal, can afford to
* Disk _space_ usually not a problem, it's disk throughput per _effective_ byte of data
  * See hardward trends at the top
* Each query accesses few projections

#### Query execution walk through

        select avg(price)
        from data
        where symbol = 'GM' AND date = xxx

Row store

* read and send entire tuples throughout the plan
        
                         AVG price
                            |
                     SELECT date = xxx
                            |
                      SELECT sym = 'GM'
                            |
                            |   (GM, 1, ... xx, ...)
                            |
        [Symbol, price, nshares, exchange, date,...]
        [GM        1    ...                 xx     ]
        [GM        2    ...                 xx     ]
        [GM        3    ...                 xx     ]
        [AAPL      4    ...                 xx     ]
        ...



Columnar using row store

* send complete tuples up after `construct()`
* due to record overhead, can be slower than vanilla row store

                           Avg price
                                |
                        SELECT date = xx
                                |
                        SELECT sym = GM
                                |
                                |   (GM, 1, xx)
                                |
                  Construct (Symbol, Price, Date)
               /     |                              \
        [Symbol]   [price]   [nshares]  [exchange]  [date] ...
          GM        1                                 xx  
          GM        2                                 xx
          GM        3                                 xx
          AAPL      4                                 xx


CStore w/ late materialization

* removes record headers
* send compressed bitstrings through query plan

                           Avg price
                                |    (1,2,3)
                          Lookup price
                                |    (1,1,1,0)
                               AND
                 (1,1,1,0)  /        \   (1,1,1,1)
            SELECT sym = GM            SELECT date=xx
               /                                   \
              /                                      \
        [Symbol]   [price]   [nshares]  [exchange]  [date] ...
          GM        1                                 xx  
          GM        2                                 xx
          GM        3                                 xx
          AAPL      4                                 xx

* CStore w/ compression     

                           Avg price
                                |    (1,+1,+1)
                          Lookup price
                                |    (3x1, 1x0)
                               AND
                 (3x1,1x0) /        \   (4x1)
            SELECT sym = GM            SELECT date=xx
               /                                   \
              /                                      \
        [Symbol]   [price]   [nshares]  [exchange]  [date] ...
         3xGM         1                             4x "xx"
         AAPL        +1                                 
                     +1                                
                     +1                               


* Why the wins?
  * biggest wins from compression:   10x
    * compress better and read less
  * late materialization: bit strings instead of tuples:  ~3x
  * block-based execution: 
    * row-based: ~2 function calls e.g., `tuple.get_attr('id')` per tuple 
    * block: single operator call + loop   ~1.5x
    * has nice low level hardware properties (cache line, prefetch, simd, parallelization)


Highlights:

* >100x faster than RDBMS -- everyone uses it
* may not be as fast for random writes.
  * ensure sorted in every projection.
  * is this a problem?

## MonetDB/VectorWise

Two awesome systems.  The x100 CIDR paper is great.

MonetDB

* Highlights 
  * "Zero degrees of freedom": no interpretation on the data path
    * Operators don't have predicates
    * Each expression (e.g., +, -) is a physical BAT operator
  * full column stored at a time
  * BAT column-at-a-time physical algebra

        sum(c * (1 - d)), sum(c * (1 - d) * e)

        tmp1 = -(1, d)
        tmp2 = *(c, tmp1)
        tmp3 = sum(tmp2)
        tmp4 = *(tmp2, e)
        tmp5 = sum(tmp4)

  * Physical expression operators (e.g., +, /) that are col-at-time
  * Optimized to minimize PUC cache misses
  * cracking
* Limitations
  * lacks pull-based pipelining
  * materializes all intermediates.  massive write bandwidth
  * columns uncompressed
  * memory mapping -- relies on OS for I/O scheduling

Vectorwise

* Volcano based data flow, but processes vectors of ~L1 cache size
* Heavily templated, vectorized physical operator implementations
* Compression and other tricks
* Profiling per-vector is cheaper, can keep good stats
* Adaptively try different physical implementations on a per-vector(s) basis (uses bandits)
