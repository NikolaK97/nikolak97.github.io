# Cvičení 5 – Import dat a základní dotazy v MS Access

## Cíl cvičení

Po absolvování tohoto cvičení bude student schopen:

- importovat CSV data do MS Access
- pochopit vazbu mezi tabulkami a daty
- vytvářet základní dotazy
- spojovat tabulky pomocí vztahů
- filtrovat data
- zobrazovat informace z více tabulek

Toto cvičení navazuje na databázi vytvořenou ve Cvičení 4.

---

# Datové soubory

Použijte následující CSV soubory:

- fakulta.csv
- student.csv
- zamestnanec.csv
- predmet.csv
- zapis.csv

Tyto soubory obsahují testovací data pro databázi univerzity.
Odkaz pro stažení (https://drive.google.com/drive/folders/1q-33JDxu-6pLPTNm4Jz0Yijyn2St6p32?usp=share_link)
---

# Struktura dat

## FAKULTA

| faculty_id | nazev |
|---|---|
| 1 | Informatika |
| 2 | Ekonomie |

---

## STUDENT

| student_id | jmeno | faculty_id |
|---|---|---|
| 1 | Jan Novak | 1 |
| 2 | Eva Svobodova | 1 |
| 3 | Petr Maly | 2 |
| 4 | Anna Dvorakova | 1 |

---

## ZAMESTNANEC

| employee_id | jmeno | faculty_id |
|---|---|---|
| 1 | Dr. Kral | 1 |
| 2 | Dr. Horak | 1 |
| 3 | Dr. Benes | 2 |

---

## PREDMET

| subject_id | nazev | employee_id |
|---|---|---|
| 1 | Databaze | 1 |
| 2 | Programovani | 2 |
| 3 | Ekonomie I | 3 |

---

## ZAPIS

| student_id | subject_id | znamka |
|---|---|---|
| 1 | 1 | 1 |
| 1 | 2 | 2 |
| 2 | 1 | 1 |
| 3 | 3 | 3 |
| 4 | 2 | 2 |

---

# Krok 1 – Import CSV dat

Pro každou tabulku proveďte import dat.

Postup:

1. Otevřete databázi `univerzita.accdb`.
2. Klikněte na **External Data**.
3. Zvolte **New Data Source**.
4. Vyberte **From File → Text File**.
5. Vyberte soubor například `fakulta.csv`.
6. Zvolte **Append a copy of the records to the table**.
7. Vyberte odpovídající tabulku (např. FAKULTA).
8. Dokončete import.

Stejný postup opakujte pro všechny CSV soubory.

---

# Krok 2 – Kontrola dat

Po importu otevřete tabulky a ověřte:

FAKULTA obsahuje 2 záznamy.  
STUDENT obsahuje 4 záznamy.  
ZAMESTNANEC obsahuje 3 záznamy.  
PREDMET obsahuje 3 záznamy.  
ZAPIS obsahuje 5 záznamů.

---

# Krok 3 – Kontrola vztahů

Otevřete:

Database Tools → Relationships

Zkontrolujte, že existují vztahy:

FAKULTA.faculty_id → STUDENT.faculty_id  
FAKULTA.faculty_id → ZAMESTNANEC.faculty_id  
ZAMESTNANEC.employee_id → PREDMET.employee_id  
STUDENT.student_id → ZAPIS.student_id  
PREDMET.subject_id → ZAPIS.subject_id  

Referenční integrita musí být zapnutá.

---

# Krok 4 – První dotaz

Cíl: zobrazit všechny studenty.

Postup:

1. Create → Query Design.
2. Přidejte tabulku STUDENT.
3. Přidejte pole:

- student_id
- jmeno
- faculty_id

4. Klikněte Run.

Výsledek:
seznam všech studentů.

---

# Krok 5 – Spojení dvou tabulek

Cíl:
zobrazit studenty a jejich fakultu.

Postup:

1. Create → Query Design.
2. Přidejte tabulky:

- STUDENT
- FAKULTA

3. Přidejte pole:

STUDENT.jmeno  
FAKULTA.nazev  

4. Spusťte dotaz.

Výsledek:

| student | fakulta |
|---|---|
| Jan Novak | Informatika |
| Eva Svobodova | Informatika |
| Petr Maly | Ekonomie |

---

# Krok 6 – Spojení více tabulek

Cíl:
zobrazit studenta, předmět a známku.

Postup:

1. Create → Query Design.
2. Přidejte tabulky:

- STUDENT
- ZAPIS
- PREDMET

3. Přidejte pole:

STUDENT.jmeno  
PREDMET.nazev  
ZAPIS.znamka  

4. Spusťte dotaz.

Výsledek:

| student | predmet | znamka |
|---|---|---|
| Jan Novak | Databaze | 1 |
| Jan Novak | Programovani | 2 |
| Eva Svobodova | Databaze | 1 |

---

# Krok 7 – Dotaz s filtrem

Cíl:
zobrazit pouze studenty z fakulty Informatika.

Postup:

1. Použijte dotaz STUDENT + FAKULTA.
2. Do pole FAKULTA.nazev přidejte filtr:
"Informatika"

3. Spusťte dotaz.

Výsledek:
zobrazí pouze studenty této fakulty.

---

# Kontrolní seznam

Student by měl být schopen:

- importovat CSV data
- zkontrolovat referenční integritu
- vytvořit jednoduchý dotaz
- spojit dvě tabulky
- spojit tři tabulky
- použít filtr

## 1. SELECT

### Úkol
Vypiš všechny studenty.

```sql
SELECT * FROM student;
```

### Úkol
Vypiš jméno a příjmení.

```sql
SELECT jmeno, prijmeni FROM student;
```

---

## 2. WHERE

### Úkol
Studenti z Ostravy

```sql
SELECT * FROM student
WHERE mesto = "Ostrava";
```

---

## 3. ORDER BY

```sql
SELECT * FROM student
ORDER BY prijmeni;
```

---

## 4. Agregace

```sql
SELECT COUNT(*) FROM student;
```

---

## 5. JOIN

```sql
SELECT s.jmeno, f.nazev
FROM student AS s
INNER JOIN fakulty AS f
ON s.id_fakul = f.id;
```

---

## 6. Více tabulek

```sql
SELECT s.jmeno, p.nazev
FROM (student AS s
INNER JOIN zapis AS z ON s.id = z.id_stu)
INNER JOIN predmet AS p ON z.id_pred = p.id;
```

---

## 7. Pokročilé

```sql
SELECT TOP 1 *
FROM student
ORDER BY studijni_prumer;
```

---

# Úkol k odevzdání

Odevzdejte:

1. Screenshot importu CSV.
2. Screenshot dotazu Student – Fakulta.
3. Screenshot dotazu Student – Předmět – Známka.
4. Screenshot dotazu s filtrem.

---

# Co bude následovat

V příštím cvičení se budeme věnovat pokročilým dotazům:

- agregační dotazy
- GROUP BY
- COUNT
- formuláře

## řešení
--vyber vše z tabulky fakulty

SELECT *
FROM fakulty;

--vyber název a rozzalozeni z tabulky fakulty s aliasy 

SELECT nazev AS 'Název', rok_zalozeni AS 'Rok založení'
FROM fakulty;

--vypis vše z tabulky student 
SELECT *
FROM student;

--studenty z jihomoravského kraje
SELECT *
FROM student
WHERE kraj = 'Jihomoravský';
--studenti z města Kladno
SELECT *
FROM student
WHERE mesto = 'Kladno';

--studeti z kladna, jen ženy
SELECT *
FROM student
WHERE mesto = 'Kladno' AND pohlavi = 'Ž';
--studeti z kladna, jen ženy a kombinované stadium 

SELECT *
FROM student
WHERE mesto = 'Kladno' AND pohlavi = 'Ž' AND forma_studia = 'kombinovaná';

--všechny studenty muže, kteří jsou aktivní a nebo žijí v olomouci
SELECT *
FROM student
WHERE (mesto = 'Olomouc' OR aktivni = 'ano') AND pohlavi = 'M';

--všichni muži ve vyšším než 4 ročníku 
SELECT *
FROM student
WHERE pohlavi = 'M' AND rocnik > 4;
--všichni muži ve 4 ročníku a výše 

SELECT *
FROM student
WHERE pohlavi = 'M' AND rocnik >= 4;
--všechny studenty, kteří nejsou ženy

SELECT *
FROM student
WHERE pohlavi <> 'Ž';

--všechny studenty, kteří nestují 1 ročník, zároveň dostávají stipendium větší než 2000
SELECT *
FROM student
WHERE rocnik <> 1 AND stipendium_kc > 2000;

--kolik je žen a mužů mezi studenty

SELECT pohlavi, COUNT(*) AS 'Počet'
FROM student
group by pohlavi;

--počet student za jednolitvé kraje 

SELECT kraj, COUNT(*) AS 'Počet'
FROM student
group by kraj;

--počet student za jednolitvé kraje s vice jak 60 studenty

SELECT kraj, COUNT(*) AS 'Počet'
FROM student
group by kraj
HAVING COUNT(*) >60;
--počet studentů za jednolitvé kraje podle pohlaví

SELECT kraj, pohlavi, COUNT(*) AS 'Počet'
FROM student
group by kraj, pohlavi
;

--vyberte pouze studenty, jejichž jméno začíná na M
SELECT *
from student
where jmeno LIKE 'M*';

--VYBERTE všechny studenty, kteří jsou z Olomouckého a Plzenského kraje, zároveň jsou prezenční a jejich jméno obsahuje A nebo I 

SELECT *
from student
where kraj IN ('Olomoucký', 'Plzeňský') AND forma_studia = 'prezenční' AND (jmeno LIKE '*a*' OR jmeno LIKE '*i*') ;

--vyberte studenty mezi 2-4 ročníkem a jsou to ženy
SELECT *
from student
where (rocnik BETWEEN 2 AND 4) AND pohlavi = 'Ž';

--počet předmětů, které mají vice jak 8 kreditů a zároven jsou povinne
SELECT *
from predmet
where kredity > 8 AND typ = 'povinný';

--jaký je maximální, minimální a průměrný počet kreditů u předmětů, rozděl do kategorií podle typu

SELECT typ, MIN(kredity) AS 'MIN', MAX(kredity) AS 'MAX', AVG(kredity) AS 'Průměr'
from predmet
GROUP BY typ;



