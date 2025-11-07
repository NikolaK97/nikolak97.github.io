---
title: CV08 – Hydrologické analýzy
layout: default
description: Detailní návod pro provádění hydrologických analýz v ArcGIS Pro – práce s DEM, směry toku, akumulace, povodí a hierarchie toků.
---

# CV08 – Hydrologické analýzy
Metodický návod pro ArcGIS Pro – analýza toků, povodí a hydrologických jevů

---

## Obsah
1. [Úvod](#úvod)
2. [Základní principy hydrologických analýz](#základní-principy-hydrologických-analýz)
3. [Data pro cvičení](#data-pro-cvičení)
4. [Postup zpracování v ArcGIS Pro](#postup-zpracování-v-arcgis-pro)
   - [Připojení DMR5G a příprava dat](#připojení-dmr5g-a-příprava-dat)
   - [Vyplnění depresí – Fill](#vyplnění-depresí--fill)
   - [Výpočet směru toku – Flow Direction](#výpočet-směru-toku--flow-direction)
   - [Výpočet akumulace toku – Flow Accumulation](#výpočet-akumulace-toku--flow-accumulation)
   - [Definice toků a reklasifikace](#definice-toků-a-reklasifikace)
   - [Převod do vektorového formátu – Stream to Feature](#převod-do-vektorového-formátu--stream-to-feature)
   - [Analýzy povodí a hierarchie toků](#analýzy-povodí-a-hierarchie-toků)
5. [Teoretické vysvětlení algoritmů](#teoretické-vysvětlení-algoritmů)
   - [Flow Direction – D8, MFD, DINF](#flow-direction--d8-mfd-dinf)
   - [Flow Accumulation – princip výpočtu](#flow-accumulation--princip-výpočtu)
6. [Závěrečné hodnocení a výstupy](#závěrečné-hodnocení-a-výstupy)

---

## Úvod

Hydrologické analýzy v prostředí GIS umožňují studium procesů souvisejících s povrchovým odtokem, povodími, akumulací vody a chováním vodních toků.  
ArcGIS Pro obsahuje nástroje v rámci **Spatial Analyst** rozšíření, které umožňují tyto výpočty provádět nad digitálním modely reliéfu (DEM).

Typické úlohy:
- výpočet směru toku a akumulace,
- identifikace povodí a subpovodí,
- modelování srážkoodtokových procesů,
- simulace povodní a určení záplavových území.

---

## Základní principy hydrologických analýz

### Digitální výškový model (DEM)
DEM (Digital Elevation Model) je základní vstupní dataset.  
Z něj se odvozují:
- směry odtoku (Flow Direction),
- akumulace toku (Flow Accumulation),
- povodí (Watershed),
- definice a hierarchie toků (Stream Order).

---

### Klíčové kroky hydrologické analýzy

| Krok | Nástroj | Účel |
|------|----------|------|
| 1 | Fill | Vyplnění depresí v DEM |
| 2 | Flow Direction | Určení směru odtoku vody |
| 3 | Flow Accumulation | Výpočet akumulace toku |
| 4 | Stream Definition | Identifikace vodních toků |
| 5 | Stream Order | Klasifikace toků podle řádu |
| 6 | Watershed | Vytvoření povodí dle pour pointů |

---

## Data pro cvičení

### Použitá data
| Datový zdroj | Popis | Odkaz |
|---------------|--------|--------|
| DMR 5G | Digitální model reliéfu 5. generace (výška povrchu) | [ČÚZK ImageServer](https://ags.cuzk.cz/arcgis2/rest/services/dmr5g/ImageServer) |
| DIBAVOD | Povodí III. řádu | [DIBAVOD – struktura](https://www.dibavod.cz/27/struktura-dibavod.html) |

### Oblast zájmu
Území: Lhotka, Ludgeřovice, Petřkovice a Ostrava  
Analýzy budou prováděny na části povodí III. řádu vybraného z vrstvy DIBAVOD.

---

## Postup zpracování v ArcGIS Pro

---

### Připojení DMR5G a příprava dat

1. Spusť ArcGIS Pro a vytvoř nový projekt.
2. V **Catalog Pane → Portal → All Portal → Living Atlas** vyhledej:  
   `https://ags.cuzk.cz/arcgis2/rest/services/dmr5g/ImageServer`
3. Přidej DMR5G jako rastrovou vrstvu.
4. Přidej polygonovou vrstvu **povodí III. řádu (DIBAVOD)**.
5. Ořízni DMR5G podle polygonu vybraného povodí:
   - Nástroj: **Extract by Mask**
   - Input raster: DMR5G  
   - Feature mask: vybrané povodí  
   - Výstup: `DMR5G_clip.tif`

---

### Vyplnění depresí – Fill

**Nástroj:**  
`Spatial Analyst → Hydrology → Fill`

**Cíl:** odstranit lokální deprese (přehlubně) v DEM, které by bránily správnému výpočtu odtoku.

**Postup:**
- Input surface raster: `DMR5G_clip.tif`
- Zvol výstup: `DMR5G_filled.tif`
- Ostatní parametry ponech výchozí.
- Spusť nástroj.

Výsledek: hladký model terénu bez nesmyslných jam.

---

### Výpočet směru toku – Flow Direction

**Nástroj:**  
`Spatial Analyst → Hydrology → Flow Direction`

**Cíl:** určit směr, kterým voda teče z každé buňky.

**Postup:**
- Input surface raster: `DMR5G_filled.tif`
- Output raster: `flowdir.tif`
- Algorithm: `D8` (doporučeno)

**Poznámka:**  
Každé buňce se přiřadí hodnota podle směru toku (1, 2, 4, 8, 16, 32, 64, 128).  
Tyto hodnoty reprezentují 8 sousedních buněk (směry podle světových stran).

---

### Výpočet akumulace toku – Flow Accumulation

**Nástroj:**  
`Spatial Analyst → Hydrology → Flow Accumulation`

**Cíl:** zjistit, kolik vody přitéká do každé buňky na základě směru toku.

**Postup:**
- Input flow direction raster: `flowdir.tif`
- Output raster: `flowacc.tif`

**Interpretace:**
- Buňky s vysokou hodnotou akumulace leží ve žlabech a údolnicích.
- Nízké hodnoty = vrcholy nebo hřebeny.

**Vizuální kontrola:**
- Nastav symbologii → Stretch → logaritmická stupnice.
- Podklad: OpenStreetMap (pro kontrolu proti reálným tokům).

---

### Definice toků a reklasifikace

**Cíl:** vymezit buňky, které tvoří vodní toky, podle prahové hodnoty akumulace.

1. Otevři **Symbology** pro `flowacc.tif`.
2. Zkoušej různé prahové hodnoty – např. 500, 1000, 2000 – až vizuálně odpovídá skutečným tokům.
3. Použij nástroj:
   - `Raster Calculator` → výraz:
     ```
     Con("flowacc" > 1000, 1)
     ```
   - Výstup: `streams.tif`
4. Výsledné buňky s hodnotou 1 představují vodní toky.
   Vše ostatní má hodnotu NoData.

---

### Převod do vektorového formátu – Stream to Feature

**Nástroj:**  
`Spatial Analyst → Hydrology → Stream to Feature`

**Postup:**
- Input stream raster: `streams.tif`
- Input flow direction raster: `flowdir.tif`
- Output polyline features: `streams.shp`

Výsledek: vektorová vrstva vodních toků, která kopíruje přirozené linie.

---

### Analýzy povodí a hierarchie toků

#### Stream Order
**Nástroj:**  
`Spatial Analyst → Hydrology → Stream Order`

**Cíl:** klasifikovat toky podle hierarchie (Strahler nebo Shreve).

- Input stream raster: `streams.tif`
- Input flow direction raster: `flowdir.tif`
- Output raster: `stream_order.tif`
- Method: Strahler

#### Watershed
**Nástroj:**  
`Spatial Analyst → Hydrology → Watershed`

**Cíl:** vymezit povodí, které přispívá do vybraného bodu (pour point).

- Input flow direction raster: `flowdir.tif`
- Pour point feature: zvolený bod (např. soutok)
- Output raster: `watershed.tif`

Výsledek: rastrová nebo polygonová vrstva povodí.

---

## Teoretické vysvětlení algoritmů

### Flow Direction – D8, MFD, DINF

| Metoda | Popis | Vhodné použití |
|--------|--------|----------------|
| D8 | Každá buňka má pouze jeden směr toku – do sousední buňky s největším spádem | Jednoduchý a rychlý model |
| MFD | Voda se může rozdělit do více směrů | Komplexní terény |
| DINF | Směr toku určován úhlem největšího spádu | Přesnější modelování odtoku na svazích |

Více informací:  
[ArcGIS Pro – Flow Direction](https://pro.arcgis.com/en/pro-app/latest/tool-reference/spatial-analyst/how-flow-direction-works.htm)

---

### Flow Accumulation – princip výpočtu

1. Směr odtoku je stanoven pro každou buňku (z `Flow Direction`).
2. Pro každou buňku se spočítá, kolik jiných buněk do ní přispívá vodou.
3. Výsledek: raster, kde každá hodnota = počet přítokových buněk.

Interpretace:
- Vyšší hodnota = silnější tok (větší akumulace).
- Nízká hodnota = suché místo nebo vrchol.

---

## Závěrečné hodnocení a výstupy

### Doporučená struktura výstupů
| Vrstva | Typ | Popis |
|---------|------|--------|
| `DMR5G_filled.tif` | raster | vyplněný model terénu |
| `flowdir.tif` | raster | směr odtoku |
| `flowacc.tif` | raster | akumulace toku |
| `streams.tif` | raster | identifikované toky |
| `streams.shp` | vector | vektorové vodní toky |
| `stream_order.tif` | raster | hierarchie toků |
| `watershed.tif` | raster | povodí dle pour pointu |

#
