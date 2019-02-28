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


{% assign reviews3 = site.data.reviews3 | reverse %}
{% for r in reviews3 %}
  <p class="review">
  <h3>{{r.Timestamp}} {{r.Name}}</h3>

  <div class="block"><h5>{{ r.Role | safe }}</h5></div>
  <div class="block">{{ r.Summary | newline_to_br | safe }}</div>
  <div class="block">{{ r.Response | newline_to_br | safe }}</div>
  <div class="block">{{ r.Comments | newline_to_br | safe }}</div>
  </p>
  <hr/>
{% endfor %}




