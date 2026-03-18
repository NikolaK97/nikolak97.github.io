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
