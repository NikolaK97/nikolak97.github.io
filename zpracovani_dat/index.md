---
layout: page
title: Zpracování dat v GIS – cvičení
permalink: /zpracovani_dat/
---


<ol>
{%- for i in (1..10) -%}
  {%- assign num = i | prepend: '0' | slice: -2, 2 %}
  <li><a href="{{ '/zpracovani_dat/cviceni-' | append: num | relative_url }}">Cvičení {{ i }}</a></li>
{%- endfor %}
  <li><a href="{{ '/zpracovani_dat/hodnoceni' | relative_url }}">Hodnocení</a></li>
</ol>
