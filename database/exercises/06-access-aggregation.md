# Cvičení 6 – Agregační dotazy v MS Access

## # JOINy v SQL – teorie

JOIN je operace v SQL, která umožňuje **spojit data z více tabulek do jednoho výsledku**.

Používá se v situacích, kdy jsou data rozdělena do více tabulek (např. kvůli normalizaci databáze) a potřebujeme je zobrazit společně.

---

##  Proč JOINy existují?

V relačních databázích se data často ukládají do více tabulek, aby se:
- zabránilo duplicitám
- zlepšila přehlednost
- usnadnila správa dat

Například:
- tabulka `studenti` obsahuje jména studentů
- tabulka `tridy` obsahuje názvy tříd

Místo ukládání názvu třídy ke každému studentovi se uloží pouze **odkaz (ID)**.

JOIN pak umožní tato data znovu propojit.

---

##  Jak JOIN funguje?

JOIN pracuje tak, že:
1. vezme dvě tabulky
2. porovná hodnoty ve vybraných sloupcích
3. spojí řádky, které splňují podmínku

Tato podmínka se zapisuje pomocí:
ON tabulka1.sloupec = tabulka2.sloupec

---

### Typy JOINů (princip)

INNER JOIN
Vrací pouze ty řádky, kde existuje shoda v obou tabulkách.
Pokud neexistuje odpovídající záznam, řádek se do výsledku nedostane.

LEFT JOIN
Vrací všechny řádky z levé tabulky.
Pokud pro některý řádek neexistuje shoda v pravé tabulce, doplní se hodnoty NULL.

RIGHT JOIN
Vrací všechny řádky z pravé tabulky.
Funguje stejně jako LEFT JOIN, jen z opačné strany.

FULL JOIN
Vrací všechny řádky z obou tabulek.
Pokud neexistuje shoda, chybějící hodnoty se doplní NULL.

---

### Co znamená „levá“ a „pravá“ tabulka?
Pořadí tabulek v dotazu je důležité:
FROM tabulka1
LEFT JOIN tabulka2
tabulka1 = levá tabulka
tabulka2 = pravá tabulka
LEFT JOIN tedy znamená: „vezmi vše z tabulka1“.

---

### NULL hodnoty při JOINu
Pokud neexistuje odpovídající záznam:
SQL doplní NULL
znamená to „žádná hodnota / neznámá hodnota“
To je důležité například při filtrování výsledků.

---

### Kartézský součin
Pokud se nepoužije podmínka ON, vznikne tzv. kartézský součin:
každý řádek z první tabulky se spojí s každým řádkem z druhé tabulky
To většinou není žádoucí a vede k velkému množství dat.

---

### JOIN vs. WHERE
Podmínky v JOINu:
ON → určuje, jak se tabulky spojují
WHERE → filtruje výsledky po spojení
Tyto dvě věci mají odlišnou roli a neměly by se zaměňovat.

---

## 1) Více příkladů na `WHERE`

