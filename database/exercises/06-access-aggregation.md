# Cvičení 6 – Agregační dotazy v MS Access

## Cíl cvičení

Po absolvování tohoto cvičení bude student schopen:

- používat agregační funkce v databázových dotazech
- vytvářet dotazy typu GROUP BY
- počítat počet záznamů pomocí COUNT
- počítat průměr pomocí AVG
- analyzovat data pomocí agregací
- pracovat s větším datasetem

Toto cvičení navazuje na Cvičení 5 a pracuje s rozšířeným datasetem.

---

# Dataset

Použijte následující soubory:

- fakulta_big.csv
- student_big.csv
- zamestnanec_big.csv
- predmet_big.csv
- zapis_big.csv

Dataset obsahuje větší množství dat, aby bylo možné provádět statistické analýzy.

---

# Struktura datasetu

Po importu bude databáze obsahovat přibližně:

| Tabulka | Počet záznamů |
|---|---|
| FAKULTA | 5 |
| STUDENT | 30 |
| ZAMESTNANEC | 14 |
| PREDMET | 18 |
| ZAPIS | 160 |

To umožňuje analyzovat:

- kolik studentů studuje na jednotlivých fakultách
- kolik předmětů vyučují jednotliví zaměstnanci
- kolik studentů je zapsáno na jednotlivé předměty
- jaké jsou průměrné známky

---

# Import datasetu

Import provádějte ve správném pořadí.

1. FAKULTA  
2. STUDENT  
3. ZAMESTNANEC  
4. PREDMET  
5. ZAPIS  

Postup:

1. Otevřete databázi v MS Access.
2. Klikněte **External Data**.
3. Zvolte **New Data Source → From File → Text File**.
4. Vyberte CSV soubor.
5. Zvolte **Append a copy of the records to the table**.
6. Vyberte odpovídající tabulku.
7. Dokončete import.

---

# Kontrola vztahů

Otevřete:

Database Tools → Relationships

Zkontrolujte vztahy:

FAKULTA.faculty_id → STUDENT.faculty_id  
FAKULTA.faculty_id → ZAMESTNANEC.faculty_id  
ZAMESTNANEC.employee_id → PREDMET.employee_id  
STUDENT.student_id → ZAPIS.student_id  
PREDMET.subject_id → ZAPIS.subject_id  

Referenční integrita musí být zapnutá.

---

# Teoretická část

## Agregační funkce

Agregační funkce pracují s více řádky dat.

Nejčastější:

| Funkce | Popis |
|---|---|
| COUNT | počet záznamů |
| AVG | průměr |
| SUM | součet |
| MIN | nejmenší hodnota |
| MAX | největší hodnota |

---

## GROUP BY

GROUP BY seskupuje data podle určitého sloupce.

Například:

- počet studentů na fakultě
- počet zápisů na předmět
- průměrná známka na předmětu

---

# Praktická část

## Dotaz 1 – Počet studentů na fakultě

Cíl:

zjistit, kolik studentů studuje na jednotlivých fakultách.

Postup:

1. Create → Query Design
2. Přidejte tabulky

STUDENT  
FAKULTA  

3. Přidejte pole

FAKULTA.nazev  
STUDENT.student_id  

4. Klikněte **Totals (Σ)**

5. Nastavte:

FAKULTA.nazev → Group By  
STUDENT.student_id → Count  

Spusťte dotaz.

Výsledek:

| fakulta | pocet_studentu |
|---|---|

---

# Dotaz 2 – Počet studentů na předmětu

Cíl:

zjistit kolik studentů je zapsáno na jednotlivé předměty.

Postup:

1. Query Design
2. Přidejte tabulky

PREDMET  
ZAPIS  

3. Přidejte pole

PREDMET.nazev  
ZAPIS.student_id  

4. Zapněte Totals

5. Nastavte:

PREDMET.nazev → Group By  
ZAPIS.student_id → Count  

---

# Dotaz 3 – Průměrná známka na předmětu

Cíl:

zjistit průměrnou známku.

Postup:

1. Query Design
2. Přidejte tabulky

PREDMET  
ZAPIS  

3. Přidejte pole

PREDMET.nazev  
ZAPIS.znamka  

4. Zapněte Totals

5. Nastavte

PREDMET.nazev → Group By  
ZAPIS.znamka → Avg  

---

# Dotaz 4 – Počet předmětů vyučovaných zaměstnanci

Cíl:

zjistit kolik předmětů vyučuje každý zaměstnanec.

Postup:

1. Query Design
2. Přidejte tabulky

ZAMESTNANEC  
PREDMET  

3. Přidejte pole

ZAMESTNANEC.jmeno  
PREDMET.subject_id  

4. Zapněte Totals

5. Nastavte

ZAMESTNANEC.jmeno → Group By  
PREDMET.subject_id → Count  

---

# Dotaz 5 – Nehodnocené zápisy

Cíl:

zjistit které zápisy ještě nemají známku.

Postup:

1. Query Design
2. Přidejte tabulky

STUDENT  
ZAPIS  
PREDMET  

3. Přidejte pole

STUDENT.jmeno  
PREDMET.nazev  
ZAPIS.znamka  

4. Do pole ZAPIS.znamka přidejte podmínku
is null

---

# Kontrolní seznam

Student by měl být schopen:

- vytvořit agregační dotaz
- použít GROUP BY
- použít COUNT
- použít AVG
- filtrovat NULL hodnoty

---

# Úkol k odevzdání

Odevzdejte:

1. Screenshot dotazu „počet studentů na fakultě“
2. Screenshot dotazu „počet studentů na předmětu“
3. Screenshot dotazu „průměrná známka“
4. Screenshot dotazu „nehodnocené zápisy“

---

# Co bude následovat

V příštím cvičení se budeme věnovat:

- formulářům
- praktickému rozhraní pro databázi
- přípravě na první test
