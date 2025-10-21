---
title: "CV07 – Nalezení trasy s nejnižšími náklady (ArcGIS Pro)"
layout: default
nav_order: 7
description: "Podrobný krok-za-krokem návod v ArcGIS Pro pro cost-distance analýzu s DMR5G a CLC 2018 na území obce Jistebník."
---

# CV07 – Nalezení trasy s nejnižšími náklady (ArcGIS Pro)

> **Cíl:** Naplánovat trasu **elektrického vedení** s nejnižšími kumulativními náklady mezi **hlavním uzlem (start)** a **dvěma místními uzly (cíle)** na území **obce Jistebník**. Náklady vycházejí z **CLC 2018** (land cover) a jsou **zvýšeny o sklon svahu** z **DMR5G**.

- **Vstupy:** DMR5G ImageService, CLC 2018, polygon AOI (Jistebník), body Start/Targets (`Id`).
- **Výstupy:** `cost_slope`, `costdist`, `backlink`, `path_line` + mapový layout A3 a krátký report.

---

## Obsah
- [1) Struktura projektu](#1-struktura-projektu)
- [2) Založení projektu a prostředí (Environments)](#2-založení-projektu-a-prostředí-environments)
- [3) DMR5G: ořez + výpočet sklonu](#3-dmr5g-ořez--výpočet-sklonu)
- [4) CLC 2018 → rastr → reklasifikace na cost](#4-clc-2018--rastr--reklasifikace-na-cost)
- [5) Zapojení sklonu (Raster Calculator)](#5-zapojení-sklonu-raster-calculator)
- [6) Cost Distance + Backlink](#6-cost-distance--backlink)
- [7) Cost Path jako polylinie](#7-cost-path-jako-polylinie)
- [8) Měření a reportování](#8-měření-a-reportování)
- [9) Symbolizace a layout](#9-symbolizace-a-layout)
- [10) QA checklist](#10-qa-checklist)
- [11) Výkon a časté problémy](#11-výkon-a-časté-problémy)
- [12) Bonusy](#12-bonusy)
- [13) Mini workflow tabulka](#13-mini-workflow-tabulka)
- [14) ArcPy – kompletní skript](#14-arcpy--kompletní-skript)
- [15) Odevzdání](#15-odevzdání)

---

## 1) Struktura projektu

```text
CV07_Jistebnik/
  data_raw/       # vstupní/stažená data (CLC, AOI, body)
  gdb/            # File Geodatabase (CV07.gdb)
  scratch/        # dočasné výstupy
  outputs/        # finální PDF mapy, exporty
  scripts/        # volitelné .py
```

**Doporučený SRS:** S-JTSK / Krovak East North (EPSG:5514) nebo ETRS89 / LAEA Europe (EPSG:3035). Drž se **jednoho** SRS napříč všemi vrstvami.

---

## 2) Založení projektu a prostředí (Environments)

1. **ArcGIS Pro → New → Map**.
2. Vytvoř **File Geodatabase**: `gdb/CV07.gdb`.
3. **Map Properties → Coordinate System**: nastav cílový SRS.

**Analysis → Environments…**
- **Processing Extent**: *Same as layer* → `AOI_Jistebnik`
- **Mask**: `AOI_Jistebnik` (volitelné, praktické)
- **Snap Raster**: (nastaví se po klipu DMR) → `DMR5G_clip`
- **Cell Size**: *Same as layer* → `DMR5G_clip`
- **Current/Scratch Workspace**: `gdb/CV07.gdb` / `scratch/`

> 📌 Pokud Environments upravíš až později, spusť klíčové nástroje znovu, ať se rastry sjednotí.

---

## 3) DMR5G: ořez + výpočet sklonu

**Připojení ImageService**
- URL: `https://ags.cuzk.cz/arcgis2/rest/services/dmr5g/ImageServer`  
  Přidej do mapy přes **Catalog/Insert → New ArcGIS Server/Portal**.

**Ořez (Extract by Mask)**
- **Input raster:** `dmr5g`
- **Feature mask:** `AOI_Jistebnik`
- **Output:** `DMR5G_clip`
- Poté nastav v **Environments**: **Snap Raster = `DMR5G_clip`**, **Cell Size = `DMR5G_clip`**.

**Slope (Surface → Slope)**
- **Input:** `DMR5G_clip`
- **Output measurement:** **DEGREE**
- **Z factor:** **1** (pokud XY i Z v metrech)
- **Output:** `slope_deg`

---

## 4) CLC 2018 → rastr → reklasifikace na cost

**Clip (vektor)**
- **Input Features:** `CLC2018_polygon`
- **Clip Features:** `AOI_Jistebnik`
- **Output:** `CLC2018_AOI`

**Feature to Raster**
- **Input:** `CLC2018_AOI`
- **Field:** kód třídy (např. `code_18`)
- **Cell Size:** **stejné** jako `DMR5G_clip` (typicky 5 m)
- **Output:** `CLC_ras`
- Pokud cell size nesedí, **Resample** (Nearest) na 5 m.

**Reclassify (CLC → cost)**
- **Input:** `CLC_ras` (`Value`)
- Reklasifikuj **jen třídy přítomné v AOI**. Doporučené hodnoty:
  - Urban (1xx) → **10**
  - Agriculture (2xx) → **5**
  - Forest (3.1–3.2.x) → **8**
  - Scrub/Řídké (3.3.x) → **6–7**
  - Water/Wetlands (5xx) → **20**
- **Output:** `cost_clc` (INTEGER)

> ⚠️ **NoData** nesmí zůstat uvnitř AOI. Doplň hodnoty pro všechny použité CLC kódy.

---

## 5) Zapojení sklonu (Raster Calculator)

**Raster Calculator**
```text
cost_slope = cost_clc * (1 + "slope_deg" / 90)
```

- **Output:** `cost_slope` (FLOAT)  
- Očekávané rozmezí ≈ `min(cost_clc)` → `~2 × max(cost_clc)`.

---

## 6) Cost Distance + Backlink

**Cost Distance**
- **Source data:** `Start` (hlavní uzel – bod)
- **Input cost raster:** `cost_slope`
- **Output distance raster:** `costdist`
- **Output backlink raster:** `backlink` *(nezbytné pro Cost Path)*

> ✅ `costdist` musí plynule růst od startu, bez „děr“ NoData v AOI.

---

## 7) Cost Path jako polylinie

**Cost Path As Polyline** (doporučeno)
- **Destination data:** `Targets` (2 cílové body)
- **Destination field:** `Id`
- **Input cost distance:** `costdist`
- **Input backlink:** `backlink`
- **Path type:** **EACH_CELL**
- **Output:** `path_line`

> Alternativně **Cost Path** (rastrově) + **Raster to Polyline** převod.

---

## 8) Měření a reportování

**Délka trasy**
- **Add Geometry Attributes**
  - **Input:** `path_line`
  - **Geometry:** **LENGTH**
  - **Unit:** **Meters**
  - Přidá `LENGTH_M`.

**Kumulativní náklad (cost) v cílech**
- **Extract Values to Points**
  - **Input points:** `Targets`
  - **Input raster:** `costdist`
  - **Output:** `Targets_cost`
  - Pole `RASTERVALU` = kumulativní náklad od `Start` k danému cíli (podelně nejlevnější trasy).

---

## 9) Symbolizace a layout

**Mapová kompozice**
- `costdist`: Stretch/Classified (7–9 tříd), neutrální paleta.
- `path_line`: výrazná linka (2–3 pt).
- `AOI`: jemná hranice.
- `Start/Targets`: kontrastní symboly + popisky (`Id`).

**Layout A3**
1. **Insert → New Layout → A3** (Landscape/Portrait).
2. **Map Frame**, **Legend**, **Scale Bar**, **North Arrow**.
3. Tiráž: název, autor, datum, zdroje (DMR5G – ČÚZK, CLC 2018 – CENIA).
4. **Share → Export Layout → PDF (300 dpi)**.

---

## 10) QA checklist

- [ ] **Environments:** Extent = AOI, Mask = AOI, Snap = DMR5G_clip, CellSize = DMR5G_clip
- [ ] **SRS:** jednotné napříč vektor/raster (bez on-the-fly chyb)
- [ ] **CLC reclass:** žádné NoData v `cost_clc`/`cost_slope`
- [ ] **costdist:** plynulý gradient od startu
- [ ] **Trasa:** obchází „drahé“ plochy (urban, voda) a strmé svahy
- [ ] **Měření:** délka (`LENGTH_M`) a kumulativní cost (`RASTERVALU`) zaznamenány
- [ ] **Metadata:** zdroje dat, datum, reclass tabulka (verze)

---

## 11) Výkon a časté problémy

- **Pomalý výpočet:** dbej na cell size (~5 m) a sjednocené Environments.
- **Křivé linie po převodu:** zkontroluj Snap Raster a toleranci (XY Resolution/Tolerance).
- **Trasa přes vodu:** zvyšení cost (např. 30–50) nebo bariéry (Set Null + pokročilé postupy).
- **NoData v cost_slope:** chybějící třídy v reclass mapě – doplň je.

---

## 12) Bonusy

**Ochranná území / zóny**
```text
cost_final = Con( Protected == 1, cost_slope * 1.5, cost_slope )
```
Pak použij `cost_final` v Cost Distance.

**Citlivostní analýza CLC**
- Varianty reclass (např. Forest 7/8/10) a porovnání dopadu na trasu.

**Oddělené výstupy pro každý cíl**
- Filtrem `Id` spusť **Cost Path As Polyline** dvakrát → srovnání tras.

---

## 13) Mini workflow tabulka

| Krok | Nástroj | Parametry (klíčové) | Výstup |
|---|---|---|---|
| 1 | Extract by Mask | DMR5G + AOI | `DMR5G_clip` |
| 2 | Slope | DEGREE, Z=1 | `slope_deg` |
| 3 | Feature to Raster | field=`code_18`, cell=5 m | `CLC_ras` |
| 4 | Reclassify | CLC→cost | `cost_clc` |
| 5 | Raster Calculator | `cost_clc*(1+slope_deg/90)` | `cost_slope` |
| 6 | Cost Distance | Source=`Start`, Cost=`cost_slope` | `costdist`,`backlink` |
| 7 | Cost Path as Polyline | Dest=`Targets`, `Id`, EACH_CELL | `path_line` |
| 8 | Add Geometry Attr. | LENGTH (m) | délka |
| 9 | Extract Values to Points | `Targets` × `costdist` | kumulativní cost |

---

## 14) ArcPy – kompletní skript

> Uprav názvy vrstev a cesty. Vyžaduje Spatial Analyst.

```python
import arcpy
from arcpy.sa import *

arcpy.CheckOutExtension("Spatial")

# Cesty uprav dle sebe:
gdb = r"C:\...\CV07_Jistebnik\gdb\CV07.gdb"
scratch = r"C:\...\CV07_Jistebnik\scratch"
arcpy.env.workspace = gdb
arcpy.env.scratchWorkspace = scratch

# Sjednocení prostředí
arcpy.env.extent = "AOI_Jistebnik"
arcpy.env.mask = "AOI_Jistebnik"

# 1) DMR5G -> slope
# Předpoklad: máš už DMR5G_clip (Extract by Mask)
arcpy.env.snapRaster = "DMR5G_clip"
arcpy.env.cellSize = "DMR5G_clip"

Slope("DMR5G_clip", "DEGREE", 1).save("slope_deg")

# 2) CLC reclass (příklad: uprav na reálné kódy v AOI)
# Doporučeno použít RemapValue s explicitními kódy, zde ukázkově RemapRange
remap = RemapRange([
    [100,200,10],  # Urban
    [200,300,5],   # Agriculture
    [310,333,8],   # Forest 3.1–3.2.x
    [334,340,7],   # Scrub 3.3.x
    [500,600,20]   # Water/Wetlands
])
Reclassify("CLC_ras", "Value", remap, "NODATA").save("cost_clc")

# 3) Sklonový faktor
cost_slope = Raster("cost_clc") * (1 + Raster("slope_deg") / 90.0)
cost_slope.save("cost_slope")

# 4) Cost Distance + Backlink
cd, bl = CostDistance("Start", "cost_slope", "", "", "", "BACKLINK")
cd.save("costdist")
bl.save("backlink")

# 5) Cost Path jako polyline (pro oba cíle najednou)
CostPathAsPolyline("Targets", cd, bl, "EACH_CELL", "Id").save("path_line")

# 6) Délka v metrech
arcpy.management.AddGeometryAttributes("path_line", "LENGTH", "METERS")

# 7) Kumulativní cost v cílech
ExtractValuesToPoints("Targets", "costdist", "Targets_cost", interpolate_values="NONE")
```

---

## 15) Odevzdání

- **PDF (A3)** s mapou: AOI, `costdist`, `path_line`, start/cíle, legenda, měřítko, severka, tiráž.
- **Report (1–2 str.)**: data a zdroje, reclass tabulka, vliv sklonu (bez vs. se sklonem), délky tras, kumulativní cost.
- **GDB/GPKG**: `cost_slope`, `costdist`, `backlink`, `path_line`, `Targets_cost` (+ případně `path_ras`).

---

### Poznámka k re-klasifikaci CLC
Klíčové je přizpůsobit cost hodnoty **skutečným třídám v AOI** (statistika CLC). Pokud chceš, snadno sem přidám **konkrétní finální tabulku** pro Jistebník (pošleš seznam kódů nebo výřez).
