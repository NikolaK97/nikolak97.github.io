---
layout: page
title: Nástroje ArcGIS Pro pro tento kurz
---

# Nástroje ArcGIS Pro pro tento kurz

Základy ArcGIS Pro (projekt, načtení dat, atributová tabulka, základní symbologie, layout) se předpokládají. Tato stránka je rychlá reference k tomu, co je pro kurz specifické – abyste v hodině nehledali.

## Souřadnicový systém a data

- Projekt i mapy v **S-JTSK / Krovak East North (EPSG:5514)**.
- WMS/WFS ČGS a ČÚZK: Insert → Connections → Server → New WMS/WFS Server.
- Datový balíček kurzu: File Geodatabase `vyrez.gdb` (vrstvy viz [Data]({{ '/geocartography/data/' | relative_url }})).
- Tabulka se souřadnicemi: *XY Table To Point*.

## Symbologie

| Potřeba | Kde |
|---|---|
| geologické značky (sklon/směr, zlomy, hranice) | Symbology → Gallery → přidat styl **Geology 24K** (Esri) nebo FGDC styl (USGS) |
| rotace značky podle azimutu | Symbology → *Vary symbology by attribute* → Rotation, pole `dip_dir`, typ **Geographic** |
| stratigrafické barvy | styl ICS (`.stylx`) poskytnutý kurzem, nebo ruční RGB |
| litologické šrafy | výplň typu *Hatched fill* / *Picture fill* ve stylu Geology 24K |
| typ hranice (zjištěná/předpokládaná/zakrytá) | Unique values podle pole `typ`, styly linií plná/čárkovaná/tečkovaná |
| kartogram s klasifikací | Symbology → **Graduated colors**, metody: Equal interval, Quantile, Natural breaks, Standard deviation, Geometric interval, Manual; histogram přímo v panelu |
| koláčové / sloupcové diagramy | Symbology → **Charts** (Pie, Bar, Stacked) |
| barvoslepost | View → **Color Vision Deficiency Simulator** |

## Zpracování (Geoprocessing)

| Lekce | Nástroj |
|---|---|
| 05 – agregace bodů do jednotek | *Summarize Within*, *Spatial Join*, *Summary Statistics* |
| 06 – histogram, statistika | Create Chart → Histogram; *Summary Statistics* |
| 07 – růžice orientací | Create Chart → Bar chart, polární rozložení; nebo Stereonet a vložit obrázek |
| 09 – interpolace | *Create TIN* + *TIN Contour* (3D Analyst); *IDW*, *Spline*, *Contour* (Spatial Analyst); **Geostatistical Wizard** → Ordinary Kriging + Prediction Standard Error (Geostatistical Analyst) |
| 09 – ořez | *Minimum Bounding Geometry* (Convex hull) → *Buffer* → *Extract by Mask* |
| 09 – řez | *Interpolate Shape* + Elevation Profile; nebo konstrukce v layoutu |
| 10 – generalizace | *Dissolve*, *Eliminate*, *Simplify Polygon*, *Smooth Polygon*, *Simplify Line*; topologie: *Must Not Overlap*, *Must Not Have Gaps* |

## Layout

- Insert → New Layout, A3 landscape.
- Legenda: vypnout *Synchronize with map* a ručně přeřadit položky do **stratigrafického pořadí** (lekce 02); položky lze přejmenovat přes Layer → Symbology labels.
- Měřítko vždy **grafické** (Scale bar), ne jen text.
- Vedlejší mapka: druhý Map Frame + *Extent indicator* pro rámec v hlavní mapě.
- Řez, růžice, histogram: Insert → Picture nebo Chart Frame.
- Export → PDF, 300 dpi, vektorové výstupy zachovat.

## Odevzdávání

- Úkol 2 se odevzdává jako **Project Package (.ppkx)** včetně dat (Share → Project → Package Project).
- Storymapa (Úkol 3): mapy publikovat jako web layers do ArcGIS Online (Share → Web Layer), poté vložit do ArcGIS StoryMaps.

## Poznámka k QGIS

Kurz běží v ArcGIS Pro. Vše lze udělat i v QGIS, ale vyučující při cvičení podporuje pouze ArcGIS Pro.
