---
order: 5
display-title: "Annotation"
btn-class: "btn-annotation btn-pill"
---

<h1>Pathway Figures by Annotation</h1> 
<p>Explore ontologies below to identify pathway figures tagged with related terms.</p>
{% assign type-group = site.annotations | group_by: "type" | reverse %}
{% for type in type-group %}
<section class="facet">
  <div class="facet-header">
    <h2 class="facet-title">{{ type.name }}</h2>
  </div>
  <div class="facet-body" id="{{ type.name }}">
      {% for annot in type.items %}
        <a class="btn btn-sm btn-pill btn-annotation" href="{{annot.url}}">{{annot.value}}</a>
      {% endfor %}
  </div>
</section>
{% endfor %}