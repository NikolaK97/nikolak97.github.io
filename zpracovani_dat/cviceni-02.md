---
layout: default
title: "CV02 – Klimatická data ve formátu NetCDF a GRIB"
---

# CV02 – Klimatická data ve formátu NetCDF a GRIB

## Obsah

- [Formát prostorových dat GRIB](#formát-prostorových-dat-grib)
- [Formát prostorových dat NetCDF](#formát-prostorových-dat-netcdf)
- [Data potřebná ke cvičení](#data-potřebná-ke-cvičení)
- [Zadání cvičení](#zadání-cvičení)
- [Postup cvičení v QGIS](#postup-cvičení-v-qgis)
- [Postup cvičení v ArcGIS Pro](#postup-cvičení-v-arcgis-pro)

---

## Formát prostorových dat GRIB

Formát **GRB** (též známý jako **GRIB**, z anglického *GRIdded Binary*) je standardizovaný formát pro ukládání a přenos meteorologických dat, často používaný pro distribuci výstupů z numerických předpovědních modelů počasí. Je vyvinut a udržován Světovou meteorologickou organizací (WMO) a je široce používán meteorologickými službami po celém světě.

### Hlavní charakteristiky formátu GRB/GRIB

**Binární formát**
: GRB je binární formát, což umožňuje vysokou kompresi a efektivní ukládání velkých objemů meteorologických dat. Tato efektivita je důležitá zejména pro globální modely, které produkují obrovské množství dat.

**Data v mřížce (gridded data)**
: Data jsou organizována v mřížce, kde každá buňka obsahuje určitou meteorologickou veličinu, jako je teplota, tlak, vítr, vlhkost atd. Tato mřížka může pokrývat celý zemský povrch nebo specifické regiony.

**Podpora více vrstev a parametrů**
: GRB může obsahovat různé parametry (např. tlak, teplotu, vlhkost) pro různé vertikální úrovně atmosféry (např. povrch, hladiny v troposféře, stratosféře atd.). To umožňuje analýzu předpovědí v různých výškách a vrstvách atmosféry.

**Komprese a efektivní ukládání**
: GRB využívá ztrátové i bezztrátové kompresní metody, aby minimalizoval velikost souboru. To je zvláště užitečné pro distribuční účely a přenos přes internet.

**Verze formátu**
: Existují dvě hlavní verze formátu: **GRIB1** a **GRIB2**. GRIB2 je novější verze, která poskytuje vylepšené možnosti komprese a podporu širší škály meteorologických proměnných.

---

## Formát prostorových dat NetCDF

**NetCDF** (*Network Common Data Form*) je formát určený pro ukládání a sdílení vědeckých dat, zejména těch, která mají více dimenzí (například čas, výška, šířka). NetCDF umožňuje efektivní uložení a přístup k velkým souborům dat, což je často potřeba v meteorologii, klimatologii, oceánografii a dalších vědních oborech.

### Struktura NetCDF souboru

NetCDF soubor se skládá ze tří hlavních komponent: **dimenzí**, **proměnných** a **atributů**.

#### Dimenze

- Dimenze definují velikost datových polí (například čas, šířka, výška).
- Každá dimenze má název a délku.
- Dimenze mohou být neomezené (*unlimited*), což znamená, že jejich délka může růst (např. časová dimenze).

#### Proměnné

- Proměnné jsou multidimenzionální pole dat uložená v NetCDF souboru.
- Každá proměnná má přidružené dimenze a může mít také přidružené atributy.

#### Atributy

- Atributy poskytují metadata o souboru nebo jednotlivých proměnných.
- **Globální atributy** jsou přidružené k celému souboru, zatímco **atributy proměnných** jsou přidružené k jednotlivým proměnným.

---

## Data potřebná ke cvičení

**ERA5 Hourly Data on Single Levels from 1940 to Present:** <[ERA Link](https://cds.climate.copernicus.eu/datasets/reanalysis-era5-single-levels?tab=overview)>

ERA5 je pátá generace klimatických dat produkovaných Evropským centrem pro střednědobé předpovědi počasí (ECMWF). ERA5 poskytuje globální klimatická data na hodinové bázi od roku 1940 do současnosti. Tato data jsou široce využívána při výzkumu klimatu, v meteorologii a dalších geovědních oborech.

Stažení těchto dat je časově náročné, proto pro účely tohoto cvičení už byla data pro rok 2023 stažena s těmito parametry:

<!-- Sem vložte obrázek s parametry stažení, např.: -->
<!-- ![Parametry stažení dat ERA5](img/era5_parametry.png) -->

---

## Zadání cvičení

1. V prostředí **QGIS** vizualizujte data ERA5 o směru větru ve výšce 10 m nad povrchem a vytvořte animovaný GIF (soubor GRB).
2. V prostředí **ArcGIS Pro** analyzujte průměrné hodnoty teploty za posledních 10 let (2014–2023) ve vámi vytvořených bodech a vykreslete graf.
3. Vypočítejte **zonální statistiku** v jednotlivých okresech ČR.

---

## Postup cvičení v QGIS

1. **Přidejte data ERA5** do prostředí QGIS – realizujte přes **Prohlížeč**, ne přes nástroj *Správce otevřených zdrojů dat*.
   - U této vrstvy nastavte souřadnicový systém **WGS 84 (EPSG:4326)**.
   - Přidejte si i vrstvu **krajů ČR**.
   - Jako souřadnicový referenční systém projektu nastavte **Pseudo-Mercator (EPSG:3857)**.
   - Podle časového rozsahu stažených dat se zobrazuje první rastr v celé sadě, v našem případě síla větru v m/s 1. 1. 2023 v čase 0:00.

   <!-- ![Data ERA5 v QGIS](img/qgis_era5.png) -->

2. **Symbologie** – ve vlastnostech vrstvy s klimatickými daty v kartě *Symbologie* je možné zvolit, jakou proměnnou chceme vykreslit (vítr, teplota, srážky). U větru je možné zapnout **vektory směru větru**. Otestujte různé možnosti nastavení symbologie u větru (barevný rozsah, vzhled vektoru apod.).

   <!-- ![Symbologie větru](img/qgis_symbologie.png) -->

3. **Časový ovladač** – v nástrojové liště je k dispozici nástroj *Panel časového ovladače*, který umožňuje pracovat s daty, u kterých je k dispozici časová složka. Vyzkoušejte si možnosti animované časové navigace.

4. **Popisek s datem a časem** – před exportem a tvorbou animovaného GIFu je vhodné doplnit informace o datu a čase:
   1. Vytvořte dočasnou bodovou vrstvu pro popisek, který umístíte do rastru (*Vrstva → Vytvořit vrstvu → Nová dočasná pracovní vrstva*).
   2. Ve vlastnostech této vrstvy v záložce *Časový* zaškrtněte možnost **Dynamic Temporal Control** a z rozbalovacího menu vyberte možnost **Překreslit pouze vrstvu**.
   3. V záložce *Popisky* zvolte možnost **Jednotlivé popisky** a do pole *Hodnota* vložte výraz:

      ```
      format_date(@map_start_time, 'dd MMMM yyyy') || '\n' || format_date(@map_start_time, 'HH:mm')
      ```

      > **Pozor:** při kopírování textu z jiných dokumentů se mohou apostrofy změnit na typografické (`‘ ’`). Výraz vyžaduje rovné apostrofy (`'`).

   <!-- ![Nastavení popisku](img/qgis_popisek.png) -->

5. **Export animace** – vyberte si časový interval v rozsahu **1 týdne** a exportujte animaci.

6. **Tvorba GIFu** – využijte některý z online nástrojů, např. <https://ezgif.com/>, a vytvořte animovaný GIF.

---

## Postup cvičení v ArcGIS Pro

1. **Přidání dat** – soubor `mean2014_2023.nc`, který obsahuje průměrné měsíční hodnoty teplot v **K** od roku 2014 do roku 2023, přidejte pomocí *Add Data → Multidimensional Raster Layer*.

   > V ArcGIS Pro označuje *multidimensional raster* typ rastrového datového formátu, který ukládá data s více dimenzemi. Typicky zahrnuje tři hlavní dimenze: prostorovou (x, y), časovou a případně i další, jako je výška nebo hloubka. Tento formát se často používá pro uchování komplexních datových sad, jako jsou klimatické modely, oceánografické údaje nebo meteorologická data.

   <!-- ![Multidimenzionální rastr v ArcGIS Pro](img/arcgis_multidim.png) -->

2. Po přidání multidimenzionálního rastru se aktivuje menu **Multidimensional** (vpravo nahoře v liště), kde je možné vybírat konkrétní datum.

3. **Bodová vrstva** – vytvořte si vlastní bodovou vrstvu (**minimálně 10 bodů** v celé ČR), pro kterou se budou odečítat údaje o průměrných teplotách.

4. **Nástroj Sample** – nechte pro každý bod extrahovat průměrnou hodnotu teploty v daném měsíci od roku 2014 do 2023. Pro vybrané body vykreslete v Excelu průběh hodnot a interpretujte průběh i vzhledem k poloze bodu.

5. **Zonální statistika** – pro kraje ČR vypočítejte nástrojem **Zonal Statistics as Table** (statistika *mean*) zonální statistiku; data je nutné nechat zpracovat jako **multidimenzionální**.
   - Získáte tabulku, kde pro každý měsíc budete mít spočítanou průměrnou teplotu v daném kraji.
   - Převeďte hodnoty na stupně Celsia: `°C = K − 273,15`.
   - Pro jeden měsíc hodnoty správně vizualizujte v mapě krajů. Hodnoty pouze za jeden vybraný měsíc lze v tabulce získat aplikováním filtru pomocí **Definition Query** ve vlastnostech tabulky.

### Co je zonální statistika

Zonální statistika je analytická technika, která se používá k výpočtu statistických hodnot (např. průměr, součet, minimum, maximum, směrodatná odchylka) rastrových dat v rámci definovaných zón. Zóny jsou definovány jako oblasti vektorové vrstvy, které mohou být reprezentovány polygonovými, liniovými nebo bodovými prvky. Tento typ analýzy umožňuje porovnávat hodnoty rastrových dat mezi různými oblastmi zájmu.
