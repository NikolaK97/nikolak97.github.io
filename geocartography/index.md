---
layout: page
title: Navigace
---

# Kartografie pro geology

Cvičení z tematické kartografie pro bakalářské studium geologie. Zásady tvorby map jsou obecné, ale všechny příklady, data a úkoly vycházejí z geologické praxe: základní geologické mapy, vrtné databáze, geochemie, strukturní měření, hydrogeologie.

Kurz vychází z české tradice tematické kartografie (Kaňok 1999; Voženílek, Kaňok et al. 2011) a z mezinárodních standardů geologického mapování (FGDC 2006; Lisle et al. 2011). Každá lekce odkazuje na literaturu, ze které čerpá; úplný seznam je na stránce [Literatura]({{ '/literatura/' | relative_url }}).

Opakovaný motiv celého kurzu: **hranice na geologické mapě je hypotéza, ne pozorování.** Mapa je interpretace a kartografie je způsob, jak tuto interpretaci sdělit poctivě.

Celý kurz pracuje s jedním **studijním výřezem** (doporučeno: Moravský kras a okolí, list 24-41 Vyškov / 24-23 Protivanov, nebo jiné území dle vyučujícího). Studenti tak vidí stejnou oblast z deseti různých kartografických pohledů.

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
