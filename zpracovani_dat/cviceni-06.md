---
layout: default
title: "CV06 – Nalezení trasy s nejnižšími náklady nad rastrovými daty"
description: "Cvičení ArcGIS Pro: cost distance / cost path nad DMR z ArcČR 500 a Corine Land Cover"
lang: cs
---

# CV06 – Nalezení trasy s nejnižšími náklady nad rastrovými daty

*Cvičení v prostředí ArcGIS Pro · data: ArcČR 500, Corine Land Cover*


## Úvod: analýza šíření a frikční povrchy

Cílem analýzy šíření je najít optimální trasu mezi dvěma body (nebo jedním startem a více cíli) tak, aby náklady na přesun byly co nejnižší. Pracuje se se dvěma typy rastrů:

- **Frikční (nákladový) povrch** – každé buňce rastru je přiřazena hodnota nákladů na přesun přes její plochu do sousední buňky (v jednotkách času, peněz, obtížnosti apod.).
- **Povrch akumulovaných nákladů** – pro každou buňku udává nejnižší možný součet nákladů na cestu z výchozí buňky (startu) až do ní.

Nad těmito povrchy se pak hledá tzv. *least-cost path* – trasa, která se od cíle zpět ke startu „skutálí“ po nejmenších akumulovaných nákladech. Stejnou logikou lze modelovat i šíření kontaminace, požáru nebo migraci druhů.

**Příklady využití:**

- plánování trasy přesunu terénem pro armádní nebo záchranné jednotky,
- vyhledání vhodné trasy pro liniové stavby – komunikace, energetická a obslužná vedení, produktovody,
- plánování turistických a cyklistických tras s ohledem na sklon a povrch.

## Zadání úlohy

Naplánujte trasu s nejmenšími náklady na výstavbu **elektrického vedení** mezi hlavním distribučním uzlem (**start**, 1 bod) a **dvěma místními uzly** (**cíle**, 2 body). Náklady odvoďte z krajinného pokryvu (CLC 2018) a zohledněte vliv sklonu svahů z DMR ArcČR 500: čím sklonitější terén, tím dražší výstavba.

Výstupem je **mapový layout** (A4 nebo A3, export do PDF), který zobrazuje obě nalezené trasy nad nákladovým rastrem, body startu a cílů a hranici zájmového území – podrobné požadavky jsou v kroku 7.

### Výběr zájmového území

Zájmové území si volíte sami, musí ale splňovat tyto podmínky:

- **Dostatečná velikost.** DMR z ArcČR 500 je generalizovaný model s velkou velikostí buňky (řádově stovky metrů – ověřte si ji ve vlastnostech rastru). Jedna obec by měla v rastru jen několik desítek buněk a výsledná trasa by byla nesmyslná. Volte proto celé **ORP, okres**, případně skupinu sousedních ORP. Orientačně: území by mělo mít alespoň **300–500 km²**.
- **Členitý terén.** Aby bylo vliv sklonu v trase vidět, vyberte území s kopci nebo údolími (podhůří, vrchoviny). V rovinatém území (Polabí, Haná) bude trasa téměř přímá.
- **Pestrý krajinný pokryv.** ݚzemí by mělo obsahovat alespoň 4–5 různých tříd CLC (zástavba, orná půda, les, vodní plocha, louky…), jinak nákladový rastr nebude mít co rozlišit.
- **Start a cíle** umístíte sami (krok 1), ideálně do různých částí území tak, aby mezi nimi ležela překážka (hřeben, řeka, město), kterou musí trasa obejít.

Hranici území vezměte z vrstvy administrativního členění v ArcČR 500 (ORP nebo okres), nekreslete ji ručně.

## Vstupní data

