---
title: CV10 – Explorační analýza dat, deterministické interpolační metody
layout: default
description: Podrobný návod v ArcGIS Pro – import netCDF, EDA, generování vzorku, převod Kelvin→Celsius, IDW, Spline, Natural Neighbor a hodnocení přesnosti (cross-validation, RMSE, MAE).
---

# CV10 – Explorační analýza dat, deterministické interpolační metody

Metodický návod pro ArcGIS Pro 3.x (Spatial Analyst, případně Geostatistical Analyst)

---

## Obsah
1. [Cíle cvičení](#cíle-cvičení)
2. [Teoretický základ](#teoretický-základ)
   - [Explorační analýza dat (EDA)](#explorační-analýza-dat-eda)
   - [Deterministické interpolační metody](#deterministické-interpolační-metody)
3. [Vstupní data a předpoklady](#vstupní-data-a-předpoklady)
4. [Postup v ArcGIS Pro](#postup-v-arcgis-pro)
   - [Import netCDF a výběr dat pro prosinec 2014](#import-netcdf-a-výběr-dat-pro-prosinec-2014)
   - [Volitelně: příprava i ledna 2014](#volitelně-příprava-i-ledna-2014)
   - [Generování 100 náhodných bodů na území ČR](#generování-100-náhodných-bodů-na-území-čr)
   - [Odečet hodnot do bodů a převod K→°C](#odečet-hodnot-do-bodů-a-převod-kc)
   - [EDA v ArcGIS Pro: statistiky a grafy](#eda-v-arcgis-pro-statistiky-a-grafy)
   - [Interpolace: IDW, Spline, Natural Neighbor](#interpolace-idw-spline-natural-neighbor)
   - [Hodnocení přesnosti: cross-validation, RMSE, MAE](#hodnocení-přesnosti-cross-validation-rmse-mae)
5. [Doporučené nastavení prostředí a souřadnic](#doporučené-nastavení-prostředí-a-souřadnic)
6. [Výstupy k odevzdání](#výstupy-k-odevzdání)
7. [Časté chyby a kontrolní seznam](#časté-chyby-a-kontrolní-seznam)

---

## Cíle cvičení

- Prozkoumat data teplot z netCDF a připravit jednorázový raster pro prosinec 2014.
- Náhodně vygenerovat 100 bodů v rámci hranice ČR a odečíst hodnoty teplot do atributů.
- Převést teploty z Kelvinů na stupně Celsia.
- Provest EDA: deskriptivní statistiky, histogram, krabicový graf, rozptylový graf.
- Interpolovat bodová data deterministickými metodami: IDW, Spline (Regularized/Tension), Natural Neighbor.
- Otestovat vliv parametrů interpolace a zhodnotit přesnost pomocí cross-validation, RMSE, MAE.

---

## Teoretický základ

### Explorační analýza dat (EDA)

Základní kroky v kontextu GIS:
- Studium rozdělení hodnot: průměr, medián, rozptyl, směrodatná odchylka, min–max.
- Identifikace odlehlých hodnot (outliers) a ověření jejich důvodu.
- Identifikace globálních trendů (např. latitudální, nadmořská výška).
- Prostorová vizualizace (mapy, heatmapy) a prostorové testy shluků (hot-spots).

Typické EDA nástroje:
- Deskriptivní statistika (souhrny, percentily).
- Histogram, krabicový graf (box plot).
- Scatter plot (vztah dvou proměnných, např. teplota vs. nadmořská výška).
- Mapové náhledy, symbolika podle kvantilů.

### Deterministické interpolační metody

1) **IDW – Inverse Distance Weighting**  
Princip: interpolovaná hodnota je váženým průměrem vzorků; váha = 1 / vzdálenost^p, kde p je exponent (power).  
Vlastnosti:
- Lokální metoda, vyšší p ⇒ silnější vliv blízkých bodů, menší p ⇒ hladší povrch.
- Citlivá na nerovnoměrné rozložení bodů.
- Nepředpokládá globální trend.

2) **Spline** (Regularized, Tension)  
Princip: hladký povrch procházející všemi body s minimalizací zakřivení.
- **Regularized**: klade důraz na hladkost.
- **Tension**: parametr napětí řídí „přitažení“ k bodům (vyšší napětí ⇒ méně hladký).
Vhodné pro plynule se měnící veličiny (teplota, vlhkost).

3) **Natural Neighbor**  
Princip: využívá Voronoiho diagram; váhy odvozené z plochy překryvu.  
Vlastnosti:
- Bez nutnosti nastavovat mnoho parametrů.
- Přirozeně hladká interpolace, dobře funguje u nepravidelných vzorků.
- Nevytváří hodnoty mimo konvexní obálku dat.

---

## Vstupní data a předpoklady

- NetCDF soubor: `mean2014_2023.nc` (průměrné teploty, dimenze obsahuje čas).
- Pro export rastru je vybrán **prosinec 2014** (1. 12. 2014).  
- Náhodné body generujeme na území **České republiky**.
- V bodech odečítáme hodnoty teplot pro **leden 2014** (je-li požadováno i pro jiný měsíc, připravíme samostatný subset stejného netCDF).

Poznámka k datům: teplota je v **Kelvinech**. Převod na stupně Celsia: `°C = K − 273.15`.

---

## Postup v ArcGIS Pro

### Import netCDF a výběr dat pro prosinec 2014

1. Vytvoř projekt typu Map.
2. Přidej netCDF: `Catalog → Folders → Add → mean2014_2023.nc`.
3. Panel **Multidimensional**:
   - V části **Current Slice** nastav datum na `2014-12-01` (prosinec 2014).
   - Zkontroluj, že proměnná odpovídá průměrné teplotě.
4. V **Multidimensional → Data Management → Subset**:
   - Vyber proměnnou a časovou dimenzi.
   - Zadej konkrétní datum `2014-12-01`.
   - Výstup: `temp_2014_12_01.crf` (jednorozměrný raster pro prosinec 2014).

### Volitelně: příprava i ledna 2014

Potřebuješ-li z téhož netCDF odečíst hodnoty pro **leden 2014**:
1. Změň v **Multidimensional** datum na `2014-01-01`.
2. Opět použij **Subset** → výstup `temp_2014_01_01.crf`.

### Generování 100 náhodných bodů na území ČR

1. Přidej polygon hranice ČR (např. vrstvy administrativního členění).
2. Nástroj **Create Random Points**  
   `Data Management Tools → Feature Class → Create Random Points`
   - Constraining Feature Class: hranice ČR
   - Number of Points: `100`
   - Output: `rand_points_100.gdb/rand_points_100`
3. Zkontroluj rozmístění bodů. V případě potřeby vyluč oblasti bez dat (masky).

### Odečet hodnot do bodů a převod K→°C

1. **Extract Values to Points**  
   `Spatial Analyst Tools → Extraction → Extract Values to Points`
   - Input point features: `rand_points_100`
   - Input raster: `temp_2014_01_01.crf` (pokud cvičení vyžaduje leden; jinak použij prosinec)
   - Output: `rand_points_100_temp`
   - Zatrhni „Interpolate values“ pro sub-pixel přesnost, je-li vhodné.
2. Otevři atributovou tabulku výsledných bodů.
3. Přidej pole `Temp_C` (Double).
4. **Field Calculator** pro převod Kelvin→Celsius:
