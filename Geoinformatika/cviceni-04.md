---
title: "Cvičení 04 – Kriminalita v krajích ČR"
---

# Cvičení 04 – Kriminalita v krajích ČR

Kartogram a kartodiagram z dat Policie ČR o kriminalitě podle takticko-statistické klasifikace (TSK).

## Zadání

1. Ze stránek Policie ČR stáhněte nejnovější statistiky kriminality pro kraje ČR ([Statistické přehledy kriminality](https://policie.gov.cz)).
2. Z tabulky vyberte jeden ukazatel ze sloupce takticko-statistické klasifikace (TSK). Sloupec musí obsahovat větší počet hodnot.
3. Vytvořte vlastní tabulku v Excelu se sloupci:
   - `NAZEV` – název kraje,
   - `KOD_KRAJE` – kód kraje (shodný s kódem v polygonové vrstvě krajů ČR),
   - `TSK` – počet vybraného trestného činu v kraji,
   - `PODIL_TSK` – podíl kraje na celkovém počtu vybraného TSK.
4. Tabulku připojte k prostorové vrstvě krajů.
5. Vytvořte dva mapové výstupy:
   - **kartogram** ze sloupce `PODIL_TSK`,
   - **kartodiagram** ze sloupce `TSK`.
6. Oba výstupy vyexportujte.

## Vstupní data

| Údaj | Hodnota |
|---|---|
| Zdroj | Policie ČR – Statistické přehledy kriminality, sestava 01a |
| Soubor | `2025_12_Prosinec_sest_01a.xlsx` |
| Období | 1. 1. – 31. 12. 2025 |
| Vybraný ukazatel | **TSK 511 – podvod (§ 209)** |
| Ukazatel v tabulce | počet registrovaných skutků |
| Prostorová vrstva | polygony krajů ČR (14 krajů) |

**Proč TSK 511:** podvod má ze všech jednotlivých TSK (bez souhrnných řádků) nejvyšší počet registrovaných skutků. V roce 2025 jich bylo 18 722. Ve všech krajích má nenulové a dobře rozlišitelné hodnoty.

Policejní sestava má jeden list za každé krajské ředitelství policie (KŘP). Od roku 2010 odpovídají KŘP krajům 1 : 1, takže data lze převzít přímo.

## Postup

### 1. Příprava tabulky

Z každého krajského listu sestavy jsem převzala řádek TSK 511 se všemi sloupci (registrováno, objasněno, pachatelé podle kategorií, škoda). Podíl kraje jsem dopočítala vzorcem:

```
PODIL_TSK = TSK_kraje / SUM(TSK všech 14 krajů) × 100
```

Součet za kraje se shoduje s celkem za ČR (18 722), podíly tedy dávají dohromady 100 %.

Názvy sloupců mají nejvýše 10 znaků a neobsahují diakritiku. Delší názvy by se při uložení do shapefile ořízly.

### 2. Kódy krajů

Join funguje jen tehdy, když `KOD_KRAJE` v tabulce odpovídá kódu ve vrstvě krajů. Kódy kraje se ale v různých zdrojích liší. Tabulka proto obsahuje číselník se čtyřmi systémy a kód se přepíná výběrem.

| Kraj | CZ-NUTS 3 | ČSÚ | RÚIAN | ISO 3166-2 |
|---|---|---:|---:|---|
| Hlavní město Praha | CZ010 | 3018 | 19 | CZ-10 |
| Moravskoslezský kraj | CZ080 | 3140 | 132 | CZ-80 |

*(ukázka, úplný číselník je v datovém souboru)*

Kromě hodnot musí sedět i **datový typ** pole. Kód uložený jako text (`"3140"`) se nespojí s kódem uloženým jako číslo (`3140`).

### 3. Výsledná tabulka

| NAZEV | KOD_KRAJE | TSK | PODIL_TSK (%) |
|---|---|---:|---:|
| Hlavní město Praha | CZ010 | 3 135 | 16,75 |
| Středočeský kraj | CZ020 | 2 639 | 14,10 |
| Jihočeský kraj | CZ031 | 1 192 | 6,37 |
| Plzeňský kraj | CZ032 | 1 092 | 5,83 |
| Karlovarský kraj | CZ041 | 557 | 2,98 |
| Ústecký kraj | CZ042 | 1 196 | 6,39 |
| Liberecký kraj | CZ051 | 819 | 4,37 |
| Královéhradecký kraj | CZ052 | 1 034 | 5,52 |
| Pardubický kraj | CZ053 | 753 | 4,02 |
| Kraj Vysočina | CZ063 | 839 | 4,48 |
| Jihomoravský kraj | CZ064 | 1 828 | 9,76 |
| Olomoucký kraj | CZ071 | 963 | 5,14 |
| Zlínský kraj | CZ072 | 807 | 4,31 |
| Moravskoslezský kraj | CZ080 | 1 868 | 9,98 |
| **Celkem** | | **18 722** | **100,00** |

Kompletní tabulka se všemi sloupci: <a href="/download/kriminalita_kraje_TSK_2025.xlsx" download>stáhnout kriminalita_kraje_TSK_2025.xlsx</a>

### 4. Mapové výstupy

> Kartogram i kartodiagram.

Obě mapy obsahují název, legendu, měřítko, zdroj dat, autora a datum.


## Zdroje

- Policie ČR: Statistické přehledy kriminality, soubor `2025_12_Prosinec_sest_01a.xlsx`.
- Polygonová vrstva krajů ČR: podklady ke cvičení.

---

