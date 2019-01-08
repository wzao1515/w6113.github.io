---
layout: index
---

#### Overview

This course is intended as an advanced graduate-level course in database systems research.  
The content will cover classic and modern database systems research.  Topics will range from classic database system design, modern optimizations in single-node and multi-node settings, data cleaning and explanation, and data provenance.

The class places a _heavy emphasis on paper reading and writing good paper reviews_.   The point is to practice reading papers critically, writing proper reviews, implementing ideas in research papers, and conducting research.  As such, students will be expected to read papers in depth, complete assignments based ideas from the readings, and conduct a semester-long research project.

Students are expected to be comfortable with a range of programming languages, reading code, actively participate in discussions, and presenting.

<small style="color: grey">Course capped at 25.  If waitlist is huge, an assignment will be used to choose participants.</small>



#### Recent Announcements

#### Schedule

Topics will cover a subset of papers from [the redbook](http://www.redbook.io), as well as modern provenance/lineage, data analysis, cleaning, and perhaps databases+ML.

<table class="table table-striped schedule">
  <thead>
  <tr>
    <th class="idx" style="width: 3em; max-width:3em;"></th>
    <th class="date" style="width: 4em; max-width: 4em;"> <p> <span>Date </span> </p> </th>
    <th style="min-width: 30%;"> <p> <span>Topic </span> </p> </th>
    <th style="width: 25%"> <p> <span>Readings </span> </p> </th>
    <th style="width: 10%;"> <p> <span>Assigned</span> </p> </th>
    <th style="width: 10%;"> <p> <span>Due</span> </p> </th>
  </tr>
  </thead>
{% assign idx = 0 %}

{% for r in site.data.schedule %}
  {% assign idx = idx | plus: 1  %}
  <tr style="background-color: {{r.color}}; ">
    <td class="idx">L{{idx}}</td>
    <td class="date">{{r.date}}</td>
    <td class="slug">
      <a href="{{r.link}}">
        {{r.slug}}
      </a> 
      <br/>
      {{r.title}}

      {% if r.optional %}<br/>{% endif%}
      {{r.optional | safe}}
      </td>
    <td class="readings">{{r.readings | safe}}</td>
    <td>{{r.assigned | safe}}</td>
    <td>{{r.due | safe}}</td>
  </tr>
{% endfor %}
</table>


#### Tentative list of papers

Each week we will discuss classic research and modern takes.


* Intro
  1. Syllabus + life of a query + meta stuff
    * Codd
  2. DB primer/background
    * Storage hierarchies and [numbers you should know](https://gist.github.com/hellerbarde/2843375)
    * The importance of simple equations
    * 5 minute rule
    * Other principles
      * Buffers/Caches/Prefetching
      * Temporal/Spatial Locality
      * Skew and fast paths
      * Communication
        * messaging, shared memory/space
* Basics
  1. [Architecture of a DB](http://db.cs.berkeley.edu/papers/fntdb07-architecture.pdf)
  2. [SystemR retrospective](http://db.cs.berkeley.edu/cs262/SystemR-annotated.pdf) or
     [Design of Postgres](http://db.cs.berkeley.edu/cs286/papers/postgres-cacm1986.pdf)
* Indexes
  1. R-trees
  2. [GIST-trees](http://db.cs.berkeley.edu/papers/vldb95-gist.pdf)
* Joins
  1. Shapiro: Join Processing in Database Systems with Large Main Memories  
  2. Ripple joins?
* Execution
  1. Volcano
  2. Eddies
* Lower level Optimizations
  * DBMin
  * [http://db.cs.berkeley.edu/cs286/papers/caching-sigmod1996.pdf](Hybrid Caching)
* Query Plan Optimization
  * Classics
    * Selinger
    * Cascades
    * Volcano Optimizer
  * Modern stuff
    * Using RL for join optimization
* Languages
  1. datalog
  2. schemaSQL
* Systems Architectures
  1. C-Store
    * Column-Stores vs. Row-Stores: How Different Are They Really?
  2. H-store/Hekaton
* Systems Architectures
  1. Query Compilation
    * T. Neumann, Efficiently Compiling Efficient Query Plans for Modern Hardware
    * K. Krikellas, et al., Generating Code for Holistic Query Evaluation, in ICDE, 2010
    * How to Architect a Query Compiler, Revisited
  2. ??
* Materialization for physical database design
  1. Materialized Views
    * Updating Materialized Views
    * Surajit's paper
      * http://www.vldb.org/conf/2007/papers/special/p3-chaudhuri.pdf
  2. Datacubes
    * Jim Gray paper
    * [Implementing data cubes efficiently](http://db.cs.berkeley.edu/cs286/papers/datacube-sigmod1996.pdf)
* Approx Query Processing
  1. Sampling basics and challenges
  2. CONTROL, Blink, Sample-and-seek, AQP++
* Streaming
  1. Windowed Streaming
    * DataFlow paper
    * CQL/Stream project
    * TelegraphCQ
  2. Complex event processing
    * SASE
* X in DBs
  * ML in SQL
    * MADLib
    * SystemML
  * Graph in SQL
    * Jignesh's papers
    * Vertexica
* X in DBs
  1. Vis in SQL


<!--

* Distributed Query Processing
  1. Gamma: distributed Joins
  2. 
* Concurrency Control
  1. Serializability, Linearizability, tec overview
  2. MVCC and OCC
* Basics of Plan Execution / optimization
  * SEDA?
  * dbmin
* Languages
  * SchemaSQL
  * datalog
* Storage
* Recovery
  * ARIES
  * H-store
* Architectures
  * C-Store + ten years later
  * Hekaton
  * MR/Spark
  * Naiad
  * Query compilation + Tarik
* Concurrency Control
  * MVCC vs OCC vs Pessimistic vs CALM
* Slightly modern stuff
  * In network execution?
  * ML in SQL
    * MADLib
    * SystemML
  * Graph in SQL
    * Jignesh's papers
    * Vertexica
* Scheduling
* Networking
-->
