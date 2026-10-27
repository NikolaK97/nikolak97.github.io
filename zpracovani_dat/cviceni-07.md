---
title: CV08 – Síťové analýzy a geokódování
layout: default
description: Návod krok za krokem pro ArcGIS Online a ArcGIS Pro – geokódování adres, trasy, dostupnost, TSP a Dijkstrův algoritmus
---

# CV08 – Síťové analýzy a geokódování

Návod krok za krokem pro ArcGIS Online a ArcGIS Pro

---

## Než začneš

### Co se v tomto cvičení naučíš

1. **Geokódovat adresy** – udělat z tabulky adres body v mapě.
2. **Porovnat přesnost** dvou geokodérů.
3. **Počítat trasy a dostupnost po silnicích** – ne „vzdušnou čarou“, ale tak, jak by jelo auto.

### Co budeš potřebovat

| Co | Poznámka |
|----|----------|
| Účet ArcGIS Online (školní) | Síťové analýzy a geokódování spotřebovávají **kredity** – pracuj s malým počtem bodů. |
| ArcGIS Pro | Přihlášený stejným účtem (vpravo nahoře). |
| ArcČR 500 | Hlavně vrstva **obcí** (s počtem obyvatel). |
| Excel nebo Google Sheets | Pro tabulku adres. |

### Jeden příběh pro celé cvičení

Aby se ti to nepletlo, dělej všechny úlohy na **jednom scénáři**:

> Pracuješ pro krajský úřad (např. **Jihomoravský kraj**). Zajímá tě, jak dobře jsou obce dostupné k **nemocnicím** a kam by se hodila nová.

- **Obce** = místa, kde žijí lidé (body z ArcČR 500).
- **Nemocnice** = zařízení (body, které si vytvoříš geokódováním jejich adres – viz Část A).

Pokud chceš jiný kraj nebo jiný typ zařízení (hasiči, školy, pošty), klidně – jen to drž stejné ve všech úlohách.

### Přehled cvičení

| Část | Kde | Co děláš | Výstup |
|------|-----|----------|--------|
| **A** | Excel → ArcGIS Online → Google Sheets → ArcGIS Pro | Geokódování a porovnání přesnosti | 2 bodové vrstvy + tabulka rozdílů |
| **B** | ArcGIS Online | Nejbližší zařízení, dojezdové zóny, plán trasy | 3 mapy + komentář |
| **C** | ArcGIS Pro | Route, TSP, Service Area, Closest Facility, VRP, Location-Allocation | 6 map + komentář |
| **D** | Word / PDF | Dokumentace | Odevzdávaný soubor |

---

# Teorie v kostce

Přečti si to před prací – stačí pět minut. Pomůže ti to pochopit, *co* vlastně nástroje počítají.

## Síť = graf

Silniční síť si GIS představuje jako **graf**:

- **Uzly** (nodes) = křižovatky, konce silnic, obce.
- **Hrany** (edges) = úseky silnic mezi uzly.
- Každá hrana má **cenu průjezdu** = **impedance** – nejčastěji *čas* (minuty) nebo *vzdálenost* (km).

Síťová analýza pak hledá třeba cestu, jejíž součet impedancí je co nejmenší.

## Slovníček

| Pojem | Co to znamená | Příklad |
|-------|---------------|---------|
| **Impedance** | „Cena“ průjezdu úsekem | 2 minuty, 1,5 km |
| **Cost** | Součet impedancí celé trasy | trasa trvá 23 minut |
| **Travel Mode** | Způsob dopravy | auto, chůze, kamion |
| **Facility** (zařízení) | Cíl, ke kterému se vztahuje analýza | nemocnice |
| **Incident / Demand Point** | Místo, odkud vzniká poptávka | obec |
| **Cutoff** | Maximální čas / vzdálenost, dál se nehledá | 20 minut |
| **Breaks / Cutoffs** | Intervaly pro dojezdové zóny | 5, 10, 15 minut |

## Jaké otázky síťové analýzy řeší

