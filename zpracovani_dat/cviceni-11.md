---
---

# Cvičení: Mapa náchylnosti ke svahovým deformacím – Radhošť, Beskydy

## O čem cvičení je

Sestavíte mapu, která ukazuje, kde jsou severní svahy Radhošťského hřbetu nad Trojanovicemi náchylné k sesuvům. Použijete digitální model reliéfu, geologickou mapu a vodní toky a výsledek porovnáte se skutečnými sesuvy z registru České geologické služby.

**Proč Beskydy:** Moravskoslezské Beskydy leží ve flyšovém pásmu Vnějších Západních Karpat. Střídání pevných pískovců a měkkých jílovců, strmé svahy a hojné srážky z nich dělají jednu z nejsesuvnějších oblastí v Česku. Na svazích Radhoště je sesuvů mnoho a ČGS je tu zmapovala v měřítku 1 : 10 000.

**Proč to geologové dělají:** Mapy náchylnosti slouží pro územní plánování, posudky staveb a silnic a pro obce a kraje. Princip je vždy stejný: vybrat faktory, které sesuvy ovlivňují, ohodnotit je a zkombinovat.

**Co se naučíte:** založit projekt a nastavit prostředí analýz, odvodit sklon svahů, obkreslit geologickou mapu do vlastní vektorové vrstvy, převést vektory na rastr, počítat vzdálenosti, reklasifikovat rastry, počítat v Raster Calculatoru, ověřit model a vytvořit výstupní mapu.

**Co odevzdáte:** mapu náchylnosti v PDF, vyplněnou tabulku z úkolu 6 a krátké odpovědi na otázky.

**Časová náročnost:** 3 × 90 minut. První blok: úkoly 1 a 2 a začátek obkreslování geologie (úkol 3a–c). Druhý blok: dokončení úkolu 3 a úkol 4. Třetí blok: úkoly 5–7. Na konci každého bloku projekt uložte (**Project → Save**).

**Jak číst postup:** Názvy tlačítek a nástrojů jsou **tučně** v angličtině, tak jak je uvidíte v ArcGIS Pro. Názvy vrstev a souborů jsou `takto`. Šipka → znamená další klik v nabídce.

## Data a software

Od vyučujícího dostanete soubor `Beskydy_sesuvy.zip`. Obsahuje geodatabázi se čtyřmi vrstvami, všechny v souřadnicovém systému S-JTSK (EPSG:5514). Pátou vrstvu, geologii, si v úkolu 3 obkreslíte sami podle geologické mapy ČGS.

