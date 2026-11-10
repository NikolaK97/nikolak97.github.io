# CV10 – Explorační analýza dat a deterministické interpolační metody

Podrobný a vysvětlující návod pro ArcGIS Pro 3.x (Spatial Analyst, volitelně Geostatistical Analyst)

---

## 1. O čem cvičení je (velký obrázek)

Máš k dispozici „pravdivý“ spojitý povrch teploty – raster z netCDF souboru, který pokrývá celé území. Ve cvičení si nasimuluješ situaci, která je v praxi běžná: teplotu neznáme všude, ale jen v několika bodech (jako by to byly meteostanice). Z těchto bodů se pak snažíme povrch teploty zpětně dopočítat (interpolovat) a zjišťujeme, jak dobře to jednotlivé metody zvládnou.

Celý postup má proto logiku, kterou je dobré mít v hlavě, než začneš klikat:

1. **Připravíš zdrojová data** – z vícerozměrného netCDF (mnoho měsíců) vytáhneš jeden měsíc jako obyčejný 2D raster.
2. **Vytvoříš „stanice“** – 100 náhodných bodů v ČR.
3. **Změříš v nich teplotu** – odečteš hodnotu rastru do bodů a převedeš ji z Kelvinů na °C.
4. **Data prozkoumáš (EDA)** – než data použiješ, musíš vědět, jak vypadají: rozsah, rozdělení, odlehlé hodnoty, trendy.
5. **Interpoluješ** – ze 100 bodů vytvoříš spojité povrchy metodami IDW, Spline a Natural Neighbor.
6. **Ověříš přesnost** – křížovou validací spočítáš chyby (RMSE, MAE, ME) a metody porovnáš.
7. **Interpretuješ** – která metoda a nastavení vyšly nejlépe a proč.

Důležité je pochopit, proč je EDA před interpolací: interpolace jen „věří“ bodům. Pokud je v datech chyba (např. bod mimo raster s hodnotou −9999), interpolace ji poslušně rozmaže do okolí a výsledek je k ničemu. EDA je tedy kontrola kvality a zároveň nápověda, jakou metodu a parametry zvolit.

### Co budeš potřebovat
- **Data:** soubor `mean2014_2023.nc` (měl bys mít z minulých cv) a polygon hranice ČR. Volitelně digitální model reliéfu (DEM) pro bodový graf teplota vs. nadmořská výška.
- **Znalosti:** práce s atributovou tabulkou (přidání pole, Calculate Field), spouštění nástrojů v panelu Geoprocessing a základní práce se symbologií.

---

## 2. Teorie: explorační analýza dat (EDA)

EDA je soubor jednoduchých statistických a grafických technik, kterými si „osaháš“ data. Neodpovídá na žádnou konkrétní hypotézu, ale ukáže, co v datech je a na co si dát pozor.

### 2.1 Popisné (deskriptivní) statistiky

**Průměr (mean)** je součet hodnot dělený jejich počtem. Je citlivý na extrémy – jediná chybná hodnota ho výrazně posune.

**Medián** je prostřední hodnota po seřazení. Na extrémy citlivý není. Pokud se průměr a medián výrazně liší, je rozdělení zešikmené nebo jsou v datech odlehlé hodnoty.

**Rozptyl a směrodatná odchylka (std)** popisují, jak moc hodnoty kolísají kolem průměru. Směrodatná odchylka je ve stejných jednotkách jako data (°C), proto se lépe interpretuje. Bude se ti hodit i později: RMSE interpolace je dobré porovnat se směrodatnou odchylkou dat. Pokud je RMSE skoro stejně velké jako std, interpolace není o moc lepší, než kdybys všude dosadil průměr.

**Minimum, maximum a rozpětí** jsou nejrychlejší kontrola chyb – hodnota −10 272 °C nebo +500 °C okamžitě prozradí problém.

**Percentily a kvartily** – 25. percentil (Q1) je hodnota, pod kterou leží čtvrtina dat, 75. percentil (Q3) tři čtvrtiny. Rozdíl Q3 − Q1 je mezikvartilové rozpětí (IQR).

### 2.2 Odlehlé hodnoty (outliers)

Odlehlá hodnota je pozorování, které se výrazně liší od ostatních. Běžné pravidlo (používá ho i krabicový graf): za odlehlé se považují hodnoty menší než Q1 − 1,5·IQR nebo větší než Q3 + 1,5·IQR.

Odlehlá hodnota ale **nemusí být chyba**. U teploty v ČR bude typicky odlehlý bod na hřebenech hor (Krkonoše, Šumava, Jeseníky) – je chladnější, protože je výš. Takový bod je skutečný a do analýzy patří. Naopak hodnota −9999 (NoData) je technická chyba a musí pryč. Proto se u každé odlehlé hodnoty podívej na mapu, kde bod leží, a rozhodni, zda je vysvětlitelná.

