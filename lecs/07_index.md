# Overview

* Both papers have weaknesses.  Were they still worth doing?  why?

## RTrees

Balanced secondary index structure

* Nodes contain list of (rect, pointer)
* Rect is tightest cover for subtree
* min/max fill factor before condense/split
  * empirically, min = 2 works

The general optimization is the following

        min amount of X?
        s.t. Tree obeys R-tree structure

* Where X is a proxy for preformance: area of each enclosing rectangle in the inner nodes
* R * paper questions this choice.  
  * Why not minimize the margin or the overlap of such mlnlmum bounding rectangles? 
  * Why not optimize storage utlllzatlon? 
  * Why not optunlze all of these criteria at the same hme?
  * Yet another example of "let the empirical findings make decisions for us" aka the data-driven approach.
* Can this be maintained via local decisions?

Insert E

* walk down based on rect that needs least enlargement to include E
* If need to split, run split node (the tricky part) 
* create new index entry into parent (maybe need to split again)
* update rects as splits propogate up
* sensitive to insert ORDER!

Delete E

* delete node, propogate up the tree
* re-insert the deleted records

Search E

* Worst case (big search rect), read all leaf nodes

Node splitting heuristic

* assign entries to the 2 nodes s.t. total area of two covering rects is minimized
* 2 approaches
* quadratic
  * try all pairs of entries and pick pair that maximizes aera (area(R1 U R2) - area(R1) - area(R2))
  * greedily chose entries based on max difference between the area increase for adding to node 1 and node 2
* linear
  * pick pair
    * for each dimension, compute diff between high side of R1 and low side of R2
    * normalize by dimension bounds
    * pick the biggest diff

Experiments

* Linear with minsize = 2 works pretty well across the board!
* Experiment is pretty good
  * Warmup the tree, and evaluate last 10% of inserts 
  * Search 100 random 5% selectivity rectangles (square or thin?)
  * Delete every 10th entry.
  * Is there randomization?  No
  * Vary page size, fill factors, algorithms
* Notice that the cad datasets are very amenable to the linear heuristic:
  * long thin rectangles, high clustering


Extensions: R * tree

* Fairly exhaustive, empirical study of effective optimization criteria 
* Same delete, query operators
* On insert:
  * minimize rect overlap for leaf nodes
  * minizime enlargement and area for index nodes
* On split
  * topological split along a dimension, then minimize overlap


### GIST trees

Overview

* Intellectually unifies many indexing structures, hints at theory of indexibility
  * when and under what conditions does indexing make sense?
* Got it to work in actual systems (SHORE, Postgres)
* The mathematical depiction of linear, 2D, and set-based indexes is a nice touch


What is an index really?

* Internal nodes form a directory, leaves point to data, connected as a linked list, perhaps some ordering
* Each node is like a bloom filter: tells if following a pointer will likely lead to the search result
  * Consistent: search key doesn't rule out data below the pointer
* Search key: _any_ predicate that holds for all datums below the key
  * typically ranges, but could be anything
* Search Tree: hirearchy of dataset partitions, where each partition has label that holds for all data in partition
  * Split functions define partitions
  * Key funtion defines Partition -> Labels 
* Union: given set of predicates, derive predicate for its union.  Basis for bulk loading, splitting (determining predicates for split nodes)

Penalty(pi, k)

* What is the cost of inserting K into ci, or one of its children
  * It better te computable just from pi and k's information.

        Insert [k]

        [ p1 | p2 | ... pn ]
          /     |         \
      [.c1.]  [.c2.]       [.c3.]

* Will use penaly to choose insert location.  At each node, find predicate pi such that

        pi = argmin_{p in node} Penalty(p, k)


Linear ordering

* Cool that GIST can support it naturally
* Why not define a 1-to-1 mapping from object to natural numbers, and use a B tree?
* Why care about this special case?  
  * Want to go down a single path in index, and linearly scan
  * Vs reading lots of inner pages.  So? Random reads vs linear scan.
* Additional Requirements
  * Know where to insert an entry within a node:  Compare(p1, p2) --> (<, =, >)
  * For any two nodes P1, P2 at the same level, all items in P1 < all items in P2
  * Keys on a node don't overlap (keys on different nodes of same level already don't overlap)
    * Consistent(p1, p2) = False for p1, p2 on same node
* FindMin and Next are basically B-tree range query traversal

GIST for R-trees

* Key Predicate: Contains(xl, xu, yl, yu)
* Predicates: Contains, Overlap, Equal
  * Note, a point is a zero area box
* Consistent(box1, box2) = Overlaps(box1, box2)
* Union(box1... boxn) = bounding box
* compress(polygon) = bounding box
* decompress = identity
* penalty(E1, E2).  area(Union(E1, E2)) - area(E1)
  * Some trees such as R+ store duplicates.  Other trees may be Dags instead
* picksplit(P): run blackbox function aka Gutmann et al.


Experiments

* The two axes of performance is really nice
  * Data overlap: how skewed is the data?
  * Lossy Keys: false positive rate of index
  * Are these axes orthogonal? 
    * All data can be identical, but non-lossy keys mean only specifci queries read all data
    * But if lossy keys, in worst case every query reads all data
* Why do they use RD tree?  
  * Control over data overlap
* Observations of hotspots in RD, and creating hot-spots via denormalization is akin to join cardinality estimation.