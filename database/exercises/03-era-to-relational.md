# Cvičení 3 – Převod ERA modelu do relačního modelu

# Normální formy v relačním modelu – detailní výklad s příklady


# 1. Proč vůbec normalizujeme?

Představ si, že ukládáš data do jedné velké tabulky. Funguje to – dokud:

- nezačneš něco měnit,
- nezačneš mazat,
- nezačneš přidávat nové záznamy.

Pak vznikají problémy:

## Aktualizační anomálie
Změníš jednu hodnotu, ale zapomeneš ji změnit jinde.

## Mazací anomálie
Smažeš jeden řádek a tím ztratíš důležitou informaci.

## Vkládací anomálie
Nemůžeš vložit informaci, protože ti chybí jiná část dat.

Normalizace tyto problémy odstraňuje.

---
Použité pojmy:
- Funkční závislost (FD) `X → Y`: stejné X vždy znamená stejné Y.
- Uzávěr `X⁺`: co všechno z X odvodím pomocí FD.
- Kandidátní klíč: minimální X takové, že `X⁺` obsahuje všechny atributy.
- 2NF řeší částečné závislosti (jen u složeného klíče).
- 3NF řeší tranzitivní závislosti (neklíčový atribut určuje jiný neklíčový atribut).
- BCNF je přísnější: v každé FD musí být levá strana superklíč.

---
# 4. 1NF – První normální forma

## Definice

Relace je v 1NF, pokud:

- všechny hodnoty jsou atomické,
- neexistují opakující se skupiny.

## Příklad porušení

Objednávka(id_obj, zákazník, produkty)

| id_obj | zákazník | produkty        |
|--------|----------|-----------------|
| 1      | Novák    | A12, B45, C33   |

Sloupec produkty obsahuje více hodnot.

## Rozklad

Objednávka(id_obj, zákazník)  
Položka(id_obj, produkt)

---

# 2. Příklad z praxe – Školní systém

## Špatný návrh (nenormalizovaný)

Tabulka:

Zápis

| student_id | student_jméno | předmět_id | předmět_název | vyučující | známka |
|------------|---------------|------------|---------------|-----------|--------|
| 1          | Anna          | DB         | Databáze      | Novák     | 1      |
| 1          | Anna          | ALG        | Algoritmy     | Svoboda   | 2      |
| 2          | Petr          | DB         | Databáze      | Novák     | 3      |

---

## Co se zde děje?

### Problém 1 – Redundance
Jméno studenta Anna je uloženo vícekrát.  
Název předmětu Databáze je uložen vícekrát.  
Vyučující Novák je uložen vícekrát.

### Problém 2 – Aktualizační anomálie
Pokud se vyučující Novák přejmenuje, musíme změnit všechny řádky.

### Problém 3 – Mazací anomálie
Když smažeme posledního studenta z předmětu ALG, ztratíme informaci, že předmět existuje.

---

# 3. Přechod do 2NF

Primární klíč je složený:
(student_id, předmět_id)

Podívejme se, co na čem závisí:

- student_id → student_jméno
- předmět_id → předmět_název, vyučující
- (student_id, předmět_id) → známka

Problém:
některé atributy závisí jen na části klíče.

---

## Oprava – rozdělení tabulky

Student  
(student_id, student_jméno)

Předmět  
(předmět_id, předmět_název, vyučující)

Zápis  
(student_id, předmět_id, známka)

---

## Co se změnilo?

- Jméno studenta je uloženo jen jednou.
- Název předmětu je uložen jen jednou.
- Změna se provádí na jednom místě.

Toto je 2NF.

---

# 4. Další problém – Tranzitivní závislost

Teď si představ jiný systém.

## Tabulka objednávek

| objednávka_id | datum | zákazník_id | zákazník_jméno | město |
|---------------|-------|-------------|----------------|-------|
| 100           | 1.3.  | 10          | Novák          | Brno  |
| 101           | 2.3.  | 10          | Novák          | Brno  |

---

## Co se zde děje?

objednávka_id určuje zákazník_id  
zákazník_id určuje zákazník_jméno a město  

Vzniká řetěz závislostí:

objednávka_id → zákazník_id → zákazník_jméno

Tomu se říká tranzitivní závislost.

---

## Problém

Pokud se zákazník přestěhuje, musíme změnit město ve všech objednávkách.

---

# Řešení – 3NF

Rozdělení:

Objednávka  
(objednávka_id, datum, zákazník_id)

Zákazník  
(zákazník_id, zákazník_jméno, město)

---

## Výsledek

- Každá informace je uložena právě tam, kam logicky patří.
- Neexistují tranzitivní závislosti.
- Tabulky jsou ve 3NF.

---

# 5. Příklad, kde 3NF nestačí (BCNF)

## Rozvrh

| učitel | předmět | učebna |
|--------|----------|--------|
| Novák  | DB       | A1     |
| Svoboda| ALG      | A2     |

Platí:
- kombinace (učitel, předmět) určuje učebnu
- každá učebna je vyhrazena jen pro jeden předmět

Tedy:
učebna → předmět

---

## Co to znamená?

učebna není klíč, ale přesto určuje jiný atribut.

