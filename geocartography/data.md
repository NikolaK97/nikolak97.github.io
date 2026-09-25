---
layout: page
title: Data a nástroje
---

# Data a nástroje

Zdroje geologických dat pro cvičení a úkoly. Před použitím vždy zkontrolujte aktuální licenční podmínky na stránkách poskytovatele.

## Česká geologická služba (ČGS)

Hlavní zdroj pro celý kurz. Většina služeb je dostupná jako WMS/WFS, které lze načíst přímo do ArcGIS Pro (Insert → Connections → Server).

- **Geologická mapa 1:50 000** (GeoČR50) – základní areálový podklad, WMS
- **Geologická mapa 1:500 000** – pro cvičení generalizace
- **Vrtná databáze / Geologicky dokumentované objekty (GDO)** – vrty, dokumentační body; základ pro interpolaci
- **Sesuvy a svahové nestability** – registr, WMS
- **Radonový index geologického podloží** – pro Úkol 1
- **Hydrogeologické rajony** – územní jednotky pro kartogramy
- **Geochemický atlas ČR / geochemické mapování** – obsahy prvků v půdách a sedimentech
- **Mapová aplikace Geovědní mapy** – náhled bez GIS
- **Archiv map** – historické geologické mapy pro storymapu

Adresy služeb hledejte na `geology.cz` v sekci mapové služby; mění se, proto zde neuvádíme konkrétní URL.

## ČÚZK

- **DMR 5G** – digitální model reliéfu pro stínování a řezy
- **ZABAGED** – vodstvo, komunikace, sídla jako topografický podklad
- **Ortofoto** – pouze potlačeně pod geologií (lekce 08)
- Základní mapy ČR jako WMS

## ČHMÚ

- povodí, vodoměrné stanice, hydrologická data

## Mezinárodní zdroje

- **ICS – International Chronostratigraphic Chart** – oficiální barvy stratigrafických jednotek (ke stažení včetně RGB)
- **OneGeology** – geologické mapy světa přes WMS
- **USGS** – FGDC Digital Cartographic Standard for Geologic Map Symbolization (referenční standard značek)
- **BGS** – OpenGeoscience, dobrý příklad zveřejňování geologických dat

## Barvy

- **ColorBrewer** – sekvenční, divergentní a kvalitativní stupnice
- **Scientific colour maps (Crameri)** – vhodné pro barvoslepé, ke stažení jako `.clr`/RGB tabulka
- **Viridis** – výchozí pro rastry

## Software

- **ArcGIS Pro** (univerzitní licence) – hlavní nástroj kurzu, viz [Nástroje ArcGIS Pro]({{ '/geocartography/arcgis-nastroje/' | relative_url }})
- Rozšíření: *Spatial Analyst* (interpolace), *Geostatistical Analyst* (kriging s mapou chyby), *3D Analyst* (TIN, profily)
- Styly: *Geology 24K* (Esri), FGDC geologické značky (USGS), ICS stratigrafické barvy jako `.stylx`
- **ArcGIS Online / StoryMaps** – publikace pro Úkol 3
- **Stereonet** (Allmendinger) nebo **mplstereonet** (Python) – stereogramy a růžice
- **ArcGIS StoryMaps** nebo **StoryMapJS** – Úkol 3
- **Inkscape** – finální úpravy exportovaného SVG

## Datové balíčky pro cvičení

Pro studijní výřez připraví vyučující balíček (`data/vyrez.gdb` jako File Geodatabase) s vrstvami:

- `geologie_25k`, `geologie_50k`, `geologie_500k` – polygony s poli `index`, `souvrstvi`, `skupina`, `utvar`, `litologie`
- `zlomy`, `hranice` – linie s polem `typ` (zjistena / predpokladana / zakryta)
- `mereni` – body s `dip_dir`, `dip`, `typ`
- `vrty` – body s `hloubka`, `baze_kvarteru`, `mocnost_q`
- `lomy` – body s modálním složením a `n_samples`
- `geochemie` – body s obsahy prvků
- `hg_rajony`, `povodi` – polygony

Balíček není součástí repozitáře (velikost, licence); distribuuje se přes univerzitní úložiště.
