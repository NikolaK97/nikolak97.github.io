---
title: CV08 – Síťové analýzy a geokódování
layout: default
description: Detailní návod pro ArcGIS Pro a ArcGIS Online – geokódování, síťové analýzy, TSP a Dijkstrův algoritmus
---

# CV08 – Síťové analýzy a geokódování
Podrobný metodický návod pro ArcGIS Pro a ArcGIS Online

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
# Geokódování adres – přehled metod

Níže najdete čtyři způsoby, jak jednoduše převést adresy na geografické souřadnice (latitude, longitude). Každá varianta má své výhody – od rychlých online nástrojů až po integraci přímo v Google Sheets.

---

## Aleternativa A – Geoapify (online, copy-paste / CSV)

**Vhodné pro:** přesnější výsledky, menší objem dat (cca 10–100 adres)

1. Otevřete web [Geoapify Online Geocoding](https://www.geoapify.com/geocoding-api) (nebo vyhledejte „Geoapify online geocoding“).  
2. Vložte až 10 adres do textového pole – každá adresa na samostatný řádek.  
   Alternativně můžete nahrát CSV soubor s adresami.  
3. Pokud nahráváte CSV, namapujte sloupce – určete, který obsahuje plnou adresu.  
4. Klikněte na „Geocode“ (nebo podobné tlačítko) a spusťte zpracování.  
5. Po dokončení si stáhněte výsledky jako CSV – soubor bude obsahovat:  
   - Latitude a Longitude  
   - Doplňkové informace (např. typ shody, skóre přesnosti)  
6. Soubor si uložte k sobě (např. `results.csv`).

---

## Alternativa B – MapDevelopers: Batch Geocode (nejrychlejší jednorázovka)

**Vhodné pro:** rychlé jednorázové použití bez registrace

1. Otevřete stránku [MapDevelopers – Batch Geocode](https://www.mapdevelopers.com/batch_geocode_tool.php).  
2. Vložte 10 adres (každou na nový řádek).  
3. Klikněte na „Find Addresses“.  
4. U každé adresy se zobrazí souřadnice.  
5. Výslednou tabulku zkopírujte do Excelu nebo Google Sheets.  
6. Pomocí funkce „Text do sloupců“ (oddělovač TAB) tabulku upravte a uložte jako CSV.


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

# Síťové analýzy v ArcGIS Online a ArcGIS Pro

Tento úkol se zaměřuje na aplikaci síťových (network) analýz v prostředí **ArcGIS Online** a **ArcGIS Pro**.  
Cílem je naučit se využívat síťová data k analýze dostupnosti, vyhledávání nejbližších zařízení, optimalizaci tras a rozvozů, a návrhu umístění nových služeb.  

Pro práci použijte **datovou sadu ArcČR 500** – především vrstvy:
- **Dopravní síť** (silnice, komunikace)
- **Obce** nebo **části obcí**
- **Veřejná zařízení** (např. nemocnice, školy, pošty – dle dostupných dat nebo vytvořených bodů)
- Volitelné vrstvy: hranice krajů, centroidy obcí, administrativní členění

---

## 1. Síťové analýzy v ArcGIS Online

### Aktivace nástrojů
1. Přihlaste se do svého účtu **ArcGIS Online**.  
2. Otevřete novou nebo existující webovou mapu.  
3. V horní liště zvolte **Analysis → Use Proximity**.  
4. Zkontrolujte, že máte přístup k síťovým službám (vyžadují *credits*).

---

### 1.1 Find Nearest

**Účel:** zjistit, které zařízení (např. nemocnice) je nejblíže zadaným adresám (např. obcím).  

**Postup:**
1. `Input layer`: vrstvy adres (obce ArcČR 500).  
2. `Near layer`: vrstvy zařízení (např. školy, nemocnice).  
3. `Measurement type`: *Driving distance*.  
4. `Search radius`: 30 km.  
5. `Nearest locations`: 1.  
6. Klikněte na **Run Analysis**.

**Odevzdat:**
- Výsledná mapa s liniemi (adresy → zařízení).  
- Tabulka s atributy (vzdálenost, čas jízdy, ID zařízení).  
- Krátký popis interpretace: *Jaká je průměrná dostupnost? Jsou regionální rozdíly?*

---

### 1.2 Create Drive-Time Areas

**Účel:** zjistit, jak daleko jsou zařízení dosažitelná v určitém čase (5, 10, 15 minut).  

**Postup:**
1. `Input layer`: zařízení (např. nemocnice, hasičské stanice).  
2. `Measure`: *Driving time*.  
3. `Drive times`: 5, 10, 15 minut.  
4. `Travel mode`: *Auto*.  
5. Klikněte na **Run Analysis**.

**Odevzdat:**
- Mapa s polygony dostupnosti (izochny) s popisem vrstev.  
- Krátká interpretace: *Jaké oblasti nejsou v 15 min dostupnosti? Kde je potřeba nové zařízení?*

---

### 1.3 Plan Routes

**Účel:** optimalizovat pořadí zastávek na trase (např. inspekční nebo doručovací trasa).  

**Postup:**
1. `Stops layer`: adresy zastávek (např. 8–12 obcí).  
2. `Travel mode`: *Driving*.  
3. `Reorder stops`: zapnuto.  
4. Klikněte na **Run Analysis**.  

**Odevzdat:**
- Mapa s trasou a optimálním pořadím zastávek.  
- Popis: *Jaký je rozdíl mezi ručně zadaným pořadím a optimalizovaným výsledkem?*

---

## 2. Síťové analýzy v ArcGIS Pro

### 2.1 Aktivace Network Analyst
1. Otevřete **Customize → Extensions → Network Analyst**.  
2. Zapněte **Network Analyst Toolbar**.  
3. Připravte datové vrstvy (dopravní síť z ArcČR 500, obce, zařízení).  
4. Ověřte, že síťová vrstva má atributy pro výpočet času nebo délky (`TravelTime`, `Length`).

---

### 2.2 Route – nalezení nejkratší cesty

**Cíl:** vypočítat nejkratší nebo nejrychlejší trasu mezi body.

**Postup:**
1. Vytvořte nový projekt a otevřete mapu s dopravní sítí.  
2. **Network Analyst → New Route**.  
3. Přidejte zastávky: **Stops → Load Locations** (adresy).  
4. Nastavte `Impedance = TravelTime` a zapněte `Reorder Stops`.  
5. Klikněte na **Solve**.

**Odevzdat:**
- Mapa s výslednou trasou.  
- Porovnání dvou variant: trasa podle vzdálenosti vs. podle času.  
- Diskuze: *Jak volba impedance ovlivňuje výsledek?*

---

### 2.3 Service Area – analýza dostupnosti

**Cíl:** vytvořit zónu dostupnosti kolem zařízení v různých časových intervalech.

**Postup:**
1. **Network Analyst → New Service Area**.  
2. Přidejte zařízení (např. nemocnice).  
3. Nastavte `Breaks`: 5, 10, 15 minut.  
4. `Travel direction`: From facility.  
5. Klikněte na **Solve**.

**Odevzdat:**
- Polygony dostupnosti.  
- Analýzu: *Kolik obcí spadá do 15min oblasti?*

---

### 2.4 Closest Facility – hledání nejbližšího zařízení

**Cíl:** určit, které zařízení je nejblíže jednotlivým obcím.

**Postup:**
1. **New Closest Facility**.  
2. `Facilities`: zařízení (např. nemocnice).  
3. `Incidents`: obce nebo adresy.  
4. `Impedance`: TravelTime.  
5. `Cutoff`: 20 min.  
6. Klikněte na **Solve**.

**Odevzdat:**
- Trasy z obcí k nejbližším nemocnicím.  
- Statistiku: *Kolik obcí je mimo 20min dostupnost?*

---

### 2.5 Vehicle Routing Problem (VRP)

**Cíl:** naplánovat rozvoz zboží z depa k zákazníkům s omezením počtu objednávek na jedno vozidlo.

**Postup:**
1. **New Vehicle Routing Problem**.  
2. `Orders`: zákazníci (např. obce s poptávkou).  
3. `Depots`: sklady (výchozí body).  
4. `Routes`: vozidla (např. 2–3).  
5. `MaxOrderCount`: např. 10 zákazníků.  
6. Klikněte na **Solve**.

**Odevzdat:**
- Mapa s trasami vozidel.  
- Popis optimalizace: *Jak se změnila celková délka oproti ručnímu rozdělení tras?*

---

### 2.6 Location-Allocation – výběr optimálního umístění

**Cíl:** najít nejlepší umístění nových zařízení vzhledem k poptávce.

**Postup:**
1. **New Location-Allocation**.  
2. `Demand Points`: obce (populace).  
3. `Facilities`: potenciální umístění zařízení (např. školy).  
4. `Problem Type`: Maximize coverage.  
5. `Facilities to locate`: 2.  
6. Klikněte na **Solve**.

**Odevzdat:**
- Mapa s vybranými lokalitami.  
- Interpretace: *Proč byla vybrána právě tato místa?*

---

### 2.7 TSP – Problém obchodního cestujícího

**Teorie:**  
Cílem je navštívit všechny body právě jednou a vrátit se na start s minimální délkou trasy.  
ArcGIS používá heuristiku TSP založenou na **Dijkstrově algoritmu**.

**Postup v ArcGIS Pro:**
1. **New Route**  
2. Přidejte body (Stops).  
3. Aktivujte **Reorder Stops to Find Optimal Route**.  
4. Klikněte na **Solve**.

**Odevzdat:**
- Optimalizovanou trasu.  
- Porovnání s ručně zadaným pořadím zastávek.

---

### 2.8 Dijkstrův algoritmus (princip)

**Použití:** v trasování, Closest Facility, Route i VRP.  
Zajišťuje nalezení nejkratší cesty mezi uzly dopravní sítě.

**Princip:**
1. Každý uzel má počáteční hodnotu ∞ (nekonečno), start = 0.  
2. Vybere se nejbližší nevyřešený uzel.  
3. Aktualizují se vzdálenosti k sousedním uzlům.  
4. Postup se opakuje, dokud nejsou všechny uzly zpracovány.  

Varianta s heuristikou využívá **A\*** algoritmus (rychlejší výpočet díky odhadu vzdálenosti k cíli).

---

## 3. Zadání úkolu

### Cíl
Pro jeden vybraný kraj vytvořte a zdokumentujte příklady síťových analýz v **ArcGIS Online** a **ArcGIS Pro**.  
Zaměřte se na **dostupnost služeb**, **optimalizaci tras** a **návrh nových lokalit**.

### Datové podklady
- ArcČR 500 (silniční síť, obce, zařízení)
- Volitelně: vlastní body adres, doplňková zařízení (např. hasičské stanice, školy)
- V případě potřeby převeďte vrstvy do formátu vhodného pro ArcGIS Online (Feature Layer)

### Odevzdání
1. **Dokumentace (word nebo PDF)** obsahující:
   - popis postupu každé analýzy,  
   - nastavené parametry,  
   - interpretaci výsledků (2–3 věty ke každé úloze),  
   - krátké porovnání mezi online a desktopovou verzí.  
2. **Bonus:** vytvořte webovou mapu nebo *dashboard* s kombinací výsledků.



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

