# Cvičení 3 – Převod ERA modelu do relačního modelu

## Cíl cvičení

Po absolvování tohoto cvičení bude student schopen:

- systematicky převést ERA diagram do relačního modelu
- aplikovat pravidla pro 1:1, 1:N a M:N vztahy
- správně určit primární a cizí klíče
- pochopit vazbu mezi parcialitou a NOT NULL omezením
- připravit model připravený pro implementaci v SQL nebo MS Access

---

# Teoretická část (minimálně 20–25 minut)

## 1. Konceptuální vs. logický model

ERA model:
- popisuje realitu
- pracuje s entitami a vztahy
- neřeší konkrétní implementaci

Relační model:
- popisuje tabulky
- definuje klíče
- je připraven pro databázový systém

Transformace probíhá takto:

ERA model → Relační model → SQL implementace

---

## 2. Pravidla převodu

### Entita → Tabulka

Každá entita se stává tabulkou.

Primární klíč entity se stává PRIMARY KEY tabulky.

Atributy se stávají sloupci.

---

### Vztah 1:N

Cizí klíč se umisťuje do tabulky na straně N.

Příklad:

Fakulta 1:N Student

Tabulka STUDENT bude obsahovat faculty_id jako FK.

---

### Vztah 1:1

Cizí klíč umisťujeme do jedné z tabulek.
Obvykle do té, která má povinnou účast.

---

### Vztah M:N

Každý M:N vztah se musí převést na novou tabulku.

Tato tabulka:

- obsahuje dva cizí klíče
- má obvykle složený primární klíč
- obsahuje atributy vztahu

---

### Parcialita

Povinná účast → NOT NULL  
Volitelná účast → NULL povoleno

---

# Řízený příklad

Z ERA modelu:

Zaměstnanec 1:N Předmět  
Každý předmět je vyučován právě jedním zaměstnancem.

---

## Převod

ZAMESTNANEC
- employee_id (PK)
- jmeno

PREDMET
- subject_id (PK)
- nazev
- employee_id (FK)

Protože Předmět je na straně N.

Parcialita:
employee_id bude NOT NULL.

---

# Hlavní úloha – Univerzitní systém

Použij ERA model z předchozího cvičení.

---

## Krok 1 – Seznam entit

- Fakulta
- Student
- Zaměstnanec
- Předmět
- Zápis

---

## Krok 2 – Převod entit na tabulky

FAKULTA
- faculty_id (PK)
- nazev

STUDENT
- student_id (PK)
- jmeno

ZAMESTNANEC
- employee_id (PK)
- jmeno

PREDMET
- subject_id (PK)
- nazev

ZAPIS
- student_id
- subject_id
- znamka

---

## Krok 3 – Převod vztahů 1:N

Fakulta 1:N Student

→ STUDENT dostane faculty_id (FK)

Fakulta 1:N Zaměstnanec

→ ZAMESTNANEC dostane faculty_id (FK)

Zaměstnanec 1:N Předmět

→ PREDMET dostane employee_id (FK)

---

## Krok 4 – Převod M:N

Student M:N Předmět

Vzniká tabulka ZAPIS:

ZAPIS
- student_id (FK)
- subject_id (FK)
- znamka

Primární klíč:
(student_id, subject_id)

---

# Kompletní relační model

FAKULTA
- faculty_id (PK)
- nazev

STUDENT
- student_id (PK)
- jmeno
- faculty_id (FK)

ZAMESTNANEC
- employee_id (PK)
- jmeno
- faculty_id (FK)

PREDMET
- subject_id (PK)
- nazev
- employee_id (FK)

ZAPIS
- student_id (PK, FK)
- subject_id (PK, FK)
- znamka

---

# Vazba na SQL implementaci

V další fázi budou:

- PK → PRIMARY KEY
- FK → FOREIGN KEY
- povinná účast → NOT NULL

Například:

faculty_id INTEGER NOT NULL REFERENCES fakulta(faculty_id)

---

# Kontrolní checklist

- Každá entita má tabulku.
- Každý vztah 1:N je převeden přes FK.
- Neexistuje přímý M:N vztah.
- Asociativní tabulka má složený PK.
- Povinné vztahy jsou identifikovány.

---

# Pokročilé příklady – vyšší obtížnost

Následující příklady obsahují:

- slabé entity
- identifikující vztahy
- vícenásobné M:N vztahy
- kombinaci 1:1, 1:N a M:N
- víceúrovňovou závislost

U každého příkladu je uvedeno řešení.

---

# Příklad 8 – Slabá entita

## Zadání

Objednávka obsahuje položky.  
Položka je identifikována pouze v rámci konkrétní objednávky číslem položky.  
Každá položka patří právě jedné objednávce.

---

## Analýza

Položka:

- nemá globální identifikátor
- existuje pouze v rámci objednávky
- je slabá entita

---

## Řešení

OBJEDNAVKA  
- order_id (PK)  
- datum  

POLOZKA  
- order_id (PK, FK)  
- cislo_polozky (PK)  
- nazev  
- cena  

Primární klíč POLOZKA:
(order_id, cislo_polozky)