To porušuje BCNF.

---

# Řešení

Učebna  
(učebna, předmět)

Rozvrh  
(učitel, učebna)

---

# 6. Velký komplexní příklad – Fakturační systém

## Špatný návrh

| faktura_id | datum | zákazník | produkt | cena | množství |
|------------|-------|----------|---------|------|----------|

Problém:
- cena produktu se opakuje
- jméno zákazníka se opakuje

---

## Co se děje logicky?

- faktura určuje zákazníka
- produkt určuje cenu
- kombinace (faktura, produkt) určuje množství

---

# Správný návrh

Faktura  
(faktura_id, datum, zákazník_id)

Zákazník  
(zákazník_id, jméno)

Produkt  
(produkt_id, název, cena)

Položka  
(faktura_id, produkt_id, množství)

---

# Proč je to lepší?

- Cena produktu je uložena jednou.
- Zákazník je uložen jednou.
- Faktura může mít libovolný počet položek.
- Žádné anomálie.

---

Použité pojmy:
- Funkční závislost (FD) `X → Y`: stejné X vždy znamená stejné Y.
- Uzávěr `X⁺`: co všechno z X odvodím pomocí FD.
- Kandidátní klíč: minimální X takové, že `X⁺` obsahuje všechny atributy.
- 2NF řeší částečné závislosti (jen u složeného klíče).
- 3NF řeší tranzitivní závislosti (neklíčový atribut určuje jiný neklíčový atribut).
- BCNF je přísnější: v každé FD musí být levá strana superklíč.

---

## Příklad 1 – Zahřátí: klíč přes uzávěr a kontrola 3NF/BCNF

### Zadání
Relace `R(A, B, C, D)`  
FD:
- `A → B`
- `B → C`
- `A → D`

### Co se tady děje
Hodnota A určí B a D. A když mám B, určí mi C. Takže A vlastně určí všechno.

### Řešení
**1) Uzávěr `A⁺`:**
- start: `{A}`
- `A → B` ⇒ `{A, B}`
- `B → C` ⇒ `{A, B, C}`
- `A → D` ⇒ `{A, B, C, D}`

`A⁺` obsahuje všechny atributy ⇒ `A` je kandidátní klíč.

**2) 2NF**
Primární klíč je jednoduchý (`A`) ⇒ 2NF je automaticky splněna.

**3) 3NF a BCNF**
V každé FD je vlevo `A` nebo `B`.
- `A → B` a `A → D`: vlevo je klíč ⇒ OK pro 3NF i BCNF
- `B → C`: je `B` superklíč? Ne (protože `B⁺ = {B, C}`), takže BCNF by to porušovalo.
  Pozor: tady je důležitý detail:
  - 3NF může být ještě splněna, pokud je pravá strana (C) součástí nějakého kandidátního klíče. Kandidátní klíč je jen `A`, takže C není „prime“ atribut.
  ⇒ Relace není v 3NF ani v BCNF.

**4) Rozklad do 3NF/BCNF**
Rozkládáme podle `B → C`:
- `R1(B, C)`
- `R2(A, B, D)`

Kontrola:
- `R1(B, C)` je v BCNF (B je klíč v R1).
- `R2(A, B, D)` je v BCNF (A je klíč v R2, platí `A → B, D`).

---

## Příklad 2 – 2NF: „Zápis studenta“ (klasika, ale správně a s vysvětlením)

### Zadání
Relace `Zápis(student_id, předmět_id, student_jméno, předmět_název, kredity, známka)`  
FD:
- `student_id → student_jméno`
- `předmět_id → předmět_název, kredity`
- `(student_id, předmět_id) → známka`

### Co se tady děje
Jeden řádek říká: „Student X má u předmětu Y známku Z.“  
Jenže spolu s tím opakujeme informace:
- jméno studenta (pořád dokola u všech jeho předmětů),
- název a kredity předmětu (pořád dokola u všech studentů).

### Řešení
**1) Kandidátní klíč**
Z definice: známka patří ke dvojici (student, předmět), takže klíč je složený:
- `(student_id, předmět_id)⁺`:
  - start `{student_id, předmět_id}`
  - `student_id → student_jméno` ⇒ přidám `student_jméno`
  - `předmět_id → předmět_název, kredity` ⇒ přidám `předmět_název, kredity`
  - `(student_id, předmět_id) → známka` ⇒ přidám `známka`
  ⇒ mám vše.
Kandidátní klíč: `(student_id, předmět_id)`.

**2) Proč je to porušení 2NF**
2NF říká: neklíčové atributy musí záviset na *celém* složeném klíči.
- `student_jméno` závisí jen na `student_id` (část klíče) ⇒ porušení
- `předmět_název, kredity` závisí jen na `předmět_id` (část klíče) ⇒ porušení

**3) Rozklad (do 3NF rovnou)**
- `Student(student_id PK, student_jméno)`
- `Předmět(předmět_id PK, předmět_název, kredity)`
- `Zápis(student_id FK, předmět_id FK, známka, PK(student_id, předmět_id))`

