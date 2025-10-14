---
title: "CV05 – Multikriteriální analýza (MKA)"
layout: default
---

## Teoretická část

---

### 1. Úvod do prostorových funkcí

Prostorové funkce patří mezi základní nástroje geografických informačních systémů (GIS).  
Umožňují analyzovat, jak spolu jednotlivé geografické objekty **prostorově souvisejí** – tedy jaké mezi nimi existují vztahy (překrývání, dotyk, vzdálenost apod.).

Zatímco atributová data popisují **vlastnosti objektů** (např. počet obyvatel, typ půdy, třída silnice), **geometrická složka** dat určuje jejich **polohu a tvar** v prostoru. Kombinací obou lze provádět tzv. **prostorové analýzy** – získávat nové informace, které by jinak nebyly patrné.

Příklady prostorových dotazů:
- Kolik obyvatel žije do 500 m od železniční stanice?
- Jaká část města leží v záplavovém území?
- Které silnice protínají lesní oblasti?

---

### 2. Základní principy prostorových funkcí

#### 2.1 Topologické překrytí (overlay)

*Overlay* znamená porovnání **dvou nebo více vrstev** a analýzu jejich prostorového vztahu.  
Historicky se překrývaly mapy na fóliích; dnes se overlay provádí matematicky – GIS vyhodnocuje průniky, sjednocení či rozdíly geometrií.

**Výsledek overlay analýzy** → nová vrstva s kombinovanými informacemi z obou vstupních dat.

---

#### 2.2 Booleanovská logika v prostorových analýzách

Prostorové operace se řídí **logickými operátory**:

| Operátor | Význam | Logický příklad | Prostorový příklad |
|-----------|---------|----------------|--------------------|
| **AND (∩)** | průnik | město AND les | území, které je zároveň město i les |
| **OR (∪)** | sjednocení | město OR les | území, které je buď město, nebo les |
| **NOT (-)** | rozdíl | město NOT les | město bez lesních oblastí |

---

#### 2.3 Typy prostorových dat

| Typ geometrie | Popis | Příklad |
|----------------|--------|----------|
| **Bod (point)** | přesná poloha | studna, strom |
| **Linie (polyline)** | lineární prvek bez šířky | silnice, řeka |
| **Polygon (area)** | plošný prvek | obec, les |

Při kombinaci dat musí být typy geometrie **vzájemně kompatibilní** (např. CLIP může ořezávat linie polygonem).

---

### 3. Přehled základních prostorových funkcí

#### 3.1 CLIP – ořezání dat
- **Princip:** ořezává vstupní vrstvu podle ořezové.
- **Vstup:** libovolná vrstva + polygonová ořezová vrstva.  
- **Výstup:** objekty ležící uvnitř ořezové vrstvy.  
- **Příklad:** silnice pouze na území Poruby.

---

#### 3.2 BUFFER – obalová zóna
- **Princip:** vytváří území v určité vzdálenosti od objektu.  
- **Vstup:** body, linie nebo polygony + vzdálenost.  
- **Výstup:** polygonová vrstva zón.  
- **Příklad:** pásma 500 m a 3 km kolem železničních stanic.

---

#### 3.3 INTERSECT – průnik
- **Princip:** najde překrývající se části více vrstev.  
- **Výstup:** nová vrstva pouze s průnikem.  
- **Příklad:** lesy ležící uvnitř města Ostravy.

---

#### 3.4 UNION – sjednocení
- **Princip:** spojí dvě polygonové vrstvy, zachová i nepřekrývající se části.  
- **Výstup:** vrstva všech oblastí z obou vrstev.  
- **Příklad:** sjednocení lesů a vodních ploch.

---

#### 3.5 MERGE – spojení dat
- **Princip:** sloučí více vrstev stejného typu bez ohledu na polohu.  
- **Výstup:** jedna vrstva s daty ze všech vstupů.  
- **Příklad:** spojení vrstev Poruba a Zábřeh.

