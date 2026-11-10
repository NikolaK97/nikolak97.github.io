---
layout: default
title: "CV09 – Opakování: Volební účast v komunálních volbách 2026"
---

# CV10 – Opakování: Kde lidé chodí k volbám?

> **Výstup cvičení:** velký mapový výstup A3 se třemi mapami, grafem, tabulkou a textovým komentářem – exportovaný do PDF a PNG.


## Úvod

Mapa nemusí ukázat všechno najednou – často je lepší zvýraznit jednu hlavní informaci a zbytek potlačit. Velký mapový výstup ale může vedle hlavní mapy nabídnout i detail, přehledku, graf a krátký komentář, které čtenáři pomohou mapu pochopit. V tomto cvičení si na datech ArcČR 4.3 a výsledcích komunálních voleb 2026 zopakujete přípravu tabulky, join, výběry, Dissolve, různé typy symbologie a klasifikace, normalizaci a popisky – a všechno spojíte do jednoho layoutu.

## Scénář

Pracujete pro krajský úřad Moravskoslezského kraje. Po komunálních volbách 2026 chce vedení kraje vědět, **ve kterých obcích chodí lidé k volbám a ve kterých ne**, aby mohlo cílit informační kampaň před dalšími volbami. Výsledek představíte na jednom plakátu A3.

- **Publikum:** krajští zastupitelé a úředníci, mapově gramotní; plakát bude viset na chodbě úřadu a půjde i jako PDF.
- **Účel:** odpovědět na otázky „Kde byla volební účast nejnižší?“ a „Jak se liší okresy?“



## Data

| Data | Zdroj | Poznámka |
|---|---|---|
| vrstvy **Obce** a **Kraje** | ArcČR 4.3 (ARCDATA PRAHA) | obce mají pole `kod` (kód obce), `nazev`, `statuskod` – názvy ověřte ve své geodatabázi |
| výsledky komunálních voleb 2026 v obcích MSK | ČSÚ, volby.cz | <a href="/download/volby_2026_MSK_obce.xlsx" download>volby_2026_MSK_obce.xlsx</a> – list **Obce** (300 obcí) a list **Souhrn podle okresů** |


---

# Část A: Příprava dat

## Krok 1: Založte projekt a přidejte data 


## Krok 2: Vyberte obce kraje 
**a)** **Select By Attributes** na vrstvě Kraje: název kraje = `Moravskoslezský kraj`.

**b)** **Select By Location**: Input Features = Obce, Relationship = **Have their center in**, Selecting Features = Kraje. Nebo pomocí nástroje CLIP. 

**c)** **Export Features** → `Obce_MSK` do projektové geodatabáze. 



## Krok 3: Připravte tabulku voleb pro GIS 

**a)** Otevřete `volby_2026_MSK_obce.xlsx` v Excelu a prohlédněte oba listy.

**b)** List **Obce** zkopírujte do nového sešitu a přejmenujte sloupce na krátké názvy bez mezer, diakritiky a znaku % (jako v CV03). Nepotřebné sloupce smažte.

| Původní název | Nový název |
|---|---|
| Okres | `OKRES` |
| Kód obce | `KOD_OBCE` |
| Obec | `OBEC` |
| Voliči v seznamu | `VOLICI` |
| Vydané obálky | `OBALKY` |
| Volební účast v % | `UCAST` |
| Platné hlasy | `PLATNE_HL` |

**c)** Uložte jako `volby_MSK_GIS.xlsx`.

## Krok 4: Připojte výsledky voleb 
**a)** Přidejte list z `volby_MSK_GIS.xlsx`. **Add Join**: `kod` (vrstva) ↔ `KOD_OBCE` (tabulka). **Validate Join** – musí se spárovat 300 záznamů.

**b)** **Export Features** → `Obce_MSK_volby`. Dál pracujte s touto vrstvou; `Obce_MSK` z mapy odstraňte.


## Krok 5: Vytvořte okresy a hranici kraje – Dissolve 

**a)** **Pairwise Dissolve**: Input = `Obce_MSK_volby`, Dissolve Field = `OKRES`, Statistics Fields = `VOLICI` **SUM** a `OBALKY` **SUM** → `Okresy_MSK`. Výsledek má 6 prvků.

**b)** Do `Okresy_MSK` přidejte pole `UCAST_OKR` (Double) a v **Calculate Field** zadejte:

```
!SUM_OBALKY! / !SUM_VOLICI! * 100
```

**c)** Porovnejte výsledek s listem **Souhrn podle okresů** v Excelu – hodnoty se musí shodovat.

**d)** **Pairwise Dissolve** nad `Obce_MSK_volby` **bez** Dissolve Field → `Kraj_MSK` (jeden polygon kraje pro přehledku).

---

# Část B: Mapy a symbologie