**4) Proč je to lepší**
- studentovo jméno je uložené jednou,
- údaje o předmětu jsou uložené jednou,
- tabulka Zápis obsahuje jen to, co patří ke vztahu „student–předmět“.

---

## Příklad 3 – 3NF: objednávky, zákazník a PSČ→město (tranzitivní řetěz)

### Zadání
Relace `Objednávka(obj_id, datum, zákazník_id, zákazník_jméno, psč, město)`  
FD:
- `obj_id → datum, zákazník_id`
- `zákazník_id → zákazník_jméno, psč`
- `psč → město`

### Co se tady děje
Objednávka patří zákazníkovi. Zákazník má PSČ. A PSČ určuje město.  
Když to necháš v jedné tabulce, budeš opakovat jméno/PSČ/město v každé objednávce.

### Řešení
**1) Klíč**
`obj_id⁺`:
- start `{obj_id}`
- `obj_id → datum, zákazník_id` ⇒ přidám `datum, zákazník_id`
- `zákazník_id → zákazník_jméno, psč` ⇒ přidám `zákazník_jméno, psč`
- `psč → město` ⇒ přidám `město`
⇒ vše. Kandidátní klíč: `obj_id`.

**2) Kde je problém (3NF)**
Protože:
- `obj_id → zákazník_id` a `zákazník_id → zákazník_jméno, psč`  
  ⇒ `obj_id → zákazník_jméno, psč` tranzitivně
- `obj_id → psč` a `psč → město`  
  ⇒ `obj_id → město` tranzitivně

To je typické porušení 3NF: neklíčový atribut (zákazník_id, psč) určuje další neklíčové atributy.

**3) Rozklad**
- `Objednávka(obj_id PK, datum, zákazník_id FK)`
- `Zákazník(zákazník_id PK, zákazník_jméno, psč FK)`
- `PSČ(psč PK, město)`

---

## Příklad 4 – BCNF: „učebna určuje předmět“ (3NF ještě projde, BCNF ne)

### Zadání
Relace `Rozvrh(učitel, učebna, předmět)`  
FD:
- `(učitel, předmět) → učebna`
- `učebna → předmět`  (učebna je specializovaná jen na jeden předmět)

### Co se tady děje
V jedné učebně se učí vždy stejný předmět. To znamená, že pokud znám učebnu, vím předmět.

### Řešení
**1) Kandidátní klíče**
- `(učitel, předmět)⁺`:
  - start `{učitel, předmět}`
  - `(učitel, předmět) → učebna` ⇒ přidám `učebna`
  ⇒ mám vše ⇒ klíč.
- `(učitel, učebna)⁺`:
  - start `{učitel, učebna}`
  - `učebna → předmět` ⇒ přidám `předmět`
  ⇒ mám vše ⇒ klíč.

Kandidátní klíče: `(učitel, předmět)` a `(učitel, učebna)`.

**2) Proč to není BCNF**
FD `učebna → předmět`: je `učebna` superklíč?  
`učebna⁺ = {učebna, předmět}` (chybí učitel) ⇒ není superklíč ⇒ porušení BCNF.

**3) Rozklad do BCNF**
- `Učebna(učebna PK, předmět)`
- `Rozvrh(učitel, učebna, PK(učitel, učebna))`

---

## Příklad 5 – Zkouškový: faktura v jedné tabulce (2NF i 3NF dohromady)

### Zadání
Relace  
`FakturaPoložka(faktura_id, datum, zákazník_id, zákazník_jméno, produkt_id, produkt_název, cena, množství)`  
FD:
- `faktura_id → datum, zákazník_id`
- `zákazník_id → zákazník_jméno`
- `produkt_id → produkt_název, cena`
- `(faktura_id, produkt_id) → množství`

### Co se tady děje
Jeden řádek je „položka faktury“.  
Jenže do jedné tabulky jsme nacpali i:
- hlavičku faktury (datum, zákazník),
- údaje o zákazníkovi,
- údaje o produktu.

### Řešení
**1) Klíč**
Pro položku faktury přirozeně platí PK `(faktura_id, produkt_id)`.

**2) Problém 2NF (částečné závislosti)**
Neklíčové atributy, které závisí jen na části klíče:
- `faktura_id → datum, zákazník_id`
- `produkt_id → produkt_název, cena`

To je porušení 2NF.

**3) Rozklad (do 3NF/BCNF)**
- `Faktura(faktura_id PK, datum, zákazník_id FK)`
- `Zákazník(zákazník_id PK, zákazník_jméno)`
- `Produkt(produkt_id PK, produkt_název, cena)`
- `Položka(faktura_id FK, produkt_id FK, množství, PK(faktura_id, produkt_id))`

**4) Proč je to nejlepší praxe**
- Jedna faktura může mít libovolně mnoho položek.
- Cena produktu je uložená jednou (a je konzistentní).
- Údaje o zákazníkovi jsou uložené jednou.

---

## Příklad 6 – „Vypadá to nevinně“, ale 3NF padá: zaměstnanec a funkce→tarif

### Zadání
Relace `Zaměstnanec(os_č, jméno, funkce, tarif)`  
FD:
- `os_č → jméno, funkce`
- `funkce → tarif`

