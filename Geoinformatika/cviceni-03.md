---
layout: default
title: "CV04 – Opakování"
---

# CV04 – Opakování: atributové dotazy, připojení tabulky, kartogram a kartodiagram

> **Termín odevzdání:** [dd. mm. 2026, 23:59]  
> **Odevzdání:** [e-mail vyučující / odkaz na LMS]  
> **Software:** [QGIS 3.x / ArcGIS Pro]

## Obsah
{:.no_toc}

- TOC
{:toc}

---

## Cíle cvičení

Po absolvování cvičení umíte:

- klást atributové dotazy nad vektorovou vrstvou a číst základní statistiky atributu,
- připravit tabulku v Excelu ve struktuře vhodné pro připojení k prostorové vrstvě (join),
- připojit externí tabulku k polygonové vrstvě přes společný klíč,
- správně zvolit a vytvořit **kartogram** (relativní hodnoty) a **kartodiagram** (absolutní hodnoty),
- sestavit mapovou kompozici a exportovat ji do rastru.

## Data ke stažení

| Soubor | Popis |
|---|---|
| [okresy_CR.zip](data/okresy_CR.zip) | polygonová vrstva okresů ČR (atributy `NAZEV`, `MIRA_NEZAM`, `[POCET_OBYV]`) |
| [kraje_CR.zip](data/kraje_CR.zip) | polygonová vrstva krajů ČR |
| [kraje_kody.xlsx](data/kraje_kody.xlsx) | tabulka s názvy a kódy krajů (`NAZEV`, `KOD_KRAJE`) |
| [Statistické přehledy kriminality za rok 2025](https://policie.gov.cz/clanek/statisticke-prehledy-kriminality-za-rok-2025.aspx) | Policie ČR – soubory xlsx pod článkem |

---

## Část A – Opakování (test 1)

### A1. Atributové dotazy nad vrstvou okresů

Načtěte vrstvu okresů a pomocí atributové tabulky, statistik pole a výběru podle atributu zodpovězte:

1. Jaká je **průměrná míra nezaměstnanosti** (`MIRA_NEZAM`) v okresech ČR?
2. **Kolik okresů** je v ČR?
3. Který okres má **nejnižší** míru nezaměstnanosti? (Seřaďte sloupec nebo použijte statistiku pole.)
4. Kolik okresů má název **začínající písmenem P**? Zapište použitý dotaz, např. `"NAZEV" LIKE 'P%'`.
5. Kolik okresů má **více než 100 000 obyvatel**? Zapište použitý dotaz.

Odpovědi včetně znění dotazů zapište do souboru `CV04_A_prijmeni.txt`.

### A2. Kartogram míry nezaměstnanosti

- Vytvořte kartogram (choropleth) z atributu `MIRA_NEZAM`.
- Zvolte vhodnou klasifikaci (např. kvantily nebo přirozené zlomy, 5 tříd) a **sekvenční** barevnou škálu; volbu stručně zdůvodněte v odevzdání.
- Mapová kompozice musí obsahovat: **název mapy, legendu, grafické měřítko, směrovku, tiráž** (autor, datum, zdroj dat, souřadnicový systém).
- Exportujte do PNG, min. 300 DPI, název `CV04_A_kartogram_prijmeni.png`.

---

## Část B – Kriminalita v krajích ČR

### B1. Získání dat

1. Ze stránky Policie ČR stáhněte přehled kriminality za rok 2025 členěný podle krajů (krajských ředitelství).
2. Ve sloupci takticko-statistické klasifikace (**TSK**) vyberte **jeden ukazatel** (druh trestného činu).
   Podmínka: ukazatel musí mít nenulové hodnoty ve všech nebo téměř všech krajích. Vzácné trestné činy s jednotkami případů jsou nevhodné – mapa by neměla vypovídací hodnotu.
3. Policie ČR člení data podle krajských ředitelství, která odpovídají krajům. Zkontrolujte shodu názvů s tabulkou `kraje_kody.xlsx` (např. „Hl. m. Praha" vs. „Praha").

### B2. Příprava tabulky v Excelu

Vytvořte sešit `CV04_B_prijmeni.xlsx` s jedním listem a přesně těmito sloupci:

| Sloupec | Typ | Popis |
|---|---|---|
| `NAZEV` | text | název kraje (shodně s vrstvou krajů) |
| `KOD_KRAJE` | text / číslo | kód kraje z `kraje_kody.xlsx` – **klíč pro join** |
| `TSK` | celé číslo | počet vybraného trestného činu v kraji za rok 2025 |
| `PODIL_TSK` | desetinné číslo | podíl kraje na celkovém počtu tohoto trestného činu v ČR (%) |

Výpočet: `PODIL_TSK = TSK / SUMA(TSK všech krajů) * 100`. Součet sloupce `PODIL_TSK` musí být **100 %**.

Pravidla pro bezproblémový join:

- názvy sloupců bez diakritiky a mezer,
- žádné sloučené buňky, žádné prázdné řádky,
- čísla uložená jako čísla (ne jako text),
- jeden řádek na kraj – celkem **14 řádků**.

### B3. Připojení tabulky k vrstvě krajů

1. Připojte tabulku k vrstvě krajů přes pole `KOD_KRAJE` (*Join / Připojit atributy podle hodnoty pole*).
2. Ověřte, že se připojilo všech 14 záznamů a žádný nemá prázdné hodnoty. Pokud ano, zkontrolujte typ klíčového pole (text × číslo) a překlepy v názvech.
3. Připojení uložte exportem do nové vrstvy `kraje_kriminalita` (shapefile nebo geodatabáze).

### B4. Mapové výstupy

Vytvořte **dvě samostatné mapy** a exportujte je do PNG (min. 300 DPI):

**1. Kartogram** z atributu `PODIL_TSK`  
Relativní údaj (podíl v %), sekvenční barevná škála, 4–5 tříd, metoda klasifikace uvedená v legendě.  
Soubor: `CV04_B_kartogram_prijmeni.png`

**2. Kartodiagram** z atributu `TSK`  
Absolutní počet zobrazený proporcionálními symboly (kruhy) nad podkladem krajů; podkladová vrstva jednobarevná, legenda s ukázkovými velikostmi symbolů.  
Soubor: `CV04_B_kartodiagram_prijmeni.png`

Obě mapy obsahují všechny kompoziční prvky: název s uvedeným trestným činem a rokem, legendu, měřítko, směrovku a tiráž se zdrojem *„Policie ČR, 2025"*.

### B5. Kontrolní otázky

Zapište do `CV04_B_prijmeni.txt`:

1. Proč se pro `PODIL_TSK` používá kartogram a pro `TSK` kartodiagram, a ne naopak?
2. Který kraj má nejvyšší a který nejnižší podíl? Odpovídá pořadí velikosti kraje / počtu obyvatel? Krátce komentujte.
3. Jaké problémy jste řešili při připojení tabulky?

---

## Odevzdání

Jeden ZIP `CV04_prijmeni.zip` obsahující:

- `CV04_A_prijmeni.txt`
- `CV04_A_kartogram_prijmeni.png`
- `CV04_B_prijmeni.xlsx`
- `CV04_B_kartogram_prijmeni.png`
- `CV04_B_kartodiagram_prijmeni.png`
- `CV04_B_prijmeni.txt`

## Hodnocení

| Položka | Váha |
|---|---|
| A1 – správné odpovědi a dotazy | 20 % |
| A2 – kartogram | 20 % |
| B2–B3 – tabulka a správný join | 20 % |
| B4 – kartogram | 15 % |
| B4 – kartodiagram | 15 % |
| B5 – kontrolní otázky | 10 % |

Za chybějící kompoziční prvek nebo nesprávný typ mapy (např. kartogram z absolutních hodnot) se strhávají body.

---

*Poslední aktualizace: září 2026*
