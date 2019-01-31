---
layout: page
---

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