### Co se tady děje
Tarif je vlastnost funkce, ne konkrétní osoby.  
Jakmile změníš tarif funkce, musíš to přepsat u všech zaměstnanců s touto funkcí.

### Řešení
**1) Klíč**
`os_č` je klíč, protože určí všechno.

**2) Problém (3NF)**
`os_č → funkce` a `funkce → tarif` ⇒ `os_č → tarif` tranzitivně.  
To je porušení 3NF.

**3) Rozklad**
- `Zaměstnanec(os_č PK, jméno, funkce_id FK)`
- `Funkce(funkce_id PK, název_funkce, tarif)`

---

## Příklad 7 – Složitější uzávěry: více kroků, více klíčů

### Zadání
Relace `R(A, B, C, D, E)`  
FD:
- `A → B`
- `B → C`
- `C, D → E`
- `E → A`

### Co se tady děje
Je to „kruh“: z E dostanu A, z A dostanu B, z B dostanu C…  
A k vytvoření E potřebuji C a D.

### Řešení
**1) Najdi klíče přes uzávěry**

Zkusíme `(C, D)⁺`:
- start `{C, D}`
- `C, D → E` ⇒ `{C, D, E}`
- `E → A` ⇒ `{C, D, E, A}`
- `A → B` ⇒ `{C, D, E, A, B}`
- `B → C` ⇒ C už mám
⇒ mám všechny atributy.  
Kandidátní klíč je `(C, D)`.

Zkusíme `D⁺`:
- start `{D}` nic dalšího neodvodím ⇒ D není klíč.

Zkusíme `E⁺`:
- `{E}`
- `E → A` ⇒ `{E, A}`
- `A → B` ⇒ `{E, A, B}`
- `B → C` ⇒ `{E, A, B, C}`
Chybí D ⇒ E není klíč.

**2) BCNF**
FD `A → B`: je A superklíč?  
`A⁺ = {A, B, C}` (přes B→C), chybí D, E ⇒ není superklíč ⇒ porušení BCNF.

**3) Rozklad do BCNF (postupně)**
Rozložíme podle `A → B`:
- `R1(A, B)`
- `R2(A, C, D, E)`

V `R2` stále platí (odvozeně) `A → C` (protože A→B a B→C), a také `C, D → E` a `E → A`.  
Pokud chceš, dá se pokračovat dál, ale pro cvičení stačí umět udělat první správný rozklad a vysvětlit proč.

---

## Příklad 8 – Rychlá diagnostika: poznáš typ problému?

### Zadání a řešení
1) V buňce je seznam „A12, B15, C99“.  
- Problém: 1NF (neatomická hodnota / opakující se skupina).

2) PK je `(objednávka_id, produkt_id)` a `produkt_id → produkt_název`.  
- Problém: 2NF (závislost na části složeného klíče).

3) `zákazník_id → psč` a `psč → město`, ale město ukládám u zákazníka.  
- Problém: 3NF (tranzitivní závislost).

4) `učebna → předmět`, ale klíč je `(učitel, učebna)`.  
- Problém: BCNF (determinant není superklíč).

---

# Doporučený „mechanický“ postup u jakékoliv úlohy

1. Sepiš FD (co určuje co).
2. Spočti uzávěry a najdi kandidátní klíče.
3. 2NF: je klíč složený? Pokud ano, neexistuje FD z části klíče na neklíčový atribut?
4. 3NF: neexistuje řetězec typu klíč → X → Y, kde X i Y jsou neklíčové?
5. BCNF: u každé FD musí být levá strana superklíč.
6. Rozkládej podle porušující FD a kontroluj, že rozklad dává smysl (tabulky odpovídají „věcem“ a „vztahům“).

---

Pokud chceš, můžu navázat další sadou 10+ „příběhových“ úloh (knihovna, půjčovna, servis, sklad, hospitalizace), kde je vždy:
- krátký příběh,
- ukázková „špatná tabulka“,
- seznam anomálií,
- FD,
- klíče přes uzávěry,
- rozklad do 3NF/BCNF.

# 8. Komplexní příklad – Kompletní postup

Relace:

Rezervace(id_rez, id_hosta, jméno_hosta, pokoj, cena_za_noc)

FD:
- id_rez → id_hosta, pokoj
- id_hosta → jméno_hosta
- pokoj → cena_za_noc

## Krok 1 – Kandidátní klíč

id_rez⁺ = všechny atributy

PK = id_rez

## Krok 2 – 2NF

Jednoduchý klíč → splňuje 2NF.

## Krok 3 – 3NF

Tranzitivní závislosti:

id_rez → id_hosta → jméno_hosta  
id_rez → pokoj → cena_za_noc

Porušení 3NF.

## Krok 4 – Rozklad

Rezervace(id_rez, id_hosta, pokoj)  
Host(id_hosta, jméno_hosta)  
Pokoj(pokoj, cena_za_noc)

Výsledné schéma je v BCNF.

---

# 9. Procvičovací úlohy

## Úloha 1

Projekt(id_projekt, název, id_vedoucí, jméno_vedoucí, kancelář)

FD:
- id_projekt → název, id_vedoucí
- id_vedoucí → jméno_vedoucí, kancelář

