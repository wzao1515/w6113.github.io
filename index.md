---
layout: index
---

#### Overview

This course will cover classic and modern database research papers.  It will place a heavy focus on paper reading, writing good paper reviews.  Students will complete assignments based on some of the readings, and conduct a semester-long project.

Students are expected to actively participate in discussions and present a paper in class. 
<small style="color: grey">Course capped at 25.  If waitlist is huge, a small assignment will be used to choose participants.</small>



#### Recent Announcements

#### Schedule

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