| Vrstva | Co obsahuje | Původní zdroj |
| --- | --- | --- |
| `uzemi` | Obdélník zájmového území, asi 6 × 6 km | připravil vyučující |
| `dmr5g` | Digitální model reliéfu, buňka 5 m, výšky v m | [ČÚZK, DMR 5G](https://geoportal.gov.cz/php/micka/record/basic/CZ-CUZK-ATOM-DMR5G-SJTSK?dlang=eng) |
| `vodni_toky` | Síť vodních toků | [DIBAVOD, VÚV TGM](http://www.dibavod.cz/index.php?id=27) |
| `sesuvy` | Zmapované plošné svahové deformace | [ČGS, registr svahových nestabilit](https://mapy.geology.cz/arcgis/rest/services/Geohazardy/sesuvy_Geofond/MapServer/0/query?where=1%3D1&outFields=*&f=geojson) |
| `sesuvy_body` | Zmapované plošné svahové deformace | [ČGS, registr svahových nestabilit](https://mapy.geology.cz/arcgis/rest/services/Geohazardy/sesuvy_Geofond/MapServer/1/query?where=1%3D1&outFields=*&f=geojson) |
| `geologie` (vytvoříte sami) | Geologické jednotky s popisem horniny | obkresleno podle [geologické mapy ČGS 1 : 50 000]([https://mapy.geology.cz/geocr50/](http://inspire.geology.cz/geoserver/wfs?service=WFS&version=1.0.0&request=GetFeature&typeName=gsmlp:CZE_CGS_500k_Geology_Lito&outputFormat=SHAPE-ZIP)) |

**Software:** ArcGIS Pro 3.x s extenzí **Spatial Analyst**. Bez ní nebudou fungovat nástroje Slope, Reclassify, Euclidean Distance, Raster Calculator ani Extract by Mask.

## Úkol 1: Založení projektu a načtení dat (asi 25 min)

Cílem je mít v mapě všechna data ve správném souřadnicovém systému a nastavené prostředí analýz, aby všechny další rastry měly stejnou velikost buňky, rozsah a polohu buněk.

### a) Příprava složky

1. Vytvořte si pracovní složku, například `C:\GIS\Prijmeni`. Cesta nesmí obsahovat mezery ani diakritiku, jinak některé rastrové nástroje hlásí chyby.
2. Do této složky rozbalte `Beskydy_sesuvy.zip` (pravé tlačítko → **Extrahovat vše**). Uvnitř složky musí být složka `Beskydy_sesuvy.gdb`.

### b) Založení projektu

{: start="3"}
3. Spusťte ArcGIS Pro a přihlaste se. Na úvodní obrazovce v části **New Project** klikněte na **Map**.
4. V okně Create a New Project vyplňte:
    - **Name:** `Sesuvy_Prijmeni`
    - **Location:** vaše složka `C:\GIS\Prijmeni`
    - nechte zaškrtnuté **Create a new folder for this project**
    - klikněte na **OK**
5. ArcGIS Pro otevře prázdnou mapu s podkladovou mapou. Projekt má vlastní geodatabázi `Sesuvy_Prijmeni.gdb`, kam se budou automaticky ukládat všechny výsledky.

### c) Připojení a načtení dat

{: start="6"}
6. Otevřete panel **Catalog**. Pokud ho nevidíte, zvolte na kartě **View** tlačítko **Catalog Pane**.
7. V panelu Catalog klikněte pravým tlačítkem na **Folders → Add Folder Connection**, vyberte `C:\GIS\Prijmeni` a klikněte na **OK**.
8. Rozbalte připojenou složku a geodatabázi `Beskydy_sesuvy.gdb`. Se stisknutou klávesou **Ctrl** označte všechny čtyři vrstvy a přetáhněte je do mapy.
9. Pokud se ArcGIS Pro zeptá, zda vytvořit statistiky nebo pyramidy pro rastr `dmr5g`, klikněte na **Yes**.
10. V panelu **Contents** uspořádejte vrstvy přetažením shora dolů: `sesuvy`, `vodni_toky`, `uzemi`, `dmr5g`.

### d) Souřadnicový systém mapy

{: start="11"}
11. V Contents klikněte pravým tlačítkem na **Map** (nejvyšší položka) → **Properties** → záložka **Coordinate Systems**.
12. V řádku **Current XY** musí být **S-JTSK Krovak East North**. Pokud tam je něco jiného, napište do vyhledávání `5514`, vyberte S-JTSK Krovak East North a klikněte na **OK**.

### e) Základní symbologie

{: start="13"}
13. Klikněte na symbol vrstvy `uzemi` v Contents. V panelu Symbology zvolte záložku **Properties** a nastavte **Color:** No color, **Outline color:** černá, **Outline width:** 2 pt. Klikněte na **Apply**.
14. Stejně nastavte vrstvě `sesuvy` bez výplně, obrys červený, 1,5 pt. Vrstvě `vodni_toky` dejte modrou barvu.
15. Vrstvě `dmr5g` nastavte v Symbology barevnou škálu pro výšky (**Color scheme**, například od zelené po hnědou), ať je vidět reliéf.

### f) Prostředí analýz (Environments)

{: start="16"}
16. Na kartě **Analysis** klikněte ve skupině Geoprocessing na **Environments**.
17. Vyplňte tyto položky a potvrďte tlačítkem **OK**:
    - **Output Coordinate System:** Current Map
    - **Processing Extent:** Same as layer `uzemi`
    - **Cell Size:** Same as layer `dmr5g`
    - **Snap Raster:** `dmr5g`
    - **Mask:** `uzemi`

### g) Seznámení s daty

{: start="18"}
18. Klikněte pravým tlačítkem na `sesuvy` → **Attribute Table**. Dole v tabulce je počet záznamů. Zapište si, kolik sesuvů v území je.
19. Prohlédněte si, kde sesuvy leží: na hřebeni, na svazích, nebo v údolích. Geologii zatím nemáte, vytvoříte ji v úkolu 3.
20. Uložte projekt: **Project → Save** (nebo Ctrl + S).

**Kontrola:** Všechny vrstvy se v mapě překrývají a leží v Beskydech jižně od Frenštátu pod Radhoštěm. Kdyby některá ležela jinde, má špatně definovaný souřadnicový systém (ověřte v jejích **Properties → Source**) a oznamte to vyučujícímu.

**Otázky:**

- Proč je užitečné nastavit Snap Raster? Co by se mohlo stát, kdyby každý rastr měl buňky trochu posunuté?
- Kolik sesuvů je v území a ve které části reliéfu (hřeben, svahy, údolí) leží nejčastěji?

## Úkol 2: Sklon svahů a stínovaný reliéf (asi 15 min)

Sklon je nejdůležitější faktor: na rovině se sesuv nerozjede, na strmém svahu mnohem spíš. Z DMR ho spočítáte pro každou buňku 5 × 5 m.

### a) Výpočet sklonu

1. Na kartě **Analysis** klikněte na **Tools**. Vpravo se otevře panel Geoprocessing.
2. Do vyhledávání napište `Slope` a otevřete nástroj **Slope (Spatial Analyst Tools)**.
3. Vyplňte parametry:
    - **Input raster:** `dmr5g`
    - **Output raster:** `sklon` (ArcGIS Pro doplní cestu do geodatabáze projektu)
    - **Output measurement:** Degree
    - **Method:** Planar
    - **Z factor:** 1
4. Klikněte na **Run** dole v panelu. Po dokončení se objeví zelená zpráva a vrstva `sklon` se přidá do mapy. Červená zpráva obvykle znamená chybějící licenci Spatial Analyst.

### b) Stínovaný reliéf

{: start="5"}
5. V panelu Geoprocessing se šipkou zpět vraťte na vyhledávání a otevřete nástroj **Hillshade (Spatial Analyst Tools)**.
6. Vyplňte:
    - **Input raster:** `dmr5g`
    - **Output raster:** `stinovani`
    - **Azimuth:** 315, **Altitude:** 45, **Z factor:** 1
7. Klikněte na **Run**. V Contents přetáhněte `stinovani` pod vrstvu `sklon`.

### c) Zobrazení sklonu ve třídách

{: start="8"}
8. Klikněte pravým tlačítkem na `sklon` → **Symbology**.
9. Nastavte **Primary symbology:** Classify, **Method:** Manual Interval, **Classes:** 5.
10. V tabulce tříd přepište ve sloupci **Upper value** hodnoty na 5, 10, 15, 25 a 90. Zvolte barevnou škálu od zelené po červenou.
11. Označte vrstvu `sklon` v Contents, na kartě **Raster Layer** nastavte **Transparency** na 40 %. Reliéf pak bude prosvítat.

### d) Prozkoumejte výsledek

{: start="12"}
12. Klikněte pravým tlačítkem na `sklon` → **Properties → Source**, rozbalte **Statistics** a zapište si minimum, maximum a průměr.
13. Nástrojem **Explore** (karta Map) klikejte do mapy: na hřeben Radhoště, na svahy pod ním a na dno údolí. Hodnota sklonu se zobrazí v okně s informacemi.
14. Zapněte vrstvu `sesuvy` a podívejte se, ve kterých třídách sklonu sesuvy leží.

**Kontrola:** Hodnoty sklonu se pohybují od 0 do zhruba 50–60°. Hodnoty přes 70° obvykle znamenají umělé stupně (zářezy cest, lomy) nebo chybu v DMR.

**Otázky:**

- Kde v území jsou nejstrmější svahy? Souvisí to nějak s geologií?
- Leží sesuvy spíš na nejstrmějších svazích, nebo na středně strmých?

## Úkol 3: Geologie a vzdálenost od vodních toků (asi 70 min)

Typ horniny rozhoduje, jak snadno se svah utrhne. V Beskydech se střídají pevné pískovce s měkkými jílovci, které po nasycení vodou ztrácejí pevnost. Geologickou mapu jako data nemáte, proto si hlavní horniny obkreslíte sami do nové vektorové vrstvy. Přesně tak geologové převádějí starší papírové mapy do GIS.

### a) Podklad: geologická mapa ČGS

1. V prohlížeči otevřete aplikaci ČGS [Geologická mapa 1 : 50 000](https://mapy.geology.cz/geocr50/) a najděte své území (Trojanovice, severní svahy Radhoště).
2. Klikáním do mapy zjistěte popis jednotlivých jednotek. Do sešitu si sepište všechny jednotky v území: barvu v mapě, popis horniny a případně název souvrství. Bude jich zhruba 6–10.
3. V ArcGIS Pro zvolte **Map → Add Data → Data From Path** a vložte adresu `https://mapy.geology.cz/arcgis/rest/services/Geologie/geologicka_mapa50/MapServer`. Geologická mapa se přidá jako podkladová vrstva.
4. V Contents ji přetáhněte těsně nad `dmr5g`.

### b) Nová vrstva pro geologii

{: start="5"}
5. V panelu Catalog klikněte pravým tlačítkem na geodatabázi projektu `Sesuvy_Prijmeni.gdb` → **New → Feature Class**.
6. Na první stránce průvodce vyplňte **Name:** `geologie_kresba`, **Alias:** Geologie, **Feature Class Type:** Polygon a klikněte na **Next**.
7. Na stránce **Fields** přidejte dvě pole (klikněte na řádek *Click here to add a new field*):
    - `hornina`, **Data Type:** Text, **Length:** 150
    - `nach`, **Data Type:** Short
8. Klikněte na **Next**. Jako souřadnicový systém zvolte S-JTSK Krovak East North (nebo **Current Map**) a klikněte na **Finish**. Nová prázdná vrstva se přidá do mapy.

### c) Obkreslení hornin

{: start="9"}
9. Přibližte mapu na měřítko zhruba 1 : 10 000 až 1 : 15 000 (pole s měřítkem je dole ve stavovém řádku).
10. Na kartě **Edit** zapněte **Snapping** (přichytávání), aby se nové vrcholy přichytávaly k už nakresleným hranicím.
11. Klikněte na **Create**. V panelu Create Features vyberte `geologie_kresba` a nástroj **Polygon**.
12. Obkreslete první jednotku: klikáním vkládejte vrcholy podél její hranice a polygon dokončete klávesou **F2** nebo dvojklikem. Kde jednotka zasahuje za okraj území, kreslete aspoň 100 m za hranici `uzemi`. Přebytek později oříznete.
13. Další jednotky kreslete nástrojem **Auto-Complete Polygon**. Začněte kliknutím na hranici už nakresleného polygonu, obkreslete novou hranici a skončete opět na hranici existujícího polygonu. ArcGIS Pro společnou hranici doplní sám, takže mezi polygony nevzniknou mezery ani překryvy.
14. Po nakreslení každého polygonu otevřete na kartě Edit panel **Attributes** a do pole `hornina` vepište popis podle svých poznámek. Pole `nach` zatím nechte prázdné.
15. Plochy menší než zhruba 100 × 100 m nekreslete, připojte je k sousední jednotce. Jde o regionální model, ne o přesnou kopii mapy.
16. Chybný krok vrátíte zkratkou **Ctrl + Z**. Tvar hotového polygonu upravíte nástrojem **Edit Vertices** na kartě Edit.
17. Průběžně ukládejte tlačítkem **Save** na kartě Edit.

### d) Ořez a kontrola mezer

{: start="18"}
18. Spusťte **Pairwise Clip**: **Input Features** `geologie_kresba`, **Clip Features** `uzemi`, **Output Feature Class** `geologie`.
19. Spusťte **Pairwise Erase**: **Input Features** `uzemi`, **Erase Features** `geologie`, **Output Feature Class** `mezery`.
20. Otevřete atributovou tabulku `mezery`. Pokud nemá žádné prvky (nebo jen nepatrné plíšky), geologie pokrývá celé území. Jinak mezery dokreslete do `geologie_kresba` a kroky 18–20 zopakujte.

### e) Ohodnocení hornin

{: start="21"}
21. Otevřete atributovou tabulku vrstvy `geologie`. Polygonů je málo, takže hodnoty do pole `nach` můžete zapsat přímo dvojklikem do buňky podle tabulky níže.
22. U více polygonů se stejnou horninou je rychlejší **Select By Attributes** (například `hornina` **contains the text** `jílovc`) a pak pravé tlačítko na záhlaví `nach` → **Calculate Field** s hodnotou třídy.
23. Uložte úpravy (**Edit → Save**) a zrušte výběr (**Map → Clear**).
24. Kontrola: Select By Attributes s podmínkou `nach` **is null** musí vrátit 0 záznamů.

| Třída | Horniny typické pro Beskydy |
| --- | --- |
| 5 | sesuvné akumulace, deluviální (svahové) hlinitokamenité sedimenty, veřovické souvrství (černé jílovce), rožnovské souvrství (převaha jílovců), menilitové souvrství, pestré jílovce a slíny |
| 4 | lhotecké souvrství (jílovce s polohami pískovců), godulské souvrství – spodní a svrchní část (jemně až středně rytmický flyš), vápnité jílovce |
| 3 | godulské souvrství – střední část (převaha pískovců), deluviofluviální a proluviální sedimenty (náplavové kužely) |
| 2 | istebňanské souvrství (hrubozrnné pískovce a slepence), další masivní pískovce |
| 1 | fluviální sedimenty v nivách (hlíny, písky, štěrky), vyvřelé horniny (těšínity) |

Horninu, která v tabulce není, zařaďte podle převládající horniny a své rozhodnutí si poznamenejte.

### f) Převod geologie na rastr

{: start="25"}
25. Otevřete nástroj **Polygon to Raster** (Conversion Tools) a vyplňte:
    - **Input Features:** `geologie`
    - **Value field:** `nach`
    - **Output Raster Dataset:** `lito_r`
    - **Cell assignment type:** Cell center
    - **Priority field:** NONE
    - **Cellsize:** vyberte ze seznamu vrstvu `dmr5g` (velikost 5)
26. Klikněte na **Run**. Ve vrstvě `lito_r` nastavte symbologii **Unique Values**, abyste viděli jednotlivé třídy.

### g) Vzdálenost od vodních toků

Vodní toky podemílají paty svahů a zvyšují vlhkost, proto je blízkost toku další faktor.

{: start="27"}
27. Otevřete nástroj **Euclidean Distance** (Spatial Analyst Tools). Pokud ho vaše verze označí jako zastaralý (deprecated), použijte **Distance Accumulation** se stejnými vstupy.
28. Vyplňte:
    - **Input raster or feature source data:** `vodni_toky`
    - **Output distance raster:** `vzd_toky`
    - **Maximum distance:** nechte prázdné
    - **Output cell size:** 5 (převezme se z Environments)
    - **Distance Method:** Planar
29. Klikněte na **Run**. Každá buňka výsledku nese vzdálenost k nejbližšímu toku v metrech.
30. V **Properties → Source → Statistics** si zapište maximální vzdálenost. Budete ji potřebovat v úkolu 4.

**Kontrola:** `lito_r` obsahuje jen hodnoty 1–5 a pokrývá celé území. `vzd_toky` má na tocích hodnotu 0 a směrem k hřbetům roste.

**Otázky:**

- Souhlasíte s ohodnocením hornin v tabulce? Kterou horninu ze svého území byste zařadili jinak a proč?
- Na které hornině leží v území nejvíc sesuvů? Kde bylo obkreslování nejobtížnější a proč?

## Úkol 4: Reklasifikace faktorů (asi 20 min)

Sklon je ve stupních, vzdálenost v metrech a litologie ve třídách. Abychom je mohli sečíst, musí mít všechny stejnou škálu: 1 = nízká náchylnost, 5 = vysoká. Litologie už tuto škálu má, zbývají sklon a vzdálenost.

| Třída | Sklon (°) | Vzdálenost od toku (m) |
| --- | --- | --- |
| 1 | 0–5 | 500 až maximum |
| 2 | 5–10 | 200–500 |
| 3 | 10–15 | 100–200 |
| 4 | 15–25 | 50–100 |
| 5 | 25–90 | 0–50 |

### a) Sklon

1. Otevřete nástroj **Reclassify (Spatial Analyst Tools)**.
2. Vyplňte **Input raster:** `sklon` a **Reclass field:** Value. Pod ním se zobrazí tabulka **Reclassification**.
3. Klikněte na tlačítko **Classify**, zvolte **Method:** Equal Interval, **Classes:** 5 a potvrďte **OK**. Tabulka bude mít pět řádků.
4. V každém řádku přepište hodnoty **Start**, **End** a **New** podle sloupce Sklon v tabulce výše. První řádek tedy bude Start 0, End 5, New 1. Hodnota přesně na hranici (například 5,0) spadne do nižší třídy.
5. Zkontrolujte, že poslední řádek končí hodnotou 90. Kdyby končil nižší hodnotou než maximum sklonu, nejstrmější buňky by vypadly jako NoData.
6. Políčko **Change missing values to NoData** nechte nezaškrtnuté.
7. **Output raster:** `sklon_r`. Klikněte na **Run**.

### b) Vzdálenost od toků

{: start="8"}
8. Znovu otevřete **Reclassify**, tentokrát s **Input raster:** `vzd_toky`.
9. Klikněte na **Classify**, zvolte 5 tříd a hodnoty přepište podle sloupce Vzdálenost. Pozor, škála je obrácená: řádek 0–50 m dostává New 5, řádek 500 až maximum dostává New 1. Jako konec posledního řádku zadejte maximum, které jste si zapsali v úkolu 3.
10. **Output raster:** `voda_r`. Klikněte na **Run**.

### c) Kontrola

{: start="11"}
11. U vrstev `sklon_r`, `voda_r` a `lito_r` nastavte symbologii **Unique Values** se stejnou barevnou škálou (1 zelená až 5 červená).
12. U každé vrstvy ověřte v **Properties → Source → Statistics**, že minimum je 1 a maximum 5.
13. Vrstvy postupně zapínejte a vypínejte a porovnejte je se sesuvy.

**Kontrola:** Rastry `sklon_r`, `voda_r` a `lito_r` obsahují jen hodnoty 1 až 5 a uvnitř zájmového území nemají žádná prázdná místa (NoData).

**Otázka:** Je rozumné, že nejstrmější svahy mají nejvyšší třídu? Napadne vás typ svahu, kde to neplatí?

## Úkol 5: Vážený součet a mapa náchylnosti (asi 25 min)

Tři faktory sečtete, ale každý s jinou váhou, protože sklon má na sesuvy větší vliv než vzdálenost od toku. Váhy dohromady dávají 1, takže výsledek zůstane na škále 1–5.

**N = 0,5 · S + 0,3 · L + 0,2 · V**

Kde N je náchylnost, S sklon, L litologie a V vzdálenost od vodního toku (vše po reklasifikaci na 1–5).

### a) Výpočet v Raster Calculatoru

1. Otevřete nástroj **Raster Calculator (Spatial Analyst Tools)**. Vlevo je seznam **Rasters**, uprostřed operátory a dole pole pro výraz.
2. Sestavte výraz. Názvy rastrů vkládejte dvojklikem ze seznamu, čísla a operátory pište ručně:

    ```
    0.5 * "sklon_r" + 0.3 * "lito_r" + 0.2 * "voda_r"
    ```

3. Desetinná čísla pište s tečkou (`0.5`), ne s čárkou. Zelená fajfka pod polem znamená, že výraz je správně.
4. **Output raster:** `nachylnost`. Klikněte na **Run**.
5. V **Properties → Source → Statistics** ověřte, že minimum je aspoň 1 a maximum nejvýš 5.

### b) Převod na pět tříd

{: start="6"}
6. Výsledek má desetinné hodnoty. Otevřete **Reclassify**, zadejte **Input raster:** `nachylnost`, klikněte na **Classify**, zvolte 5 tříd a hodnoty přepište podle tabulky:

| Start | End | New | Název do legendy | Barva |
| --- | --- | --- | --- | --- |
| 1,0 | 1,8 | 1 | velmi nízká | tmavě zelená |
| 1,8 | 2,6 | 2 | nízká | světle zelená |
| 2,6 | 3,4 | 3 | střední | žlutá |
| 3,4 | 4,2 | 4 | vysoká | oranžová |
| 4,2 | 5,0 | 5 | velmi vysoká | tmavě červená |

{: start="7"}
7. **Output raster:** `nach_tridy`. Klikněte na **Run**.

### c) Symbologie

{: start="8"}
8. Klikněte pravým tlačítkem na `nach_tridy` → **Symbology** a zvolte **Unique Values**, pole Value.
9. Každé třídě klikněte na barevný čtvereček a nastavte barvu podle tabulky.
10. Dvojklikem do sloupce **Label** přepište popisky 1–5 na názvy do legendy (velmi nízká … velmi vysoká).
11. Přetáhněte `nach_tridy` těsně nad `stinovani`. Na kartě **Raster Layer** nastavte **Transparency** 40 %.
12. Ostatní mezivrstvy (`sklon`, `sklon_r`, `lito_r`, `voda_r`, `vzd_toky`, `nachylnost`) vypněte, ale nemazejte.

### d) Experiment s váhami

{: start="13"}
13. Spusťte Raster Calculator znovu s výrazem `0.8 * "sklon_r" + 0.1 * "lito_r" + 0.1 * "voda_r"` a výstupem `nachylnost_b`.
14. Označte v Contents vrstvu `nachylnost_b`, na kartě **Raster Layer** zvolte **Swipe** a táhnutím myši po mapě porovnávejte obě varianty.

**Otázka:** Jak se mapa změnila, když měl sklon váhu 0,8? Která varianta podle vás lépe sedí na skutečné sesuvy?

## Úkol 6: Ověření proti skutečným sesuvům (asi 25 min)

Model je dobrý jen tehdy, když skutečné sesuvy leží hlavně v třídách s vysokou náchylností. Spočítáte, kolik buněk každé třídy leží uvnitř sesuvů a kolik v celém území.

### a) Výřez náchylnosti uvnitř sesuvů

1. Otevřete nástroj **Extract by Mask (Spatial Analyst Tools)** a vyplňte:
    - **Input raster:** `nach_tridy`
    - **Input raster or feature mask data:** `sesuvy`
    - **Output raster:** `nach_v_sesuvech`
    - **Extraction Area:** Inside
2. Klikněte na **Run**. V mapě zůstanou barevné jen buňky uvnitř sesuvů.

### b) Počty buněk

{: start="3"}
3. Klikněte pravým tlačítkem na `nach_tridy` → **Attribute Table**. Sloupec **Value** je třída, sloupec **Count** počet buněk té třídy v celém území. Pokud se tabulka neotevře, spusťte nejdřív nástroj **Build Raster Attribute Table** na tento rastr.
4. Stejně otevřete tabulku `nach_v_sesuvech`. Count tu znamená počet buněk té třídy uvnitř sesuvů.
5. Hodnoty přepište do tabulky níže. Jedna buňka má 25 m², takže plochu v km² získáte jako Count × 25 / 1 000 000.
6. Dopočítejte poslední sloupec: (buňky v sesuvech / buňky v celém území) × 100.

| Třída náchylnosti | Buněk v celém území | Buněk v sesuvech | Podíl plochy třídy zasažený sesuvy (%) |
| --- | --- | --- | --- |
| 1 velmi nízká |  |  |  |
| 2 nízká |  |  |  |
| 3 střední |  |  |  |
| 4 vysoká |  |  |  |
| 5 velmi vysoká |  |  |  |
| Celkem |  |  |  |

### c) Dvě souhrnná čísla

{: start="7"}
7. Spočítejte, kolik procent celého území tvoří třídy 4 a 5 dohromady.
8. Spočítejte, kolik procent plochy sesuvů leží v třídách 4 a 5.
9. Pokud je druhé číslo výrazně vyšší než první, model sesuvy „hledá“ lépe než náhoda.

### d) Sesuv, který model nezachytil

{: start="10"}
10. Zapněte `sesuvy` nad `nach_tridy` a najděte sesuv, který leží hlavně v třídě 1 nebo 2.
11. Nástrojem **Explore** klikněte doprostřed tohoto sesuvu. V okně s informacemi se zobrazí hodnoty všech zapnutých vrstev. Zapněte proto předem `sklon`, `lito_r` a `vzd_toky` a zapište si jejich hodnoty.

**Jak výsledek číst:** Pokud model funguje, podíl v posledním sloupci od třídy 1 k třídě 5 roste. Když roste jen málo nebo nepravidelně, některý faktor nebo váha nesedí. Stejný postup můžete zopakovat pro variantu `nachylnost_b` z úkolu 5.

**Otázka:** Proč model nezachytil sesuv z kroku 10? Který faktor ho „podcenil“?

## Úkol 7: Výstupní mapa a odevzdání (asi 25 min)

Výsledkem je mapa, kterou by šlo přiložit k posudku: čitelná, s legendou, měřítkem a uvedenými zdroji.

### a) Příprava mapy

1. V Contents nechte zapnuté jen `uzemi`, `sesuvy`, `vodni_toky`, `nach_tridy` a `stinovani`. Podkladovou mapu (Topographic) a geologickou mapu ČGS vypněte.
2. Přejmenujte vrstvy tak, aby v legendě dávaly smysl (klikněte na název, stiskněte **F2** a přepište):
    - `nach_tridy` → Náchylnost ke svahovým deformacím
    - `sesuvy` → Zmapované svahové deformace (ČGS)
    - `vodni_toky` → Vodní toky
    - `uzemi` → Hranice území

### b) Rozvržení stránky

{: start="3"}
3. Na kartě **Insert** klikněte na **New Layout** a zvolte **ISO – A4 Portrait**.
4. Na kartě **Insert** klikněte na **Map Frame**, vyberte svou mapu a tahem myši nakreslete rám přes horní dvě třetiny stránky.
5. V panelu Contents layoutu klikněte pravým tlačítkem na vrstvu Hranice území → **Zoom To Layer**. Pak dole ve stavovém řádku přepište měřítko na zaokrouhlenou hodnotu, například 1 : 35 000, aby se území celé vešlo do rámu.

### c) Mapové prvky

{: start="6"}
6. **Insert → Legend** a nakreslete legendu pod mapu. Ve vlastnostech legendy (dvojklik) v části **Legend Items** odškrtněte `stinovani`, aby se v legendě nezobrazovalo.
7. **Insert → North Arrow** a umístěte šipku do rohu mapy.
8. **Insert → Scale Bar**, zvolte metrické měřítko a v jeho vlastnostech nastavte jednotky na kilometry.
9. **Insert → Text** a nad mapu napište nadpis: *Mapa náchylnosti ke svahovým deformacím – severní svahy Radhošťského hřbetu*. Písmo nastavte na 16 pt, tučně.
10. Další textové pole dejte do spodní části stránky s tímto obsahem (doplňte své jméno a datum):
    - Model: N = 0,5 × sklon + 0,3 × litologie + 0,2 × vzdálenost od toku
    - Zdroje dat: DMR 5G © ČÚZK; geologická mapa 1 : 50 000 (obkresleno) a registr svahových nestabilit © ČGS; vodní toky DIBAVOD © VÚV TGM
    - Souřadnicový systém S-JTSK (EPSG:5514)
    - Autor, datum
11. Dobrovolně: přidejte malý přehledný rámeček s polohou území v rámci ČR (další Map Frame s novou mapou v malém měřítku).

### d) Export

{: start="12"}
12. Na kartě **Share** klikněte na **Export Layout**. Zvolte **File Type:** PDF, **Resolution:** 300 dpi, název `Sesuvy_Prijmeni.pdf` a klikněte na **Export**.
13. Otevřete PDF a zkontrolujte, že je vše čitelné. Projekt uložte (**Ctrl + S**).

**Odevzdáte:** PDF mapy, vyplněnou tabulku z úkolu 6 se dvěma souhrnnými čísly a krátké odpovědi na otázky z úkolů 1–6 a z následující sekce.

## Otázky k zamyšlení

Odpovědi stačí v rozsahu dvou až tří vět.

1. Které faktory ovlivňující sesuvy v Beskydech v modelu chybějí? Kde byste pro ně našli data?
2. DMR má rozlišení 5 m, geologická mapa je v měřítku 1 : 50 000. Co tento rozdíl a vaše vlastní obkreslování znamenají pro přesnost výsledku?
3. Do třídy 5 v litologii patří i sesuvné akumulace z geologické mapy. Proč to může zkreslit ověření v úkolu 6?
4. Na hřebeni Radhoště jsou masivní pískovce, které v modelu dostaly nízkou třídu, a přesto se tu vyskytují velké svahové deformace. Jak je to možné?
5. Je mapa náchylnosti totéž co mapa rizika? Co by bylo potřeba doplnit, abyste mohli mluvit o riziku?
6. Starosta Trojanovic chce podle vaší mapy rozhodnout, kde povolit stavbu domu. Co byste mu k mapě řekli?
