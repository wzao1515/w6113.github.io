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

  <div>Response:</div>
  <p class="block">{{ r.review | newline_to_br | safe }}</p>
  <div>Comments:</div>
  <p class="block">{{ r.comments | newline_to_br | safe }}</p>
  </p>
  <hr/>
{% endfor %}