| Otázka | Typ analýzy | Nástroj v ArcGIS |
|--------|-------------|------------------|
| *Jak se nejrychleji dostanu z A do B?* | Trasování | Route |
| *V jakém pořadí objet zastávky, abych ujel co nejméně?* | Problém obchodního cestujícího (TSP) | Route (přeuspořádání zastávek), Plan Routes |
| *Kam dojedu do 10 minut?* | Dostupnost | Service Area, Create Drive-Time Areas |
| *Která nemocnice je pro tuto obec nejbližší?* | Nejbližší zařízení | Closest Facility, Find Nearest |
| *Jak rozdělit zákazníky mezi 3 auta?* | Rozvoz (VRP) | Vehicle Routing Problem |
| *Kam postavit 2 nové nemocnice, aby pokryly co nejvíc lidí?* | Umístění (alokace) | Location-Allocation |

## Dijkstrův algoritmus – jak počítač najde nejkratší cestu

Na Dijkstrově algoritmu (Edsger Dijkstra, 1959) stojí skoro všechny nástroje výše.

**Princip jednou větou:** postupně „rozšiřuj“ hledání od startu vždy do toho uzlu, který je zatím nejblíž, a průběžně si zapisuj nejlepší známou vzdálenost ke každému uzlu.

**Kroky:**

1. Start má hodnotu 0, všechny ostatní uzly ∞ (zatím neznámé).
2. Vyber nevyřešený uzel s **nejmenší** hodnotou a označ ho jako vyřešený.
3. Pro jeho sousedy spočítej: *hodnota vybraného uzlu + délka hrany*. Pokud je to méně, než mají zapsáno, přepiš to.
4. Opakuj krok 2–3, dokud nevyřešíš cíl (nebo všechny uzly).

### Vyřešený příklad

Síť s pěti uzly, čísla jsou minuty jízdy:

| Hrana | A–B | A–C | C–B | B–D | C–D | C–E | D–E |
|-------|-----|-----|-----|-----|-----|-----|-----|
| Čas (min) | 4 | 2 | 1 | 5 | 8 | 10 | 3 |

Hledáme nejrychlejší cestu **z A do E**. Tučně = uzel v tomto kroku vyřešen.

| Krok | Vybraný uzel | A | B | C | D | E |
|------|--------------|---|---|---|---|---|
| Start | – | 0 | ∞ | ∞ | ∞ | ∞ |
| 1 | A | **0** | 4 | 2 | ∞ | ∞ |
| 2 | C | | 3 *(2+1 < 4)* | **2** | 10 | 12 |
| 3 | B | | **3** | | 8 *(3+5 < 10)* | 12 |
| 4 | D | | | | **8** | 11 *(8+3 < 12)* |
| 5 | E | | | | | **11** |

**Výsledek:** A → C → B → D → E, celkem **11 minut**.

Všimni si: přímá cesta C → E (10 min) vypadá lákavě, ale celkem by to bylo 12 minut. Algoritmus „objížďku“ přes B a D najde sám.

**A\*** je vylepšený Dijkstra: navíc odhaduje, jak daleko je to ještě do cíle (třeba vzdušnou čarou), takže zbytečně nezkoumá uzly, které vedou opačným směrem. Je proto rychlejší.

**TSP (problém obchodního cestujícího):** navštívit všechna místa právě jednou a vrátit se na start co nejkratší cestou. ArcGIS nejdřív Dijkstrovým algoritmem spočítá vzdálenosti mezi všemi dvojicemi zastávek a pak heuristikou (přibližnou metodou) hledá nejlepší pořadí. Přesné řešení by u mnoha zastávek trvalo extrémně dlouho.

## ArcGIS Online vs. ArcGIS Pro

