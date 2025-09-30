---
layout: page
title: Sekce A – cvičení
permalink: /Geoinformatika/
---

<ol>
{% for i in (1..12) %}
  {% capture num %}{% if i < 10 %}0{{ i }}{% else %}{{ i }}{% endif %}{% endcapture %}
  <li><a href="{{ '/Geoinformatika/cviceni-' | append: num | relative_url }}">Cvičení {{ i }}</a></li>
{% endfor %}
</ol>
