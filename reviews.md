---
layout: page
---

# Paper Reviews

You are expected to write and submit a paper review of the readings before each class, and answer some questions about the readings.  The review should be akin to a conference paper review.  The purpose of the readings is to provide an illustrative example of the research area.  You are encouraged but not required to read the supplemental readings to better understand the materials.  

You can discuss questions and ask for clarifications with your colleagues and/or on piazza.  You are expected to formulate your own opinion of the reading(s) and write the review yourself.  See for a description of what we expect in paper reviews.  

We may select a random review to read and discuss in class.  This serves to highlight important characteristics of reading papers and writing good reviews.


### Submission

The first review ([System R](#lec2)) is special, so look  at the paper description for submission details.  


Otherwise, follow the following instructions: 

* [Submission form](https://goo.gl/forms/uNfVx9jUBXdj6wXd2)
* Reviews are due 6PM EST day before lecture.  
* Late submissions are given a score of 0 without prior approval.  
* You may miss submissions for up to **2 lectures**.


### Reading Tips

Ask the following questions while readings

* What is the context of this work?
  * What was the unmet need or opportunity?  Does it make sense?
  * What were existing approaches and why do they work or not work?
  * Does the paper (and its contributions) matter?
  * What are the actual hypotheses?
* Approach
  * How do they seek to validate their hypotheses? Do they make sense?
  * Is the evaluation cursory or deep?
  * Do you believe their results?
  * Are the results presented well?

# The Papers


### L2 System R Overview <a name="lec2"/>

Readings 

* Required: <a href="./files/papers/systemr-retrospective.pdf">System R Retrospective</a>
* Optional: <a href="./files/papers/ingres-retrospective.pdf">Ingres Design</a>


Paper Review 

* System R was an impressive research and engineering effort, and the reading is a retrospective of the 6 year project.  
* The paper discusses "the Convoy Problem".  Discuss the problem:  What is it?  Why does it exist?
* The paper discusses many many topics.  Identify and pick one aspect (different than the convoy problem) that you are particularly impressed with.  Discuss what and why.
* Note: The format of this review will be different than future reviews because this is a warmup paper

Submission

* Due: 12PM Thursday 1/24 
* [SUBMIT YOUR REVIEW HERE](https://goo.gl/forms/tv0wcPqzvs3hcyNG3)


### L3 INGRES/PostGRES    <a name='lec3' />

Readings

* Required: [Design of Postgres (Initial design)](./files/papers/postgres-retrospective.pdf)
* Optional: [The Postgres Next-Generation DBMS (Midterm design)](./files/papers/postgres-nextgen-cacm.pdf)
* Optional: [Design of INGRES](./files/papers/ingres-retrospective.pdf)
  * Worth skimming: QUEL, leveraging UNIX, concurrency control arguments
* Optional: [The Landsharks are on the Squawk Box - Stonebraker Turing Award Lecture](https://cacm.acm.org/magazines/2016/2/197423-the-land-sharks-are-on-the-squawk-box/fulltext)


Paper Review 

* What were the main goals for the Postgres system and why do you think they chose those goals?  Do they make sense?
* Pick one of the (many) ideas in the paper that most interests you.  Why is it interesting?   Does the proposed design hold water?  Feel free to read related work.
* Note: The format of this review will be different than future reviews because this is a warmup paper

Submission

* Due 6PM EST day before lecture
* [SUBMIT YOUR REVIEW HERE](https://goo.gl/forms/tv0wcPqzvs3hcyNG3)



### L4 Column Stores    <a name='lec4' />

Readings

* Required: [C-Store: A Column-oriented DBMS ](./files/papers/cstore-vldb05.pdf)
* Required: [MonetDB/X100: Hyper-Pipelining Query Execution](./files/papers/monetdb-cidr05.pdf)
* Optional: [Integrating Compression and Execution in Column-Oriented Database Systems](./files/papers/abadi-sigmod2006.pdf)
* Optional: [An Experimental Study of Bitmap Compression vs. Inverted List Compression](./files/papers/sidm338-wangA.pdf)




* Required: [Self-Tuning Database Systems: A Decade of Progress ](./files/papers/selftuning-chaudhuri-vldb07.pdf)

### L5 OLTP Stores    <a name='lec5' />

Readings

* Required: [OLTP Through the Looking Glass, and What We Found There](./files/papers/oltpperf-sigmod08.pdf)
* Optional: [Hekaton: SQL Server’s Memory-Optimized OLTP Engine](./files/papers/hekaton-sigmod13.pdf)

### L6 Query Compilation    <a name='lec6' />

Readings

* Required: [Efficiently Compiling Efficient Query Plans for Modern Hardware](./files/papers/p539-neumann.pdf)
* Optional: [How to Architect a Query Compiler, Revisited] (./files/papers/tahboub-sigmod18.pdf)
* Optional: [Generating code for holistic query evaluation](./files/papers/krikellas-icde2010.pdf)
* Optional: [SMOKE: Fine-grained Lineage at Interactive Speed](./files/papers/smoke-vldb18.pdf)

### L7 Indexes    <a name='lec7' />

Readings

* Required: [R-Trees: A Dynamic Index Structure for Spatial Searching](./files/papers/rtree-gut84.pdf)
* Optional: [Generalized Search Trees for Database Systems](./files/papers/gist-vldb95.pdf)

### L8  Joins   <a name='lec8' />

Readings

* Required: [Shapiro: Join Processing in Database Systems with Large Main Memories](./files/papers/gracejoin-shapiro.pdf)

### L9 Distributed Joins    <a name='lec9' />

Readings

* Required: [TrackJoin](./files/papers/trackjoin-sigmod14.pdf)


### L10 Exchange Operator    <a name='lec10' />

Readings

* Required: [Encapsulation of parallelism in the volcano query processing system](./files/papers/volcanoparallelism-89.pdf)

### L11  Eddies   <a name='lec11' />

Readings

* Required: [Eddies: Continuously Adaptive Query Processing](./files/papers/eddies-sigmod00.pdf)

### L12 TBA    <a name='lec12' />

### L13 Hybrid Caching/UDFs    <a name='lec13' />

Readings

* Required: [Hybrid Caching](./files/papers/caching-sigmod1996.pdf)


### L14 Top-down Optimization    <a name='lec14' />

Readings

* Required: [Volcano Optimizer](./files/papers/volcanooptimizer-icde93.pdf)


### L15  RL for Join Optimization   <a name='lec15' />

Readings

* Required: Sanjay Paper
* Optional: SkinnerDB


### L16  Datalog and Recursion   <a name='lec16' />

Readings

* Required: Datalog Survey

### L17  Lineage   <a name='lec17' />

Readings:

* Required: Provenance Semirings
* Optional: Smoke

### L18  MockPC   <a name='lec18' />

Readings:

* Your assigned papers


### L19  MockPC   <a name='lec19' />

Readings:

* Your assigned papers



### L20 Materialized Views    <a name='lec20' />


### L21 Data Cubes    <a name='lec21' />

### L22 Self-tuning DBs    <a name='lec22' />


### L23 TBA    <a name='lec23' />
### L24 TBA    <a name='lec24' />
### L25 TBA    <a name='lec25' />
### L26 TBA    <a name='lec26' />