## Krok 6: Kartogram volební účasti 
**a)** Vrstvu `Obce_MSK_volby` přejmenujte na **Volební účast**. Symbology → **Graduated Colors**, Field = `OBALKY`, Normalization = *None*.


**b)** Nastavte **Normalization** = `VOLICI` a porovnejte hodnoty tříd s polem `UCAST`.



**c)** Normalizaci vraťte na *None* a Field přepněte na `UCAST`. 5 tříd, sekvenční jednobarevná škála. Porovnejte **Natural Breaks (Jenks)**, **Quantile** a **Equal Interval** a jednu vyberte. Popisky tříd upravte na celá procenta.



## Krok 7: Kartodiagram počtu voličů a hranice okresů 

**a)** Vrstvu **Volební účast** zkopírujte (Copy → Paste na název mapy) a kopii přejmenujte na **Počet voličů**.

**b)** U kopie nastavte **Graduated Symbols**, Field = `VOLICI`, Natural Breaks, 5 tříd. Ověřte, že kopie nemá výplň polygonů.

**c)** **Template** → **Properties** → u **Color** otevřete **Color Properties** a zadejte vlastní barvu v RGB, která kontrastuje s kartogramem (pro modrý kartogram např. R 230, G 97, B 1). Obrys tmavě šedý 0,5 pt, **Apply**. Upravte **Minimum** a **Maximum size**, aby kruh Ostravy nepřekryl půlku okresu.

**d)** Přidejte `Okresy_MSK` nad kartogram: bez výplně, tmavě šedá hranice 1,5 pt. Hranice obcí v kartogramu nastavte tenčí a světlejší (0,3 pt) – hierarchie čar má odpovídat hierarchii jednotek.


## Krok 8: Detail Ostravska s popisky 

**a)** V **Catalog** rozbalte **Maps**, pravým na mapu **Volební účast MSK 2026** → **Copy**, pravým na **Maps** → **Paste**. Novou mapu přejmenujte na **Detail Ostravsko** a otevřete ji.

**b)** Přibližte se na okres **Ostrava-město** a vytvořte záložku **Map → Bookmarks → New Bookmark** `Ostravsko`.

**c)** Ve vrstvě **Volební účast** zapněte popisky (**Labeling → Label**, Field = `nazev`). V **Label Class → Symbol** nastavte bezpatkové písmo 8 pt a bílé **Halo** 1 pt, v **Position** umístění **Horizontal in polygon** a **Stack label**.

> ❓ Proč je u popisků halo, a ne silný černý obrys písma?
>
> <details><summary>Odpověď</summary>Halo odděluje text od podkladu, takže je čitelný na světlých i tmavých třídách. Silný obrys by písmo zahustil a na malém rozměru by se slévalo.</details>

**d)** V hlavní mapě **Volební účast MSK 2026** popisky také zapněte, ale v **Label Class → Class → SQL query** sestavte výraz `VOLICI` *is greater than or equal to* `5000` – v měřítku kraje popíšete jen 26 největších obcí.

## Krok 9: Přehledka a graf (5 min)

**a)** **Insert → New Map**, pojmenujte ji **Přehledka**. Přidejte do ní vrstvu **Kraje** (šedá výplň, bílá hranice) a nad ni `Kraj_MSK` (výrazná barva). Podkladovou mapu odstraňte – přehledka má být co nejjednodušší.

**b)** Vraťte se do hlavní mapy. Pravým na `Okresy_MSK` → **Create Chart → Bar Chart**:
- Category = `OKRES`, Aggregation = *Sum*, Numeric field = `UCAST_OKR`,
- seřaďte sloupce sestupně podle hodnoty,
- název grafu *Volební účast v okresech (%)*, osu Y popište *%*.


---

# Část C: Velký layout

Výsledný plakát může vypadat třeba takto (rozvržení je doporučení, ne povinnost):



## Krok 10: Stránka a mapové rámce

**a)** **Insert → New Layout** → **A3 Landscape**. Layout přejmenujte na **Plakát volby 2026**.

**b)** Pravým na pravítko → **Add Guide** a přidejte vodítka pro okraje 10 mm a pro dělení stránky podle náčrtu. Všechny prvky budete zarovnávat na vodítka.

**c)** **Insert → Map Frame** → mapa **Volební účast MSK 2026** – nakreslete hlavní rámec. Pravým na rámec → **Activate**, nastavte výřez tak, aby kraj vyplnil rámec, zaokrouhlete měřítko (např. 1 : 400 000) a **Close Activation**.

**d)** Druhý rámec pro mapu **Detail Ostravsko**: vložte ho, aktivujte a přejděte na záložku `Ostravsko` (**Map → Bookmarks**). Měřítko opět zaokrouhlete.

**e)** Třetí, malý rámec pro mapu **Přehledka** s celou ČR.

## Krok 11: Indikátory rozsahu 

