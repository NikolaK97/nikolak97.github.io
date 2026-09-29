---
layout: default
title: "CV03 – Mapová algebra v ArcGIS Pro: výběr míst pro FVE"
---

# CV03 – Mapová algebra v ArcGIS Pro: výběr míst pro FVE

**Téma:** výběr ploch vhodných pro fotovoltaickou elektrárnu (FVE) v obci Albrechtice (okres Karviná) pomocí mapové algebry v ArcGIS Pro (Spatial Analyst).

**Kritéria (všechna musí platit současně):**

1. sklon terénu ≤ 15°
2. roční úhrn globálního záření ≥ 1000 kWh/m²
3. mimo zastavěná a průmyslová území, lesy, mokřady a vodní plochy (podle CORINE Land Cover 2018)

**Výstup:** binární rastr `PV_suitable` (1 = vhodné, 0 = nevhodné), mapová kompozice se třemi mezivýsledky a stručný komentář.

---

## Vstupní data

Pracujeme se stejnou mřížkou 5 m a souřadnicovým systémem S-JTSK / Krovak East North (EPSG:5514). Všechny vstupy jsou zdarma pro výuku.

| Vrstva | Zdroj | Jak ji dostat do Pro | Na co si dát pozor |
| --- | --- | --- | --- |
| Hranice obce | **ArcČR 4.3** (ARCDATA PRAHA, ČÚZK, ČSÚ) – vrstva `Obce` | geodatabáze ArcČR ze sdílené složky / arcdata.cz, nebo online feature služby ArcČR v ArcGIS Online / Living Atlas (hledej „ArcČR“) | jméno obce není jedinečné (Albrechtic je v ČR pět) – vybírej i podle okresu nebo kódu obce; atributy jsou malými písmeny (`nazev`, `kod`, …) |
| Hranice obce (alternativa) | RÚIAN – ČÚZK (Living Atlas „Správní hranice“, WFS, Geoportál ČÚZK) | Add Data → Living Atlas / WFS | negeneralizovaná hranice; pro cvičení stačí ArcČR |
| Výškový model | **DMR 5G** – ČÚZK, otevřená data (CC BY 4.0), stažení po listech SM 5 ve formátu LAZ: [Geoprohlížeč ČÚZK – ATOM dmr5g](https://ags.cuzk.gov.cz/geoprohlizec/?atom=dmr5g) | stáhni ZIPy pro listy pokrývající buffer obce, v Pro vytvoř LAS Dataset a převeď na rastr 5 m (krok 2) | stažená data jsou uzly TIN v nepravidelné síti – rastr vzniká interpolací; image služba ČÚZK je jen on-the-fly náhled, ne zdroj pro analýzu |
| Krajinný pokryv | **CLC 2018** (Copernicus Land Monitoring Service / CENIA), polygony s polem `CODE_18` | stažená data ze sdílené složky, nebo Esri Living Atlas „CORINE Land Cover 2018“ | minimální mapovací jednotka 25 ha – v měřítku obce hrubé |
| Krajinný pokryv (alternativy) | ZABAGED (ČÚZK); Esri „Sentinel-2 10 m Land Use/Land Cover“ (Living Atlas); LPIS (MZe) | Living Atlas / WFS / stažení | vhodné pro samostatnou práci; reklasifikují se jiné kódy, princip zůstává |

ArcČR obsahuje i vrstvy `Okresy`, `ORP`, `Kraje` (pro filtr obce), definiční body obcí a atributy ze SLDB 2021 – hodí se do komentáře (počet obyvatel, rozloha). Vrstvy `Lesy`, `Sídla` apod. jsou generalizované pro 1 : 500 000, proto je **nepoužívej** jako náhradu CLC při analýze jedné obce.

**Citace do tiráže mapy:** Data ArcČR © ČÚZK, ČSÚ, ARCDATA PRAHA; DMR 5G © ČÚZK; CLC 2018 © European Union, Copernicus Land Monitoring Service, EEA.

---

## 0) Příprava projektu a prostředí

Nastavení Environments je nejdůležitější krok cvičení – zajistí, že všechny rastry mají stejnou mřížku, rozsah a systém a dají se kombinovat v Raster Calculatoru.

1. Nový projekt typu **Map**, název `CV03_MapAlgebra_Albrechtice`. Výchozí geodatabáze projektu stačí (rastry se do Feature Datasetu neukládají).
2. Vlastnosti mapy → **Coordinate Systems**: S-JTSK / Krovak East North (EPSG:5514).
3. **Analysis → Environments** (platí pro celý projekt), nastavuj postupně:
   - **Output Coordinate System:** EPSG:5514 – hned na začátku.
   - **Cell Size:** 5 m – hned na začátku.
   - **Snap Raster:** `DMR5G_buffer` – po jeho vytvoření v kroku 2.
   - **Processing Extent:** `Albrechtice_buffer` – po vytvoření bufferu v kroku 1.
   - **Mask:** **zatím nenastavuj.** Maska by ořízla vstup pro výpočet záření a model by „neviděl“ horizont za hranicí obce. Masku `Albrechtice_boundary` nastav až před krokem 6.

**Proč buffer:** sklon i sluneční záření se počítají z okolí pixelu. Ořez DMR přesně na hranici obce vytváří chybné hodnoty podél hranice (u záření i hlouběji dovnitř – chybí stínění okolním terénem). Pracujeme proto na obci rozšířené o 1 km a na hranici obce ořízneme až výsledek.

---

## 1) Hranice obce Albrechtice (ArcČR 4.3)

1. Přidej do mapy vrstvu **Obce** z ArcČR 4.3.
2. Otevři atributovou tabulku a zkontroluj názvy polí. Obec má název v poli `nazev` a kód obce (RÚIAN) v poli `kod`.
3. **Select By Attributes** – nevybírej jen podle jména, Albrechtic je v ČR pět:
   - `nazev = 'Albrechtice' AND nazev_okres = 'Karviná'` (přesný název pole okresu si ověř v tabulce),
   - nebo kód obce (ověř v RÚIAN): `kod = 598925`,
   - nebo **Select By Location** proti vrstvě `Okresy` (Karviná) a pak klikni na správný polygon.

   Zkontroluj, že je vybrán **právě jeden** prvek.
4. Export: pravým na vrstvu → Data → **Export Features** → `Albrechtice_boundary` (do GDB projektu, EPSG:5514).
5. **Buffer** (Analysis → Tools): vstup `Albrechtice_boundary`, Distance **1000 m**, výstup `Albrechtice_buffer`. Nastav ho jako Processing Extent.

---

## 2) DMR 5G – stažení, rastr 5 m a sklon

### Stažení DMR 5G

1. Otevři Geoprohlížeč ČÚZK s ATOM feedem DMR 5G: <https://ags.cuzk.gov.cz/geoprohlizec/?atom=dmr5g>. Data jsou otevřená (CC BY 4.0), po mapových listech SM 5 (2,5 × 2 km), formát LAZ v S-JTSK.
2. Najdi Albrechtice a vyber všechny listy, které pokrývají `Albrechtice_buffer` (kolem 10 listů). Stáhni ZIPy do složky projektu a rozbal.
3. Data Management → LAS Dataset → **Create LAS Dataset**: vstup všechny `.laz`, souřadnicový systém EPSG:5514, zaškrtni Compute statistics, výstup `DMR5G.lasd`. Pokud nástroj soubory LAZ odmítne, převeď je nejdřív nástrojem **Convert LAS** do `.zlas`.
4. Conversion → To Raster → **LAS Dataset To Raster**: Input `DMR5G.lasd`, Value field Elevation, Interpolation **Triangulation → Natural Neighbor** (body DMR 5G jsou uzly TIN, Binning by nechal díry), Output data type Float, Sampling type Cell Size, Sampling value **5**, Z factor 1. Výstup `DMR5G_5m`.
5. Spatial Analyst → Extraction → **Extract by Mask**: Input `DMR5G_5m`, maska `Albrechtice_buffer`, výstup `DMR5G_buffer`. Ověř Cell Size 5 m, EPSG:5514 a výšky v m n. m. (Albrechtice ≈ 260–350 m).
6. V Environments nastav **Snap Raster = `DMR5G_buffer`**. Od této chvíle budou všechny rastry na stejné mřížce.

**Náhradní cesta – image služba ČÚZK** (jen pro náhled, nebo když stažení selže): Insert → Connections → Server → Add ArcGIS Server Connection s URL `https://ags.cuzk.cz/arcgis2/rest/services`, přetáhni `dmr5g` do mapy, Processing Templates → **None** (jinak vidíš jen stínovaný reliéf), pak Extract by Mask na `Albrechtice_buffer`. Pro odevzdání použij stažená data.

### Sklon

7. Spatial Analyst → Surface → **Slope**: Input `DMR5G_buffer`, Output measurement **DEGREE**, Method Planar, Z-factor **1** (výšky i souřadnice jsou v metrech). Výstup `Slope_deg`.

Volitelně: DMR 5G v 5 m mřížce obsahuje drobný šum (meze, příkopy). Před sklonem můžeš model vyhladit nástrojem **Focal Statistics** (Mean, okno 3 × 3) → `DMR5G_smooth` a použít ho i pro výpočet záření.

---

## 3) CLC 2018 – ořez, rasterizace, reklasifikace

**Nevhodné třídy CLC (→ 0):**

| Skupina | Kódy `CODE_18` | Co to je |
| --- | --- | --- |
| 1 – umělé povrchy | 111, 112, 121, 122, 123, 124, 131, 132, 133, 141, 142 | zástavba, průmysl, doprava, těžba, skládky, městská zeleň a sport |
| 3.1 – lesy | 311, 312, 313 | listnaté, jehličnaté, smíšené |
| 3.2.4 – nízký les (volitelně) | 324 | zarůstající plochy; po dohodě s vyučujícím |
| 4 – mokřady | 411, 412 | vnitrozemské mokřady, rašeliniště |
| 5 – vody | 511, 512 | toky, plochy |

Vše ostatní (2xx zemědělská půda, 321–323, 33x) → 1. V Albrechticích narazíš hlavně na 112, 211, 231, 242, 243, 311, 313, 512.

**Postup:**

1. Přidej polygonovou vrstvu **CLC 2018** pro ČR. Ověř, že má pole `CODE_18` (textové, např. `'211'`). Pokud se pole jmenuje jinak (`CLC_CODE`, `Code_18`), použij ho – hodnoty jsou stejné.
2. Analysis → Tools → **Clip**: Input `CLC_2018`, Clip Features `Albrechtice_buffer`, výstup `CLC18_clip`. Ořezáváme na buffer, ne na obec.
3. Conversion → To Raster → **Polygon to Raster**: Input `CLC18_clip`, Value field `CODE_18`, Cell assignment **Maximum Area**, Cellsize 5, výstup `CLC18_r5`.
4. Spatial Analyst → Reclass → **Reclassify**: Input `CLC18_r5`, Reclass field `CODE_18` (nebo `Value`), klikni **Unique**, uvedené kódy nastav na **0**, zbytek na **1**. Volbu *Change missing values to NoData* nech vypnutou. Výstup `CLC_ok`.

**Kontrola:** `CLC_ok` má jen hodnoty 0 a 1 a NoData nejvýš na okraji bufferu. Díry uvnitř obce znamenají chybějící polygon v CLC nebo ořez na hranici obce místo bufferu.

CLC má minimální mapovací jednotku 25 ha a šířku liniových prvků 100 m – malé lesíky, osamělé domy či menší rybníky v něm nejsou. Pro samostatnou práci lze CLC nahradit ZABAGED nebo Esri Sentinel-2 10 m LULC.

---

## 4) Reklasifikace sklonu

Spatial Analyst → Reclass → **Reclassify**: Input `Slope_deg`, Classify → 2 třídy, Manual, hranice **15**:

- 0 – 15 → **1**
- 15 – 90 → **0**

Výstup `Slope_ok`. Stejného výsledku dosáhneš v Raster Calculatoru: `Con("Slope_deg" <= 15, 1, 0)`.

Reclassify zahrnuje horní mez intervalu, takže 15,0° spadá do třídy 1 (kritérium je ≤ 15°).

---

## 5) Sluneční záření

V ArcGIS Pro 3.2 a novějším použij nástroj **Raster Solar Radiation** (nahrazuje starší Area Solar Radiation – je rychlejší, umí GPU a masku). **Pozor na jednotky:** Raster Solar Radiation dává výsledek v **kWh/m²**, starý Area Solar Radiation ve **Wh/m²**. Kdo převod udělá u nesprávného nástroje, dostane buď všude 0, nebo všude 1.

**Raster Solar Radiation** (Spatial Analyst → Solar Radiation):

| Parametr | Hodnota |
| --- | --- |
| Input surface raster | `DMR5G_buffer` (nebo `DMR5G_smooth`) |
| Output global radiation raster | `Solar_kWh` |
| Start date and time | 1. 1. 2025 00:00 |
| End date and time | 31. 12. 2025 23:59 (rozpětí max. 1 rok) |
| Time zone | (UTC+01:00) Prague |
| Input analysis mask | `Albrechtice_boundary` – zrychlí výpočet, horizont se stále počítá z celého bufferu |
| Slope/aspect input | From DEM |
| Diffuse proportion / Transmittivity | výchozí 0,3 / 0,5 |
| Target device | GPU, pokud je k dispozici, jinak CPU |

Výstup `Solar_kWh` je roční úhrn globálního záření v kWh/m². Pro Albrechtice čekej zhruba 900–1200 kWh/m²; jižní svahy nejvíc, severní svahy a údolí nejméně.

**Starší verze Pro – Area Solar Radiation:** Input `DMR5G_buffer`, Time configuration *Whole year with monthly interval*, Sky size 200, Z-factor 1, ostatní výchozí. Výstup `Solar_Whm2`. Převod v Raster Calculatoru: `Solar_kWh = "Solar_Whm2" / 1000`.

### 5.1 Prahování

Raster Calculator:

```
Solar_ok = Con("Solar_kWh" >= 1000, 1, 0)
```

Výstup `Solar_ok` je binární 0/1.

**Než jdeš dál, podívej se na histogram** (Symbology → Histogram, nebo Layer Properties → Source → Statistics). Práh 1000 kWh/m² je modelová hodnota závislá na parametrech. Pokud po prahování vyjde téměř celá obec 0 nebo téměř celá 1, práh nic nerozlišuje – v komentáři uveď min/max/mean a navrhni relativní práh (např. ≥ průměr obce). Pro odevzdání ale zachovej práh ze zadání.

---

## 6) Kombinace kritérií – Raster Calculator

Teď teprve nastav v Environments **Mask = `Albrechtice_boundary`** (a klidně i Processing Extent na obec). Výsledek se tím ořízne na hranici obce, zatímco mezivýsledky byly spočítány správně z bufferu.

Chceme pixely, které splňují všechny tři podmínky (logické AND):

```
PV_suitable = Con(("Slope_ok" == 1) & ("CLC_ok" == 1) & ("Solar_ok" == 1), 1, 0)
```

Kratší zápis se stejným výsledkem (součin binárních rastrů je 1 jen tam, kde jsou 1 všechny):

```
PV_suitable = "Slope_ok" * "CLC_ok" * "Solar_ok"
```

Pozor: **součet** rastrů (`"Slope_ok" + "CLC_ok" + "Solar_ok"`) dává hodnoty 0 až 3, ne binární výsledek. Hodí se jako doplňkový rastr `PV_score` do komentáře (kolik kritérií pixel splňuje), ale jako výsledek odevzdej binární `PV_suitable`.

---

## 7) Kontroly kvality

- **Zarovnání mřížky:** Layer Properties → Source u `Slope_ok`, `CLC_ok`, `Solar_ok`, `PV_suitable` – stejná Cell Size (5 m), stejný Extent a stejné souřadnice levého horního rohu. Liší-li se, chyběl Snap Raster.
- **Hodnoty:** každý binární rastr má jen 0 a 1. Hodnota 2 nebo 3 = někdo sčítal místo násobil.
- **NoData:** `PV_suitable` má NoData jen mimo obec. Díry uvnitř obce ukazují na CLC nebo na ořez DMR na hranici obce místo bufferu.
- **Okraje obce:** `Slope_ok` a `Solar_ok` nemají podél hranice pruh nesmyslných hodnot. Pokud ano, přepočítej z `DMR5G_buffer`.
- **Plocha:** v tabulce `PV_suitable` vezmi Count pro hodnotu 1 × 25 m² = plocha vhodných pixelů v m² (÷ 10 000 = ha). Číslo uveď v komentáři.
- **Vizuální kontrola nad ortofotem** (ČÚZK Ortofoto z Living Atlas / WMS): vhodné plochy leží na polích a loukách, ne na střechách, v lese ani na hladině Těrlické přehrady.

---

## 8) (Volitelné) Čištění výsledku

Binární rastr obsahuje osamělé pixely a úzké proužky. Pro FVE má smysl minimální souvislá plocha **1 ha = 400 pixelů** při 5 m.

1. Spatial Analyst → Generalization → **Majority Filter** na `PV_suitable` (Four, Majority) → `PV_mf`.
2. Raster Calculator: `PV_only1 = SetNull("PV_mf" == 0, 1)` – Region Group má dostat jen vhodné plochy.
3. Spatial Analyst → Generalization → **Region Group**: Input `PV_only1`, Number of neighbors **Four** → `PV_regions`. Tabulka výstupu má pole `COUNT` = počet pixelů regionu.
4. Raster Calculator (Lookup vytáhne COUNT jako hodnotu pixelu):

   ```
   PV_suitable_clean = Con(Lookup("PV_regions", "COUNT") >= 400, 1)
   ```

5. Conversion → **Raster to Polygon** (Simplify polygons vypnuto) → `PV_suitable_poly`. Přidej pole `plocha_ha` = `Shape_Area / 10000` (Calculate Geometry).
6. Volitelně Cartography → **Eliminate Polygon Part** (Area < 1 ha) pro odstranění malých děr uvnitř ploch.

---

## 9) Mapové výstupy a komentář (co odevzdat)

### 9.1 Kompoziční mapa

1. Insert → New Layout, A3 na šířku.
2. Čtyři mapové rámy se stejným měřítkem a rozsahem (Map Frame → Constraint → Link to another map frame):
   - `Slope_ok` – 0 šedě, 1 světle zeleně
   - `CLC_ok` – 0 šedě, 1 světle zeleně
   - `Solar_ok` – 0 šedě, 1 světle zeleně
   - `PV_suitable` – 1 sytě zeleně, 0 průhledně nebo světle šedě, pod tím ortofoto nebo hranice obce
3. Do každého rámu hranici obce (`Albrechtice_boundary`, jen obrys).
4. Legenda, grafické měřítko, severka, titul, autor, datum, souřadnicový systém (S-JTSK / Krovak East North), zdroje dat.
5. Export → PDF a PNG, 300 dpi: `CV03_Albrechtice_mapa.pdf`, `CV03_Albrechtice_mapa.png`.
6. Přilož Layer Files (.lyrx) čtyř výsledných rastrů.

### 9.2 Stručný komentář (½–1 strana)

- **Metodika:** DMR 5G (ČÚZK, 5 m), Slope (planar, stupně), Raster Solar Radiation (celý rok, kWh/m²), CLC 2018 (vyloučené kódy – vypiš), reklasifikace na 0/1, logický průnik. Uveď, proč se počítalo na bufferu 1 km.
- **Zjištění:** plocha vhodných pixelů v ha a v % rozlohy obce; kde leží hlavní plochy; které kritérium nejvíce omezuje (porovnej součty jedniček v `Slope_ok`, `CLC_ok`, `Solar_ok`).
- **Limitace:** CLC v měřítku obce (MMU 25 ha), modelové hodnoty záření závislé na parametrech, nezohledněné lokální stínění (stromy, budovy), územní plán, ochrana ZPF (I. a II. třída ochrany), ochranná pásma, sítě.
- **Doporučení:** další kritéria a datové zdroje – vzdálenost od VN vedení, vzdálenost od zástavby, ZCHÚ a Natura 2000 (AOPK ČR – otevřená data), BPEJ / třídy ochrany ZPF, orientace svahu (Aspect).

---

## 10) Samostatná práce – obec bydliště

Stejný postup, pouze v kroku 1 vyber svou obec (opět ověř kód obce nebo okres – nespoléhej jen na název). Kritéria zůstávají: sklon ≤ 15°, záření ≥ 1000 kWh/m², CLC mimo třídy 1xx, 311–313, 41x, 51x.

**Odevzdej:**

- mapu se třemi mezivýsledky (`Slope_ok`, `CLC_ok`, `Solar_ok`) a výsledkem (`PV_suitable`); titul a text v mapě s názvem obce a datem
- komentář podle osnovy 9.2 včetně plochy vhodných ploch v ha
- Layer Files (.lyrx) výsledných rastrů

U velkých obcí (nad cca 50 km²) použij Cell Size 10 m, jinak výpočet záření na CPU poběží desítky minut. Pro obce s velmi členitým reliéfem (Beskydy, Jeseníky) zvětši buffer na 2 km.

**Bonus (nepovinné):** nahraď CLC vrstvou ZABAGED nebo Esri 10 m LULC a porovnej výslednou plochu s variantou CLC. Rozdíl okomentuj.

---

## Tahák nástrojů

| # | Nástroj | Vstup → výstup |
| --- | --- | --- |
| 1 | Select By Attributes + Export Features | `Obce` (ArcČR 4.3) → `Albrechtice_boundary` |
| 2 | Buffer 1000 m | `Albrechtice_boundary` → `Albrechtice_buffer` |
| 3 | Create LAS Dataset → LAS Dataset To Raster (5 m) → Extract by Mask | LAZ z ATOM ČÚZK → `DMR5G_buffer` |
| 4 | Environments: Snap Raster, Cell Size 5, Extent | `DMR5G_buffer`, `Albrechtice_buffer` |
| 5 | Slope (DEGREE, Z = 1) | `DMR5G_buffer` → `Slope_deg` |
| 6 | Reclassify (0–15 → 1, > 15 → 0) | `Slope_deg` → `Slope_ok` |
| 7 | Clip | `CLC_2018` → `CLC18_clip` |
| 8 | Polygon to Raster (`CODE_18`, Max Area, 5 m) | `CLC18_clip` → `CLC18_r5` |
| 9 | Reclassify (1xx, 311–313, 41x, 51x → 0; ostatní → 1) | `CLC18_r5` → `CLC_ok` |
| 10 | Raster Solar Radiation (celý rok, mask obec) | `DMR5G_buffer` → `Solar_kWh` |
| 11 | Raster Calculator `Con("Solar_kWh" >= 1000, 1, 0)` | → `Solar_ok` |
| 12 | Environments: Mask = `Albrechtice_boundary` | |
| 13 | Raster Calculator `"Slope_ok" * "CLC_ok" * "Solar_ok"` | → `PV_suitable` |

## Časté záseky

- **Vidím jen stínovaný reliéf a Slope dává nesmysly** → u image služby `dmr5g` není Processing Template nastaven na None. Lepší je pracovat se staženými daty.
- **Create LAS Dataset odmítá .laz** → převeď soubory nástrojem Convert LAS do .zlas.
- **Rastry se nedají kombinovat / výsledek má posunutou mřížku** → Snap Raster nebyl nastaven před výpočtem; přepočítej dotčený rastr.
- **Solar_ok je všude 0 nebo všude 1** → špatné jednotky: Raster Solar Radiation dává kWh/m² (nepřeváděj), Area Solar Radiation Wh/m² (děl 1000). Podívej se na statistiku rastru.
- **Pruh podivných hodnot podél hranice obce** → DMR ořezaný na obec místo bufferu, nebo Mask nastavena už před výpočtem záření.
- **Vybralo se víc obcí** → filtr jen podle názvu; přidej okres nebo kód obce.
- **Výpočet záření běží věčně** → počítáš bez masky nebo na celé službě; použij lokální `DMR5G_buffer`, Analysis Mask obce, případně Cell Size 10 m nebo GPU.
- **CLC nemá pole CODE_18** → připoj legendu (Join) přes ID třídy, nebo použij pole s kódem pod jiným názvem.
- **Mix S-JTSK a WGS84** → Slope i záření vyžadují projekci v metrech; Output Coordinate System = 5514 hned na začátku.
