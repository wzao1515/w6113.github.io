---
layout: page
---
<style>
p.review {
  border-left: 2px solid grey;
}
</style>

{% assign reviews = site.data.reviews | reverse %}
{% assign reviews2 = site.data.reviews2 | reverse %}

{% for r in reviews2 %}
  <p class="review">
  <h3>{{r.Timestamp}} {{r.Name}}</h3>

  {{ r.summary | newline_to_br | safe }}
  <br/>
  {{ r.strengths | newline_to_br | safe }}
  <br/>
  {{ r.weaknesses | newline_to_br | safe }}
  <br/>
  {{ r.details | newline_to_br | safe }}

  </p>
  <hr/>
{% endfor %}


{% for r in reviews %}
  <p class="review">
  <h3>{{r.Timestamp}} {{r.Name}}</h3>

  {{ r.Review | newline_to_br | safe }}

  </p>
  <hr/>
{% endfor %}

