---
title: CV08 – Síťové analýzy a geokódování
layout: default
description: Detailní návod pro ArcGIS Pro a ArcGIS Online – geokódování, síťové analýzy, TSP a Dijkstrův algoritmus
---

# CV08 – Síťové analýzy a geokódování
Podrobný metodický návod pro ArcGIS Pro a ArcGIS Online

---

## Obsah
1. [Úvod](#úvod)
2. [Část A – Geokódování a příprava dat](#část-a--geokódování-a-příprava-dat)
   - [Vytvoření dat v Excelu](#vytvoření-vstupních-dat-v-excelu)
   - [Geokódování v ArcGIS Online](#geokódování-v-arcgis-online)
   - [Geokódování pomocí Google Awesome Table](#geokódování-pomocí-google-awesome-table)
   - [Porovnání výsledků v ArcGIS Pro](#porovnání-obou-výsledků-v-arcgis-pro)
3. [Část B – Síťové analýzy](#část-b--síťové-analýzy-v-arcgis-pro-a-arcgis-online)
   - [Základy a principy](#základy-síťových-analýz)
   - [Síťové analýzy v ArcGIS Online](#síťové-analýzy-v-arcgis-online)
   - [Síťové analýzy v ArcGIS Pro](#síťové-analýzy-v-arcgis-pro)
   - [TSP a Dijkstrův algoritmus](#tsp--problém-obchodního-cestujícího)
   - [Export a interpretace](#prezentace-a-export-výsledků)
4. [Shrnutí úkolu](#shrnutí-postupu-cv08)

---

## Úvod

Síťové analýzy představují soubor nástrojů pro řešení úloh typu nalezení nejkratší trasy, optimalizace rozmístění zdrojů, určování dostupnosti nebo hledání nejbližších zařízení.  
Hlavním zdrojem dat je ohodnocená uliční síť, kde každý úsek má atribut nákladnosti – nejčastěji čas nebo vzdálenost.

---

# Část A – Geokódování a příprava dat

## Vytvoření vstupních dat v Excelu

Cíl: připravit tabulku adres, které budou později geokódovány.

| Sloupec | Popis |
|----------|--------|
| ID | unikátní identifikátor |
| Ulice | název ulice |
| Cislo_popisne | číslo popisné |
| Nazev_obce | město nebo obec |

Příklad datové sady:

| ID | Ulice | Cislo_popisne | Nazev_obce |
|----|--------|----------------|-------------|
| 1 | Česká | 25 | Brno |
| 2 | Masarykova | 10 | Brno |
| 3 | Nádražní | 7 | Modřice |
| 4 | Dlouhá | 123 | Šlapanice |
| 5 | Hlavní | 52 | Rajhrad |
| 6 | Komenského | 8 | Židlochovice |
| 7 | Těšanská | 15 | Měnín |
| 8 | Masarykovo náměstí | 2 | Pohořelice |
| 9 | Svatopluka Čecha | 1 | Hustopeče |
| 10 | Nádražní | 10 | Ivančice |

Ulož jako `Adresy_CV08.xlsx`.

---

## Geokódování v ArcGIS Online

### Přidání dat
1. Přihlaš se na [ArcGIS Online](https://www.arcgis.com).
2. Content → Add item → From your computer → Upload `Adresy_CV08.xlsx`.
3. Zaškrtni **Publish this file as a hosted layer** a potvrď.

### Nastavení geokódování
1. V dialogu **Publish a layer** vyber:
   - Location information is in multiple fields
2. Nastav pole:
   - Street → `Ulice` + `Cislo_popisne`
   - City → `Nazev_obce`
3. Klikni **Publish** → ArcGIS spustí geokódování.

### Kontrola a opravy
- Zkontroluj polohu bodů v mapě.
- Nesprávné body oprav pomocí **Edit → Move point**.
- Vrstva by měla být typu *Hosted Feature Layer*.

### Export
- … → Export data → Shapefile (.zip)
- Ulož jako `Adresa_AGOL.shp`.

---

## Geokódování pomocí Google Awesome Table

### Postup
1. Vytvoř stejnou tabulku v Google Sheets.  
2. Otevři [Awesome Table](https://awesome-table.com).  
3. Add-ons → Geocode by Awesome Table → Start.  
4. Nastav:
   - Street → `Ulice + Cislo_popisne`
   - City → `Nazev_obce`
5. Po zpracování vzniknou nové sloupce: `Latitude`, `Longitude`.

### Export
- File → Download → CSV  
- Soubor: `Adresa_Awesome.csv`.

---

## Porovnání obou výsledků v ArcGIS Pro

### Načtení dat
1. Spusť ArcGIS Pro → vytvoř nový projekt `Map`.
2. Přidej vrstvy:
   - `Adresa_AGOL.shp`
   - `Adresa_Awesome.csv` → převod pomocí **XY Table To Point**.

### XY Table To Point
- Input Table → `Adresa_Awesome.csv`  
- X Field → `Longitude`  
- Y Field → `Latitude`  
- Coordinate System → WGS 1984  
- Run → vrstva `Adresa_Awesome_Point`.

### Porovnání
- Odliš symboly: AGOL = modrá, Awesome = červená.
- Změř rozdíly (Measure nebo Select By Location).
- Vyhodnoť přesnost:

| Zdroj | Přesnost | Poznámka |
|--------|-----------|----------|
| ArcGIS Online | velmi přesná | využívá Esri World Geocoder |
| Awesome Table | střední | může mít chyby v číslech popisných |

---

# Část B – Síťové analýzy v ArcGIS Pro a ArcGIS Online

## Základy síťových analýz

Síťová analýza modeluje pohyb po síti – silnice, cesty, vedení.  
Každá hrana má atributy jako délka, čas, rychlost nebo omezení.

| Úloha | Nástroj | Příklad |
|--------|----------|---------|
| Route | Route | Nejkratší cesta z A do B |
| Service Area | Service Area | Kam dojedu za 10 minut |
| Closest Facility | Closest Facility | Nejbližší nemocnice |
| VRP | Vehicle Routing Problem | Optimalizace rozvozu |
| Location-Allocation | Location-Allocation | Rozmístění skladů |

---

## Síťové analýzy v ArcGIS Online

### Aktivace
Otevři mapu → Analysis → Use Proximity

### Dostupné nástroje
- Find Nearest
- Create Drive-Time Areas
- Plan Routes
- Location-Allocation
- Vehicle Routing Problem

---

### Find Nearest
1. Input layer – adresy  
2. Near layer – zařízení  
3. Measurement type – Driving distance  
4. Search radius – 30 km  
5. Nearest locations – 1  
6. Run Analysis

Výsledek: čáry k nejbližším bodům, atributy vzdálenosti a času.

---

### Create Drive-Time Areas
1. Input layer: zařízení  
2. Measure: Driving time  
3. Drive times: 5, 10, 15  
4. Travel mode: Auto  
5. Run Analysis

Výsledek: polygony dostupnosti, barvy podle času.

---

### Plan Routes
1. Stops layer: Adresy  
2. Travel mode: Driving  
3. Reorder stops: zapnuto  
4. Run Analysis

Výsledek: optimální pořadí a celkový čas.

---

## Síťové analýzy v ArcGIS Pro

### Aktivace Network Analyst
1. Customize → Extensions → Network Analyst  
2. Panel Network Analyst Toolbar

---

### Route
1. Network Analyst → New Route  
2. Stops → Load Locations (adresy)  
3. Solve  
4. Výsledek = nejkratší cesta.

Parametry:  
- Impedance: TravelTime  
- Reorder Stops: zapnuto (TSP)

---

### Service Area
1. New Service Area  
2. Přidej zařízení (Facilities).  
3. Breaks: 5, 10, 15  
4. Travel direction: From facility  
5. Solve

Výsledek: polygony dostupnosti.

---

### Closest Facility
1. New Closest Facility  
2. Facilities – zařízení  
3. Incidents – adresy  
4. Impedance: TravelTime  
5. Cutoff: 20 min  
6. Solve

Výsledek: trasy z adres k nejbližšímu zařízení.

---

### VRP – Vehicle Routing Problem
1. New Vehicle Routing Problem  
2. Orders – zákazníci  
3. Depots – sklady  
4. Routes – vozidla  
5. MaxOrderCount – počet zákazníků  
6. Solve

Výsledek: optimalizované rozvozy.

---

### Location-Allocation
1. New Location-Allocation  
2. Demand Points: adresy  
3. Facilities: potenciální místa  
4. Problem Type: Maximize coverage  
5. Facilities to locate: 2  
6. Solve

Výsledek: optimální umístění zařízení.

---

## TSP – Problém obchodního cestujícího

### Teorie
Cíl: projít všechny body právě jednou a vrátit se na start s minimální délkou trasy.  
ArcGIS používá heuristiku TSP založenou na Dijkstrovi.

### V ArcGIS Pro
1. New Route  
2. Přidej body (Stops).  
3. Reorder Stops to Find Optimal Route – zapnuto  
4. Solve

Výsledek: optimální pořadí návštěv.

---

## Dijkstrův algoritmus

Použit v trasování, Closest Facility i VRP.

Princip:
1. Všechny uzly mají hodnotu ∞, start = 0.  
2. Vyber nejbližší nevyřešený uzel.  
3. Aktualizuj hodnoty sousedů.  
4. Opakuj, dokud nejsou všechny uzly zpracovány.

Výsledek: nejkratší cesty od zdroje ke všem uzlům.  
Varianta s heuristikou = A* algoritmus.

---

## Prezentace a export výsledků

| Úkon | Postup | Výstup |
|------|--------|--------|
| Export z Pro | Layer → Data → Export Features | SHP / GDB |
| Export z Online | Layer → Export Data | CSV / GeoJSON |
| Sdílení | Share → Web Map / Web App | Web prezentace |
| Vizualizace | Symbology → Graduated Colors | Mapové přehledy |

---

## Shrnutí postupu CV08

| Krok | Prostředí | Nástroj | Cíl |
|------|-------------|----------|-----|
| 1 | Excel | – | Příprava dat |
| 2 | ArcGIS Online | Geocoding | Umístění bodů |
| 3 | Google Awesome Table | Geocoding | Porovnání |
| 4 | ArcGIS Pro | XY Table To Point | Vizualizace |
| 5 | ArcGIS Online | Network Analysis | Síťové analýzy |
| 6 | ArcGIS Pro | Route / Closest Facility / VRP | Optimalizace |
| 7 | ArcGIS Pro | TSP / Dijkstra | Teorie a praxe |

---

## Doporučení k odevzdání
- Exportuj mapové výstupy do PDF s legendou, měřítkem a popisy.
- Uveď parametry: čas, vzdálenost, počet zařízení.
- Doplň popis metody a zhodnocení přesnosti geokódování.

---

