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

{% assign reviews2 = site.data.reviews2 | reverse %}

{% for r in reviews2 %}
  <p class="review">
  <h3>{{r.Timestamp}} {{r.Name}}</h3>

  <div class="block">{{ r.summary | newline_to_br | safe }}</div>
  <div class="block">{{ r.strengths | newline_to_br | safe }}</div>
  <div class="block">{{ r.weaknesses | newline_to_br | safe }}</div>
  <div class="block">{{ r.details | newline_to_br | safe }}</div>

  </p>
  <hr/>
{% endfor %}


