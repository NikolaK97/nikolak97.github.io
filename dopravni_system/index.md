---
layout: page
title: Dopravní systémy – cvičení
permalink: /dopravni_system/
---

<ol>
{% for i in (1..2) %}
  {% capture num %}{% if i < 10 %}0{{ i }}{% else %}{{ i }}{% endif %}{% endcapture %}
  <li><a href="{{ '/dopravni_system/cviceni-' | append: num | relative_url }}">Cvičení {{ i }}</a></li>
{% endfor %}
</ol>