**a)** Vyberte rámec přehledky → pravým **Properties** → **Extent Indicators** → **Add**, zdrojem je hlavní mapový rámec. V přehledce se objeví obdélník, který ukazuje, kde leží hlavní mapa.

**b)** Stejně přidejte indikátor do **hlavního rámce** se zdrojem rámec **Detail Ostravsko** – čtenář uvidí, kterou část kraje detail zvětšuje.

**c)** Symbol indikátorů nastavte výrazný, ale tenký (např. červený obrys 1 pt, bez výplně).


## Krok 12: Legenda 

**a)** Vyberte hlavní mapový rámec → **Insert → Legend** a nakreslete legendu.

**b)** V panelu **Contents** layoutu rozbalte legendu a vypněte položky, které do ní nepatří (podkladová mapa, indikátor rozsahu…).

**c)** Názvy položek upravte tak, aby nesly jednotky: *Volební účast (%)*, *Počet voličů*, *Hranice okresů*. Legenda nemá nadpis „Legenda“.

**d)** Ve vlastnostech legendy (**Arrangement**) rozdělte položky do 2 sloupců, aby legenda nebyla zbytečně vysoká. Zkontrolujte, že všechny jevy v mapě jsou v legendě a nic navíc.

## Krok 13: Graf a tabulka 

**a)** **Insert → Chart Frame** → vyberte graf z kroku 9 a umístěte ho podle náčrtu. Zkontrolujte, že je čitelný (velikost písma popisků ≥ 7 pt).

**b)** **Insert → Table Frame** → vrstva `Okresy_MSK`. Ve vlastnostech tabulky ponechte jen pole `OKRES`, `SUM_VOLICI` a `UCAST_OKR`.

**c)** Aby tabulka měla srozumitelné hlavičky a formát čísel, upravte v hlavní mapě pole vrstvy `Okresy_MSK` (pravým na vrstvu → **Data Design → Fields**): aliasy *Okres*, *Počet voličů*, *Účast (%)*, u účasti formát čísla na 1 desetinné místo. Uložte změny polí.


## Krok 14: Texty a kompoziční prvky

**a)** **Název** (CO – KDE – KDY): *Volební účast v komunálních volbách 2026 v obcích Moravskoslezského kraje*. Pod něj menší **podtitul**, např. *Podíl voličů, kteří převzali úřední obálku*.

**b)** **Textový komentář** (**Insert → Text → Rectangle Text**): 3–4 vlastní věty o tom, co mapa ukazuje – kde je účast nejnižší, jak se liší města a venkov, který okres vybočuje.

**c)** **Měřítko** u hlavní mapy (grafické, kulaté dělení v km) a samostatné měřítko u detailu – každý rámec má jiné měřítko. **Směrovka** v češtině, otočená pro S-JTSK.

**d)** **Tiráž**: zdroje dat (*Data ArcČR © ČÚZK, ČSÚ, ARCDATA PRAHA 2024*; ČSÚ – výsledky komunálních voleb 2026, volby.cz), souřadnicový systém (S-JTSK / Krovak East North), autor. Datum vložte jako **Insert → Dynamic Text → Date Saved** – aktualizuje se samo.

**e)** Volitelně **Insert → Picture** – logo školy do rohu tiráže.

## Krok 15: Kontrola, zarovnání a export 

**a)** Vyberte prvky, které mají být v jedné linii, pravým → **Align** (Left, Top…) a **Distribute**. Okraje všech prvků mají sedět na vodítkách.

**b)** Projděte layout podle kontrolního seznamu níže. Na plakátu se nic nepřekrývá, všechny texty jsou čitelné.

**c)** **Share → Export Layout** → **PDF** (vektorový, pro tisk) a **PNG 300 DPI** → `CV10_volby_prijmeni.pdf` a `.png`. Uložte projekt.


---

## Bonus: Kde žijí nevoliči – Heat Map vs. Kernel Density

**a)** Do `Obce_MSK_volby` přidejte pole `NEVOLICI` (Long) = `!VOLICI! - !OBALKY!` (Calculate Field).

**b)** **Feature To Point** (*Inside*) → `Obce_MSK_body`. Jedna kopie bodů → symbologie **Heat Map**, **Weight field** = `NEVOLICI`.

**c)** **Kernel Density** (Spatial Analyst): Population Field = `NEVOLICI`, Cell Size 250, Search Radius 5000, Area units = Square kilometers. Porovnejte s heat mapou nástrojem **Swipe**.

> ❓ Čím se liší heat mapa od rastru z Kernel Density?
>
> <details><summary>Odpověď</summary>Heat mapa je jen způsob vykreslení – barvy se přepočítávají podle výřezu, takže stejné místo má při různém přiblížení jinou barvu. Kernel Density vytvoří rastr s pevnými hodnotami (nevoliči na km²), které nezávisí na měřítku a dají se dál analyzovat.</details>

---




