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
<p class="block">Presents a method (2 main variations) to compress data into bit indices organized in manners that maximizes cpu bit parallelism</p>

<h3>--</h3>
<p class="block">The paper proposes a method of optimizing full table scans to run in a single processor cycle or less per column</p>

<h3>--</h3>
<p class="block">Fast scan method for column stores by exploiting intra-cycle parallelism and bits in processor words.</p>

<h3>--</h3>
<p class="block">1. Improve storage format from VBP, HBP as a weaving 2. Apply early pruning to the new weaving method</p>

<h3>--</h3>
<p class="block">This paper proposes BitWeaving which exploits all the bits in processor words gainfully to perform fast scans in main memory database systems.</p>

<h3>--</h3>
<p class="block">Introduce BitWeaving, a bit-level technique to optimize scan operation in space and achieve natural limit</p>

<h3>--</h3>
<p class="block">BitWeaving methods that fully exploits intra-cycle parallelism. Arithmetic framework for predicate evaluation on BitWeaved data.</p>

<h3>--</h3>
<p class="block">Achieves 4-20x speedups on full tables scans by tightly packing bits into processor words</p>

<h3>--</h3>
<p class="block">Propose the two storage layout methodHBP and VBP of organizing the data into processor word to exploit the processor cycle clock parallelism.</p>

<h3>--</h3>
<p class="block">SMOKE is a system for lineage, tightly integrating lineage into the query algera and query compilation for good performance</p>

<h2 id="reviews">Reviews</h2>

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The paper is very dense technically, providing major innovation by creating a technique for bit compression (applicable to bit map indices) that improves flaws in existing methods. From the highest level, the paper attempts to maximize the speed of scans(column scans in particular) by fully untilizing the compute capabilities of a single core. Naively storing objects in memory and looping through it incurs overhead in terms of both the space of memory to search through and computation cycles. Earlier works to address this includes the introduction of SIMD (Single instruction, multiple data) Cpu instructions, which allows bit level manipulation of register data. This made it possible to compress multiple data units into a single SIMD word, enabling processing of multiple data units with a single cycle that process that word since the processor process bits in parallel. A major weakness however is that not all data fits neatly into the size of a SIMD word. The question of how to vectorize data so that it fits into SIMD registers become a new area of research, along with algorithms to use these vectorize data. In general, there are two methods - split a data instance across sequenial horizontal bands on the same row, or vertically across rows so that each row have a bit of the data. Bit slicing works by assuming that there is limited unique values, encoding each instance as a binary value. 

This paper build on the general methods of vectorizing data to introduce 2 new ways of representing arbitrary length data in bits. Horizontal bit parallel storage works as outlined above, with 1 bit seperators between encodings. Each column segment is arranged vertically from left to right. Scans with predicates such as =, &lt;,&gt;, != etc can be computed using bitwise operators, followed by bit shifting to minimize loops. Vertical bit parallel storage is also as outline - one advantage it holds over horizontal is that there is less wasted space (padding for last word), requiring exactly the number of rows as bits necessary. The major advantage though is that since each significant bit is in a different row, processing 1 row at a time enables prunning of large number of word columns that does not fit the criteria. Finally, bit weaving tries to incorporate the horizontal segment layout into vertical storage and vice versa to enable better cache prediction.

Evaluation wise, both micro benchmarks (using scan with &lt; predicate) and general benchmark on TCP-H are done. Experiments were ran on codes from 1 bit to 32. All variations of bit weaving uses significantly (&gt;5x) less cycles and instructions compared to a naive implementation, with vertical scaling slightly better than horizontal due to pruning opportunities and reduced padding. Horizontal is slightly better in terms of cache misses/lookup since a single encoding is guaranteed to be continuous. Speedup gains over naive are similarly the best with all variations of bit weaving. 

The method was implemented in Wisconsin's quickstep, which is later open sourced on apache (might not be currently used). This is definitely useful for column store systems looking for state of the art scan speeds, though simply supporting SMID gives &gt;50% performance increase over naive. Overall, the paper is impressive for eeking out significant performance gains over an already optimized system. As mentioned in the paper, future improvements include more alogithms and a more thorough evaluation of other operators (joins, aggregations etc). It's also interesting to note that in a future paper, the authors eliminated padding between words, further reducing storage for columns. 