| Vrstva | Zdroj | Formát | Souřadnicový systém | Použití |
| --- | --- | --- | --- | --- |
| DMR (digitální model reliéfu) | ArcČR 500 v3.x – rastrový dataset v geodatabázi (název typu `DMR` / `DigitalniModelReliefu`, podle verze) | rastr (file GDB) | S-JTSK / Krovak East North (EPSG 5514) | výpočet sklonu |
| Administrativní členění – ORP, okresy | ArcČR 500 v3.x – geodatabáze administrativního členění | polygon | S-JTSK (EPSG 5514) | hranice zájmového území, maska |
| Corine Land Cover 2018 (CLC 2018) | [geoportal.gov.cz – ATOM download, CLC12R_CZ_epsg5514.zip](https://geoportal.gov.cz/atom/corine_land_cover/CLC12R_CZ_epsg5514.zip) | polygon (SHP / GDB) | S-JTSK (EPSG 5514) – již transformováno | nákladový rastr |
| Legenda CLC 2018 | [landcover.cenia.cz](https://landcover.cenia.cz/corine-land-cover/#tridy-corine-land-cover) | web | – | význam kódů tříd |
| Start a cíle | vlastní bodové vrstvy (vytvoříte v kroku 1) | bod (file GDB) | S-JTSK (EPSG 5514) | zdroj a cíle analýzy |

**Kde najít DMR v ArcČR 500:** v panelu *Catalog* rozbalte geodatabázi ArcČR 500 – rastrové datasety mají ikonu mřížky. Klikněte na DMR pravým tlačítkem → *Properties* → záložka *Source*: zapište si **velikost buňky (Cell Size)**, **souřadnicový systém** a **rozsah hodnot** (minimum/maximum nadmořské výšky). Tyto údaje uvedete v tiráži layoutu.

**Souřadnicové systémy:** všechny vrstvy v ArcČR 500 i stažený balík CLC (`CLC12R_CZ_epsg5514.zip`) jsou v S-JTSK (EPSG 5514), žádná transformace tedy není nutná. Po rozbalení ZIP a přidání vrstvy do mapy přesto zkontrolujte souřadnicový systém ve vlastnostech vrstvy (*Properties → Source → Spatial Reference*). Pokud byste použili jinou distribuci CLC (např. evropskou v EPSG 3035), musíte ji nejprve přetransformovat nástrojem *Project* – rastrové nástroje jinak pracují s posunutými výsledky.

**Lokální kopie:** celou ArcČR 500 nekopírujte. Do pracovní geodatabáze projektu si exportujte jen vybrané ORP/okres (krok 1); DMR a CLC budete stejně ořezávat.

## Příprava projektu a nastavení prostředí

Společné nastavení prostředí geoprocessingu zajistí, že všechny rastry budou mít stejnou velikost buňky, zarovnání a rozsah. Bez toho Raster Calculator a Cost Distance vrátí posunuté nebo oříznuté výsledky.

1. Založte nový projekt ArcGIS Pro (šablona *Map*), pojmenujte ho `CV07_prijmeni`. Projekt automaticky vytvoří pracovní file geodatabázi `CV07_prijmeni.gdb` – všechny výstupy ukládejte do ní.
2. Nastavte souřadnicový systém mapy na **S-JTSK / Krovak East North** (vlastnosti mapy → *Coordinate Systems*).
3. Ověřte, že máte licenci rozšíření **Spatial Analyst** (*Project → Licensing*). Bez ní nástroje Slope, Reclassify, Cost Distance a Cost Path nepůjdou spustit.
4. Nastavte prostředí geoprocessingu (*Analysis → Environments*) – toto nastavení se pak dědí do všech nástrojů:

| Parametr prostředí | Hodnota | Proč |
| --- | --- | --- |
| Output Coordinate System | S-JTSK / Krovak East North (EPSG 5514) | všechny výstupy ve stejném systému |
| Processing Extent | *Same as layer* → vaše zájmové území (vrstva ORP/okresu z kroku 1) | rastry nebudou zbytečně velké |
| Snap Raster | DMR z ArcČR 500 | zarovnání buněk všech rastrů na mřížku DMR |
| Cell Size | *Same as layer* → DMR z ArcČR 500 | jednotná velikost buňky |
| Mask | vaše zájmové území | buňky mimo území budou NoData |
| Current Workspace / Scratch Workspace | `CV07_prijmeni.gdb` | výstupy se ukládají na jedno místo |

5. Pojmenovávejte výstupy systematicky, např. `dmr_clip`, `sklon`, `clc_clip`, `clc_rastr`, `naklady`, `naklady_sklon`, `cost_dist`, `cost_backlink`, `cost_path`, `trasa_linie`. Názvy v geodatabázi nesmí začínat číslicí a nesmí obsahovat mezery ani diakritiku.

## Krok 1: Zájmové území a bodové vrstvy start / cíle

### 1a) Zájmové území

1. Z geodatabáze ArcČR 500 přidejte do mapy vrstvu **ORP** (nebo **okresy**).
2. Vyberte zvolené území nástrojem *Select* nebo *Select By Attributes* (např. `NAZ_ORP = 'Frýdlant nad Ostravicí'`).
3. Výběr exportujte do pracovní geodatabáze: pravým tlačítkem na vrstvu → *Data → Export Features*, výstup `uzemi`.
4. Vrstvu `uzemi` použijte v nastavení prostředí jako *Processing Extent* a *Mask* (předchozí kapitola).

### 1b) Bodové vrstvy start a cíle

Vytvořte dvě samostatné bodové třídy prvků – odděleně proto, že Cost Distance bere jako vstup pouze zdroj (start) a Cost Path pouze cíle.

1. V panelu *Catalog* klikněte pravým tlačítkem na `CV07_prijmeni.gdb` → *New → Feature Class*.
2. Název `start`, typ **Point**, souřadnicový systém **S-JTSK / Krovak East North**. Při definici polí přidejte pole `Id` typu *Short* (celé číslo).
3. Stejně vytvořte třídu prvků `cile` s polem `Id`.
4. Na kartě *Edit → Create* digitalizujte: 1 bod do `start` (hodnota `Id = 1`) a 2 body do `cile` (`Id = 1` a `Id = 2`). Uložte editaci (*Edit → Save*).
5. Body umístěte tak, aby mezi startem a cíli ležela terénní nebo krajinná překážka a aby žádný bod neležel na okraji území (trasa pak může „utéct“ k hranici).

Pole `Id` slouží v Cost Path k rozlišení tras k jednotlivým cílům – bez něj výstup nerozpoznáte.

## Krok 2: DMR z ArcČR 500 – oříznutí a výpočet sklonu

### 2a) Oříznutí DMR na zájmové území

Nástroj: **Extract by Mask** (Spatial Analyst Tools → Extraction).

| Parametr | Hodnota |
| --- | --- |
| Input raster | DMR z ArcČR 500 |
| Input raster or feature mask data | `uzemi` |
| Output raster | `dmr_clip` |

Alternativou je *Clip Raster* (Data Management → Raster → Raster Processing), ten ale ořezává jen na obdélník rozsahu; Extract by Mask nastaví buňky mimo polygon na NoData, což je pro nákladovou analýzu vhodnější.

Po spuštění zkontrolujte: `dmr_clip` má stejnou velikost buňky jako původní DMR, hodnoty odpovídají nadmořským výškám v metrech a mimo území je NoData (v mapě průhledné).

### 2b) Výpočet sklonu

Nástroj: **Slope** (Spatial Analyst Tools → Surface).

| Parametr | Hodnota | Poznámka |
| --- | --- | --- |
| Input raster | `dmr_clip` |  |
| Output raster | `sklon` |  |
| Output measurement | **Degree** | vzorec v kroku 4 počítá se stupni (0–90) |
| Method | Planar | S-JTSK je rovinný systém, geodetická metoda není třeba |
| Z factor | **1** | výšky i souřadnice jsou v metrech |

Z factor měňte pouze tehdy, pokud by výšky byly v jiných jednotkách než souřadnice (např. stopy vs. metry) – zde ne. Pokud byste nechali souřadnicový systém v zeměpisných souřadnicích (stupně), sklon by vyšel nesmyslně.

**Kontrola výsledku:** v generalizovaném DMR ArcČR 500 budou sklony nízké – typicky maximum do 15–25° i v horských oblastech, protože velká buňka terén vyhlazuje. To je v pořádku a pro úlohu to postačí; do tiráže uveďte rozsah hodnot sklonu. Pro srovnání: nad DMR 5G (předchozí varianta cvičení) by sklony dosahovaly přes 50°.

### Otázka k zamyšlení

Jak by se změnil výsledek analýzy, kdybyste místo ArcČR 500 použili DMR 5G? Zapište si odhad – na konci cvičení k němu můžete v rozšiřujícím úkolu dojít výpočtem.

## Krok 3: CLC 2018 – oříznutí, převod na rastr a nákladový rastr

### 3a) Transformace a oříznutí

1. Stáhněte a rozbalte balík CLC z geoportálu (odkaz v kapitole Vstupní data) a přidejte vrstvu do mapy. Ověřte souřadnicový systém S-JTSK (EPSG 5514); transformace nástrojem *Project* je potřeba jen u jiné distribuce CLC.
2. **Clip** (Analysis Tools → Extract): Input Features vrstva CLC, Clip Features `uzemi`, výstup `clc_clip`.
3. Otevřete atributovou tabulku `clc_clip` a zjistěte, **které třídy CLC se v území vyskytují**: pravým tlačítkem na pole s kódem třídy (název podle verze dat, např. `CODE_12` nebo `CODE_18` – dále v návodu označeno jako `CODE`) → *Summarize* (nebo *Statistics*). Získáte seznam kódů a jejich plochu. Význam kódů dohledáte v [legendě CLC](https://landcover.cenia.cz/corine-land-cover/#tridy-corine-land-cover). Seznam tříd s výměrou v km² si uložte – patří do layoutu nebo protokolu.

### 3b) Převod na rastr

Nástroj: **Feature to Raster** (Conversion Tools → To Raster).

| Parametr | Hodnota |
| --- | --- |
| Input features | `clc_clip` |
| Field | `CODE` (pole s kódem třídy CLC, viz 3a) |
| Output raster | `clc_rastr` |
| Output cell size | *Same as layer* → `dmr_clip` (dědí se z prostředí, zkontrolujte) |

Velikost buňky musí být shodná s DMR, jinak Raster Calculator v kroku 4 bude rastry převzorkovávat a výsledek bude nepřesný. Pole s kódem CLC bývá textové – Feature to Raster ho převede na celočíselný rastr s atributovou tabulkou; pokud to selže, vytvořte si nejprve číselné pole (*Calculate Field*, `int(!CODE!)`).

### 3c) Reklasifikace na nákladový rastr

Nástroj: **Reclassify** (Spatial Analyst Tools → Reclass). Input raster `clc_rastr`, Reclass field `Value`, výstup `naklady`.

Každému kódu CLC přiřaďte nákladovou hodnotu. Výchozí návrh podle hlavních skupin:

| Skupina CLC | Kódy (1. úroveň) | Příklady tříd | Zdůvodnění | Náklad |
| --- | --- | --- | --- | --- |
| Urbanizované plochy | 1xx | 111, 112 zástavba; 121 průmysl; 122 komunikace; 131 těžba; 141, 142 zeleň a sport | hustá zástavba, existující sítě, výkupy pozemků | 10 |
| Zemědělské plochy | 2xx | 211 orná půda; 222 sady; 231 louky a pastviny; 242, 243 mozaika | střední náklady, zásah do hospodaření | 5 |
| Lesy a polopřírodní plochy | 3xx | 311, 312, 313 lesy; 321 přirozené louky; 324 nízký porost | kácení, průseky, ochrana přírody | 8 |
| Mokřady a vodní plochy | 4xx, 5xx | 411 mokřady; 511 vodní toky; 512 vodní plochy | přemostění nebo obcházení | 20 |

Návrh **upravte podle vlastního uvážení** – například průmyslové areály (121) mohou být levnější než hustá zástavba (111), louky (231) levnější než orná půda (211). Každou odchylku od tabulky krátce zdůvodněte v protokolu. Jediná pevná pravidla: nejnižší náklad **nesmí být 0** (nulová buňka by znamenala přesun zdarma a trasa by ji vždy vyhledala) a hodnoty NoData ponechte jako NoData (trasa jimi neprojde).

Výstup `naklady` zkontrolujte vizuálně: lesy a města tmavé, pole světlá, vodní plochy nejtmavší.

## Krok 4: Aplikace vlivu sklonu svahů

Nákladový rastr z CLC upravíte podle sklonu tak, aby strmější buňky byly dražší. Nástroj: **Raster Calculator** (Spatial Analyst Tools → Map Algebra), výstup `naklady_sklon`.

Výraz (vrstvy vybírejte dvojklikem ze seznamu, aby byly správně uvozené):

```
"naklady" * (1 + "sklon" / 90.0)
```

Hodnota nákladů v každé buňce se vynásobí koeficientem z intervalu 1,0–2,0 podle sklonu: na rovině (0°) zůstává náklad beze změny, na svahu 45° je 1,5násobný, na teoretické stěně 90° dvojnásobný. Vliv sklonu je tak plynulý, bez skokových tříd.

Pište `90.0`, ne `90` – u celočíselného dělení by Raster Calculator vrátil pro všechny reálné sklony nulu a koeficient by byl všude 1.

**Kontrola:** `naklady_sklon` má minimum rovné nejnižšímu nákladu z reklasifikace a maximum o něco vyšší než 20 (vodní plocha na svahu). Porovnejte `naklady` a `naklady_sklon` přepínáním viditelnosti – rozdíl musí být vidět na svazích.

### Volitelná úprava

Protože DMR ArcČR 500 dává nízké sklony, může být vliv terénu v trase málo patrný. Pokud chcete terén zdůraznit, zesilte koeficient, např. `"naklady" * (1 + "sklon" / 30.0)` (sklon 30° zdvojnásobí náklad). Použitý vzorec vždy uveďte v layoutu i protokolu.

## Krok 5: Cost Distance – povrch akumulovaných nákladů

Nástroj: **Cost Distance** (Spatial Analyst Tools → Distance → Legacy). Pro každou buňku spočítá nejnižší možný součet nákladů na cestu ze startu až do ní a zároveň si zapamatuje, ze kterého směru tato nejlevnější cesta přišla.

| Parametr | Hodnota | Význam |
| --- | --- | --- |
| Input raster or feature source data | `start` | výchozí místo, ze kterého se náklady šíří |
| Input cost raster | `naklady_sklon` | frikční povrch – náklad na průchod jednou buňkou |
| Output distance raster | `cost_dist` | akumulované náklady od startu do každé buňky |
| Output backlink raster | `cost_backlink` | kód směru (1–8) k sousední buňce ležící na nejlevnější cestě zpět ke startu |
| Maximum distance | prázdné | neomezovat |

Backlink je v dialogu označen jako volitelný, **pro krok 6 je ale nezbytný** – bez něj Cost Path nemá podle čeho trasu zpětně vysledovat.

**Kontrola výsledku:**

- `cost_dist` má v buňce startu hodnotu 0 a roste směrem od něj; izolinie akumulovaných nákladů nejsou kruhové, ale deformované podle krajinného pokryvu a terénu (kolem vodních ploch a měst se „stlačují“).
- `cost_backlink` obsahuje celočíselné hodnoty 0–8 (0 = buňka startu).
- Hodnota `cost_dist` v místě cílů = celkové náklady trasy; odečtěte je nástrojem *Explore* (kliknutím do mapy) a zapište do protokolu.

**Novější alternativa:** ArcGIS Pro nabízí také nástroj *Distance Accumulation* (výstup back direction raster) a návazný *Optimal Path As Line*. Dávají srovnatelný výsledek a přímo liniový výstup; v tomto cvičení použijte klasickou dvojici Cost Distance + Cost Path, aby byl vidět princip backlink rastru.

## Krok 6: Cost Path – nalezení nejlevnějších tras

Nástroj: **Cost Path** (Spatial Analyst Tools → Distance → Legacy). Z každého cíle sleduje backlink rastr zpět až ke startu a vyznačí buňky, kterými trasa prochází.

| Parametr | Hodnota | Význam |
| --- | --- | --- |
| Input raster or feature destination data | `cile` | cílová místa (2 body) |
| Destination field | `Id` | identifikátor cíle, podle něj se trasy rozliší |
| Input cost distance raster | `cost_dist` | z kroku 5 |
| Input cost backlink raster | `cost_backlink` | z kroku 5 |
| Output raster | `cost_path` | rastr tras |
| Path type | **EACH_CELL** | samostatná trasa pro každou cílovou buňku (= každý cíl) |

Výstupem je celočíselný rastr, kde hodnota 1 označuje start a další hodnoty jednotlivé trasy (v atributové tabulce pole `DESTID` nese `Id` cíle). Obě trasy se v blízkosti startu zpravidla spojí do společného úseku – to je správně.

### Převod trasy na linii

Pro layout a měření délky převeďte rastr na linii. Dvě možnosti:

1. **Raster to Polyline** (Conversion Tools → From Raster): Input raster `cost_path`, Field `Value`, Background value ZERO, výstup `trasa_linie`. Zaškrtněte *Simplify polylines*, jinak linie kopíruje schodovitý okraj buněk.
2. **Cost Path As Polyline** (Spatial Analyst Tools → Distance → Legacy): stejné vstupy jako Cost Path, výstupem je rovnou linie – přeskočí krok s rastrem.

### Délka a náklady tras

1. Otevřete atributovou tabulku `trasa_linie` a přidejte pole `delka_km` (Double). Vypočtěte je přes *Calculate Geometry* → Length, jednotky kilometry.
2. Celkové akumulované náklady každé trasy odečtěte z `cost_dist` v místě příslušného cíle (nástroj *Explore*, nebo *Extract Values to Points* s vrstvou `cile`).
3. Výsledky zapište do tabulky, kterou vložíte do layoutu:

| Cíl (Id) | Délka trasy (km) | Akumulované náklady (–) | Vzdušná vzdálenost od startu (km) |
| --- | --- | --- | --- |
| 1 |  |  |  |
| 2 |  |  |  |

Vzdušnou vzdálenost změřte nástrojem *Measure*. Poměr délky trasy a vzdušné vzdálenosti ukazuje, jak moc musela trasa „kličkovat“ kvůli nákladům.

## Krok 7: Výstupní layout

### Obsah mapy (mapový rám)

| Vrstva | Zobrazení |
| --- | --- |
| `trasa_linie` | výrazná linie (2–3 pt), odlišná barva pro každý cíl (symbolizace podle `DESTID` / `grid_code`), nahoře v pořadí vrstev |
| `start`, `cile` | kontrastní bodové symboly s popisky („Start“, „Cíl 1“, „Cíl 2“) |
| `naklady_sklon` | podklad – klasifikovaná nebo spojitá barevná škála (světlá = levné, tmavá = drahé), částečná průhlednost 20–30 % |
| `uzemi` | pouze obrys (bez výplně), tmavá linka |
| stínovaný reliéf | volitelně: *Hillshade* z `dmr_clip` pod nákladovým rastrem pro plastičnost |

Nezobrazujte zároveň `cost_dist` a `naklady_sklon` – mapa by byla nečitelná. Pokud chcete ukázat i akumulované náklady, přidejte druhý menší mapový rám.

## Nejčastější chyby a jejich řešení

| Problém | Příčina | Řešení |
| --- | --- | --- |
| Nástroje Spatial Analyst jsou šedé / hlásí chybu licence | není aktivní rozšíření | *Project → Licensing* → zaškrtnout Spatial Analyst, restart Pro |
| Rastry z CLC a DMR na sebe nesedí, trasa „ujíždí“ | různá velikost buňky nebo chybí Snap Raster | nastavit Cell Size a Snap Raster na DMR v prostředí, znovu spustit Feature to Raster |
| Sklon má všude hodnotu 0 nebo nesmyslně vysokou | DMR v zeměpisných souřadnicích nebo špatný Z factor | ověřit S-JTSK, Z factor = 1 |
| Koeficient sklonu je všude 1 | celočíselné dělení `sklon / 90` | psát `90.0` |
| Cost Path hlásí chybějící backlink | v Cost Distance nebyl vyplněn Output backlink raster | spustit Cost Distance znovu s backlinkem |
| Trasa vede po okraji území nebo přes NoData | cíl či start leží mimo masku, nebo náklad 0 | přesunout body dovnitř, zkontrolovat Reclassify (žádná 0) |
| Vznikla jen jedna trasa pro dva cíle | Path type nastaven na BEST_SINGLE | nastavit EACH_CELL a doplnit Destination field `Id` |
| Feature to Raster selže na poli s kódem CLC | textové pole | vytvořit číselné pole `int(!CODE!)` |
| Výstup se uložil jako dočasný rastr a po zavření zmizel | výstup ve Scratch nebo v paměti | ukládat vždy do `CV07_prijmeni.gdb` s explicitním názvem |