Určete:
- kandidátní klíč,
- nejvyšší normální formu,
- rozklad do BCNF.

---

## Úloha 2

R(A, B, C, D)

FD:
- A → B
- B → C
- C → D

Určete:
- kandidátní klíče,
- zda je relace ve 3NF,
- rozklad do BCNF.

---

## Úloha 3

Objednávka(id_obj, id_produkt, název_produktu, cena, množství)

FD:
- id_produkt → název_produktu, cena
- (id_obj, id_produkt) → množství

Určete:
- primární klíč,
- zda je relace ve 2NF,
- rozklad do 3NF.

---

# 2. Příklad z praxe – Školní systém

## Špatný návrh (nenormalizovaný)

Tabulka:

Zápis

| student_id | student_jméno | předmět_id | předmět_název | vyučující | známka |
|------------|---------------|------------|---------------|-----------|--------|
| 1          | Anna          | DB         | Databáze      | Novák     | 1      |
| 1          | Anna          | ALG        | Algoritmy     | Svoboda   | 2      |
| 2          | Petr          | DB         | Databáze      | Novák     | 3      |

---

## Co se zde děje?

### Problém 1 – Redundance
Jméno studenta Anna je uloženo vícekrát.  
Název předmětu Databáze je uložen vícekrát.  
Vyučující Novák je uložen vícekrát.

### Problém 2 – Aktualizační anomálie
Pokud se vyučující Novák přejmenuje, musíme změnit všechny řádky.

### Problém 3 – Mazací anomálie
Když smažeme posledního studenta z předmětu ALG, ztratíme informaci, že předmět existuje.

---

# 3. Přechod do 2NF

Primární klíč je složený:
(student_id, předmět_id)

Podívejme se, co na čem závisí:

- student_id → student_jméno
- předmět_id → předmět_název, vyučující
- (student_id, předmět_id) → známka

Problém:
některé atributy závisí jen na části klíče.

---

## Oprava – rozdělení tabulky

Student  
(student_id, student_jméno)

Předmět  
(předmět_id, předmět_název, vyučující)

Zápis  
(student_id, předmět_id, známka)

---

## Co se změnilo?

- Jméno studenta je uloženo jen jednou.
- Název předmětu je uložen jen jednou.
- Změna se provádí na jednom místě.

Toto je 2NF.

---

# 4. Další problém – Tranzitivní závislost

Teď si představ jiný systém.

## Tabulka objednávek

| objednávka_id | datum | zákazník_id | zákazník_jméno | město |
|---------------|-------|-------------|----------------|-------|
| 100           | 1.3.  | 10          | Novák          | Brno  |
| 101           | 2.3.  | 10          | Novák          | Brno  |

---

## Co se zde děje?

objednávka_id určuje zákazník_id  
zákazník_id určuje zákazník_jméno a město  

Vzniká řetěz závislostí:

objednávka_id → zákazník_id → zákazník_jméno

Tomu se říká tranzitivní závislost.

---

## Problém

Pokud se zákazník přestěhuje, musíme změnit město ve všech objednávkách.

---

# Řešení – 3NF

Rozdělení:

Objednávka  
(objednávka_id, datum, zákazník_id)

Zákazník  
(zákazník_id, zákazník_jméno, město)

---

## Výsledek

- Každá informace je uložena právě tam, kam logicky patří.
- Neexistují tranzitivní závislosti.
- Tabulky jsou ve 3NF.

---

# 5. Příklad, kde 3NF nestačí (BCNF)

## Rozvrh

| učitel | předmět | učebna |
|--------|----------|--------|
| Novák  | DB       | A1     |
| Svoboda| ALG      | A2     |

Platí:
- kombinace (učitel, předmět) určuje učebnu
- každá učebna je vyhrazena jen pro jeden předmět

Tedy:
učebna → předmět

---

## Co to znamená?

učebna není klíč, ale přesto určuje jiný atribut.

To porušuje BCNF.

---

# Řešení

Učebna  
(učebna, předmět)

Rozvrh  
(učitel, učebna)

---

# 6. Velký komplexní příklad – Fakturační systém

## Špatný návrh

| faktura_id | datum | zákazník | produkt | cena | množství |
|------------|-------|----------|---------|------|----------|

Problém:
- cena produktu se opakuje
- jméno zákazníka se opakuje

---

## Co se děje logicky?

- faktura určuje zákazníka
- produkt určuje cenu
- kombinace (faktura, produkt) určuje množství

---

# Správný návrh

Faktura  
(faktura_id, datum, zákazník_id)

Zákazník  
(zákazník_id, jméno)

Produkt  
(produkt_id, název, cena)

Položka  
(faktura_id, produkt_id, množství)

---

# Proč je to lepší?

- Cena produktu je uložena jednou.
- Zákazník je uložen jednou.
- Faktura může mít libovolný počet položek.
- Žádné anomálie.

---
# Normalizace dat – další detailní úlohy s vysvětlením „co se děje“ (včetně řešení)