If I have to cherry pick about the weakness of the paper, it would be that the paper skips over the algorithms (eg why that combination of boolean operators for &gt;). Perhaps it is something obvious to people more familiar with the field, but it made it difficult to appreciate the techniques and significance of design choices (layout etc) without refererncing additional information.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">Seems to be about column denormalization to speed up scans, with data structures to keep track of duplications etc. Might be difficult to update</p>
  <div>Predictions 2:</div>
  <p class="block">Does something similar to bitweaving, focusing more on the algorithms for evaluate a greater range of operations (complex predicates in this case)</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">The paper describes two types of data storage, BitWeaving/V and BitWeaving/H, that can be used as either a native storage method or indexing. BitWeaving/V and BitWeaving/H are built upon two bit-parallel methods, Horizontal Bit-Parallel (HBP) and Vertical Bit-Parallel (VBP), that are comprised of a storage format and a column-scalar scan method. The horizontal and vertical refer to how column encodings are laid out in processor words, that is, row-oriented vs. column-oriented storage. Early pruning is a technique that can be applied to both HBP and VBP and amounts to avoiding bit-level access of unnecessary data by getting rid of tuples not matching parts of a complex predicate clause. BitWeaving/V applies early pruning to VBP and BitWeaving/H does so to HBP.

The theoretical benchmark proposed is that a bit-parallel method must run in O(nk/w) full word instructions for n codes. The practical implementation benchmark the paper uses for comparison is the SIMD (single instruction, multiple data) scan. Seven of the TPC-H queries are used. The performance of the BitWeaving methods is impressive, at an order of magnitude better than the standard. This seems like a valid benchmark and does not seem overly cherry-picked to skew the results. 

The introduction is extremely well written: It intuitively makes sense that the bit-level optimization of the sequential scan that BitWeaving/V does would be a huge performance gain, and it also makes sense that looking at fewer bits improves performance as well. The way the authors detail how they will structure the rest of the paper greatly aids in navigating it, especially given the switching back and forth between BitWeaving/H and BitWeaving/V.

The papers figures are elegant and improve the clarity significantly. In particular, the figure depicting the layout of bits from column codes to HBP and VBP storage explain the concept concisely. 

Overall, the paper could be greatly improved by a clearer layout of the applicable terms, perhaps in a chart, early in the paper. Also, the proofs in the paper could have been put into an appendix; the combination of inline proofs and a series of similar terms makes it more difficult to follow the procedure than it needs to be.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">The WideTable seems like it's a good solution when you often need to join different tables and aren't redoing the same joins over and over, so a single flat table suits your needs. It seems like the WideTable is best for use cases where most accesses are reads, because otherwise the cost of up keeping denormalized tables seems too high.</p>
  <div>Predictions 2:</div>
  <p class="block">Vectorizing Column Scans seems like it makes the most sense when ICARD for each column is high, and columns don't have that many unique values.</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">Since scan operations are inevitable within a data processing task, this paper proposed a method, BitWeaving, which apply scan operations with predicate evaluation on columnar in-memory database engine where they optimize for the number of CPU instructions that are needed to process the data as well as the number of CPU cache lines.
