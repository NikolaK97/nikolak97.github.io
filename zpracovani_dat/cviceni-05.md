---
title: "CV05 – Multikriteriální analýza (MKA)"
layout: default
---

# CV05 – Multikriteriální analýza (MKA)

## Cíl
Najít vhodné lokality pro těžbu dřeva v **Moravskoslezských Beskydech** pomocí váženého překryvu (*Weighted Overlay*) nad faktory a omezeními.

## Vstupní datové vrstvy

Všechny vstupní vrstvy byly již dopředu ořezány (funkce Clip) na území Moravskoslezských Beskyd.

- vektorové vrstvy z databáze ArcČR 500 v 3.3 – silnice, železnice
- stát - polygon
- sídla dřevozpracujícího průmyslu (bodová vektorová vrstva) [📁 Otevřít složku download na GitHubu](https://github.com/NikolaK97/nikolak97.github.io/tree/main/download)

- digitální model reliéfu (rastr) - také z ArcČR v 3.3.
- chráněná maloplošná území (vektor)
- lesní plochy (vektor)

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

##  Omezení (Boolean)

### 1.1 Nadmořská výška ≤ 700 m
- pomocí Reclassify, rozdělte na dvě třídy, kde hodnoty min 700 m bude 1 a ostatní hodnoty 0
**Výstup:** `dem_min_700` (celé číslo 0/1)

### 1.2 Mimo MCHÚ
Nejdříve si maloplošné chránněná území pomocí nástroje MERGE spojte s polygonem Stát, s názvem MCHU_stat.
Vytvořte nový sloupec IS_MCHU, kde pro místa s MCHU nastavte 1 a pro ostatní 0.
Následně převeďte na raster, nástroj Polygon to Raster. (v priority  field vložte sloupec IS_MCHU)
Zkontroluj hodnoty a NoData.  
**Výstup:** `MCHU_raster`

### 1.3 Lesní plochy
Nejdříve si Lesy pomocí nástroje MERGE spojte s polygonem Stát, s názvem Lesy_stat.
Vytvořte nový sloupec IS_Forest, kde pro místa s MCHU nastavte 1 a pro ostatní 0.
Následně převeďte na raster, nástroj Polygon to Raster. (v priority  field vložte sloupec IS_MCHU)
**Výstup:** `forest_raster`  
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

Stanovení vah proběhlo pomocí **metody párového porovnání AHP (Analytic Hierarchy Process)** podle Saatyho.  
Každé kritérium bylo posuzováno vůči ostatním z hlediska významnosti pro výběr vhodných lokalit těžby.

---

### 4.1 Párové porovnání faktorů

Matice relativní důležitosti vyjadřuje, jak jsou jednotlivé faktory významné vůči sobě.  
Hodnoty jsou stanoveny podle stupnice Saaty (1–9), kde 1 = stejná důležitost, 9 = extrémní převaha.

**Tab. 1: Vyjádření relativní důležitosti jednotlivých faktorů v párovém srovnání**

| Faktor | vzdál. k silnicím | vzdál. k železnicím | vzdál. k dř. průmyslu | sklon svahu |
|:--|:--:|:--:|:--:|:--:|
| vzdál. k silnicím | 1 | 1 | 1/5 | 1/7 |
| vzdál. k železnicím | 1 | 1 | 1/5 | 1/7 |
| vzdál. k dř. průmyslu | 5 | 5 | 1 | 1/5 |
| sklon svahu | 7 | 7 | 5 | 1 |
| **Suma** | **14** | **14** | **6.4** | **1.486** |

---

### 4.2 Normalizace a výpočet průměrných vah

Jednotlivé hodnoty v buňkách matice (z tabulky 1) jsou nejprve děleny součtem buněk ve sloupci (Suma).  
Získáme tak **normalizovanou matici**, ze které se následně pro každý řádek vypočítá **průměr** – ten odpovídá váze daného faktoru.

**Tab. 2: Výpočet vah jednotlivých faktorů**

| Faktor | vzdál. k silnicím | vzdál. k železnicím | vzdál. k dř. průmyslu | sklon svahu | **váha faktoru** |
|:--|:--:|:--:|:--:|:--:|:--:|
| vzdál. k silnicím | 0.071 | 0.071 | 0.031 | 0.096 | **0.067** |
| vzdál. k železnicím | 0.071 | 0.071 | 0.031 | 0.096 | **0.067** |
| vzdál. k dř. průmyslu | 0.357 | 0.357 | 0.156 | 0.135 | **0.252** |
| sklon svahu | 0.500 | 0.500 | 0.781 | 0.673 | **0.614** |

---

### 4.3 Výsledné váhy

| Kritérium | Váha |
|:--|:--:|
| **Sklon svahu** | **0.614** |
| **Vzdál. k dřevozpracujícímu průmyslu** | **0.252** |
| **Vzdál. k silnicím** | **0.067** |
| **Vzdál. k železnicím** | **0.067** |

> Kontrola konzistence: CR < 0.1 → výpočet je konzistentní.

---

### 4.4 Vážený překryv (Weighted Overlay)

Každý faktor je standardizován (0–255) a následně kombinován podle vypočtených vah:

```python
wsum = ( "std_roads_0_255"    * 0.067
       + "std_rails_0_255"    * 0.067
       + "std_woodproc_0_255" * 0.252
       + "std_slope_0_255"    * 0.614 )

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

