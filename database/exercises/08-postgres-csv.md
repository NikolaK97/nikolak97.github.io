---

# Import univerzitních CSV do PostgreSQL přes pgAdmin

---
## STÁHNI SOUBORY 
[Stáhnout soubor](https://drive.google.com/drive/folders/1Rs33_-E1h-xiray8HaVMxhVW8ycx8cl0?usp=share_link)

## 1. Jaké soubory máš k dispozici

Import budeš dělat z těchto souborů:

- `univerzita.csv`
- `fakulty.csv`
- `student.csv`
- `zamestnanci.csv`
- `predmet.csv`
- `zapis.csv`
- `hodnoceni.csv`


## 3. Jaká je logika vazeb

Z README plyne původní logika vazeb:

- `student.id_fakul` odkazuje na fakultu
- `zamestnanci.id_fakul` odkazuje na fakultu
- `predmet.id_zam` / v aktuálním souboru `id_vyucujici` odkazuje na zaměstnance
- `zapis.id_stu` odkazuje na studenta
- `zapis.id_pred` odkazuje na předmět
- `hodnoceni.id_zap` odkazuje na zápis 

Proto v SQL níže používám tuto praktickou interpretaci:

- `student.id_fakul -> fakulty.id`
- `zamestnanci.id_fakul -> fakulty.id`
- `predmet.id_vyucujici -> zamestnanci.id`
- `zapis.id_stu -> student.id`
- `zapis.id_pred -> predmet.id`
- `hodnoceni.id_zap -> zapis.id`

## 4. Doporučené pořadí importu

Kvůli cizím klíčům importuj tabulky v tomto pořadí:

1. `univerzita`
2. `fakulty`
3. `student`
4. `zamestnanci`
5. `predmet`
6. `zapis`
7. `hodnoceni`

> !!! Pokud bys importoval například `student` dřív než `fakulty`, PostgreSQL může hlásit chybu cizího klíče !!!

## 5. Vytvoření databáze

Nejprve si vytvoř databázi, například:

```sql
CREATE DATABASE univerzita_db;
```

Pak se v pgAdmin připoj do této databáze a otevři **Tools -> Query Tool**.

## 6. CREATE TABLE skript pro aktuální soubory

Níže je kompletní skript připravený pro import z nahraných CSV.

```sql
CREATE TABLE univerzita (
    id INTEGER PRIMARY KEY,
    jmeno TEXT NOT NULL
);


CREATE TABLE student (
    id INTEGER PRIMARY KEY,
    jmeno TEXT NOT NULL,
    prijmeni TEXT NOT NULL,
    email TEXT,
    datum_narozeni DATE,
    mesto TEXT,
    kraj TEXT,
    rocnik INTEGER,
    forma_studia TEXT,
    stipendium_kc NUMERIC(10,2),
    studijni_prumer NUMERIC(3,2),
    aktivni BOOLEAN,
    id_fakul INTEGER NOT NULL,
    CONSTRAINT fk_student_fakulty
        FOREIGN KEY (id_fakul) REFERENCES fakulty(id)
);

CREATE TABLE zamestnanci (
    id INTEGER PRIMARY KEY,
    jmeno TEXT NOT NULL,
    prijmeni TEXT NOT NULL,
    email TEXT,
    pozice TEXT,
    plat_kc NUMERIC(12,2),
    datum_nastupu DATE,
    uvazek NUMERIC(3,1),
    id_fakul INTEGER NOT NULL,
    CONSTRAINT fk_zamestnanci_fakulty
        FOREIGN KEY (id_fakul) REFERENCES fakulty(id)
);

CREATE TABLE predmet (
    id INTEGER PRIMARY KEY,
    nazev TEXT NOT NULL,
    kredity INTEGER,
    typ TEXT,
    semester TEXT,
    id_vyucujici INTEGER NOT NULL,
    CONSTRAINT fk_predmet_zamestnanci
        FOREIGN KEY (id_vyucujici) REFERENCES zamestnanci(id)
);

CREATE TABLE fakulty_adresy (
    id INTEGER PRIMARY KEY,
    nazev TEXT NOT NULL,
    mesto TEXT NOT NULL,
    dekan TEXT,
    rok_zalozeni INTEGER,

    adresa TEXT NOT NULL,

    x DOUBLE PRECISION NOT NULL,
    y DOUBLE PRECISION NOT NULL
);

CREATE TABLE zapis (
    id INTEGER PRIMARY KEY,
    id_stu INTEGER NOT NULL,
    id_pred INTEGER NOT NULL,
    CONSTRAINT fk_zapis_student
        FOREIGN KEY (id_stu) REFERENCES student(id),
    CONSTRAINT fk_zapis_predmet
        FOREIGN KEY (id_pred) REFERENCES predmet(id)
);

CREATE TABLE hodnoceni (
    id INTEGER PRIMARY KEY,
    id_zap INTEGER NOT NULL,
    znamka TEXT NOT NULL,
    CONSTRAINT fk_hodnoceni_zapis
        FOREIGN KEY (id_zap) REFERENCES zapis(id)
);
```

## 7. Proč je `znamka` jako `TEXT`

V README je `hodnoceni(id, id_zap, znamka)`, ale v aktuálním souboru `hodnoceni.csv` jsou známky ve formátu písmen, například `C` a `D`, ne jako čísla. Proto je bezpečnější použít `TEXT` místo `INTEGER`. fileciteturn3file0

## 8. Jak tabulky vytvořit v pgAdmin

### Krok 1
Otevři pgAdmin.

### Krok 2
V levém panelu rozklikni svůj server.

### Krok 3
Najdi databázi, do které chceš data importovat.

### Krok 4
Klikni na databázi pravým tlačítkem nebo ji vyber a otevři **Query Tool**.

### Krok 5
Vlož celý skript z kapitoly 6.

### Krok 6
Klikni na **Execute**.

Po úspěšném spuštění se v databázi objeví všech 7 tabulek.

## 9. Jak importovat CSV přes pgAdmin

Pro každou tabulku udělej tento postup:

### Krok 1
V levém stromu najdi tabulku, například `univerzita`.

### Krok 2
Klikni pravým tlačítkem na tabulku.

### Krok 3
Vyber **Import/Export Data**.

### Krok 4
Nastav:
- **Import/Export** = `Import`
- **Filename** = vyber příslušný CSV soubor
- **Format** = `csv`
- **Header** = `Yes`
- **Delimiter** = `,`

### Krok 5
V záložce s dalšími volbami zkontroluj:
- **Encoding** = `UTF8`
- **Quote** = `"`
- **Escape** = `"`

### Krok 6
Spusť import.

Stejný postup opakuj pro všechny tabulky podle doporučeného pořadí.

## 10. Přesné pořadí importu po jednotlivých souborech

### 10.1 Import `univerzita.csv`
Cílová tabulka: `univerzita`

### 10.2 Import `fakulty.csv`
Cílová tabulka: `fakulty`

### 10.3 Import `student.csv`
Cílová tabulka: `student`

### 10.4 Import `zamestnanci.csv`
Cílová tabulka: `zamestnanci`

### 10.5 Import `predmet.csv`
Cílová tabulka: `predmet`

### 10.6 Import `zapis.csv`
Cílová tabulka: `zapis`

### 10.7 Import `hodnoceni.csv`
Cílová tabulka: `hodnoceni`

## 11. Doporučení k souboru `zamestnanci.csv`

V aktuálně nahraném `zamestnanci.csv` jsou vidět známky rozbité diakritiky. To obvykle znamená problém s kódováním souboru. Prakticky máš tři možnosti:

1. zkus při importu nastavit správné kódování
2. soubor předem otevři a ulož jako UTF-8
3. pokud bude import dělat nepořádek ve jménech, oprav nejdřív zdrojový CSV soubor a až pak importuj

> Pokud pgAdmin po importu ukáže špatné znaky ve jménech, problém není v PostgreSQL tabulce, ale ve zdrojovém CSV.

## 12. Kontrolní SQL po importu

Po importu si vždy ověř počty záznamů:

```sql
SELECT COUNT(*) AS pocet FROM univerzita;
SELECT COUNT(*) AS pocet FROM fakulty;
SELECT COUNT(*) AS pocet FROM student;
SELECT COUNT(*) AS pocet FROM zamestnanci;
SELECT COUNT(*) AS pocet FROM predmet;
SELECT COUNT(*) AS pocet FROM zapis;
SELECT COUNT(*) AS pocet FROM hodnoceni;
```

Očekávané počty podle README jsou: 5, 20, 650, 120, 218, 3933 a 3630. fileciteturn3file0

## 13. Ověření relací po importu

### 13.1 Student a jeho fakulta

```sql
SELECT
    s.id,
    s.jmeno,
    s.prijmeni,
    f.nazev AS fakulta
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
LIMIT 20;
```

### 13.2 Zaměstnanec a jeho fakulta

```sql
SELECT
    z.id,
    z.jmeno,
    z.prijmeni,
    f.nazev AS fakulta
FROM zamestnanci z
JOIN fakulty f ON z.id_fakul = f.id
LIMIT 20;
```

### 13.3 Předmět a vyučující

```sql
SELECT
    p.id,
    p.nazev,
    z.jmeno,
    z.prijmeni
FROM predmet p
JOIN zamestnanci z ON p.id_vyucujici = z.id
LIMIT 20;
```

### 13.4 Zápisy studentů do předmětů

```sql
SELECT
    s.jmeno,
    s.prijmeni,
    p.nazev AS predmet
FROM zapis zp
JOIN student s ON zp.id_stu = s.id
JOIN predmet p ON zp.id_pred = p.id
LIMIT 20;
```

### 13.5 Známky studentů

```sql
SELECT
    s.jmeno,
    s.prijmeni,
    p.nazev AS predmet,
    h.znamka
FROM hodnoceni h
JOIN zapis zp ON h.id_zap = zp.id
JOIN student s ON zp.id_stu = s.id
JOIN predmet p ON zp.id_pred = p.id
LIMIT 20;


-- Úloha 2: Vypište počet studentů na každé fakultě.
SELECT id_fakul, COUNT(*) AS pocet_studentu
FROM studenti
GROUP BY id_fakul
ORDER BY pocet_studentu DESC;
 
-- Úloha 3: Najděte studenty s průměrem lepším než 1.5 a stipendiem > 5000 Kč.
SELECT jmeno, prijmeni, studijni_prumer, stipendium_kc
FROM studenti
WHERE studijni_prumer < 1.5
  AND stipendium_kc > 5000
ORDER BY studijni_prumer;
 
-- Úloha 4: Vypište název fakulty a jméno dekana pro všechny fakulty v Brně.
SELECT nazev, dekan, mesto
FROM fakulty
WHERE mesto = 'Brno'
ORDER BY nazev;
 
-- Úloha 5: Průměrný plat podle pozice zaměstnance.
SELECT pozice, ROUND(AVG(plat_kc)) AS prumerny_plat, COUNT(*) AS pocet
FROM zamestnanci
GROUP BY pozice
ORDER BY prumerny_plat DESC;
 
-- Úloha 6: Jméno studenta + název jeho fakulty (JOIN).
SELECT s.jmeno, s.prijmeni, f.nazev AS fakulta
FROM studenti s
JOIN fakulty f ON s.id_fakul = f.id
ORDER BY f.nazev, s.prijmeni;
 
-- Úloha 7: 10 předmětů s nejvyšším počtem zapsaných studentů.
SELECT p.nazev, COUNT(z.id) AS pocet_zapsanych
FROM predmety p
JOIN zapis z ON p.id = z.id_pred
GROUP BY p.id, p.nazev
ORDER BY pocet_zapsanych DESC
LIMIT 10;
 
-- Úloha 8: Předměty bez zapsaného studenta (LEFT JOIN + NULL test).
SELECT p.id, p.nazev, p.kredity
FROM predmety p
LEFT JOIN zapis z ON p.id = z.id_pred
WHERE z.id IS NULL
ORDER BY p.id;
 
-- Úloha 9: Počet hodnocení A a celkový počet hodnocení na studenta.
SELECT s.jmeno, s.prijmeni,
       COUNT(h.id)                                  AS celkem_hodnoceni,
       COUNT(h.id) FILTER (WHERE h.znamka = 'A')    AS pocet_A
FROM studenti s
JOIN zapis z     ON s.id = z.id_stu
JOIN hodnoceni h ON z.id = h.id_zap
GROUP BY s.id, s.jmeno, s.prijmeni
ORDER BY pocet_A DESC;
 
-- Úloha 10: Zaměstnanci vyučující alespoň 3 předměty.
SELECT z.jmeno, z.prijmeni, COUNT(p.id) AS pocet_predmetu
FROM zamestnanci z
JOIN predmety p ON z.id = p.id_vyucujici
GROUP BY z.id, z.jmeno, z.prijmeni
HAVING COUNT(p.id) >= 3
ORDER BY pocet_predmetu DESC;
 
-- Úloha 11: Studenti s průměrem horším než průměr jejich fakulty (korelovaný poddotaz).
SELECT s.jmeno, s.prijmeni, s.studijni_prumer, s.id_fakul,
       (SELECT AVG(s2.studijni_prumer)
        FROM studenti s2 WHERE s2.id_fakul = s.id_fakul) AS prumer_fakulty
FROM studenti s
WHERE s.studijni_prumer > (
    SELECT AVG(s2.studijni_prumer) FROM studenti s2 WHERE s2.id_fakul = s.id_fakul
)
ORDER BY s.id_fakul, s.studijni_prumer DESC;
```

## 14. Nejčastější chyby při importu přes pgAdmin

### Chyba 1: `invalid input syntax`
To znamená, že typ sloupce neodpovídá hodnotě v CSV.

Příklad:
- do `DATE` jde text, který nevypadá jako datum
- do `NUMERIC` jde text nebo prázdná hodnota ve špatném formátu

### Chyba 2: `violates foreign key constraint`
To znamená, že importuješ ve špatném pořadí nebo v některém CSV chybí návazný záznam.

### Chyba 3: rozbitá diakritika
To je téměř vždy problém kódování CSV.

### Chyba 4: špatný delimiter
Pokud by soubor nebyl oddělen čárkou, je potřeba změnit delimiter třeba na `;`.

## 15. Volitelná úprava: indexy pro rychlejší dotazy

Po úspěšném importu můžeš přidat indexy na cizí klíče:

```sql
CREATE INDEX idx_student_id_fakul ON student(id_fakul);
CREATE INDEX idx_zamestnanci_id_fakul ON zamestnanci(id_fakul);
CREATE INDEX idx_predmet_id_vyucujici ON predmet(id_vyucujici);
CREATE INDEX idx_zapis_id_stu ON zapis(id_stu);
CREATE INDEX idx_zapis_id_pred ON zapis(id_pred);
CREATE INDEX idx_hodnoceni_id_zap ON hodnoceni(id_zap);
```

## 16. Doporučený minimální workflow

1. vytvoř databázi
2. spusť `CREATE TABLE` skript
3. importuj CSV přes pgAdmin
4. dodrž pořadí importu
5. zkontroluj počty záznamů
6. ověř vazby přes `JOIN`
7. teprve potom dělej další dotazy a analýzy

## 17. Krátké shrnutí

Pro tento dataset je nejbezpečnější postup:

- tabulky nejdřív vytvořit ručně přes SQL
- import udělat klikáním přes pgAdmin
- použít strukturu podle skutečných CSV
- počítat s tím, že `zamestnanci.csv` může mít problém s kódováním
- `hodnoceni.znamka` uložit jako `TEXT`, protože ve vstupu jsou písmenové známky