BitWeaving is a set of techniques that the author introduce which is categorized based on the storage layout used, BitWeaving/V uses Vertical Bit-Parallel method resembles a bit-level column store with data being packed at word boundaries, whereas BitWeaving/H uses Horizontal Bit-Parallel method where it packs codes into processor words.
The authors introduce a set of techniques to speed up predicate evaluation, such as early pruning and bit grouping for BitWeaving/V that tries to avoid loading contents that would be pruned.
Each method has different benefits and drawbacks, BitWeaving/V enable early pruning optimization. In the other hand, BitWeaving/H has better lookup performance. In the end, the authors show that this method produces an order of magnitude improvement over state-of-the-art approaches.</p>
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
  <p class="block">This paper proposed new methods for memory scan with predicates in bit level. "Bit level" means that they used some compression techniques like prefix, suffix to encode the string (data) into fixed length of binary numbers. The issue that they want to resolve is that since the comparsion between two codes (bit numbers) could be achieved without scanning the whole code, it would be ideal to skip the unnecessary scan. Like database storage, bits stored in memory could also be categorized as two types: column oriented and row oriented. In this paper, they introduced vertical and horizonal parallel methods for word processor to scan bits. The main strategy they depended on is a cut-off method called early pruning. It could also be used for conjunctions or disjunctions of predicates. Appears to me that the weaving method is like "weaving" the storage method and early pruning together. Using bitwise operations will definitely improve the performance, but will there always be proper encoding methods and decoding methods to process every types of data into bit? Because in the example the paper gave, it is very simple data which could be encoded into binaries, but does that work for other types of data? The other one question is that the paper only talks about SIMD, would there be a difference between SIMD, MIMD etc.?</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">The system would work under the scenario that most operations are read and there will not be insertions into the middle of data (append only). I think the system will decomposed the complex queries into scans and apply simple scan approach.</p>
  <div>Predictions 2:</div>
  <p class="block">I think their approach would be bitwise as well, and they used a vectorized scan to improve the processing.</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">This paper by Li and Patel proposes BitWeaving, a technique which fully exploits intra-cycle parallelism by using all the bits in processor words gainfully to perform fast scans in main memory data processing systems. The paper introduces two flavors of BitWeaving: BitWeaving/H and BitWeaving/V corresponding to the underlying bit-parallel storage format (Horizontal Bit-Parallel (HBP) and Vertical Bit-Parallel (VBP)). It also presents thorough experimental results comparing BitWeaving with the existing state-of-the-art methods on different benchmark and individual BitWeaving Components. The paper concludes with a brief discussion of directions for future work.

The major goal and contribution of the paper is to push intra-cycle parallelism paradigm to its natural limit to the bit level for each column in fast scans of in memory databases. What distinguishes this technique from previous work is that BitWeaving is designed to fully utilize the entire width of the processor words to reduce the number of instructions needed to process data. The paper makes this clear by starting with the discussion of bit-parallel methods HBP and VBP. Both methods are clearly explained by giving algorithm pseudocode, function procedures and concreate examples. The paper also discusses early pruning, the second main component of the BitWeaving technique. I find the pruning optimization itself to be not surprising but it is nicely incorporated into BitWeaving when it actually discusses the technique in Section 5. The evaluation is also nicely presented by both comparing the BitWeaving performance to that of state-of-art approaches and decomposing BitWeaving into main components and evaluating individually.

In general, I think this is a really nice paper that presents the new fast scanning method clearly with good supporting results which show it outperforming state-of-art by an order of magnitude under quite a few conditions. I just have a few questions resolving which I think could potentially improve the paper. First, despite the nicely presented evaluation section, I think the authors could give a little bit more intuition and explanation for the resulting performance of the technique. For example, a little intuition could be given to the fact that BW/V method has higher performance for queries with predicates that involve many columns, involve wider columns, and highly selective predicates. Although influences of wider codes are touched upon earlier in the paper, it is not super clear how the other variables might affect the relative performance of the two BitWeaving flavors.