### 2.3 Trendy

Globální trend je systematická změna hodnoty v prostoru. U teploty v ČR je hlavním „trendem“ nadmořská výška (s výškou teplota obvykle klesá, řádově o 0,5–0,65 °C na 100 m; v zimě ale mohou vztah narušovat teplotní inverze) a do jisté míry i poloha západ–východ (vliv oceánu vs. kontinentu). Deterministické metody v tomto cvičení s trendem nijak explicitně nepracují – to je dobré vědět při interpretaci: tam, kde je teplota řízena terénem a mezi body nic není, interpolace hory „nevidí“.

### 2.4 Grafy

**Histogram** ukazuje tvar rozdělení: zda je jednovrcholové (unimodální), symetrické, zešikmené, nebo má více vrcholů (to by mohlo značit dvě odlišné populace bodů, např. nížiny a hory). Pozor na šířku tříd – příliš málo tříd tvar zakryje, příliš mnoho vytvoří „zubatý“ graf; pro 100 bodů je rozumné cca 8–12 tříd.

**Krabicový graf (box plot)** zobrazuje medián, kvartily (krabice), rozsah bez extrémů („fousy“) a jednotlivé odlehlé body. Je to nejrychlejší způsob, jak outliery najít.

**Bodový graf (scatter plot)** ukazuje vztah dvou proměnných, např. teplota vs. nadmořská výška nebo teplota vs. souřadnice X/Y. Jasný sklon mračna bodů = trend.

**Mapa** – statistika ignoruje polohu, proto vždy zobraz body na mapě a obarvi je podle teploty. Uvidíš prostorové shluky, zda jsou body rozložené rovnoměrně, a kde leží outliery.

---

## 3. Teorie: deterministické interpolační metody

„Deterministické“ znamená, že metoda počítá hodnotu pevným matematickým předpisem a nepracuje s pravděpodobností ani nedává odhad nejistoty (na rozdíl od krigingu, který patří mezi geostatistické metody). Všechny tři metody vycházejí z prvního zákona geografie: bližší věci si jsou podobnější než vzdálenější.

### 3.1 IDW – Inverse Distance Weighting (vážení inverzní vzdáleností)

Hodnota v neznámém místě je vážený průměr hodnot okolních bodů. Váha bodu klesá se vzdáleností:

```
z(x) = Σ (w_i · z_i) / Σ w_i,      kde  w_i = 1 / d_i^p
```

Kde `z_i` je hodnota v i-tém bodě, `d_i` jeho vzdálenost od odhadovaného místa a `p` exponent (power).