Vysvětlení:

Slabá entita potřebuje identifikující vztah.  
Bez objednávky nemůže existovat.

---

# Příklad 9 – Identifikující vztah

## Zadání

Faktura obsahuje řádky.  
Řádek je identifikován číslem řádku v rámci faktury.

---

## Řešení

FAKTURA  
- faktura_id (PK)  
- datum  

RADEK_FAKTURY  
- faktura_id (PK, FK)  
- cislo_radku (PK)  
- produkt  
- cena  

Jedná se o identifikující vztah.  
Primární klíč je složený.

---

# Příklad 10 – Více M:N vztahů

## Zadání

Student se zapisuje na předmět.  
Předmět má více učebnic.  
Student si může půjčit více učebnic.  
Evidujeme datum půjčení.

---

## Analýza

Student M:N Předmět  
Předmět M:N Učebnice  
Student M:N Učebnice (půjčení)

---

## Řešení

STUDENT  
- student_id (PK)

PREDMET  
- predmet_id (PK)

UCEBNICE  
- ucebnice_id (PK)

ZAPIS  
- student_id (PK, FK)  
- predmet_id (PK, FK)

PREDMET_UCEBNICE  
- predmet_id (PK, FK)  
- ucebnice_id (PK, FK)

PUJCENI  
- student_id (PK, FK)  
- ucebnice_id (PK, FK)  
- datum_pujceni

---

# Příklad 11 – Kombinace 1:1 a 1:N

## Zadání

Každý uživatel má právě jeden profil.  
Profil nemůže existovat bez uživatele.  
Uživatel může mít více příspěvků.

---

## Řešení

UZIVATEL  
- user_id (PK)  
- jmeno  

PROFIL  
- profile_id (PK)  
- user_id (FK UNIQUE NOT NULL)  

PRISPEVEK  
- post_id (PK)  
- text  
- user_id (FK NOT NULL)

Vysvětlení:

1:1 je realizováno pomocí UNIQUE FK.

---

# Příklad 12 – Hierarchická závislost

## Zadání

Společnost má pobočky.  
Pobočka má oddělení.  
Oddělení má zaměstnance.  
Zaměstnanec může být vedoucím oddělení.

---

## Řešení

POBOCKA  
- pobocka_id (PK)  
- nazev  

ODDELENI  
- oddeleni_id (PK)  
- nazev  
- pobocka_id (FK NOT NULL)  
- vedouci_id (FK NULL)

ZAMESTNANEC  
- employee_id (PK)  
- jmeno  
- oddeleni_id (FK NOT NULL)

Poznámka:

vedouci_id je samoodkaz na ZAMESTNANEC.  
Jedná se o rekurzivní vztah.

---

# Příklad 13 – Rekurzivní vztah

## Zadání

Zaměstnanec může být nadřízen jinému zaměstnanci.  
Každý zaměstnanec má nejvýše jednoho nadřízeného.

---

## Řešení

ZAMESTNANEC  
- employee_id (PK)  
- jmeno  
- nadrazeny_id (FK NULL)

nadrazeny_id odkazuje na employee_id ve stejné tabulce.

---

# Příklad 14 – Víceúrovňový M:N s atributem

## Zadání

Dodavatel dodává produkty do skladů.  
Produkt může být dodáván více dodavateli.  
Evidujeme cenu dodávky a datum dodání.

---

## Řešení

DODAVATEL  
- supplier_id (PK)

PRODUKT  
- product_id (PK)

SKLAD  
- sklad_id (PK)

DODAVKA  
- supplier_id (PK, FK)  
- product_id (PK, FK)  
- sklad_id (PK, FK)  
- datum  
- cena

Jedná se o ternární vztah (3 entity).

Primární klíč je složený ze tří cizích klíčů.

---

# Nejvyšší obtížnost – Kombinovaný komplexní model

## Zadání

Pacient navštěvuje lékaře.  
Lékař pracuje na oddělení.  
Pacient může mít více diagnóz.  
Diagnóza je stanovena při konkrétní návštěvě.  
Evidujeme datum návštěvy a popis vyšetření.

---

## Řešení

PACIENT  
- pacient_id (PK)

LEKAR  
- lekar_id (PK)  
- oddeleni_id (FK NOT NULL)

ODDELENI  
- oddeleni_id (PK)

NAVSTEVA  
- navsteva_id (PK)  
- pacient_id (FK NOT NULL)  
- lekar_id (FK NOT NULL)  
- datum  
- popis

DIAGNOZA  
- diagnoza_id (PK)  
- nazev

NAVSTEVA_DIAGNOZA  
- navsteva_id (PK, FK)  
- diagnoza_id (PK, FK)

Zde:

- Pacient M:N Lékař řešeno přes NAVSTEVA
- Diagnóza je navázána na konkrétní návštěvu

---

# Shrnutí pokročilé části

Student musí být schopen:

- rozpoznat slabou entitu
- navrhnout složený primární klíč
- vytvořit rekurzivní vztah
- řešit ternární vztah
- správně modelovat identifikující vztah
- oddělit atribut vztahu od atributu entity
