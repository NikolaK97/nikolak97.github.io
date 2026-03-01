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