Co dělá exponent `p`: při p = 1 klesá váha pomalu, vzdálené body mají stále vliv a povrch je hladší. Při p = 2 (výchozí) jde o kompromis. Při p = 3 a více rozhodují prakticky jen nejbližší body; povrch je „mozaikovitý“ a kolem každého bodu vznikají typické kruhové „terče“ (bull's eyes).

Další vlastnosti, které je dobré znát:

- Výsledek **nikdy nepřekročí** minimum a maximum vstupních bodů (je to průměr). Na horách mezi body proto IDW nikdy nedá nižší teplotu, než naměřil nejchladnější bod.
- Přesně prochází vstupními body (exaktní interpolátor).
- Je citlivá na shlukování bodů: shluk blízkých bodů „přehlasuje“ osamocený bod.
- Kolik bodů se do průměru zapojí, určuje vyhledávací okolí (search radius) – viz krok 6.

### 3.2 Spline

Spline si představ jako pružnou tenkou desku (nebo gumovou blánu), kterou „napneš“ přes všechny body tak, aby byla co nejhladší – minimalizuje se celkové zakřivení povrchu. Povrch prochází přesně body, ale mezi nimi může vystoupat nad maximum nebo klesnout pod minimum dat (překmit, overshoot). To je u spojitých veličin jako teplota často žádoucí (správně odhadne lokální extrém mezi body), ale při řídkých datech to může vytvořit nesmyslné hodnoty.

V ArcGIS jsou dva typy:

- **Regularized** – hladký povrch s plynulými změnami. Parametr **Weight** (typicky 0–0,5, výchozí 0,1) určuje míru hladkosti: vyšší váha = hladší povrch, ale také výraznější překmity.
- **Tension** – povrch je „napjatější“, chová se tužeji. Parametr **Weight** (zde je to napětí, typicky 0–10, výchozí 0,1): vyšší napětí = povrch se víc drží rozsahu dat, méně překmitů, ale je méně hladký (blíží se chování IDW). Nízké napětí = pružnější, hladší povrch.

Parametr **Number of points** (výchozí 12) říká, kolik nejbližších bodů se použije pro výpočet v každé oblasti – více bodů = hladší, „globálnější“ povrch, ale delší výpočet.

### 3.3 Natural Neighbor (přirozený soused)

Kolem vstupních bodů se vytvoří Voroného (Thiessenovy) polygony – každý polygon obsahuje území, které je k danému bodu nejblíž. Když chceš odhadnout hodnotu v novém místě, „vložíš“ do diagramu nový bod. Ten si „ukousne“ části polygonů svých sousedů. Váha každého souseda je úměrná tomu, jak velkou plochu mu nový polygon ukousl.

Vlastnosti:

- Používá jen skutečné přirozené sousedy, takže se přizpůsobí nepravidelné hustotě bodů – v hustě pokrytých místech bere blízké body, v řídkých vzdálenější.
- Nemá téměř žádné parametry, výsledek je hladký, bez terčů a bez překmitů (hodnoty zůstanou v rozsahu dat).
- **Nepočítá mimo konvexní obálku bodů** (nejmenší konvexní mnohoúhelník, který obepíná všechny body). Okraje ČR, kam se obálka nedostane, zůstanou NoData. To musíš zohlednit při porovnání metod.

### 3.4 Shrnutí rozdílů

| Vlastnost | IDW | Spline | Natural Neighbor |
|---|---|---|---|
| Prochází body | ano | ano | ano |
| Může překročit rozsah dat | ne | ano | ne |
| Hladkost | závisí na p | vysoká | střední/vysoká |
| Typický artefakt | kruhové terče | překmity | NoData mimo konvexní obálku |
| Počet parametrů | více | více | téměř žádné |

---

## 4. Vstupní data a na co si dát pozor předem

**NetCDF (`mean2014_2023.nc`)** je vícerozměrný formát: kromě X a Y má i dimenzi čas (měsíce 2014–2023) a může obsahovat více proměnných. ArcGIS ho umí načíst jako tzv. multidimenzionální raster. Ty z něj potřebuješ vybrat jeden „řez“ – jednu proměnnou v jednom čase.

**Jednotky:** teplota je v Kelvinech. Převod: °C = K − 273,15. Ověř si to – pokud jsou hodnoty kolem 265–280, jsou v Kelvinech; pokud kolem 0, jsou už ve °C a nic nepřeváděj.

**Pracovní měsíc:** v celém cvičení pracuj s **prosincem 2014** – z něj vytvoříš raster i odečteš hodnoty do bodů. Je důležité, aby body i raster, se kterým je porovnáváš, byly ze stejného měsíce; jinak by validace i interpretace neměly smysl. Leden 2014 je jen volitelné rozšíření (viz konec kroku 1).

**Souřadnicový systém:** netCDF klimatická data bývají v zeměpisných souřadnicích (WGS 84, stupně). Všechny tři interpolační metody pracují se vzdálenostmi, a stupně nejsou stejně dlouhé ve směru sever–jih a západ–východ (v ČR je 1° délky cca 70 km, ale 1° šířky cca 111 km). Proto body a interpolaci připrav v metrickém systému **S-JTSK / Krovak East North (EPSG:5514)**. Pak také dává smysl zadávat velikost buňky 1000 m a poloměr hledání v km.

**Hustota bodů:** ČR má cca 78 900 km². Při 100 bodech připadá na jeden bod průměrně cca 790 km², průměrná vzdálenost mezi sousedními body je tedy řádově 25–30 km. Z toho vychází rozumné nastavení vyhledávacího poloměru (viz krok 6).

---

## 5. Krok 1 – Import netCDF a výběr prosince 2014

**Cíl:** získat obyčejný jednovrstvý raster teploty pro 1. 12. 2014. (nebo jiné datum, ale vyber jedno!)

2. Načti netCDF: v panelu **Catalog** klikni pravým na **Folders → Add Folder Connection** a připoj složku se souborem. Pak soubor přetáhni do mapy, nebo použij **Map → Add Data → Multidimensional Raster Layer** a vyber `mean2014_2023.nc`. V dialogu zvol proměnnou s teplotou.
3. Po načtení se v pásu karet objeví záložka **Multidimensional**. V ní je ovládání **Current Display Slice** (aktuální zobrazený řez) – posuvník nebo rozbalovací seznam časů. Nastav 2014-12-01 (podle toho, jak je čas v souboru uložen, to může být i jiný den v prosinci, např. 2014-12-15; vyber prosincový řez).
4. Ověř proměnnou: v **Contents** klikni pravým na vrstvu → **Properties → Source**. Uvidíš název proměnné (např. `tg`, `t2m`, `tas`), jednotky a seznam časových hodnot.
5. Teď je potřeba řez skutečně uložit jako samostatný raster. Zobrazení řezu v mapě totiž nevytváří nová data. Použij nástroj **Subset Multidimensional Raster** (Multidimensional → Data Management → Subset, nebo v Geoprocessing vyhledej název):
   - *Input Multidimensional Raster:* tvoje netCDF vrstva
   - *Output Multidimensional Raster:* `temp_2014_12_01.crf`
   - *Variables:* zaškrtni proměnnou teploty
   - *Dimension Definition:* **By Values** (podle hodnot)
   - *Dimension:* StdTime (čas), *Values:* datum prosince 2014
6. Výsledek přidej do mapy a zkontroluj: v **Properties → Source** by měl mít jen jeden časový řez. Klikni do mapy (Explore) a podívej se na několik hodnot – měly by být kolem 265–280 K.
7. Pro odevzdání můžeš raster vyexportovat i do GeoTIFF: pravý klik na vrstvu → **Data → Export Raster** → `temp_2014_12.tif`.

*Proč .crf?* CRF (Cloud Raster Format) je formát Esri, který umí uchovat vícerozměrná data a dobře se s ním pracuje v nástrojích Multidimensional. Pro předání mimo ArcGIS je praktičtější .tif.

**Volitelně leden 2014:** postup je stejný, jen v kroku 5 zadej datum ledna a výstup `temp_2014_01_01.crf`.

---

## 6. Krok 2 – Hranice ČR a 100 náhodných bodů

**Cíl:** vytvořit sadu 100 „meteostanic“ rovnoměrně náhodně rozmístěných v ČR, v metrickém souřadnicovém systému.

1. Přidej polygon hranice státu z podkladů ke cvičení (případně z ArcČR nebo RÚIAN). Pokud je v jiném souřadnicovém systému než S-JTSK, převeď ho nástrojem **Project** (Data Management Tools → Projections and Transformations → Project) do *S-JTSK Krovak East North (EPSG:5514)*.
2. Nastav souřadnicový systém mapy: pravý klik na **Map** v Contents → **Properties → Coordinate Systems** → S-JTSK Krovak East North.
3. Spusť **Create Random Points** (Data Management Tools → Sampling → Create Random Points; v některých verzích pod Feature Class):
   - *Output Location:* projektová geodatabáze
   - *Output Point Feature Class:* `rand_points_100`
   - *Constraining Feature Class:* hranice ČR (bod vznikne jen uvnitř polygonu)
   - *Number of Points:* 100
   - *Minimum Allowed Distance:* volitelné, např. 5 km – zabrání tomu, aby dva body padly téměř na sebe (to zhoršuje chování interpolací a nepřidává informaci)
4. Zkontroluj rozmístění na mapě. Náhodné body nikdy nejsou dokonale rovnoměrné – někde vzniknou shluky, jinde „díry“. To je v pořádku a je to přesně věc, kterou později popíšeš v interpretaci (jak rozložení bodů ovlivnilo výsledek).
5. Pokud raster teploty nepokrývá celé území (např. okraj u hranice), body mimo data budou mít NoData. Řeší se to v kroku 3.

*Tip:* náhodné body se při každém spuštění vygenerují jinak. Pokud chceš výsledek zopakovat, nastav v **Environments → Random Number Generator** pevné semínko (Seed).

---

## 7. Krok 3 – Odečet teplot do bodů a převod K → °C

**Cíl:** každý bod dostane atribut s teplotou ve °C.

1. Spusť **Extract Values to Points** (Spatial Analyst Tools → Extraction):
   - *Input point features:* `rand_points_100`
   - *Input raster:* `temp_2014_12_01.crf`
   - *Output point features:* `rand_points_100_temp`
   - *Interpolate values at the point locations:* zaškrtnout. Bez zaškrtnutí dostane bod hodnotu buňky, ve které leží (schodovitě); se zaškrtnutím se hodnota bilineárně dopočítá ze sousedních buněk. U hrubého klimatického rastru je to vhodnější.

   Nástroj si poradí s tím, že body jsou v S-JTSK a raster ve WGS 84 – souřadnice převede za běhu.
2. Otevři atributovou tabulku. Přibylo pole **RASTERVALU** s hodnotou v Kelvinech.
3. **Kontrola NoData:** seřaď tabulku podle RASTERVALU. Pokud má některý bod hodnotu **−9999**, leží mimo platná data rastru. Takový bod smaž (nebo posuň dovnitř). Kdybys ho nechal, po převodu by měl teplotu −10 272 °C a zničil by všechny interpolace.
4. Přidej pole: v tabulce **Add Field** → název `Temp_C`, typ **Double** → ulož (**Fields → Save**).
5. Klikni pravým na záhlaví `Temp_C` → **Calculate Field**, jazyk **Python**, výraz:

   ```
   !RASTERVALU! - 273.15
   ```

6. Zkontroluj výsledek: hodnoty by měly být realistické pro prosinec v ČR (zhruba mezi −6 a +4 °C, nejchladněji na horách).

---

## 8. Krok 4 – EDA v ArcGIS Pro

**Cíl:** popsat data čísly, grafy a mapou a najít případné problémy.

### 8.1 Deskriptivní statistiky

1. V atributové tabulce klikni pravým na záhlaví `Temp_C` → **Statistics**. Otevře se graf s panelem statistik (Count, Min, Max, Mean, Std. Dev., Median, …).
2. Zapiš si hodnoty do tabulky (budeš je potřebovat v protokolu i při hodnocení RMSE).
3. Pro trvalý výstup (tabulku) můžeš použít nástroj **Summary Statistics** (Analysis Tools → Statistics): Statistics Field = `Temp_C`, statistiky MIN, MAX, MEAN, MEDIAN, STD, COUNT.
4. Kvartily (25. a 75. percentil) nejsnáz odečteš z krabicového grafu (viz níže).

Na co se dívat: liší se výrazně průměr a medián? Je minimum či maximum podezřelé? Jak velká je směrodatná odchylka vzhledem k rozpětí?

### 8.2 Histogram

Pravý klik na vrstvu `rand_points_100_temp` → **Create Chart → Histogram** → v panelu Chart Properties zvol *Number = Temp_C*. Počet tříd (Bins) nastav na cca 8–12. Zapni zobrazení průměru, mediánu a std (zaškrtávátka v Chart Properties).

Interpretace: je rozdělení jednovrcholové a přibližně symetrické? Je „ocas“ na straně nízkých teplot (horské body)?

### 8.3 Krabicový graf

**Create Chart → Box Plot**, *Numeric field = Temp_C*. Najeď myší na krabici – zobrazí se kvartily, medián a meze. Body mimo „fousy“ jsou odlehlé hodnoty. Klikni na ně – vyberou se i v mapě a v tabulce, takže hned uvidíš, kde leží.

### 8.4 Bodový graf (trendy)

Aby bylo s čím teplotu porovnávat, přidej bodům další proměnné:

- **Souřadnice:** nástroj **Add Geometry Attributes** (Data Management → Features), Geometry Properties = *Point x- and y-coordinates*. Přibudou pole POINT_X a POINT_Y.
- **Nadmořská výška (pokud máš DEM):** **Extract Multi Values to Points** s rastrem DEM, pole pojmenuj `Elev`.

Pak **Create Chart → Scatter Plot**: X = `Elev` (nebo POINT_X / POINT_Y), Y = `Temp_C`. Zapni trendovou linii – graf ukáže R² lineární regrese. Silný záporný vztah s výškou je u teploty očekávaný a vysvětluje, proč horské body vycházejí jako outliery.

### 8.5 Prostorová vizualizace

1. **Symbologie bodů:** pravý klik na vrstvu → **Symbology → Graduated Colors**, Field = `Temp_C`, Method = **Quantile**, 5 tříd, barevná škála modrá–červená. Kvantily zajistí, že v každé třídě je stejný počet bodů – dobře se tak ukáže prostorové rozložení.
2. **Heat map:** Symbology → **Heat Map**. Pozor: klasická heat mapa zobrazuje hlavně hustotu bodů; pokud zvolíš váhu podle `Temp_C`, kombinuje hustotu a hodnotu. Ber ji jen jako orientační náhled, ne jako analýzu.

Shrň: kde jsou body teplé a studené, zda je patrný trend (hory vs. nížiny, západ vs. východ) a kde jsou mezery v pokrytí.

---

## 9. Krok 5 – Nastavení prostředí (Environments) před interpolací

Aby šly výsledky porovnat buňku po buňce, musí mít všechny výstupní rastry stejný souřadnicový systém, rozsah, velikost buňky i zarovnání mřížky. Nejjednodušší je nastavit to jednou pro celý projekt: **Analysis → Environments** (platí pro všechny nástroje).

- **Output Coordinate System:** S-JTSK Krovak East North (EPSG:5514).
- **Processing Extent:** podle vrstvy hranice ČR.
- **Cell Size:** 1000 (m).
- **Mask:** polygon hranice ČR – výsledek mimo ČR bude NoData. Maska ořezává výstup, nebrání ale výpočtu uvnitř obdélníku rozsahu.
- **Snap Raster:** první vytvořený výstupní raster (např. IDW), aby se další mřížky zarovnaly na stejné buňky.

**Pojmenování výstupů:** dávej parametry do názvů, ať se v nich vyznáš, např. `idw_p2_var12_cs1000.tif`, `spl_reg_w01_n12.tif`, `spl_ten_w5_n12.tif`, `nn_cs1000.tif`. Pozn.: pokud ukládáš do geodatabáze, nepoužívej tečky ani přípony – stačí `idw_p2_var12`.

*K velikosti buňky:* vstupní klimatický raster má nejspíš rozlišení v řádu km (často kolem 0,1°, tj. 7–11 km). Buňka 1000 m tedy nepřidává reálnou podrobnost – je to jen jemnější vykreslení interpolace ze 100 bodů. Pro účely cvičení je to v pořádku.

---

## 10. Krok 6 – Interpolace

Všechny nástroje najdeš v **Spatial Analyst Tools → Interpolation**. Jako vstup vždy `rand_points_100_temp`, *Z value field* = `Temp_C`.

### 10.1 IDW

Parametry nástroje **IDW**:

- *Output cell size:* 1000 (převezme se z Environments)
- *Power:* testuj 1, 2 a 3 (případně 1,5)
- *Search radius:*
  - **Variable** – vždy se použije pevný počet nejbližších bodů (*Number of points*, např. 8, 12, 20). Vyhledávací vzdálenost se tedy přizpůsobí hustotě. *Maximum distance* můžeš nechat prázdné.
  - **Fixed** – použijí se všechny body v pevném poloměru (*Distance*, např. 30 000 nebo 60 000 m). *Minimum number of points* (např. 5) zajistí, že se v řidších místech poloměr zvětší, dokud se nenajde dostatek bodů. Při 30 km a průměrné rozteči ~28 km je bez minima riziko, že se v buňce najde jen 1–2 body.
- *Input barrier polyline features:* nech prázdné.

**Doporučený plán testování** (aby se vyhodnocení nerozrostlo): nejdřív zafixuj okolí (Variable, 12 bodů) a měň power 1 / 2 / 3. Pak u nejlepšího power porovnej Variable 8 / 12 / 20 a Fixed 30 km / 60 km.

**Co sledovat:** s rostoucím power se objevují „terče“ kolem bodů, s malým počtem sousedů skoky a nespojitosti. Velký počet sousedů a malý power dávají příliš vyhlazený, „rozplizlý“ povrch.

### 10.2 Spline

Parametry nástroje **Spline**:

- *Spline type:* **REGULARIZED** a **TENSION** (spusť oba)
- *Weight:*
  - u Regularized testuj např. 0; 0,1; 0,5 (vyšší = hladší),
  - u Tension testuj např. 0,1; 1; 5; 10 (vyšší = tužší, méně překmitů)
- *Number of points:* 12 (výchozí), pak např. 20 a 30

**Co sledovat:** po vytvoření se podívej na min/max výstupního rastru (Properties → Source → Statistics, nebo symbologie). Pokud jsou výrazně mimo rozsah bodů (např. −15 °C, když nejchladnější bod má −5 °C), Spline překmitává – typicky na okrajích území a v místech bez bodů. Pomůže vyšší tension nebo nižší regularized weight.

### 10.3 Natural Neighbor

Nástroj **Natural Neighbor** má jen *Z value field* a *Output cell size*. Nic dalšího nastavovat nemusíš.

**Co sledovat:** u hranic ČR, kam nedosahuje konvexní obálka bodů, bude výsledek NoData. Je to vlastnost metody, ne chyba. V protokolu to zmiň a při porovnání map počítej s tím, že NN pokrývá menší plochu.

### 10.4 Mapové výstupy

Pro porovnání je klíčové, aby **všechny mapy měly stejnou barevnou stupnici**: Symbology → Stretch nebo Classify se stejnými hranicemi tříd (např. −8 až +5 °C po 1 °C). Jinak bude každá mapa automaticky roztažená na vlastní rozsah a rozdíly mezi metodami nepůjde vizuálně posoudit. Do layoutu přidej legendu, měřítko, severku, body a hranici ČR.

---

## 11. Krok 7 – Hodnocení přesnosti

Mapy samy neřeknou, která metoda je nejlepší – „hezčí“ povrch nemusí být přesnější. Potřebuješ odhadovat hodnoty v místech, kde je znáš, a porovnat je se skutečností.

### 11.1 Metriky chyb

Pro každý validační bod spočítáš reziduum `e_i = observed_i − predicted_i` (naměřeno minus odhad). Z nich:

```
ME   = (1/n) · Σ e_i                 průměrná chyba (bias)
MAE  = (1/n) · Σ |e_i|               průměrná absolutní chyba
RMSE = sqrt( (1/n) · Σ e_i² )        odmocnina průměrné kvadratické chyby
```

**ME** ukazuje systematické vychýlení: kladné = metoda teplotu soustavně podhodnocuje, záporné = nadhodnocuje. Ideál je 0. Pozor: ME blízko nuly neznamená přesnost – velké kladné a záporné chyby se mohou vyrušit.

**MAE** je „průměrná velikost chyby“ ve °C, nejsnáz interpretovatelná („odhad se v průměru mýlí o 0,6 °C“).

**RMSE** také ve °C, ale velké chyby penalizuje víc (díky umocnění). RMSE je vždy ≥ MAE; čím větší rozdíl mezi nimi, tím víc se vyskytují ojedinělé velké chyby. Nejlepší metoda má nejmenší RMSE a MAE a ME blízko nuly.

Užitečné srovnání: RMSE porovnej se směrodatnou odchylkou `Temp_C` z EDA.

### 11.2 Varianta A – Geostatistical Wizard (leave-one-out)

Vyžaduje rozšíření Geostatistical Analyst. Princip „leave-one-out“ křížové validace: postupně se vynechá každý bod, povrch se spočítá ze zbylých 99 a odhad se porovná s vynechanou hodnotou. Využijí se tak všechna data.

1. **Analysis → Geostatistical Wizard**.
2. V sekci **Deterministic methods** zvol **Inverse Distance Weighted**. Source dataset = `rand_points_100_temp`, Data field = `Temp_C`. **Next**.
3. Nastav Power a Neighborhood (Standard; Max neighbors, Min neighbors, typ sektorů). Tlačítkem **Optimize** u power ti průvodce sám najde exponent s nejnižší RMSE.
4. **Next** → stránka **Cross Validation**. Uvidíš graf naměřeno vs. odhadnuto a souhrn: **Count, Mean (= ME), Root-Mean-Square (= RMSE)**. Hodnoty si zapiš.
5. **Finish** vytvoří geostatistickou vrstvu. Pro MAE: klikni na ni pravým → **Cross Validation** (nebo spusť nástroj *Cross Validation* v Geoprocessing) a ulož výsledky do tabulky/bodů. Výstup obsahuje pole Measured, Predicted a Error. Přidej pole `abs_err` = `abs(!Error!)` a nástrojem **Summary Statistics** spočítej MEAN → to je MAE.
6. Zopakuj pro různé konfigurace (power 1/2/3, počet sousedů) a výsledky zapiš do tabulky.

Spline-like povrchy jsou v průvodci pod **Radial Basis Functions** (kernely *Completely Regularized Spline*, *Spline with Tension*) – nejsou matematicky totožné s nástrojem Spline ze Spatial Analystu, ale jsou mu blízké. **Natural Neighbor v průvodci není.** Pro porovnání všech tří metod stejným způsobem proto použij variantu B.

### 11.3 Varianta B – ruční validace (trénovací / validační sada)

Funguje pro všechny metody a nevyžaduje Geostatistical Analyst.

1. **Rozdělení bodů 80/20:**
   - s Geostatistical Analystem: nástroj **Subset Features**, Size of training = 80 (percent) → vzniknou dvě vrstvy `train` a `test`,
   - bez něj: přidej pole `split` (Short) a Calculate Field (Python):
     ```
     1 if random.random() < 0.2 else 0
     ```
     (v Code Block napiš `import random`), pak **Export Features** s dotazem `split = 0` → `train`, `split = 1` → `test`. Zkontroluj, že test má cca 20 bodů.
2. **Interpolace z trénovací sady:** spusť IDW, Spline i NN znovu, tentokrát se vstupem `train` (stejné Environments). Výstupy pojmenuj např. `val_idw_p2`, `val_spl_ten5`, `val_nn`.
3. **Odečet odhadů do validačních bodů:** použij **Extract Multi Values to Points** (Spatial Analyst → Extraction) s vrstvou `test` a všemi validačními rastry najednou. Výhoda oproti Extract Values to Points: můžeš pojmenovat pole (`pred_idw`, `pred_spl`, `pred_nn`) a nevznikne kolize s existujícím polem RASTERVALU.
4. **Kontrola NoData:** u Natural Neighbor mohou mít validační body na okraji (mimo konvexní obálku trénovacích bodů) hodnotu NoData. Ty pro NN z výpočtu vynech a v protokolu uveď, kolik bodů zůstalo.
5. **Výpočet chyb** (pro každou metodu zvlášť; příklad pro IDW): přidej pole Double a spočítej:
   ```
   res_idw    = !Temp_C! - !pred_idw!
   abs_idw    = abs(!res_idw!)
   sq_idw     = !res_idw! ** 2
   ```
6. **Souhrn:** **Summary Statistics**, Input = `test`, statistiky MEAN pro `res_*`, `abs_*` a `sq_*`, Case field prázdné. Z výsledku:
   - MEAN(res) = **ME**
   - MEAN(abs) = **MAE**
   - sqrt(MEAN(sq)) = **RMSE** (odmocninu dopočítej ručně nebo přes Calculate Field)

**Upozornění k velikosti vzorku:** 20 validačních bodů je málo, výsledek silně závisí na tom, které body náhodou padly do testovací sady. Rozdíly v řádu setin °C mezi metodami proto nepřeceňuj. Pokud máš čas, rozdělení zopakuj s jiným semínkem a výsledky zprůměruj.

### 11.4 Bonus: porovnání s „pravdou“

Protože máš původní raster, můžeš chybu spočítat na celé ploše, nejen v bodech. Převeď původní raster na °C a do stejné mřížky (**Project Raster** do S-JTSK s buňkou 1000 m a stejným Snap Rasterem), pak v **Raster Calculator**:

```
"temp_dec_C" - "idw_p2_var12"
```

Výsledná mapa rozdílů krásně ukáže, kde interpolace selhává (typicky hory a místa bez bodů). Je to výborný podklad pro interpretaci.

---

## 12. Výstupy k odevzdání a jak napsat interpretaci

**Data a rastry:**
- `temp_2014_12_01.crf` (případně export .tif),
- `rand_points_100_temp` s polem `Temp_C`,
- interpolační rastry `idw_*`, `spline_*`, `nn_*` s parametry v názvu.

**EDA:**
- screenshot / tabulka statistik `Temp_C`,
- histogram, krabicový graf, případně bodový graf (teplota vs. výška nebo souřadnice),
- mapa bodů obarvených podle teploty.

**Interpolace:**
- mapové kompozice všech metod se **stejnou legendou**, měřítkem a body.

**Přesnost:**
- report / screenshot z Geostatistical Wizard (IDW, cross-validation),
- souhrnná tabulka metrik, např.:

| Metoda | Parametry | n | ME [°C] | MAE [°C] | RMSE [°C] |
|---|---|---|---|---|---|
| IDW | p = 1, var 12 | | | | |
| IDW | p = 2, var 12 | | | | |
| IDW | p = 3, var 12 | | | | |
| Spline Regularized | w = 0,1, n = 12 | | | | |
| Spline Tension | w = 5, n = 12 | | | | |
| Natural Neighbor | – | | | | |

**Interpretace** (stačí krátký text, ale konkrétní, s čísly). Odpověz na tyto otázky:

1. Která metoda a nastavení měly nejnižší RMSE/MAE? O kolik byly lepší než ostatní, a je ten rozdíl při 20 validačních bodech vůbec významný?
2. Je některá metoda systematicky vychýlená (ME výrazně ≠ 0)? Proč by mohla být?
3. Jak se výsledky liší vizuálně (terče u IDW, překmity u Spline, NoData na okrajích u NN)?
4. Kde jsou největší chyby? Typicky hory a oblasti s řídkými body – souvisí to s nadmořskou výškou, kterou deterministické metody neberou v úvahu.
5. Jak se projevilo rozložení bodů (shluky, mezery, okraje území)?
6. Proč bylo nutné pracovat v metrické projekci (S-JTSK) a co by se stalo ve stupních (zkreslené vzdálenosti, nesmyslný poloměr hledání a velikost buňky)?

---

## 13. Časté chyby a kontrolní seznam

**Časté chyby:**

- Bod mimo data rastru s hodnotou −9999 nebyl odstraněn → po převodu −10 272 °C a zničená interpolace.
- Převod K → °C proveden dvakrát, nebo na datech, která už ve °C byla.
- Body a raster z jiného měsíce (leden vs. prosinec).
- Interpolace ve WGS 84 – vzdálenosti, poloměr a velikost buňky ve stupních.
- Každý výstup s jinou velikostí buňky nebo rozsahem → mapy nejdou porovnat.
- Mapy s automaticky roztaženou barevnou škálou → zdánlivě stejné povrchy, i když se liší.
- Validační body s NoData (NN mimo konvexní obálku) započítané do metrik.
- Validace interpolací vytvořených ze **všech** bodů na těch samých bodech – vyjde nulová chyba (metody jsou exaktní) a nic to neříká.
- RMSE spočítané jako průměr druhých mocnin bez odmocniny.