Poznámka: Úlohy jsou inspirované typickými situacemi z praxe (osoby/telefony, sklad–výrobce, zaměstnanci–funkce–plat, DVD půjčovna, knihovna a úprava ternárních vztahů), které jsou popsané i ve studijním materiálu VŠB-TUO. :contentReference[oaicite:0]{index=0}

---

## Úloha 1: 1NF – více telefonů u osoby

### Zadání
Máš tabulku `Osoba`:

| osoba_id | jméno | příjmení | adresa                 | telefon_domů | telefon_mobil | telefon_práce |
|---------:|-------|----------|------------------------|--------------|---------------|---------------|
| 1        | Jan   | Novák    | Havlíčkova 2 Praha 3   | 789456123    | 601258987     | 125789654     |
| 2        | Petr  | Kovář    | Svatoplukova 15 Brno   | 963852741    | 357951456     | 369852147     |

1. Vysvětli, proč je to v praxi problematické (i když to „vypadá“ v 1NF).
2. Navrhni lepší strukturu tabulek a ukaž, jak by vypadala data po úpravě.

### Řešení (vysvětlení)
#### Co se tady děje špatně
Na první pohled to působí „atomicky“ (každý sloupec má jednu hodnotu), ale ve skutečnosti je to **opakující se skupina**:
- telefon_domů, telefon_mobil, telefon_práce jsou jen různé „výskyty“ stejného typu informace (telefon).
- jakmile bude potřeba 4. telefon (např. služební mobil, fax…), musíš měnit strukturu tabulky (přidávat sloupce), což je špatně pro údržbu i dotazy.
- vyhledávání „podle telefonu“ je komplikované, protože telefon je ve třech sloupcích.

To odpovídá běžnému argumentu k 1NF: opakující se atributy jsou varovný signál a typické řešení je vyčlenit vícenásobnou informaci do samostatné tabulky. :contentReference[oaicite:1]{index=1}

#### Správný návrh
`Osoba(osoba_id, jméno, příjmení, adresa)`  
`Telefon(osoba_id, číslo, typ)`

- `osoba_id` je PK v `Osoba`
- v `Telefon` je (osoba_id, číslo) nebo samostatné `telefon_id` jako PK (prakticky často lepší)
- `osoba_id` v `Telefon` je FK na `Osoba`

#### Ukázka dat po úpravě
`Osoba`:

| osoba_id | jméno | příjmení | adresa               |
|---------:|-------|----------|----------------------|
| 1        | Jan   | Novák    | Havlíčkova 2 Praha 3 |
| 2        | Petr  | Kovář    | Svatoplukova 15 Brno |

`Telefon`:

| osoba_id | číslo     | typ    |
|---------:|-----------|--------|
| 1        | 789456123 | domů   |
| 1        | 601258987 | mobil  |
| 1        | 125789654 | práce  |
| 2        | 963852741 | domů   |
| 2        | 357951456 | mobil  |
| 2        | 369852147 | práce  |

---

## Úloha 2: 2NF – sklad zboží a výrobci (částečná závislost)

### Zadání
Tabulka `Sklad(název_zboží, výrobce, telefon_výrobce, cena, množství)`  
Primární klíč je složený: `(název_zboží, výrobce)`.

1. Popiš, co se stane, když má výrobce více výrobků.
2. Najdi, co porušuje 2NF.
3. Proveď rozklad do 2NF (a rovnou i do 3NF, pokud je to možné).

### Řešení (vysvětlení)
#### Co se tady děje špatně
Telefon na výrobce se bude opakovat v každém řádku pro stejnou firmu. To je redundance.

Anomálie:
- Aktualizační: změna telefonu výrobce → měníš mnoho řádků
- Mazací: smažeš poslední výrobek výrobce → ztratíš i telefon na výrobce

Přesně tento typ problému je typickým motivem pro 2NF u složeného klíče. :contentReference[oaicite:2]{index=2}

#### Funkční závislosti
- `(název_zboží, výrobce) → cena, množství`
- `výrobce → telefon_výrobce`  (telefon závisí jen na části klíče)

To je porušení 2NF (neklíčový atribut závisí pouze na části složeného PK).

#### Rozklad
`Výrobce(výrobce_id, název_výrobce, telefon)`  
`Výrobek(výrobek_id, název_zboží, výrobce_id, cena, množství)`

Alternativně bez surrogate ID (pokud to dovolí zadání):
`Výrobce(výrobce, telefon)`  
`Sklad(název_zboží, výrobce, cena, množství)` a `Sklad.výrobce` jako FK

Výsledek splňuje 2NF a typicky i 3NF, protože telefon už není tranzitivně v tabulce skladu.

---

## Úloha 3: 3NF – zaměstnanci, funkce a plat (tranzitivní závislost)

### Zadání
Tabulka:

`Zaměstnanec(os_č, jméno, příjmení, ulice, č_pop, č_or, město, PSČ, funkce, plat)`

Platí:
- `os_č → (všechny ostatní atributy)`
- `funkce → plat`
- `město → PSČ` (zjednodušeně: jedno město má jedno PSČ)

1. Vysvětli lidsky, proč je „funkce → plat“ problém v jedné tabulce zaměstnanců.
2. Ukaž, co je tranzitivní závislost.
3. Navrhni rozklad do 3NF.

