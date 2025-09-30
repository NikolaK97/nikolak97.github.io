---
layout: page
title: Cvičení 02 — Výběr lokality letiště v Moravskoslezském kraji
permalink: /dopravni_system/cviceni-02/
---

**Cvičení 02 – Prostorová analýza: návrh lokality pro nové letiště**

**Cíl:**  
Najít vhodné lokality pro nové regionální letiště v **Moravskoslezském kraji** pomocí vícekriteriální analýzy v ArcGIS Pro.

**Použité technologie:** ArcGIS Pro (Spatial Analyst), modelování vhodnosti (Weighted Overlay).

---

## 1) Zadání
Studenti určí 1–3 nejvhodnější oblasti pro letiště s ohledem na:
- vzdálenost od sídel,
- dostupnost dopravní infrastruktury,
- terénní podmínky,
- ochranu přírody a vod,
- omezení nadmořské výšky.

---

## 2) Potřebná data
- **Administrativní hranice**: Moravskoslezský kraj (ČÚZK / RÚIAN).
- **Sídelní oblasti**: RÚIAN (obce, zastavěná území).
- **Silniční síť**: ŘSD / OpenStreetMap (dálnice, rychlostní silnice).
- **Železniční síť**: Správa železnic nebo OSM (hlavní tratě).
- **DEM**: ČÚZK DMR 5G (5 m) nebo Copernicus DEM (25 m).
- **Chráněná území**: AOPK ČR (NATURA 2000, CHKO, NPR/PR).
- **Hydrologie**: DIBAVOD (řeky, vodní plochy) nebo OSM.

---

## 3) Kritéria (doporučená)
- **Sídelní oblasti**  
  - min. 10 km od velkých měst (Ostrava, Opava, Havířov, Frýdek-Místek, Karviná, Třinec),  
  - min. 3 km od menších sídel.
- **Dopravní dostupnost**  
  - do 5 km od dálnice nebo rychlostní silnice,  
  - do 5 km od hlavní železnice.
- **Terén**  
  - sklon ≤ 5° (DEM → Slope → Reclassify),  
  - nadmořská výška ≤ 500 m n. m.
- **Ochrana území**  
  - vyloučit chráněná území a NATURA 2000,  
  - buffer 1 km od vodních ploch a toků.

---

## 4) Postup analýzy v ArcGIS Pro

### 4.1 Příprava projektu
- Nový projekt → nastavit souřadnicový systém **EPSG:5514 (S-JTSK / Krovák)**.
- Načíst data a **Clip** na hranici kraje.
- Nastavit **Environment**:  
  - Processing Extent = hranice kraje  
  - Cell Size = 25 m  
  - Snap Raster = DEM  
  - Mask = hranice kraje

### 4.2 Tvorba kritérií
1. **Sídelní no-go zóna:** Buffer (10 km velká města, 3 km ostatní), sloučit, převést na raster.  
2. **Silnice:** Euclidean Distance → Reclassify (0–1 km=9, 1–3=7, 3–5=5, 5–10=3, >10=1).  
3. **Železnice:** stejně jako silnice.  
4. **Terén:** DEM → Slope → Reclassify (≤5° nejlepší).  
5. **Elevace:** Extract by Attributes (≤500 m n. m.).  
6. **Chráněná území:** polygon dissolve → raster maska.  
7. **Vodní plochy/toky:** Buffer 1 km → maska.

### 4.3 Weighted Overlay
- Vstupy (integer rastry 1–9):  
  - Silnice (váha 40 %)  
  - Železnice (20 %)  
  - Sklon (40 %)  
- Vyloučit maskou: chráněná území, voda, sídla, nad 500 m n. m.  
- Výstup = raster vhodnosti (1–9).

---

## 5) Výběr výsledné lokality
1. Threshold: vyber oblasti s hodnotou ≥8.  
2. Region Group → Raster to Polygon.  
3. Eliminuj malé polygony (<1 km²).  
4. Zkontroluj dostupnost k silnici/železnici a plochu.  
5. Vyber top 2–3 kandidátní lokality.

---

## 6) Výstupy
- Mapa vhodnosti (barevná škála 1–9).  
- Polygon vybraných lokalit (vektor).  
- Kompoziční mapa (Layout A3) s:  
  - mapou vhodnosti,  
  - přehledem kritérií,  
  - legendou, měřítkem, severkou.  
- Krátký komentář (1–2 strany): metodika, výsledky, limity.

---

## 7) Otázky k diskuzi
- Jak by výsledek změnila jiná váha kritérií?  
- Jaký vliv by měly další faktory (meteorologie, hluk, vojenské prostory)?  
- Je reálná dostupnost dat a jejich přesnost pro skutečné rozhodování?
