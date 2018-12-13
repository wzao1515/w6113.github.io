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


* Classics
  * codd
  * Architecture of a DB
  * system R
  * Gamma 
* Basics of Plan Execution / optimization
  * volcano
  * eddies
  * SEDA?
  * dbmin
* Languages
  * SchemaSQL
* Recovery
  * ARIES
* architectures
  * C-Store + ten years later
  * Hekaton
  * MR/Spark
  * Naiad
  * Query compilation + Tarik
* Concurrency Control
  * MVCC vs OCC vs Pessimistic vs CALM
* Slightly modern stuff
  * implementing data cubes efficiently
  * CONTROL
* Scheduling
* Networking
* Caching
  