### Řešení (vysvětlení)
#### Co se tady děje špatně
„Plat“ není vlastnost konkrétní osoby, ale vlastnost její „funkce“ (pokud systém chápe plat jako tarif pro funkci). Takže:
- když změníš tarif pro „Junior Developer“, musíš měnit platy všech juniorů.
- když omylem změníš plat jen u jednoho juniora, databáze začne odporovat vlastní logice.

Tento typ příkladu (závislost platu na funkci + další závislost PSČ na městě) je typická ukázka porušení 3NF. :contentReference[oaicite:3]{index=3}

#### Tranzitivní závislosti
- `os_č → funkce` a `funkce → plat` tedy `os_č → plat` přes prostřední atribut `funkce`
- `os_č → město` a `město → PSČ` tedy `os_č → PSČ` přes `město`

To porušuje 3NF: neklíčové atributy (funkce, město) určují jiné neklíčové atributy (plat, PSČ).

#### Rozklad do 3NF
`Zaměstnanec(os_č, jméno, příjmení, ulice, č_pop, č_or, město, funkce_id)`  
`Funkce(funkce_id, název_funkce, plat)`  
`Město(město, PSČ)`  (nebo `PSČ(PSČ, město)` podle toho, co je determinující)

- `Zaměstnanec.funkce_id` je FK na `Funkce`
- `Zaměstnanec.město` je FK na `Město`

---

## Úloha 4: DVD půjčovna – postup od 1NF přes 2NF až po 3NF

### Zadání
V „jedné tabulce“ eviduješ půjčování DVD:

`Výpůjčky(výpůjčka_id, datum, zákazník_jméno, zákazník_příjmení, ulice, číslo, město, PSČ, dvd_id, název_filmu, kategorie, datum_vrácení)`

1. Popiš, jaké typy anomálií hrozí.
2. Rozděl návrh do tabulek tak, aby:
   - zákazník byl evidován jednou,
   - film byl evidován jednou,
   - výpůjčka mohla obsahovat více položek (více DVD najednou),
   - bylo možné evidovat datum vrácení pro každou položku.

### Řešení (vysvětlení)
#### Co se tady děje špatně
- Data o zákazníkovi se opakují u každé výpůjčky a dokonce u každé položky.
- Data o filmu se opakují pokaždé, když se půjčí.
- Pokud výpůjčka obsahuje více DVD, vzniká „opakující se skupina“ (v praxi to často končí duplikací řádků).

V materiálu je ukázaný přesně tento směr: rozdělit data na smysluplné tabulky (zákazník, výpůjčka, položka, dvd, město/PSČ). :contentReference[oaicite:4]{index=4}

#### Cílová struktura (minimálně)
`Zákazník(zákazník_id, jméno, příjmení, ulice, číslo, PSČ)`  
`Město(PSČ, město)`  
`DVD(dvd_id, název_filmu, kategorie)`  
`Výpůjčka(výpůjčka_id, zákazník_id, datum)`  
`Položka(položka_id, výpůjčka_id, dvd_id, datum_vrácení)`

Co to řeší:
- zákazník a DVD se neopakují v položkách,
- výpůjčka může mít mnoho položek (1:N),
- datum_vrácení patří k položce (protože každé DVD může být vráceno jindy),
- PSČ a město lze vyčlenit do samostatné tabulky, pokud to dává smysl (omezení redundance). :contentReference[oaicite:5]{index=5}

---

## Úloha 5: Knihovna – ternární vztah „čtenář si půjčuje exemplář“ a proč nestačí dvě vazby

### Zadání
V knihovně řešíš situaci: čtenář provede výpůjčku a v rámci výpůjčky si vypůjčí jeden nebo více exemplářů.

Cíle:
- evidovat výpůjčky (datum, kdo, kolik kusů),
- evidovat konkrétní exempláře (evidenční číslo),
- evidovat vrácení pro každou položku výpůjčky zvlášť.

1. Popiš, proč vztah „Čtenář – Kniha“ nestačí (kniha může mít více exemplářů).
2. Navrhni entity a vztahy tak, aby to šlo realizovat relačně.
3. Uveď výsledné tabulky (relační schéma) včetně PK a FK.

### Řešení (vysvětlení)
#### 1) Co se tady děje a proč je to „zrádné“
Pokud bys propojila přímo `Čtenář` a `Kniha`, nevíš:
- který konkrétní kus (exemplář) byl půjčen,
- jak řešit dvě stejné knihy se stejným názvem,
- jak evidovat, že v jedné výpůjčce bylo více kusů.

Materiál ukazuje typickou opravu:
- zavést entitu `Exemplář` a přesunout do ní evidenční číslo,
- vztah M:N odstranit pomocí vazební entity `Položka`. :contentReference[oaicite:6]{index=6}

#### 2) Entity a vztahy (logika)
- `Kniha` je „titul“ (název, autor, žánr…)
- `Exemplář` je konkrétní kus knihy (má evidenční číslo) a patří právě jedné knize
- `Výpůjčka` je „hlavička“ (kdo, kdy)
- `Položka` je „řádek výpůjčky“ (který exemplář, datum vrácení)