### Studenti z konkrétního ročníku
```sql
--studenti z prnvího ročníku
SELECT *
FROM student
WHERE rocnik = 1;

-- Aktivní studenti
SELECT id, jmeno, prijmeni, aktivni
FROM student
WHERE aktivni = ano;

-- Neaktivní studenti
SELECT id, jmeno, prijmeni
FROM student
WHERE aktivni = ne;

-- Studenti s vysokým stipendiem
SELECT id, jmeno, prijmeni, stipendium_kc
FROM student
WHERE stipendium_kc > 5000;

-- Studenti s průměrem lepším než 2.00
SELECT id, jmeno, prijmeni, studijni_prumer
FROM student
WHERE studijni_prumer < 2.00;

-- Fakulty v Brně
SELECT *
FROM fakulty
WHERE mesto = 'Brno';

--Povinné předměty s více než 5 kredity
SELECT id, nazev, kredity, typ
FROM predmet
WHERE typ = 'povinný'
  AND kredity > 5;

-- Zaměstnanci s platem mezi 50 000 a 80 000
SELECT id, jmeno, prijmeni, plat_kc
FROM zamestnanci
WHERE plat_kc BETWEEN 50000 AND 80000;

-- Studenti z více měst brno, ostrava liberec
SELECT id, jmeno, prijmeni, mesto
FROM student
WHERE mesto IN ('Brno', 'Ostrava', 'Liberec');

-- Předměty, které nejsou volitelné
SELECT id, nazev, typ
FROM predmet
WHERE typ <> 'volitelný';

2) WHERE + JOIN
-- Aktivní studenti i s názvem fakulty
SELECT s.jmeno, s.prijmeni, f.nazev AS fakulta
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
WHERE s.aktivni = TRUE;

--Studenti z Brna a jejich fakulty
SELECT s.jmeno, s.prijmeni, s.mesto, f.nazev AS fakulta
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
WHERE s.mesto = 'Brno';

--Předměty vyučované profesory
SELECT p.nazev AS predmet, z.jmeno, z.prijmeni, z.pozice
FROM predmet p
JOIN zamestnanci z ON p.id_vyucujici = z.id
WHERE z.pozice = 'profesor';

--Zápisy jen do povinných předmětů
SELECT s.jmeno, s.prijmeni, p.nazev AS predmet, p.typ
FROM zapis zp
JOIN student s ON zp.id_stu = s.id
JOIN predmet p ON zp.id_pred = p.id
WHERE p.typ = 'povinný';

--Studenti zapsaní v letním semestru
SELECT s.jmeno, s.prijmeni, p.nazev, p.semester
FROM zapis zp
JOIN student s ON zp.id_stu = s.id
JOIN predmet p ON zp.id_pred = p.id
WHERE p.semester = 'LS';

--Studenti z kombinovaného studia na fakultách v Brně
SELECT s.jmeno, s.prijmeni, s.forma_studia, f.nazev, f.mesto
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
WHERE s.forma_studia = 'kombinované'
  AND f.mesto = 'Brno';

3) Agregace bez JOIN
-- Počet studentů
SELECT COUNT(*) AS pocet_studentu
FROM student;

--Průměrné stipendium
SELECT AVG(stipendium_kc) AS prumerne_stipendium
FROM student;

--Nejvyšší a nejnižší stipendium
SELECT
    MIN(stipendium_kc) AS min_stipendium,
    MAX(stipendium_kc) AS max_stipendium
FROM student;

--Průměrný plat zaměstnanců
SELECT AVG(plat_kc) AS prumerny_plat
FROM zamestnanci;

4) GROUP BY 
-- Počet studentů podle ročníku
SELECT rocnik, COUNT(*) AS pocet_studentu
FROM student
GROUP BY rocnik
ORDER BY rocnik;

--Počet studentů podle formy studia
SELECT forma_studia, COUNT(*) AS pocet_studentu
FROM student
GROUP BY forma_studia;

--Počet studentů podle aktivity
SELECT aktivni, COUNT(*) AS pocet_studentu
FROM student
GROUP BY aktivni;

--Průměrné stipendium podle ročníku
SELECT rocnik, AVG(stipendium_kc) AS prumerne_stipendium
FROM student
GROUP BY rocnik
ORDER BY rocnik;

--Nejlepší a nejhorší studijní průměr podle ročníku
SELECT
    rocnik,
    MIN(studijni_prumer) AS nejlepsi_prumer,
    MAX(studijni_prumer) AS nejhorsi_prumer
FROM student
GROUP BY rocnik;

--Počet předmětů podle typu
SELECT typ, COUNT(*) AS pocet
FROM predmet
GROUP BY typ;

--Počet předmětů podle semestru
SELECT semester, COUNT(*) AS pocet
FROM predmet
GROUP BY semester;

--Průměr kreditů podle typu předmětu
SELECT typ, AVG(kredity) AS prumer_kreditu
FROM predmet
GROUP BY typ;

--Počet zaměstnanců podle pozice
SELECT pozice, COUNT(*) AS pocet
FROM zamestnanci
GROUP BY pozice;

--Průměrný plat podle pozice
SELECT pozice, AVG(plat_kc) AS prumerny_plat
FROM zamestnanci
GROUP BY pozice;
-- Počet zaměstnanců podle úvazku
SELECT uvazek, COUNT(*) AS pocet
FROM zamestnanci
GROUP BY uvazek;

5) GROUP BY + JOIN
--Počet studentů na fakultách
SELECT f.id, f.nazev, COUNT(s.id) AS pocet_studentu
FROM fakulty f
LEFT JOIN student s ON s.id_fakul = f.id
GROUP BY f.id, f.nazev
ORDER BY pocet_studentu DESC;

--Průměrné stipendium na fakultách
SELECT f.nazev, AVG(s.stipendium_kc) AS prumerne_stipendium
FROM fakulty f
JOIN student s ON s.id_fakul = f.id
GROUP BY f.nazev
ORDER BY prumerne_stipendium DESC;

--Počet zaměstnanců na fakultách
SELECT f.nazev, COUNT(z.id) AS pocet_zamestnancu
FROM fakulty f
LEFT JOIN zamestnanci z ON z.id_fakul = f.id
GROUP BY f.nazev
ORDER BY pocet_zamestnancu DESC;
Průměrný plat zaměstnanců podle fakulty
SELECT f.nazev, AVG(z.plat_kc) AS prumerny_plat
FROM fakulty f
JOIN zamestnanci z ON z.id_fakul = f.id
GROUP BY f.nazev
ORDER BY prumerny_plat DESC;

--Celkové mzdy podle fakulty
SELECT f.nazev, SUM(z.plat_kc) AS celkove_mzdy
FROM fakulty f
JOIN zamestnanci z ON z.id_fakul = f.id
GROUP BY f.nazev
ORDER BY celkove_mzdy DESC;

--Počet studentů podle města fakulty
SELECT f.mesto, COUNT(s.id) AS pocet_studentu
FROM fakulty f
JOIN student s ON s.id_fakul = f.id
GROUP BY f.mesto
ORDER BY pocet_studentu DESC;

--Počet studentů v kombinovaném/prezenčním studiu na fakultách
SELECT f.nazev, s.forma_studia, COUNT(*) AS pocet_studentu
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
GROUP BY f.nazev, s.forma_studia
ORDER BY f.nazev, pocet_studentu DESC;

--Počet aktivních studentů na fakultách
SELECT f.nazev, COUNT(*) AS pocet_aktivnich
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
WHERE s.aktivni = TRUE
GROUP BY f.nazev
ORDER BY pocet_aktivnich DESC;

6) Agregace nad zápisy
--Kolik předmětů má každý student
SELECT s.id, s.jmeno, s.prijmeni, COUNT(zp.id_pred) AS pocet_predmetu
FROM student s
LEFT JOIN zapis zp ON s.id = zp.id_stu
GROUP BY s.id, s.jmeno, s.prijmeni
ORDER BY pocet_predmetu DESC;

--Kolik kreditů má každý student celkem
SELECT s.id, s.jmeno, s.prijmeni, SUM(p.kredity) AS celkem_kreditu
FROM student s
JOIN zapis zp ON s.id = zp.id_stu
JOIN predmet p ON zp.id_pred = p.id
GROUP BY s.id, s.jmeno, s.prijmeni
ORDER BY celkem_kreditu DESC;

--Kolik studentů je zapsáno na jednotlivé předměty
SELECT p.id, p.nazev, COUNT(zp.id_stu) AS pocet_studentu
FROM predmet p
LEFT JOIN zapis zp ON p.id = zp.id_pred
GROUP BY p.id, p.nazev
ORDER BY pocet_studentu DESC;

--Průměrný počet studentů na předmět podle typu
SELECT p.typ, AVG(t.pocet_studentu) AS prumer_studentu
FROM (
    SELECT p.id, p.typ, COUNT(zp.id_stu) AS pocet_studentu
    FROM predmet p
    LEFT JOIN zapis zp ON p.id = zp.id_pred
    GROUP BY p.id, p.typ
) t
JOIN predmet p ON p.id = t.id
GROUP BY p.typ;

--Kolik předmětů učí každý zaměstnanec
SELECT z.id, z.jmeno, z.prijmeni, COUNT(p.id) AS pocet_predmetu
FROM zamestnanci z
LEFT JOIN predmet p ON p.id_vyucujici = z.id
GROUP BY z.id, z.jmeno, z.prijmeni
ORDER BY pocet_predmetu DESC;

--Kolik studentů celkem učí každý vyučující
SELECT z.id, z.jmeno, z.prijmeni, COUNT(zp.id_stu) AS pocet_zapisu
FROM zamestnanci z
LEFT JOIN predmet p ON p.id_vyucujici = z.id
LEFT JOIN zapis zp ON zp.id_pred = p.id
GROUP BY z.id, z.jmeno, z.prijmeni
ORDER BY pocet_zapisu DESC;

--Počet zápisů podle semestru
SELECT p.semester, COUNT(*) AS pocet_zapisu
FROM zapis zp
JOIN predmet p ON zp.id_pred = p.id
GROUP BY p.semester;

--Počet zápisů podle typu předmětu
SELECT p.typ, COUNT(*) AS pocet_zapisu
FROM zapis zp
JOIN predmet p ON zp.id_pred = p.id
GROUP BY p.typ;

7) HAVING 
--Fakulty s více než 30 studenty
SELECT f.nazev, COUNT(s.id) AS pocet_studentu
FROM fakulty f
JOIN student s ON s.id_fakul = f.id
GROUP BY f.nazev
HAVING COUNT(s.id) > 30;

--Fakulty s průměrným stipendiem nad 4000
SELECT f.nazev, AVG(s.stipendium_kc) AS prumerne_stipendium
FROM fakulty f
JOIN student s ON s.id_fakul = f.id
GROUP BY f.nazev
HAVING AVG(s.stipendium_kc) > 4000;

--Ročníky s více než 100 studenty
SELECT rocnik, COUNT(*) AS pocet_studentu
FROM student
GROUP BY rocnik
HAVING COUNT(*) > 100;

--Pozice s průměrným platem nad 60 000
SELECT pozice, AVG(plat_kc) AS prumerny_plat
FROM zamestnanci
GROUP BY pozice
HAVING AVG(plat_kc) > 60000;

--Předměty s více než 20 zapsanými studenty
SELECT p.nazev, COUNT(zp.id_stu) AS pocet_studentu
FROM predmet p
JOIN zapis zp ON p.id = zp.id_pred
GROUP BY p.nazev
HAVING COUNT(zp.id_stu) > 20;

--Studenti, kteří mají více než 6 zapsaných předmětů
SELECT s.id, s.jmeno, s.prijmeni, COUNT(zp.id_pred) AS pocet_predmetu
FROM student s
JOIN zapis zp ON s.id = zp.id_stu
GROUP BY s.id, s.jmeno, s.prijmeni
HAVING COUNT(zp.id_pred) > 6;

--Studenti s více než 30 kredity
SELECT s.id, s.jmeno, s.prijmeni, SUM(p.kredity) AS celkem_kreditu
FROM student s
JOIN zapis zp ON s.id = zp.id_stu
JOIN predmet p ON zp.id_pred = p.id
GROUP BY s.id, s.jmeno, s.prijmeni
HAVING SUM(p.kredity) > 30;

--Vyučující, kteří učí více než 3 předměty
SELECT z.id, z.jmeno, z.prijmeni, COUNT(p.id) AS pocet_predmetu
FROM zamestnanci z
JOIN predmet p ON p.id_vyucujici = z.id
GROUP BY z.id, z.jmeno, z.prijmeni
HAVING COUNT(p.id) > 3;

--Fakulty s více než 5 zaměstnanci
SELECT f.nazev, COUNT(z.id) AS pocet_zamestnancu
FROM fakulty f
JOIN zamestnanci z ON z.id_fakul = f.id
GROUP BY f.nazev
HAVING COUNT(z.id) > 5;

--Města, kde je více než 1 fakulta
SELECT mesto, COUNT(*) AS pocet_fakult
FROM fakulty
GROUP BY mesto
HAVING COUNT(*) > 1;

8) WHERE + GROUP BY + HAVING dohromady
--Jen aktivní studenti, seskupení podle fakulty
SELECT f.nazev, COUNT(s.id) AS pocet_aktivnich
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
WHERE s.aktivni = TRUE
GROUP BY f.nazev
HAVING COUNT(s.id) > 10;

--Jen povinné předměty, které mají hodně studentů
SELECT p.nazev, COUNT(zp.id_stu) AS pocet_studentu
FROM predmet p
JOIN zapis zp ON p.id = zp.id_pred
WHERE p.typ = 'povinný'
GROUP BY p.nazev
HAVING COUNT(zp.id_stu) > 15;

--Jen zaměstnanci s plným úvazkem, průměr platů podle pozice
SELECT pozice, AVG(plat_kc) AS prumerny_plat
FROM zamestnanci
WHERE uvazek = 1
GROUP BY pozice
HAVING AVG(plat_kc) > 50000;

--Jen studenti prezenční formy, průměr stipendia podle ročníku
SELECT rocnik, AVG(stipendium_kc) AS prumerne_stipendium
FROM student
WHERE forma_studia = 'prezenční'
GROUP BY rocnik
HAVING AVG(stipendium_kc) > 3000;

--Jen LS předměty, průměr kreditů podle typu
SELECT typ, AVG(kredity) AS prumer_kreditu
FROM predmet
WHERE semester = 'LS'
GROUP BY typ
HAVING AVG(kredity) > 5;

9) LEFT JOIN příklady
--Všechny fakulty i bez studentů
SELECT f.nazev, COUNT(s.id) AS pocet_studentu
FROM fakulty f
LEFT JOIN student s ON s.id_fakul = f.id
GROUP BY f.nazev;

--Všichni zaměstnanci i bez vyučovaných předmětů
SELECT z.id, z.jmeno, z.prijmeni, COUNT(p.id) AS pocet_predmetu
FROM zamestnanci z
LEFT JOIN predmet p ON p.id_vyucujici = z.id
GROUP BY z.id, z.jmeno, z.prijmeni;

--Všichni studenti i bez zápisu
SELECT s.id, s.jmeno, s.prijmeni, COUNT(zp.id) AS pocet_zapisu
FROM student s
LEFT JOIN zapis zp ON s.id = zp.id_stu
GROUP BY s.id, s.jmeno, s.prijmeni;

-- Předměty, které si nikdo nezapsal
SELECT p.id, p.nazev
FROM predmet p
LEFT JOIN zapis zp ON p.id = zp.id_pred
WHERE zp.id IS NULL;

--Zaměstnanci, kteří neučí žádný předmět
SELECT z.id, z.jmeno, z.prijmeni
FROM zamestnanci z
LEFT JOIN predmet p ON p.id_vyucujici = z.id
WHERE p.id IS NULL;

10) Složitější a zkouškové příklady
--Fakulta s nejvyšším počtem studentů
SELECT f.nazev, COUNT(s.id) AS pocet_studentu
FROM fakulty f
JOIN student s ON s.id_fakul = f.id
GROUP BY f.id, f.nazev
ORDER BY pocet_studentu DESC
LIMIT 1;

--Student s největším počtem kreditů
SELECT s.id, s.jmeno, s.prijmeni, SUM(p.kredity) AS celkem_kreditu
FROM student s
JOIN zapis zp ON s.id = zp.id_stu
JOIN predmet p ON zp.id_pred = p.id
GROUP BY s.id, s.jmeno, s.prijmeni
ORDER BY celkem_kreditu DESC
LIMIT 1;

--Předmět s největším počtem studentů
SELECT p.id, p.nazev, COUNT(zp.id_stu) AS pocet_studentu
FROM predmet p
JOIN zapis zp ON p.id = zp.id_pred
GROUP BY p.id, p.nazev
ORDER BY pocet_studentu DESC
LIMIT 1;

--Průměrný počet předmětů na studenta
SELECT AVG(t.pocet_predmetu) AS prumer_predmetu_na_studenta
FROM (
    SELECT s.id, COUNT(zp.id_pred) AS pocet_predmetu
    FROM student s
    LEFT JOIN zapis zp ON s.id = zp.id_stu
    GROUP BY s.id
);

--Fakulty, kde je zároveň více než 20 studentů a více než 5 zaměstnanců
SELECT
    f.nazev,
    COUNT(DISTINCT s.id) AS pocet_studentu,
    COUNT(DISTINCT z.id) AS pocet_zamestnancu
FROM fakulty f
LEFT JOIN student s ON s.id_fakul = f.id
LEFT JOIN zamestnanci z ON z.id_fakul = f.id
GROUP BY f.id, f.nazev
HAVING COUNT(DISTINCT s.id) > 20
   AND COUNT(DISTINCT z.id) > 5;

--Kolik studentů má každý vyučující přes své předměty
SELECT
    z.id,
    z.jmeno,
    z.prijmeni,
    COUNT(DISTINCT zp.id_stu) AS pocet_unikatnich_studentu
FROM zamestnanci z
LEFT JOIN predmet p ON p.id_vyucujici = z.id
LEFT JOIN zapis zp ON zp.id_pred = p.id
GROUP BY z.id, z.jmeno, z.prijmeni
ORDER BY pocet_unikatnich_studentu DESC;

--Průměr kreditů předmětů na fakultách vyučujících
SELECT
    f.nazev,
    AVG(p.kredity) AS prumer_kreditu
FROM predmet p
JOIN zamestnanci z ON p.id_vyucujici = z.id
JOIN fakulty f ON z.id_fakul = f.id
GROUP BY f.nazev
ORDER BY prumer_kreditu DESC;

11) Příklady na COUNT DISTINCT
--Kolik různých měst mají studenti
SELECT COUNT(DISTINCT mesto) AS pocet_mest
FROM student;

-- Kolik studentů si zapsalo alespoň jeden předmět
SELECT COUNT(DISTINCT id_stu) AS pocet_studentu_se_zapisem
FROM zapis;

12) Vnořené dotazy s WHERE / HAVING
-- Studenti s nadprůměrným stipendiem
SELECT id, jmeno, prijmeni, stipendium_kc
FROM student
WHERE stipendium_kc > (
    SELECT AVG(stipendium_kc)
    FROM student
);

-- Zaměstnanci s platem vyšším než průměr jejich pozice
SELECT z1.id, z1.jmeno, z1.prijmeni, z1.pozice, z1.plat_kc
FROM zamestnanci z1
WHERE z1.plat_kc > (
    SELECT AVG(z2.plat_kc)
    FROM zamestnanci z2
    WHERE z2.pozice = z1.pozice
);

-- Předměty s více studenty než je průměrný počet studentů na předmět
SELECT p.id, p.nazev, COUNT(zp.id_stu) AS pocet_studentu
FROM predmet p
LEFT JOIN zapis zp ON p.id = zp.id_pred
GROUP BY p.id, p.nazev
HAVING COUNT(zp.id_stu) > (
    SELECT AVG(t.pocet_studentu)
    FROM (
        SELECT COUNT(zp2.id_stu) AS pocet_studentu
        FROM predmet p2
        LEFT JOIN zapis zp2 ON p2.id = zp2.id_pred
        GROUP BY p2.id
    ) t
);



14) Krátká sada úplně typických zkouškových zadání
-- 1. Vypiš studenty 3. ročníku
SELECT *
FROM student
WHERE rocnik = 3;

-- 2. Vypiš počet studentů podle ročníku
SELECT rocnik, COUNT(*) AS pocet
FROM student
GROUP BY rocnik;

-- 3. Vypiš fakulty s více než 20 studenty
SELECT f.nazev, COUNT(*) AS pocet
FROM student s
JOIN fakulty f ON s.id_fakul = f.id
GROUP BY f.nazev
HAVING COUNT(*) > 20;

-- 4. Vypiš studenty a jejich fakultu
SELECT s.jmeno, s.prijmeni, f.nazev
FROM student s
JOIN fakulty f ON s.id_fakul = f.id;

-- 5. Vypiš počet zapsaných předmětů na studenta
SELECT s.jmeno, s.prijmeni, COUNT(zp.id_pred) AS pocet_predmetu
FROM student s
LEFT JOIN zapis zp ON s.id = zp.id_stu
GROUP BY s.id, s.jmeno, s.prijmeni;

-- 6. Vypiš předměty s více než 10 studenty
SELECT p.nazev, COUNT(zp.id_stu) AS pocet_studentu
FROM predmet p
JOIN zapis zp ON p.id = zp.id_pred
GROUP BY p.id, p.nazev
HAVING COUNT(zp.id_stu) > 10;

-- 7. Vypiš průměrný plat podle pozice
SELECT pozice, AVG(plat_kc) AS prumerny_plat
FROM zamestnanci
GROUP BY pozice;

-- 8. Vypiš aktivní studenty s průměrem lepším než 2
SELECT id, jmeno, prijmeni
FROM student
WHERE aktivni = TRUE
  AND studijni_prumer < 2.0;
