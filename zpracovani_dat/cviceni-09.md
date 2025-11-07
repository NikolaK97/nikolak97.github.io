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
!RASTERVALU! - 273.15
kde `RASTERVALU` je název pole s odečtenou hodnotou.

### EDA v ArcGIS Pro: statistiky a grafy

1. Deskriptivní statistiky:
- V atributové tabulce klikni na záhlaví pole `Temp_C` → **Statistics**.
- Zapiš: `count, mean, median, std, min, max, percentiles`.
- Identifikuj outliers (porovnej min/max s očekávanými hodnotami).
2. Grafy:
- Pravým klikem na vrstvu `rand_points_100_temp` → **Create Chart**:
  - **Histogram** pro `Temp_C` (zkontroluj unimodalitu, šířku tříd).
  - **Box Plot** pro `Temp_C` (outliers, kvartily).
  - **Scatter Plot** (např. `Temp_C` vs. nadmořská výška, pokud máš `Z`).
3. Prostorová vizualizace:
- Symbolika body podle `Temp_C` (graduated colors, kvantily).
- Heatmapa pro vizuální trend (Map → Symbology → Heat Map).

### Interpolace: IDW, Spline, Natural Neighbor

Než spustíš interpolace, nastav jednotné prostředí (Extent, Cell Size, Mask) – viz kapitola [Doporučené nastavení prostředí a souřadnic](#doporučené-nastavení-prostředí-a-souřadnic).

#### IDW
`Spatial Analyst Tools → Interpolation → IDW`

Doporučené parametry k testování:
- **Z value field**: `Temp_C`
- **Power (p)**: testuj např. `1.5; 2; 3`
- **Search Radius**: Fixed vs. Variable
- Fixed: poloměr v metrech (pozor na projekci), např. 30–60 km podle hustoty.
- Variable: počet sousedů, např. 12–20.
- **Cell Size**: např. 1000 m (dle měřítka studie).
- **Output raster**: `idw_p2_cs1000.tif` (pojmenovávej s parametry)

Interpretace:
- Vyšší `p` zvýrazní lokální variabilitu, ale může „rozbít“ hladkost.
- Příliš malý počet sousedů může vést k artefaktům.

#### Spline
`Spatial Analyst Tools → Interpolation → Spline`

Varianty a parametry:
- **Spline Type**: `Regularized` a `Tension` (otestuj oba).
- **Weight** (Regularized) / **Tension** (Tension): testuj rozsah (např. 0.1–5).
- **Number of Points**: kolik bodů přispívá (testuj 12–30).
- **Cell Size**: jako u IDW.

Interpretace:
- Regularized: velmi hladký povrch, může rozmazat lokální extrémy.
- Tension: blíže datům, méně hladký při vyšším napětí.

#### Natural Neighbor
`Spatial Analyst Tools → Interpolation → Natural Neighbor`

Parametry:
- Minimalistická nastavení; většinou není třeba nastavovat váhy.
- **Cell Size**: stejná jako u ostatních.
- Poznámka: extrapolace mimo konvexní obálku dat obvykle není prováděna.

Interpretace:
- Hladký povrch bez výrazných oscilací.
- Vhodné při nepravidelném rozložení bodů.

### Hodnocení přesnosti: cross-validation, RMSE, MAE

Možnosti:

1) **Geostatistical Wizard** (pokročilé, pohodlné)
- `Analysis → Geostatistical Wizard`
- Zvol `Deterministic methods → Inverse Distance Weighted`.
- V průvodci zapni **Cross-Validation**.
- Po dokončení zobrazí tabulku chyb: `Mean Error, RMSE, MAE, R², standardized errors`.
- Ulož si **report** a porovnej různé konfigurace (p, search radius, neighbors).

