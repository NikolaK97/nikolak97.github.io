---
title: "CV05 – Multikriteriální analýza (MKA)"
layout: default
---

# CV05 – Multikriteriální analýza (MKA)

## 🎯 Cíl
Najít vhodné lokality pro těžbu dřeva v **Moravskoslezských Beskydech** pomocí váženého překryvu (*Weighted Overlay*) nad faktory a omezeními.

### Omezení (Boolean)
- nadmořská výška ≤ 700 m n. m.  
- mimo maloplošná chráněná území  
- lesní plochy  

### Faktory (kontinuální – po standardizaci 0–255)
- vzdálenost k dřevozpracujícímu průmyslu *(čím menší, tím lepší)*  
- vzdálenost k silnici *(čím menší, tím lepší)*  
- vzdálenost k železnici *(čím menší, tím lepší)*  
- sklon svahu *(čím menší, tím lepší)*  

---

## 0️⃣ Projekt & prostředí nástrojů

### Struktura složek
```
…\MKA_Beskydy\
├─ 01_input\     # vstupy – jen pro čtení
├─ 02_work\      # mezi-výstupy
├─ 03_output\    # finální výstupy – mapy, tabulky, reporty
└─ 04_layout\
```

### Nastavení prostředí (ArcGIS Pro > Environments)
- **Processing Extent:** `Same as layer → DMR (DEM) pro Beskydy`
- **Snap Raster:** `DMR`
- **Cell Size:** `25 m`
- **Mask (volitelné):** polygon zájmového území (MS Beskydy)
- **Coordinate System:** jednotný (doporučeno S-JTSK / Krovak East North)

> 💡 Zajišťuje přesné lícování všech rastrů – klíčové pro mapovou algebru.

---

## 1️⃣ Omezení (Boolean)

### 1.1 Nadmořská výška ≤ 700 m
```python
Con(DEM <= 700, 1, 0)
```
**Výstup:** `b_alt_le_700` (celé číslo 0/1)

### 1.2 Mimo MCHÚ
Boolean rastr: 1 = mimo CHÚ, 0 = uvnitř.  
Zkontroluj hodnoty a NoData.  
**Výstup:** `b_outside_PA`

### 1.3 Lesní plochy
Pokud převádíš z polygonů:
```python
PolygonToRaster (Cellsize = 25, Field = 1 pro les / 0 pro ostatní)
```
**Výstup:** `b_forest`  
✅ Kontrola: pouze hodnoty 0 a 1, žádné NoData.

---

## 2️⃣ Faktory

### 2.1 Vzdálenost k silnicím
```python
EuclideanDistance (roads, cellsize = 25)
```
**Výstup:** `dist_roads` (metry)

### 2.2 Vzdálenost k železnicím
**Výstup:** `dist_rails`

### 2.3 Vzdálenost k dřevozpracujícímu průmyslu
**Výstup:** `dist_woodproc`

### 2.4 Sklon svahu
```python
Slope (DEM, Output measurement = DEGREE)
```
**Výstup:** `slope_deg`

---

## 3️⃣ Standardizace faktorů (0–255)

**Vzorec:**
```python
Xi = (xi – MINi) / (MAXi – MINi) * 255
```

| Faktor | Výstup | Poznámka |
|:--|:--|:--|
| Silnice | std_roads_0_255 | menší = lepší |
| Železnice | std_rails_0_255 | menší = lepší |
| Dřevozprac. | std_woodproc_0_255 | menší = lepší |
| Sklon | std_slope_0_255 | menší = lepší |

---

## 4️⃣ Váhy faktorů – AHP (Saaty)

| Kritérium | Váha |
|:--|:--:|
| Sklon | 0.614 |
| Dřevozprac. průmysl | 0.252 |
| Silnice | 0.067 |
| Železnice | 0.067 |

> Přepočítáno párovým porovnáním (CR < 0.1).

---

## 5️⃣ Vážený překryv

### 5.1 Vážený součet faktorů
```python
wsum = ( "std_roads_0_255"    * 0.067
       + "std_rails_0_255"    * 0.067
       + "std_woodproc_0_255" * 0.252
       + "std_slope_0_255"    * 0.614 )
```
**Výstup:** `wsum_0_255`

### 5.2 Aplikace omezení
```python
result_cont = Con(
  ("b_alt_le_700" == 1) & ("b_outside_PA" == 1) & ("b_forest" == 1),
  "wsum_0_255",
  NoData
)
```

---

## 6️⃣ Klasifikace výsledku (5 tříd)

| Třída | Interval | Popis |
|:--:|:--|:--|
| 1 | 0–51 | nejvhodnější |
| 2 | 51–102 | velmi vhodné |
| 3 | 102–153 | střední |
| 4 | 153–204 | méně vhodné |
| 5 | 204–255 | nevhodné |

**Výstup:** `result_5classes`

---

## 7️⃣ Vizualizace a validace

**Styl mapy:** zelená → červená (1–5)  
Přidej vrstvy CHÚ, silnice, vrstevnice.  

**Statistika:**  
Nástroj `Tabulate Area` → přepočet ploch (ha, %)

---

## 8️⃣ Kontrolní otázka
Jednalo se o **omezení**, nikoli faktory.

---

## 9️⃣ Druhá varianta s jinými vahami

| Kritérium | Váha |
|:--|:--:|
| Sklon | 0.40 |
| Silnice | 0.20 |
| Železnice | 0.20 |
| Dřevozprac. | 0.20 |

```python
wsum_v2 = ( "std_roads_0_255"    * 0.20
          + "std_rails_0_255"    * 0.20
          + "std_woodproc_0_255" * 0.20
          + "std_slope_0_255"    * 0.40 )
```

💬 **Očekávaný výsledek:** více vhodných oblastí i v mírně strmějších svazích.

---

## 🔟 Doporučení a časté chyby
- Snap/Extent/CellSize vždy podle DEM (25 m)  
- NoData vs. 0/1: drž konzistentně  
- Globální MIN/MAX pro standardizaci  
- CR < 0.1 u AHP  
- Metadata: zdroj, datum, SRS, cellsize  

---

## 11️⃣ Bonus: ModelBuilder (na 1 klik)
Automatizuj vše jako model:

**Inputs:** DEM, roads, rails, woodproc, omezení  
**Processing chain:**  
`Euclidean Distance → Slope → Standardize → Weighted Sum → Con → Reclassify`

---

## 12️⃣ Finální výstupy

| Soubor | Popis |
|:--|:--|
| result_5classes.tif | Výstup (váhy 0.067 / 0.067 / 0.252 / 0.614) |
| result_5classes_v2.tif | Druhá varianta (0.20 / 0.20 / 0.20 / 0.40) |
| tabulate_area_result.csv | Plochy tříd (ha, %) |
| map_MKA_v1.pdf, map_MKA_v2.pdf | Mapové layouty |

---

## 13️⃣ Checklist před odevzdáním
- [x] Nastavené prostředí (Extent, Snap, CellSize)  
- [x] 4 standardizované faktory (0–255)  
- [x] 3 omezení (0/1)  
- [x] AHP váhy + kontrola CR  
- [x] Reclass (5 tříd, 1 = nejvhodnější)  
- [x] Druhá varianta + porovnání  
- [x] Komentář ke změnám  
- [x] Metadata + měřítko + legenda  