Secondly, I think more discussion regarding when and where this BitWeaving technique would be beneficial should be given. Though main memory databases with data compression may benefit especially when there are a lot of table scans in analytic workloads, it is probably not always the case. For example, if the in-memory database experiences a lot of OLTP workloads, I am concerned that data compression and vectorization as needed for BitWeaving will be beneficial. How do systems guarantee the benefits of fast scans using BW is not overshadowed by the overheads of bit updates in compressed format?</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">This paper presents the WideTable technique that aims to improve the speed of analytical data processing systems by denormalizing the database and converting complex queries into simple scans on the underlying wide table. Though the paper asserts that WideTable could theoretically work in various systems and setting, it focuses on main memory analytical data processing systems and a front-end accelerator setting so that the goal is to improve performance when evaluating a specific set of analytical queries.</p>
  <div>Predictions 2:</div>
  <p class="block">This paper presents a framework for vectorized scans with complex predicates in column-store database systems. The paper focuses on SIMD instructions which perform on multiple data elements and benefit from multiple execution ports and out-of-order execution and can take advantage of parallelism offered by multiple cores. According to the introduction, this is generally done by categorizing scan predicates into different types (range predicates, vectorizable predicates, in-list predicates and arbitrary predicate) and giving an evaluation framework for each. The procedure roughly involves reformatting the data, evaluating the predicates, extracting the results and storing them. A series of optimizations will be applied as well.</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">As in memory database management system and real-time analyze tools becomes popular, optimizations could be taken to speedup the in memory scan process. The current method of compact column values fails to fully utilize the width of word and introduce extra operations. Thus, BitWeaving could be used to further compact the space by utilizing word size and running bit operations to fast scan. The state-of-art in memory scan methods are SIMD-scan, Bit-sliced, and Blink. Based on the experiment in 6.1, the BitWeaving techniques speed up the query execution with less memory required because it weaves the bit layout to save space. The paper introduces HBP(BitWeaving-H), VBP, and BitWeaving-V and gives algorithm to column-scan/comparison. The key point is that the storage is more compact by weaving the codes into processor words and implement the functionalities based on ordinary full-word instructions. In the example, it gives how to implement a between clause and the idea can be easily extended to count, avg, union, orderby, many other operations. The experiment are done by comparing with state-of-art methods and measure the speed up on the naive approach. The key result is that BitWeaving outperforms SIMD, BL, Bit-slice in all the test cases. Also, the early pruning technique greatly reduces the execution time to nearly linear when above 12bits. There could be other operations that implement with BitWeaving such as joins and aggregations. The paper also says to apply BitWeaving in automatic physical database design and multi-threaded scans. It also an interesting research to build cost model that characterizes the BitWeaving process and use optimization methods to automatic choose BitWeaving-V or H and tune the performance in general. The BitWeaving can be applied to system that requires very fast scan operations such as OLAPs. Computational intensive operations such as data cube can also use BitWeaving to speed up. A possible drawback is that the speedup based on the state-of-art is not impressive only few x, so it is hard to argue the method is worth applying in real systems.</p>
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
  <p class="block">There is increased interest in main memory database management systems (DBMSs) due to demand for real-time analytics platforms. An important goal for DBMSs is to run scans at the speed of the processing units and exploit all functionality available inside modern processors, but state-of-the-art methods such as SIMD-scan, Bit-slicing and Blink do not fulfill that need. BitWeaving exploits "intra-cycle" parallelism by fully utilizing the entire width of the processor words to reduce the number of instructions that are needed to process data. It comes in two flavors: BitWeaving/V and BitWeaving/H, which corresponds to different storage formats. BitWeaving/V organizes the words into a layout that results in sequential memory address lookups when doing a scan and not wasting space by padding bits to fit boundaries set by hardware. BitWeaving/H stores all the bits of a column value together to provide quick access when fetching a column value. BitWeaving was compared against the state-of-the-art, where it outperforms in query execution time as well as memory and CPU performance. I enjoyed seeing the comparison of HBP and VBP and their pruned counterparts, which quantitatively showcases the amount of code reduced by the pruning technique.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">WideTable seems really focused on reducing space overheads, so it flattens the entire database into a wide... table that can be scanned.</p>
  <div>Predictions 2:</div>
  <p class="block"></p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />

<p class="review">
  <h3>--</h3>

  <div>Response:</div>
  <p class="block">This paper addresses the problem of wasted bit space in processor words, which hinders the efficiency of computation within a single processor cycle. BitWeaving aims to more fully make use of the computer's hardware to improve computational speed. 

To improve scan efficiency and, thus, scan speed, a previous approach packs multiple compressed column values into a single word. However, even this doesn't fully use the word space, since each compressed column value is padded to 32 bits (this padding being wasteful!).

This paper also exploits an early pruning technique, in which predicates may be evaluated using only the most significant bits of a column. 

BitWeaving improves efficiency by transposing the bit values of as many column values as possible and then packing them into a single word. BitWeaving then operates on them as bit vectors.

