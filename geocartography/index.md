---
layout: page
title: Navigace
---

# Kartografie pro geology

## Lekce

{% for l in site.lessons %}
1. [{{ l.title }}]({{ l.url | relative_url }})
{% endfor %}

## Úkoly

{% for a in site.assignments %}
- [{{ a.title }}]({{ a.url | relative_url }})
{% endfor %}

## Další

- [Hodnocení]({{ '/hodnoceni/' | relative_url }})
- [Data a nástroje]({{ '/data/' | relative_url }})
- [Nástroje ArcGIS Pro pro tento kurz]({{ '/arcgis-nastroje/' | relative_url }})
- [Literatura]({{ '/literatura/' | relative_url }})