| | ArcGIS Online | ArcGIS Pro |
|-|---------------|------------|
| Kde běží | V prohlížeči | Na počítači |
| Síť | Esri ji dodá (online služba) | Online služba Esri **nebo** vlastní síť (Network Dataset) |
| Nastavení | Základní, jednoduché | Podrobné (impedance, omezení, pořadí…) |
| Kredity | Ano | Ano, pokud používáš online síť Esri |
| Sdílení výsledků | Snadné (webová mapa, dashboard) | Nutné publikovat / exportovat |
| Pro koho | Rychlé úlohy, začátečníci | Detailní analýzy, projekty |

---

# Část A – Geokódování

**Geokódování** = převod adresy (text) na souřadnice (bod v mapě).

Uděláš to dvěma různými nástroji a pak porovnáš, jak moc se výsledky liší.

## A1 – Stáhni tabulku adres

**Stáhni si připravená data:**

| Soubor | K čemu |
|--------|--------|
| [Adresy_CV08.xlsx](download/Adresy_CV08.xlsx) | 10 adres pro geokódování v ArcGIS Online (A2) a Google Sheets (A3) |
| [Adresy_CV08.csv](download/Adresy_CV08.csv) | Stejné adresy jako CSV – pro Geoapify a MapDevelopers (A3) |
| [Nemocnice_CV08.xlsx](download/Nemocnice_CV08.xlsx) | Šablona – doplň adresy nemocnic ve svém kraji (žlutá pole) |

Tabulka adres vypadá takto:

| ID | Ulice | Cislo_popisne | Nazev_obce | Adresa |
|----|-------|---------------|------------|--------|
| 1 | Česká | 25 | Brno | Česká 25, Brno |
| 2 | Masarykova | 10 | Brno | Masarykova 10, Brno |
| 3 | Nádražní | 7 | Modřice | Nádražní 7, Modřice |
| 4 | Dlouhá | 123 | Šlapanice | Dlouhá 123, Šlapanice |
| 5 | Hlavní | 52 | Rajhrad | Hlavní 52, Rajhrad |
| 6 | Komenského | 8 | Židlochovice | Komenského 8, Židlochovice |
| 7 | Těšanská | 15 | Měnín | Těšanská 15, Měnín |
| 8 | Masarykovo náměstí | 2 | Pohořelice | Masarykovo náměstí 2, Pohořelice |
| 9 | Svatopluka Čecha | 1 | Hustopeče | Svatopluka Čecha 1, Hustopeče |
| 10 | Nádražní | 10 | Ivančice | Nádražní 10, Ivančice |

Sloupec **Adresa** se skládá vzorcem z ostatních sloupců:

```
=B2&" "&C2&", "&D2
```

Jedno pole s celou adresou se pak mnohem snáz nastavuje ve všech geokodérech. Pokud chceš vlastní adresy, přepiš sloupce B–D. Adresa se přepočítá sama.

> **Nemocnice:** do šablony `Nemocnice_CV08.xlsx` doplň 4–8 nemocnic ve svém kraji. První řádek je ukázka, další řádky přidej pod něj a vzorec ve sloupci Adresa stáhni dolů. Prázdné řádky v tabulce nenechávej. Tabulku geokóduj stejným postupem jako v A2 a vznikne ti vrstva **Nemocnice** pro části B a C.

## A2 – Geokódování v ArcGIS Online