Early pruning is implemented using the idea that comparisons do not need to look at all bits to get an answer - the most obvious example being equality comparison, where the moment we see two non-equal bits we know to return false.

This paper measures performance along various axes: execution time, memory usage, and size of code in bits. It seems that given the introduction, execution time is the most important value to test, but it's of course important to keep memory usage and code size in check. BitWeaving performs well along all three axes, establishing that a tradeoff isn't really required to implement the technique.

BitWeaving could really be applied not only to database scans but to many kinds of mass computations so long as the data is in a regimented sort of format. However, it seems that this system would struggle in dealing with data of varying length due to the transpositions required to make it work.</p>
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
  <p class="block">The background of this paper is the need to address the high demanding scanning in main memory database. It wants to approximate the limit of bare metal speed which means near the speed of processor. Under this background, this paper targets at intra-cycle parallelism for high performance which means that the bit weaving techniques can not only leverage the SIMD, but can also be implemented with full-word instructions. There are two methods proposed - bitweaving H and bit weaving V which are both based on columnar data in main memory data processing system. 

The detailed method is below. 

For HBP method, it mainly solve the problem of hardware SIMD that the data can not always match the processor width. There are many padding which makes the resource wasted. So, the point here is trying the store more data in one processor word to leverage the efficiency. It adds delimiter bits in the process word to represent different columnar code. For example , each code is K + 1 bits and one processor word can be filled with w/(k+1) word. Based on this storage layout, it uses some conversion to compute the predicate and generate the result bit vector. The algorithm framework about how to compute the less than, inequality and equaility are infered.

For VBP method, it is much easier. The data stores in the column in the processors' word. In this way, some optimization technique can be applied like early stopping. The paper also mentioned that the first group them, then do early stopping will be faster. 

Overall, the HBP is good for lookup and VBP is good for the scan. The experiments also show that is much faster than the state of art techniques. This could accelerate the main memory database. But the hater would be the overhead of constructing this memory layout overhead and in the HBP do the conversation. I appreciate this paper because it is kind of novel, using some processor technology which most database people are not familiar to accelerate the predicate speed. It is nice. But is this just a novel toy? It does not support join and aggregates. Even if it is in analytical database, how can this be used? And the most important, based on these two storage layout, I can hardly think of how to implement join and aggregates on it. It would be amazing to see any following work related to this.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">
Goal: accelarate query in analytical data processing. 

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
  <p class="block">S1. The paper did a good job defining the both the problem and its fundamental contributions. Lineage isn't something we've covered in that much depth before, so having clear definitions for everything was super helpful.

S2. The tight integration with the query compiler was really neat. Only injecting necessary lineage at pipeline breakers, re-using data structures (e.g. hash tables for hash joins + group bys), and the rid indexes were all pretty neat insights.

W1. One thing that wasn't super clear was the querying interface, since the workload W (which defines the set of lineage-consuming queries) must be known in advance. The paper uses the example of a visualization system -- in practice, would one use a different SMOKE instance for each dashboard/set of visualizations?

S3. The experimental section was very thorough! I liked how it broke down the performance across many different query types and tried to tease apart the different contributions from SMOKE (integration with query compilation to avoid virtual functions, workload-aware optimizations, built-in lineage consumption queries) to demonstrate each contribution's impact.</p>
  <div>Comments:</div>
  <p class="block"></p>
  <div>Predictions 1:</div>
  <p class="block">Provenance for Interactive Visualizations: In this work, I suspect there'll be some kind of integration with a grammar-of-graphics type visualization system to add interactions as to the grammar. Given the interactions, translating them back into lineage queries for efficiency should be doable (depending on the exact extensions to the visualization system).</p>
  <div>Predictions 2:</div>
  <p class="block">Titian: Data Provenance Support in Spark: I suspect this work will try to hook into Spark's mechanism for RDDs (which it uses for fault tolerance), perhaps annotating each object in the RDD with a little more information. Presumably, the "source" of the data will be I/O functions (like reading data from disk), so those operators will have to be treated specially (or maybe there's a generic operator to "start" tracking lineage?).</p>
   <div>Predictions 3:</div>
  <p class="block"></p>
  
  </p>
<hr />