#### 3) Výsledné relační schéma (jeden z čistých návrhů)

`Čtenář(čtenář_id PK, rodné_číslo, jméno, bydliště)`  
`Kniha(kniha_id PK, název, popis, autor, žánr)`  
`Exemplář(exemplář_id PK, kniha_id FK -> Kniha, evidenční_číslo UNIQUE)`  
`Výpůjčka(výpůjčka_id PK, čtenář_id FK -> Čtenář, datum)`  
`Položka(položka_id PK, výpůjčka_id FK -> Výpůjčka, exemplář_id FK -> Exemplář, datum_vrácení)`

Vazby:
- Kniha 1:N Exemplář
- Čtenář 1:N Výpůjčka
- Výpůjčka 1:N Položka
- Exemplář 1:N Položka (v čase; v jednom okamžiku bys doplnila integritní pravidla)

To odpovídá běžné úpravě ternárních vztahů pro praktickou evidenci výpůjček. :contentReference[oaicite:7]{index=7}

---

## Úloha 6: Smíšená – najdi problém s NF podle „příběhu“ a oprav

### Zadání
Firma vede tabulku:

`Zakázka(zakázka_id, datum, zákazník_id, zákazník_název, zákazník_město, pracovník_id, pracovník_jméno, sazba_za_hodinu, odpracované_hodiny)`

Pravidla (z analýzy):
- `zakázka_id → datum, zákazník_id, pracovník_id, odpracované_hodiny`
- `zákazník_id → zákazník_název, zákazník_město`
- `pracovník_id → pracovník_jméno`
- `pracovník_id → sazba_za_hodinu`

1. Popiš, které informace se budou opakovat a jaké anomálie tím vzniknou.
2. Urči, zda tabulka porušuje 2NF nebo 3NF (a proč).
3. Navrhni rozklad.

### Řešení (vysvětlení)
#### 1) Co se opakuje a proč
- Zákazník se bude opakovat u mnoha zakázek → název a město opakovaně.
- Pracovník se bude opakovat u mnoha zakázek → jméno a sazba opakovaně.

Anomálie:
- změna sazby pracovníka → přepis mnoha řádků
- smazání poslední zakázky zákazníka → ztráta údajů o zákazníkovi

#### 2) 2NF vs 3NF
PK je jednoduchý (`zakázka_id`), takže 2NF je automaticky splněná.

Porušení je v 3NF:
- `zakázka_id → zákazník_id → zákazník_název, zákazník_město` (tranzitivně)
- `zakázka_id → pracovník_id → pracovník_jméno, sazba_za_hodinu` (tranzitivně)

#### 3) Rozklad
`Zakázka(zakázka_id PK, datum, zákazník_id FK, pracovník_id FK, odpracované_hodiny)`  
`Zákazník(zákazník_id PK, zákazník_název, zákazník_město)`  
`Pracovník(pracovník_id PK, pracovník_jméno, sazba_za_hodinu)`

---

## Úloha 7: Krátká kontrolní – poznáš normální formu jen z popisu?

### Zadání
U každé situace napiš, jestli je problém typický pro 1NF, 2NF, nebo 3NF.

1. V jedné buňce ukládám „A12, B15, C99“ jako seznam kódů.
2. Mám tabulku s PK (id_objednávky, id_produktu) a sloupec `název_produktu` závisí jen na id_produktu.
3. Mám tabulku zákazníků, kde platí `město → PSČ`, ale u zákazníka ukládám i PSČ.

### Řešení
1. 1NF (neatomická hodnota / opakující se skupina)
2. 2NF (částečná závislost na části složeného klíče)
3. 3NF (tranzitivní závislost přes neklíčový atribut)

---

## Úloha 8: Vlastní mini-projekt (na procvičení) – bez řešení

### Zadání
Navrhni normalizované schéma pro servisní systém:

- zákazník může mít více zařízení,
- zařízení má výrobce a model,
- každá servisní zakázka je na jedno zařízení,
- zakázka má více úkonů (úkon má název, cenu),
- chceme evidovat i technika, který zakázku provedl (technik má sazbu).

Úkol:
1. Navrhni nejprve jednu „velkou tabulku“ (schválně špatně).
2. Popiš anomálie.
3. Rozděl do tabulek (alespoň do 3NF).
4. U každé tabulky označ PK a FK.

---

Když chceš, doplním k Úloze 8 kompletní řešení krok za krokem ve stejném stylu (včetně vysvětlení, proč přesně která tabulka vznikla).

# 7. Jak o tom přemýšlet intuitivně

Polož si otázky:

1. Opakuje se nějaká informace zbytečně?
2. Změnil bych tuto informaci na více místech?
3. Smazáním jednoho řádku neztratím důležitá data?
4. Závisí nějaký atribut na jiném neklíčovém atributu?

Pokud ano, je potřeba další normalizace.

---

# 8. Shrnutí jednoduchými slovy

1NF – žádné seznamy v buňkách  
2NF – žádná část klíče nesmí určovat data sama  
3NF – žádný neklíčový atribut nesmí určovat jiný neklíčový atribut  
BCNF – každá závislost musí vycházet z klíče  