---

#### 3.6 DISSOLVE – sloučení podle atributu
- **Princip:** slučuje prvky se stejným atributem do jednoho celku.  
- **Výstup:** vrstva s menším počtem prvků.  
- **Příklad:** sloučení všech částí obce Ostrava.

---

#### 3.7 ERASE – odečtení dat
- **Princip:** odečte jednu polygonovou vrstvu od druhé.  
- **Výstup:** území zbylé po odečtení.  
- **Příklad:** území ČR dále než 1 km od silnic.

---

### 4. Význam prostorových funkcí v praxi

Prostorové funkce jsou klíčovým nástrojem GIS:
- modelují dopady jevů (povodně, hluk, doprava),  
- hodnotí dostupnost (např. škol nebo nemocnic),  
- určují překryvy územních limitů,  
- vytvářejí nové datové vrstvy z kombinovaných informací.

---

# CV05 – Multikriteriální analýza (MKA)

## Cíl
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

##  Projekt & prostředí nástrojů

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

>  Zajišťuje přesné lícování všech rastrů – klíčové pro mapovou algebru.

---

##  Omezení (Boolean)

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
 Kontrola: pouze hodnoty 0 a 1, žádné NoData.

---

##  Faktory

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

##  Standardizace faktorů (0–255)

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

##  Váhy faktorů – AHP (Saaty)

| Kritérium | Váha |
|:--|:--:|
| Sklon | 0.614 |
| Dřevozprac. průmysl | 0.252 |
| Silnice | 0.067 |
| Železnice | 0.067 |

> Přepočítáno párovým porovnáním (CR < 0.1).

---

##  Vážený překryv

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

##  Klasifikace výsledku (5 tříd)

| Třída | Interval | Popis |
|:--:|:--|:--|
| 1 | 0–51 | nejvhodnější |
| 2 | 51–102 | velmi vhodné |
| 3 | 102–153 | střední |
| 4 | 153–204 | méně vhodné |
| 5 | 204–255 | nevhodné |

**Výstup:** `result_5classes`

---

##  Vizualizace a validace

**Styl mapy:** zelená → červená (1–5)  
Přidej vrstvy CHÚ, silnice, vrstevnice.  

**Statistika:**  
Nástroj `Tabulate Area` → přepočet ploch (ha, %)

---

##  Kontrolní otázka
Jednalo se o **omezení**, nikoli faktory.

---

##  Druhá varianta s jinými vahami

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

## Doporučení a časté chyby
- Snap/Extent/CellSize vždy podle DEM (25 m)  
- NoData vs. 0/1: drž konzistentně  
- Globální MIN/MAX pro standardizaci  
- CR < 0.1 u AHP  
- Metadata: zdroj, datum, SRS, cellsize  

---

##  Bonus: ModelBuilder (na 1 klik)
Automatizuj vše jako model:

**Inputs:** DEM, roads, rails, woodproc, omezení  
**Processing chain:**  
`Euclidean Distance → Slope → Standardize → Weighted Sum → Con → Reclassify`

---

##  Finální výstupy

| Soubor | Popis |
|:--|:--|
| result_5classes.tif | Výstup (váhy 0.067 / 0.067 / 0.252 / 0.614) |
| result_5classes_v2.tif | Druhá varianta (0.20 / 0.20 / 0.20 / 0.40) |
| tabulate_area_result.csv | Plochy tříd (ha, %) |
| map_MKA_v1.pdf, map_MKA_v2.pdf | Mapové layouty |

---

##  Checklist před odevzdáním
- [x] Nastavené prostředí (Extent, Snap, CellSize)  
- [x] 4 standardizované faktory (0–255)  
- [x] 3 omezení (0/1)  
- [x] AHP váhy + kontrola CR  
- [x] Reclass (5 tříd, 1 = nejvhodnější)  
- [x] Druhá varianta + porovnání  
- [x] Komentář ke změnám  
- [x] Metadata + měřítko + legenda  

