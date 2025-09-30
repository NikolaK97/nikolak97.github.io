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

## Zadání
Studenti určí 1–3 nejvhodnější oblasti pro letiště s ohledem na:
- vzdálenost od sídel,
- dostupnost dopravní infrastruktury,
- terénní podmínky,
- ochranu přírody a vod,
- omezení nadmořské výšky.

---

# Postup řešení – Dopravní systémy (ArcGIS Pro)

> Tato stránka převádí původní návod do podoby čitelné na GitHub Pages.
> Vše je psáno pro **ArcGIS Pro** a projekci **S-JTSK / Krovák East North (EPSG:5514)**.

## 1) Vytvoření projektu

- Otevři **ArcGIS Pro → New Project → Map**.  
- Projekční systém projektu nastav na **S-JTSK / Krovák East North (EPSG:5514)**.  
  - *Project → Options → Map and Scene → Spatial Reference → EPSG 5514.*
- Ulož projekt do nové pracovní složky (např. `Letiste_MSK`).

## 2) Načtení a příprava dat

Načti vrstvy (stažené SHP/FGDB, WFS, WMS nebo Living Atlas):

- Hranice Moravskoslezského kraje (ArcČR).
- Sídelní oblasti – obce + větší města (ArcČR).
- Silnice D a I – z ŘSD nebo OSM (*motorway, trunk*).
- Železnice – hlavní/koridorové tratě (SŽ/OSM).
- DEM (DMR 5G) – ČÚZK (nebo Copernicus DEM 30 m).
- Chráněná území + Natura 2000 – AOPK.
- Hydrologie (řeky, plochy) – DIBAVOD / OSM.

**Předzpracování:**

- Použij nástroj **Clip** (*Analysis → Tools → Clip*), vyřízni všechny vrstvy na hranici Moravskoslezského kraje.
- Vše ulož do **File Geodatabase** projektu.
- Zkontroluj projekci – všechny vrstvy musí být v **EPSG:5514**.

**Environment settings** (nastav jednou pro všechny nástroje):

- *Processing Extent:* hranice kraje.  
- *Cell Size:* 25 m.  
- *Snap Raster:* DEM.  
- *Mask:* hranice kraje.

## 3) Kritéria – příprava vstupů

### 3.1 Sídelní oblasti – „zakázané zóny“
- Vyber velká města (Ostrava, Opava, Karviná, Havířov, Frýdek-Místek, Třinec).
- Nástroj **Buffer**:  
  - velká města → **10 000 m**,  
  - ostatní obce → **3 000 m**.
- Spoj buffery pomocí **Dissolve**.
- Převod na raster: **Polygon to Raster** → hodnota = `1` (*no-go*).

### 3.2 Dopravní infrastruktura
**Dálnice a rychlostní silnice:**  
- Filtrování: vyber jen D a I (OSM *motorway/trunk*).
- **Euclidean Distance** → vzdálenost od komunikace.
- **Reclassify** (hodnoty 1–9):  
  - 0–1 km → 9,  
  - 1–3 km → 7,  
  - 3–5 km → 5,  
  - 5–10 km → 3,  
  - 10 km → 1.

**Železnice (koridorové tratě):**  
- Totéž (*Euclidean Distance → Reclassify* stejnou škálou).

### 3.3 Terén
**Sklon:**  
- Z DEM vytvoř **Slope** (jednotky: °).  
- **Reclassify:**  
  - 0–1° → 9,  
  - 1–2° → 8,  
  - 2–3° → 7,  
  - 3–4° → 5,  
  - 4–5° → 3,  
  - ≥5° → 1.

**Nadmořská výška:**  
- DEM → **Extract by Attributes:** `Value ≤ 500 m`.  
- Hodnoty nad 500 m nastav jako **NoData**.

### 3.4 Chráněná území
- Slouč všechny typy chráněných ploch (NPR, PR, CHKO, Natura 2000).
- **Dissolve** → **Polygon to Raster** (hodnota `1`).  
- Použij jako masku (**NoData**).

### 3.5 Hydrologie
- Řeky a vodní plochy → **Buffer 1000 m**.  
- **Polygon to Raster** (hodnota `1`).  
- Použij jako masku (**NoData**).

## 4) Vícekriteriální vhodnostní analýza
- Otevři **Weighted Overlay** (*Spatial Analyst*).
- Nastav **Processing Mask**:  
  *(elevace ≤ 500 m) ∧ ¬(sídelní buffery) ∧ ¬(chráněná území) ∧ ¬(hydrologické buffery).*

**Vstupy a váhy:**  
- Dostupnost k dálnici … **40 %**  
- Dostupnost k železnici … **20 %**  
- Sklon terénu … **40 %**  
- *Scale: 1–9 (vyšší = vhodnější).*

Spusť → získáš raster vhodnosti.

## 5) Výběr kandidátních lokalit
- Vyber **nejvhodnější hodnoty (≥ 8)**.
- **Raster to Polygon** → převedení na vektory.
- Použij **Eliminate** nebo **Eliminate Polygon Part** – odstraň malé polygony (**< 1 km²**).
- Spočítej plochu (*Calculate Geometry*).
- Přidej vzdálenosti k dálnici a železnici (*Near*).
- Vyber **2–3 nejvhodnější** lokality podle plochy a skóre.

## 6) Kontroly a prezentace
Zkontroluj, že žádná vybraná oblast nezasahuje do:
- chráněných území,  
- sídelních bufferů,  
- vodních bufferů,  
- oblastí **> 500 m n. m.**

**Mapová výstupní kompozice (Layout):**
- Mapa vhodnosti (1–9).  
- Vybrané lokality (polygony).  
- Dálnice, železnice, sídla.  
- Legenda, měřítko, severka, zdroje dat.

**Export:** PDF.

## 7) Odevzdání 
- **Mapa** (PDF s layoutem).  
- **Geoprocessing ModelBuilder** nebo exportovaný **History** kroků.  
- **Krátká zpráva** (1–2 strany):  
  - popis kritérií,  
  - váhy,  
  - zdůvodnění výběru lokality.

---
