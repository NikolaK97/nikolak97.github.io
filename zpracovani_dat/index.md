---
layout: page
title: Zpracování dat v GIS – cvičení
permalink: /zpracovani_dat/
---

<ol>
{% for i in (1..12) %}
  {% capture num %}{% if i < 10 %}0{{ i }}{% else %}{{ i }}{% endif %}{% endcapture %}
  <li><a href="{{ '/zpracovani_dat/cviceni-' | append: num | relative_url }}">Cvičení {{ i }}</a></li>
{% endfor %}
</ol>