2) **Manuální křížová validace**
- Rozděl body na tréninkovou (80 %) a validační (20 %) sadu:
- Nástroj **Create Random Subset** (nebo přidej pole `split` a vyplň 0/1).
- Interpoluj z tréninkových bodů.
- Pomocí **Extract Values to Points** odečti hodnoty z interpolované rastr vrstvy do validačních bodů.
- Vypočti chyby: `residual = observed - predicted`.
- Přidej pole a spočti metriky:

Vzorce:
RMSE = sqrt( (1/n) * Σ (obs_i - pred_i)^2 )
MAE = (1/n) * Σ |obs_i - pred_i|
ME = (1/n) * Σ (obs_i - pred_i) # mean error (bias)


V ArcGIS Pro lze souhrnné metriky získat nástrojem **Summary Statistics**:
- Input Table: validační body
- Statistics Field: `residual`, `abs_residual`, `residual_sq`
- Case Field: prázdné (globální metriky)

---

## Doporučené nastavení prostředí a souřadnic

Aby byly výsledky metod porovnatelné, sjednoť následující:

- **Projekce dat**: pro metody založené na vzdálenosti pracuj v **metrech** (např. S-JTSK / Krovak East North, EPSG:5514), nebo v jiné vhodné projekci pro ČR. Vyhni se výpočtům vzdáleností v zeměpisných stupních.
- **Environments** (ikona ozubeného kola v dialogu nástrojů):
  - **Extent**: hranice ČR (nebo konvexní obálka bodů).
  - **Mask**: polygon ČR (zabrání extrapolaci mimo území).
  - **Cell Size**: jednotná pro všechny interpolace (např. 1000 m).
  - **Snap Raster**: zvol jeden referenční raster pro zarovnání gridu.
- **Naming convention**: zahrň klíčové parametry do názvů výstupů:
  - `idw_p2_var12_cs1000.tif`
  - `spline_tension3_cs1000.tif`
  - `natneigh_cs1000.tif`

---

## Výstupy k odevzdání

1. Raster pro prosinec 2014: `temp_2014_12_01.crf` (nebo export `.tif`).
2. Body s odečtenými hodnotami: `rand_points_100_temp` s polem `Temp_C`.
3. EDA:
   - screenshot **Statistics** z pole `Temp_C`,
   - grafy: histogram, box plot, případně scatter plot.
4. Interpolace:
   - `idw_*.tif`, `spline_*.tif`, `natneigh_*.tif` (uveď parametry).
   - mapové kompozice s jednotnou legendou a měřítkem.
5. Hodnocení přesnosti:
   - Report z **Geostatistical Wizard** (IDW) s cross-validation,
   - tabulka metrik: RMSE, MAE, ME pro vybrané konfigurace.
6. Krátká interpretace:
   - Která metoda a jaké parametry vychází nejlépe a proč?
   - Jak rozložení bodů a volba projekce ovlivnily výsledek?

---

## Časté chyby a kontrolní seznam

Kontrola před spuštěním:
- [ ] Je raster i body v projektovaném souřadnicovém systému v metrech?
- [ ] Je sjednocený **Extent**, **Mask** a **Cell Size** ve všech bězích?
- [ ] Máš jednotný **Snap Raster**?

Během EDA:
- [ ] Zkontrolovány outliers; nejsou to chyby měření nebo geolokace?
- [ ] Porovnány statistiky a grafy se sezónním očekáváním (prosinec/leden).

Interpolace:
- [ ] U IDW otestováno více hodnot `Power` a `Search Radius`.
- [ ] U Spline otestován `Regularized` i `Tension` a různé váhy/napětí.
- [ ] Natural Neighbor vyhodnocen v porovnatelné buněčné velikosti.

Validace:
- [ ] Provedena cross-validation (Wizard) nebo manuální split 80/20.
- [ ] Spočteny RMSE, MAE, ME a porovnány napříč metodami.

Prezentace:
- [ ] Všechny mapy mají shodnou barevnou škálu a rozsah.
- [ ] Legenda, měřítko, zdroje dat a parametry jsou uvedeny v mapě či popisku.

---