1. Přihlas se na [ArcGIS Online](https://www.arcgis.com).
2. **Content → New item → Your device** → vyber `Adresy_CV08.xlsx`.
3. Zvol **Add the file and create a hosted feature layer**.
4. U nastavení polohy (*Location settings*) vyber **Addresses**.
5. Namapuj pole:
   - **Address** → `Adresa`
   - (pokud nabízí zemi, nastav **Czechia**)
6. Pojmenuj vrstvu `Adresy_AGOL` a potvrď. ArcGIS adresy převede na body.
7. **Zkontroluj body v mapě.** Leží každý bod u správné adresy? Pokud ne, otevři vrstvu v Map Vieweru a bod přesuň (**Edit → vyber bod → přetáhni**).
8. **Export:** na stránce vrstvy (item page) → **Export Data → Export to Shapefile** → vznikne nová položka → **Download**. Ulož jako `Adresy_AGOL.zip`.

## A3 – Geokódování druhým nástrojem

Vyber **jednu** z možností. Výstupem je vždy CSV se sloupci `Latitude` a `Longitude`.

### Možnost 1 – Google Sheets + Geocode by Awesome Table

1. Nahraj tabulku do Google Sheets (nebo ji tam zkopíruj).
2. **Rozšíření (Extensions) → Doplňky → Získat doplňky** → vyhledej *Geocode by Awesome Table* → nainstaluj.
3. **Rozšíření → Geocode by Awesome Table → Start Geocoding**.
4. Jako adresu vyber sloupec `Adresa` → spusť.
5. Doplněk přidá sloupce `Latitude` a `Longitude`.
6. **Soubor → Stáhnout → CSV** → ulož jako `Adresy_Druhy.csv`.

### Možnost 2 – Geoapify (když doplněk nefunguje)

1. Otevři [Geoapify – geocoding](https://www.geoapify.com/geocoding-api) (nebo vyhledej „Geoapify batch geocoding online“).
2. Vlož adresy ze sloupce `Adresa` (každou na nový řádek) nebo nahraj CSV.
3. Spusť geokódování a výsledek stáhni jako CSV → `Adresy_Druhy.csv`.

### Možnost 3 – MapDevelopers (nejrychlejší, bez registrace)

1. Otevři [MapDevelopers – Batch Geocode](https://www.mapdevelopers.com/batch_geocode_tool.php).
2. Vlož adresy (každou na nový řádek) → **Find Addresses**.
3. Výslednou tabulku zkopíruj do Excelu, případně rozděl přes **Data → Text do sloupců** (oddělovač tabulátor).
4. Ulož jako CSV → `Adresy_Druhy.csv`.

> ⚠️ **Pozor na desetinnou čárku!** Souřadnice musí mít **desetinnou tečku** (`49.1951`, ne `49,1951`). Jinak je ArcGIS nenačte jako čísla. V Google Sheets: **Soubor → Nastavení → Národní prostředí → Spojené státy** a teprve pak exportuj.

## A4 – Porovnání v ArcGIS Pro

1. Spusť ArcGIS Pro → **New → Map**.
2. **Map → Add Data** → přidej `Adresy_AGOL.zip` (shapefile).
3. CSV převeď na body nástrojem **XY Table To Point** (Analysis → Tools → vyhledej název):

   | Parametr | Hodnota |
   |----------|---------|
   | Input Table | `Adresy_Druhy.csv` |
   | Output Feature Class | `Adresy_Druhy_body` |
   | X Field | `Longitude` |
   | Y Field | `Latitude` |
   | Coordinate System | GCS WGS 1984 |

   > Zapamatuj si: **X = zeměpisná délka (Longitude), Y = zeměpisná šířka (Latitude).** Když to prohodíš, body skončí v Indickém oceánu.

4. Obarvi vrstvy odlišně: AGOL = **modrá**, druhý nástroj = **červená**.
5. Nástrojem **Measure** (karta Map → Measure) změř vzdálenost mezi modrým a červeným bodem pro každé ID a zapiš do tabulky:

   | ID | Adresa | Rozdíl (m) | Který bod je správně? |
   |----|--------|------------|------------------------|
   | 1 | Česká 25, Brno | … | … |
   | … | … | … | … |

6. Napiš 2–3 věty: *Který geokodér byl přesnější? U jakých adres vznikly největší chyby a proč (malá obec, chybějící číslo popisné, stejný název ulice ve více obcích…)?*

---

# Část B – Síťové analýzy v ArcGIS Online

**Příprava:**

1. Otevři **Map Viewer** a přidej vrstvy **Obce** (svého kraje) a **Nemocnice**.
   - Obce z ArcČR 500 nahraj jako zazipovaný shapefile (**Content → New item**) – vznikne hosted feature layer.
   - **Šetři kredity:** vyber jen obce jednoho okresu nebo do 50 obcí.
2. Vpravo klikni na **Analysis → Tools → Use Proximity**.

> V starší verzi (Map Viewer Classic) najdeš nástroje pod **Analysis → Use Proximity** a některé se jmenují trochu jinak – viz poznámky u úloh.

## B1 – Find Nearest (nejbližší nemocnice)

**Otázka:** *Ke které nemocnici to má každá obec nejblíž – po silnici?*

| Parametr | Hodnota |
|----------|---------|
| Odkud hledáš (input / analysis layer) | Obce |
| Co hledáš (near layer) | Nemocnice |
| Measurement type | Driving Time (nebo Driving Distance) |
| Limit hledání | 30 km |
| Počet nejbližších | 1 |

→ **Run**

**Odevzdej:**

- Mapu s liniemi obec → nemocnice.
- Výřez atributové tabulky (vzdálenost, čas, nemocnice).
- 2–3 věty: *Jaká je průměrná dojezdová doba? Které části kraje jsou na tom nejhůř?*

## B2 – Create Drive-Time Areas (dojezdové zóny)

> V novém Map Vieweru se nástroj jmenuje **Generate Travel Areas**.

**Otázka:** *Kam se dá od nemocnice dojet za 5, 10 a 15 minut?*

| Parametr | Hodnota |
|----------|---------|
| Input layer | Nemocnice |
| Travel mode | Driving Time |
| Časy (cutoffs) | 5, 10, 15 minut |

→ **Run**

**Odevzdej:**

- Mapu se zónami (izochronami) a legendou.
- 2–3 věty: *Které obce leží mimo 15minutovou zónu? Kde by se hodila nová nemocnice?*

## B3 – Plan Routes (optimální pořadí zastávek)

**Otázka:** *Jakým pořadím objet 8–12 obcí, abych jel co nejkratší dobu?* (např. inspekční cesta)

| Parametr | Hodnota |
|----------|---------|
| Stops | 8–12 vybraných obcí (nebo tvé adresy z části A) |
| Počet vozidel (routes) | 1 |
| Start a návrat | Krajský úřad / jedna zvolená adresa |
| Travel mode | Driving Time |

→ **Run** (Plan Routes pořadí zastávek optimalizuje automaticky.)

**Odevzdej:**

- Mapu trasy s očíslovaným pořadím zastávek.
- Celkový čas a délku trasy.
- 2–3 věty: *Liší se optimální pořadí od toho, jak bys zastávky seřadil sám? O kolik je kratší?* (Pro porovnání můžeš použít výsledek z úlohy C1.)

---

# Část C – Síťové analýzy v ArcGIS Pro

## C0 – Příprava (udělej jednou)

1. **Přihlas se** v ArcGIS Pro (vpravo nahoře) školním účtem.
2. Ověř licenci: **Project → Licensing** → u *Network Analyst* musí být **Yes**.
3. Přidej vrstvy **Obce** a **Nemocnice** (a případně tvé adresy z části A).
4. **Zdroj sítě:** karta **Analysis → Network Analysis → Network Data Source** → nech **ArcGIS Online** (výchozí). Nemusíš tak vyrábět vlastní síť.

> Všechny nástroje níže spustíš stejně: **Analysis → Network Analysis → vyber nástroj**. Do obsahu mapy se přidá nová skupina vrstev a v pásu karet se objeví nová karta (např. *Route*), kde vše nastavíš. Výpočet spustíš tlačítkem **Run**.

**Pokročilá varianta (nepovinná):** vytvořit vlastní **Network Dataset** ze silnic ArcČR 500 (nástroj *Create Network Dataset*). Je to ale výrazně pracnější – silnice musí být ve feature datasetu a musíš jim dopočítat čas průjezdu.

## C1 – Route + TSP (nejkratší trasa a nejlepší pořadí)

**Otázka:** *Jaká je nejrychlejší trasa přes vybrané body – a záleží na tom, jestli optimalizuju čas nebo vzdálenost?*

**Postup:**

1. **Analysis → Network Analysis → Route**.
2. Karta *Route* → **Import Stops** → vyber svou vrstvu bodů (5–10 bodů).
3. **Travel Mode:** *Driving Time*.
4. **Sequence:** *Use Current* (pořadí, jak jsou body v tabulce = „ruční“ pořadí) → **Run**.
5. Zapiš si celkový čas a délku: otevři atributovou tabulku podvrstvy **Routes** (pole `Total_Minutes`, `Total_Kilometers`).
6. Teď nastav **Sequence:** *Find Best* → **Run** → zapiš znovu.
   - Pro **TSP s návratem na start** přidej startovní bod ještě jednou na konec a zvol *Preserve First and Last Stop*.
7. Nakonec změň **Travel Mode** na *Driving Distance* → **Run** → zapiš.

**Výsledky zapiš do tabulky:**

| Varianta | Čas (min) | Délka (km) |
|----------|-----------|------------|
| Ruční pořadí, čas | | |
| Optimální pořadí (TSP), čas | | |
| Optimální pořadí, vzdálenost | | |

**Odevzdej:** mapy tras, tabulku a 2–3 věty: *Kolik ušetřila optimalizace pořadí? Proč je nejkratší trasa jiná než nejrychlejší?* (Nápověda: dálnice bývá delší, ale rychlejší.)

## C2 – Service Area (dojezdové zóny)

**Otázka:** *Kolik obcí je do 15 minut od nemocnice?*

**Postup:**

1. **Analysis → Network Analysis → Service Area**.
2. **Import Facilities** → Nemocnice.
3. **Direction:** *Away From Facilities* (od nemocnice ven).
4. **Cutoffs:** `5, 10, 15` (minuty), Travel Mode *Driving Time*.
5. **Run**.
6. Spočítej obce: **Map → Select By Location** → vstup *Obce*, vztah *Intersect*, výběrová vrstva *Polygons* (zóna 15 min). Počet vybraných uvidíš dole v atributové tabulce.

**Odevzdej:** mapu zón a 2–3 věty: *Kolik obcí (a kolik % obyvatel, pokud máš počet obyvatel) je do 15 minut?*

## C3 – Closest Facility (nejbližší nemocnice)

**Otázka:** *Ke které nemocnici to má každá obec nejblíž a kolik obcí je dál než 20 minut?*

**Postup:**

1. **Analysis → Network Analysis → Closest Facility**.
2. **Import Facilities** → Nemocnice.
3. **Import Incidents** → Obce.
4. **Facilities:** 1 (hledáme jednu nejbližší).
5. **Cutoff:** 20 (minut), Travel Mode *Driving Time*.
6. **Run**.
7. Počet obcí mimo 20 minut = **počet obcí − počet tras** v podvrstvě *Routes* (obce, ke kterým se trasa nenašla, jsou mimo limit).

**Odevzdej:** mapu tras obec → nemocnice a 2–3 věty: *Kolik obcí je mimo 20 minut? Kde leží?*

Jaký je rozdíl oproti B1? Úloha je skoro stejná – porovnej výsledek z ArcGIS Online a Pro. Vyšlo to stejně?

## C4 – Vehicle Routing Problem (rozvoz)

**Otázka:** *Jak rozvézt zboží ze skladu do obcí 2–3 auty, aby byl celkový čas co nejmenší?*

**Postup:**

1. **Analysis → Network Analysis → Vehicle Routing Problem**.
2. **Import Orders** → obce, kam se rozváží (15–25 obcí).
3. **Import Depots** → sklad (1 bod, např. jedna z tvých adres).
4. **Routes:** přidej 2–3 vozidla (*Import Routes* / *Add Route*).
5. Otevři atributovou tabulku podvrstvy **Routes** a vyplň u každého vozidla:
   - `StartDepotName` a `EndDepotName` = název skladu,
   - `MaxOrderCount` = 10 (max. 10 zastávek na auto).
6. **Run**.

**Odevzdej:** mapu tras (každé auto jinou barvou) a 2–3 věty: *Kolik zastávek obslouží každé auto? Jaký je celkový čas? Jak bys je rozdělil ručně a bylo by to horší?*

## C5 – Location-Allocation (kam postavit nové zařízení)

**Otázka:** *Které 2 z navržených míst jsou nejlepší pro novou nemocnici, aby do 15 minut pokryly co nejvíc lidí?*

**Postup:**

1. Připrav vrstvu **kandidátů** – 5–8 bodů, kde by nová nemocnice mohla stát (např. větší obce v oblastech mimo zóny z C2).
2. **Analysis → Network Analysis → Location-Allocation**.
3. **Import Facilities** → kandidáti.
4. **Import Demand Points** → Obce. V mapování polí přiřaď **Weight** = pole s **počtem obyvatel** (tím řekneš, že velká obec je důležitější).
5. **Problem Type:** *Maximize Coverage*.
6. **Facilities** (počet k výběru): 2.
7. **Cutoff:** 15 (minut), Travel Mode *Driving Time*.
8. **Run**. Vybraná místa mají v tabulce Facilities hodnotu `FacilityType = Chosen`.

**Odevzdej:** mapu s vybranými místy a 2–3 věty: *Proč algoritmus vybral právě tato místa? Kolik obyvatel nově pokryjí?*

---

# Část D – Odevzdání

## Co odevzdat

**Jeden dokument (Word nebo PDF)** pro jeden zvolený kraj. Pro každou úlohu použij stejnou šablonu:

| Položka | Co napsat |
|---------|-----------|
| **Úloha** | např. C2 – Service Area |
| **Otázka** | co jsem zjišťoval |
| **Vstupní vrstvy** | Nemocnice, Obce (okres …) |
| **Parametry** | Driving Time, cutoffs 5/10/15 min, … |
| **Mapa** | s legendou, měřítkem a severkou |
| **Výsledek a interpretace** | 2–3 věty |

## Kontrolní seznam

- [ ] **A:** tabulka adres, 2 vrstvy bodů, tabulka rozdílů, zhodnocení přesnosti geokódování
- [ ] **B1** Find Nearest, **B2** Drive-Time Areas, **B3** Plan Routes
- [ ] **C1** Route + TSP (tabulka 3 variant), **C2** Service Area, **C3** Closest Facility, **C4** VRP, **C5** Location-Allocation
- [ ] Krátké porovnání ArcGIS Online vs. ArcGIS Pro (co bylo jednodušší, co přesnější, vyšly B1 a C3 stejně?)
- [ ] Všechny mapy mají legendu, měřítko a popisky
- [ ] **Bonus:** webová mapa nebo dashboard v ArcGIS Online, který kombinuje výsledky

---

# Časté problémy

| Problém | Řešení |
|---------|--------|
| Body z CSV jsou v moři / u Afriky | Prohozené X a Y. X = Longitude, Y = Latitude. |
| XY Table To Point nenabízí sloupce souřadnic | Souřadnice mají desetinnou čárku → jsou text. Oprav na tečku a exportuj znovu. |
| Bod je v jiné obci se stejnou ulicí | Doplň do adresy obec (a případně PSČ) a geokóduj znovu, nebo bod přesuň ručně. |
| Network Analysis je šedé / nejde spustit | Nejsi přihlášený v ArcGIS Pro, nebo chybí licence Network Analyst (Project → Licensing). |
| Chyba „insufficient credits“ | Došly kredity – zmenši počet bodů nebo kontaktuj vyučujícího. |
| Některé body se nenapojí na síť | Bod leží daleko od silnice (např. centroid obce v lese). Posuň ho blíž k silnici. |
| Closest Facility nenajde trasu pro některé obce | To je v pořádku – jsou dál než cutoff (20 min). Právě ty počítáš jako „mimo dostupnost“. |

---

## VARIANTA FUNNY TIMES WITH ANALYSIS

- Otevřete disk [Zadáníčko](https://drive.google.com/drive/folders/1PeaxpP0Suy0DBky8KBiBZswD7NctY637?usp=share_link)
