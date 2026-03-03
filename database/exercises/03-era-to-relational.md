# Cvičení 3 – Převod ERA modelu do relačního modelu  
## Normalizace dat (1NF, 2NF, 3NF, BCNF)

Tento dokument je podpůrný materiál pro studenty. Je psaný tak, aby:
- vysvětlil **co přesně** normální formy říkají,
- ukázal **proč** existují (anomálie),
- dal **mechanický postup** řešení,
- obsahoval **typické příklady** a rozklady.

---

## 1) Proč normalizujeme

Normalizace je metoda návrhu relační databáze, která má za cíl:

- odstranit **redundanci** (zbytečné opakování dat),
- zabránit **anomáliím** (problémům při vkládání, mazání a aktualizaci),
- zajistit **konzistenci** dat (jedna informace na jednom místě).

### Typické anomálie

#### Aktualizační anomálie
Jedna informace je uložená vícekrát. Při změně je nutné upravit více řádků → snadno vznikne rozpor.

#### Mazací anomálie
Smazáním jednoho řádku ztratíme i jinou důležitou informaci (např. kontakt na výrobce).

#### Vkládací anomálie
Nelze vložit informaci bez jiné, která s ní nesouvisí (např. nelze vložit předmět bez zápisu studenta).

<img width="1808" height="1010" alt="image" src="https://github.com/user-attachments/assets/365e359c-d99e-4b25-8436-2064e8b08fdd" />


---

## 2) Základní pojmy

### Relace, atribut, n-tice
- **Relace** = tabulka
- **Atribut** = sloupec
- **N-tice** = řádek

### Funkční závislost (FD)
Zápis: `X → Y`  
Význam: pokud dva řádky mají stejnou hodnotu `X`, musí mít i stejnou hodnotu `Y`.

Příklad: `zákazník_id → zákazník_jméno, město`

### Uzávěr (closure) `X⁺`
`X⁺` = množina atributů, které umím odvodit z `X` pomocí FD.

### Kandidátní klíč
Množina atributů `K`, která:
1. určí všechny atributy relace (`K⁺` obsahuje vše),
2. je **minimální** (po odebrání libovolného atributu už neplatí bod 1).

### Superklíč
Jakákoliv množina atributů, která určí všechny atributy (nemusí být minimální).

### Prime atribut
Atribut, který je součástí **nějakého** kandidátního klíče.

---

## 3) Normální formy – definice + jak je poznat

### 1NF – První normální forma
Relace je v **1NF**, pokud:
- všechny hodnoty jsou **atomické** (jedna hodnota v buňce),
- neexistují **opakující se skupiny**.

**Varovné signály 1NF:**
- seznamy v buňkách (`"A12, B45, C33"`)
- opakované sloupce stejného významu (`telefon1, telefon2, telefon3` / `telefon_domů, telefon_mobil...`)

**Typická oprava:** vyčlenit opakující se data do samostatné tabulky (1:N).

---

### 2NF – Druhá normální forma
2NF řeší **částečné závislosti** a má smysl hlavně u tabulek se **složeným klíčem**.

Relace je v **2NF**, pokud:
- je v 1NF a
- žádný neklíčový atribut nezávisí na **části** složeného klíče.

**Varovný signál 2NF:**
- PK je `(A, B)`, ale existuje FD `A → něco` nebo `B → něco` (pro neklíčový atribut).

**Typická oprava:** rozdělit tabulku na části podle toho, na čem atributy závisí.

---

### 3NF – Třetí normální forma
3NF řeší **tranzitivní závislosti** (řetězení přes neklíčové atributy).

Intuitivně: nesmí existovat `klíč → X → Y`, kde `X` i `Y` jsou neklíčové.

Praktická kontrola:
Pro každou FD `X → A` musí platit aspoň jedno:
- `X` je superklíč, nebo
- `A` je prime atribut.

**Varovný signál 3NF:**
- v jedné tabulce mícháš atributy více „věcí“ (objednávka + zákazník, zaměstnanec + funkce, atd.)
- neklíčový atribut určuje další neklíčový atribut (`funkce → tarif`, `psč → město`)

**Typická oprava:** vyčlenit entitu, které atribut logicky patří (Zákazník, Funkce, PSČ…).

---

### BCNF – Boyce–Coddova normální forma
BCNF je přísnější než 3NF.

Relace je v **BCNF**, pokud:
- pro každou netriviální FD `X → Y` platí: `X` je **superklíč**.

**Varovný signál BCNF:**
- existuje FD, kde determinant (levá strana) není superklíč (např. `učebna → předmět`).

---

## 4) Mechanický postup řešení (zkouškový algoritmus)

1. Sepiš FD (co určuje co).
2. Spočti uzávěry a najdi kandidátní klíče.
3. 1NF: nejsou seznamy v buňkách / opakující se skupiny?
4. 2NF: je klíč složený? existuje závislost na části klíče?
5. 3NF: existuje řetězec typu `klíč → X → Y`, kde X i Y jsou neklíčové?
6. BCNF: v každé FD musí být levá strana superklíč.
7. Rozkládej podle porušující FD a kontroluj smysl tabulek (entity/vztahy + PK/FK).

---
<img width="1840" height="862" alt="image" src="https://github.com/user-attachments/assets/8fb0284a-6c8f-4825-8fc2-9b37e31620ac" />
<img width="1888" height="854" alt="image" src="https://github.com/user-attachments/assets/244afbf5-5739-4736-9672-256ad7bbc5ce" />
<img width="1842" height="874" alt="image" src="https://github.com/user-attachments/assets/5996cf64-77d1-4c4e-b652-b6fac4033ccb" />
<img width="1848" height="858" alt="image" src="https://github.com/user-attachments/assets/fba964c4-bb93-488f-91ff-a84fe65d32a3" />

---

<img width="1966" height="846" alt="image" src="https://github.com/user-attachments/assets/40792f0f-e08f-4297-b2f2-d60353af9d0c" />

---
<img width="1926" height="982" alt="image" src="https://github.com/user-attachments/assets/c4bc9f73-e210-4b77-b1b5-9e519883b353" />

---

## 5) Příklady (od jednoduchých po komplexní)

### Příklad 1 – Klíč přes uzávěr + kontrola 3NF/BCNF

**Relace:** `R(A, B, C, D)`  
**FD:**
- `A → B`
- `B → C`
- `A → D`

#### 1) Uzávěr `A⁺`
- `{A}`
- `A → B` ⇒ `{A, B}`
- `B → C` ⇒ `{A, B, C}`
- `A → D` ⇒ `{A, B, C, D}`

⇒ `A` je kandidátní klíč.

#### 2) 2NF
Klíč je jednoduchý ⇒ 2NF automaticky.

#### 3) 3NF a BCNF
FD `B → C`:
- `B` není superklíč (`B⁺ = {B, C}`),
- `C` není prime (kandidátní klíč je jen `A`).
⇒ relace není v 3NF ani BCNF.

#### 4) Rozklad do BCNF
- `R1(B, C)`
- `R2(A, B, D)`

---

### Příklad 2 – 2NF: „Zápis studenta“

**Relace:**  
`Zápis(student_id, předmět_id, student_jméno, předmět_název, kredity, známka)`  
**FD:**
- `student_id → student_jméno`
- `předmět_id → předmět_název, kredity`
- `(student_id, předmět_id) → známka`

#### 1) Klíč
Kandidátní klíč: `(student_id, předmět_id)`.

#### 2) Proč to porušuje 2NF
- `student_id → student_jméno` (část klíče)
- `předmět_id → předmět_název, kredity` (část klíče)

#### 3) Rozklad (do 3NF rovnou)
- `Student(student_id PK, student_jméno)`
- `Předmět(předmět_id PK, předmět_název, kredity)`
- `Zápis(student_id FK, předmět_id FK, známka, PK(student_id, předmět_id))`

---

### Příklad 3 – 3NF: objednávky + zákazník + PSČ→město

**Relace:** `Objednávka(obj_id, datum, zákazník_id, zákazník_jméno, psč, město)`  
**FD:**
- `obj_id → datum, zákazník_id`
- `zákazník_id → zákazník_jméno, psč`
- `psč → město`

#### Klíč
`obj_id` je klíč (určí vše).

#### Problém (3NF)
- `obj_id → zákazník_id → zákazník_jméno, psč`
- `obj_id → psč → město`

#### Rozklad
- `Objednávka(obj_id PK, datum, zákazník_id FK)`
- `Zákazník(zákazník_id PK, zákazník_jméno, psč FK)`
- `PSČ(psč PK, město)`

---

### Příklad 4 – BCNF: „učebna určuje předmět“

**Relace:** `Rozvrh(učitel, učebna, předmět)`  
**FD:**
- `(učitel, předmět) → učebna`
- `učebna → předmět`

#### Kandidátní klíče
- `(učitel, předmět)`
- `(učitel, učebna)`

#### Proč to není BCNF
`učebna → předmět` – `učebna` není superklíč.

#### Rozklad do BCNF
- `Učebna(učebna PK, předmět)`
- `Rozvrh(učitel, učebna, PK(učitel, učebna))`

---

### Příklad 5 – Faktura v jedné tabulce (2NF i 3NF)

**Relace:**  
`FakturaPoložka(faktura_id, datum, zákazník_id, zákazník_jméno, produkt_id, produkt_název, cena, množství)`  
**FD:**
- `faktura_id → datum, zákazník_id`
- `zákazník_id → zákazník_jméno`
- `produkt_id → produkt_název, cena`
- `(faktura_id, produkt_id) → množství`

#### Klíč
PK: `(faktura_id, produkt_id)`.

#### 2NF problém
- `faktura_id → datum, zákazník_id`
- `produkt_id → produkt_název, cena`

#### 3NF problém
`faktura_id → zákazník_id → zákazník_jméno`

#### Rozklad do 3NF/BCNF
- `Faktura(faktura_id PK, datum, zákazník_id FK)`
- `Zákazník(zákazník_id PK, zákazník_jméno)`
- `Produkt(produkt_id PK, produkt_název, cena)`
- `Položka(faktura_id FK, produkt_id FK, množství, PK(faktura_id, produkt_id))`

---

### Příklad 6 – Zaměstnanec a funkce→tarif

**Relace:** `Zaměstnanec(os_č, jméno, funkce, tarif)`  
**FD:**
- `os_č → jméno, funkce`
- `funkce → tarif`

#### Klíč
`os_č` je klíč.

#### Problém (3NF)
`os_č → funkce → tarif` (tranzitivně).

#### Rozklad
- `Zaměstnanec(os_č PK, jméno, funkce_id FK)`
- `Funkce(funkce_id PK, název_funkce, tarif)`

---

## 6) „Zrádné“ příklady k 1NF (opakující se skupiny)

### Více telefonů u osoby
Špatně:
- `Osoba(osoba_id, ..., telefon_domů, telefon_mobil, telefon_práce)`

Lepší:
- `Osoba(osoba_id PK, jméno, příjmení, adresa)`
- `Telefon(telefon_id PK, osoba_id FK, číslo, typ)`

---

## 7) Kompletní postup na komplexním zadání

**Relace:** `Rezervace(id_rez, id_hosta, jméno_hosta, pokoj, cena_za_noc)`  
**FD:**
- `id_rez → id_hosta, pokoj`
- `id_hosta → jméno_hosta`
- `pokoj → cena_za_noc`

### Krok 1 – Klíč
`id_rez` je klíč (`id_rez⁺` určí vše).

### Krok 2 – 2NF
Jednoduchý klíč ⇒ OK.

### Krok 3 – 3NF
Tranzitivně:
- `id_rez → id_hosta → jméno_hosta`
- `id_rez → pokoj → cena_za_noc`

⇒ porušení 3NF.

### Krok 4 – Rozklad
- `Rezervace(id_rez PK, id_hosta FK, pokoj FK)`
- `Host(id_hosta PK, jméno_hosta)`
- `Pokoj(pokoj PK, cena_za_noc)`

---

## 8) Procvičovací úlohy (s řešením)

### Úloha 1
`Projekt(id_projekt, název, id_vedoucí, jméno_vedoucí, kancelář)`  
FD:
- `id_projekt → název, id_vedoucí`
- `id_vedoucí → jméno_vedoucí, kancelář`

**Klíč:** `id_projekt`  
**NF:** není 3NF  
**Rozklad do BCNF:**
- `Projekt(id_projekt PK, název, id_vedoucí FK)`
- `Vedoucí(id_vedoucí PK, jméno_vedoucí, kancelář)`

---

### Úloha 2
`R(A, B, C, D)`  
FD:
- `A → B`
- `B → C`
- `C → D`

**Klíč:** `A`  
**3NF?** ne  
**BCNF rozklad (jedna korektní varianta):**
- `R1(A, B)`
- `R2(B, C)`
- `R3(C, D)`

---

### Úloha 3
`Objednávka(id_obj, id_produkt, název_produktu, cena, množství)`  
FD:
- `id_produkt → název_produktu, cena`
- `(id_obj, id_produkt) → množství`

**PK:** `(id_obj, id_produkt)`  
**2NF?** ne  
**Rozklad do 3NF:**
- `Produkt(id_produkt PK, název_produktu, cena)`
- `Položka(id_obj, id_produkt, množství, PK(id_obj, id_produkt))`

---

## 9) Rychlá diagnostika (5 vteřin)

1. V buňce je seznam hodnot (`"A12, B15, C99"`) → **1NF**  
2. PK `(obj_id, prod_id)` a `prod_id → prod_název` → **2NF**  
3. `zákazník_id → psč` a `psč → město` → **3NF**  
4. `učebna → předmět`, ale učebna není klíč → **BCNF**

---

## 10) Mini-projekt – servisní systém (kostra návrhu do 3NF)

Typické tabulky:
- `Zákazník(zákazník_id PK, ...)`
- `Zařízení(zařízení_id PK, zákazník_id FK, výrobce, model, ...)`
- `Technik(technik_id PK, jméno, sazba_za_hodinu)`
- `Zakázka(zakázka_id PK, zařízení_id FK, technik_id FK, datum, ...)`
- `Úkon(úkon_id PK, název, cena)`
- `PoložkaZakázky(zakázka_id FK, úkon_id FK, počet, PK(zakázka_id, úkon_id))`

---
# Procvičování normalizace – sada úloh s tabulkami a řešením (1NF–BCNF)

> Konvence:
> - PK = primární klíč, FK = cizí klíč
> - FD = funkční závislosti
> - Kandidátní klíč hledám uzávěrem (closure)
> - Cíl: typicky 3NF nebo BCNF

---

## Úloha 1 (1NF) – Objednávka se seznamem produktů v jedné buňce

### Špatná tabulka (není 1NF)
`Objednávka(id_obj, zákazník, produkty)`

| id_obj | zákazník | produkty           |
|------:|----------|--------------------|
| 1001  | Novák    | A12, B45, C33      |
| 1002  | Svoboda  | B45                |

### Problém
- `produkty` není atomická hodnota (seznam) ⇒ porušení 1NF.

### Řešení (rozklad)
- `Objednávka(id_obj PK, zákazník)`
- `Položka(id_obj FK, produkt_kód FK, PK(id_obj, produkt_kód))`

### Data po rozkladu
`Objednávka`

| id_obj | zákazník |
|------:|----------|
| 1001  | Novák    |
| 1002  | Svoboda  |

`Položka`

| id_obj | produkt_kód |
|------:|-------------|
| 1001  | A12         |
| 1001  | B45         |
| 1001  | C33         |
| 1002  | B45         |

---

## Úloha 2 (1NF prakticky) – Více emailů ve sloupcích (opakující se skupina)

### Špatná tabulka
`Kontakt(osoba_id, jméno, email1, email2, email3)`

| osoba_id | jméno | email1           | email2             | email3 |
|--------:|-------|------------------|--------------------|--------|
| 1       | Jana  | jana@a.cz        | jana.work@b.cz     | NULL   |
| 2       | Petr  | petr@a.cz        | NULL               | NULL   |

### Problém
- email je jeden typ informace, ale je rozsekaný do více sloupců.
- 4. email = změna schématu.

### Řešení (lepší návrh)
- `Osoba(osoba_id PK, jméno)`
- `Email(email_id PK, osoba_id FK, email, typ)`  
  *(typ třeba: osobní/pracovní)*

### Data po rozkladu
`Osoba`

| osoba_id | jméno |
|--------:|-------|
| 1       | Jana  |
| 2       | Petr  |

`Email`

| email_id | osoba_id | email          | typ     |
|--------:|---------:|----------------|---------|
| 1       | 1        | jana@a.cz      | osobní  |
| 2       | 1        | jana.work@b.cz | práce   |
| 3       | 2        | petr@a.cz      | osobní  |

---

## Úloha 3 (2NF) – Sklad: zboží × výrobce (telefon výrobce se opakuje)

### Špatná tabulka
`Sklad(název_zboží, výrobce, telefon_výrobce, cena, množství)`  
PK: `(název_zboží, výrobce)`

| název_zboží | výrobce | telefon_výrobce | cena | množství |
|------------|---------|------------------|-----:|--------:|
| Myš        | Logitech| 111222333        | 500  | 20      |
| Klávesnice | Logitech| 111222333        | 900  | 10      |
| Monitor    | Dell    | 999888777        | 5000 | 5       |

### FD
- `(název_zboží, výrobce) → cena, množství`
- `výrobce → telefon_výrobce`

### Problém
- `telefon_výrobce` závisí jen na části složeného klíče (`výrobce`) ⇒ porušení 2NF.

### Rozklad (do 3NF rovnou)
- `Výrobce(výrobce PK, telefon_výrobce)`
- `Sklad(název_zboží, výrobce FK, cena, množství, PK(název_zboží, výrobce))`

### Data po rozkladu
`Výrobce`

| výrobce  | telefon_výrobce |
|---------|------------------|
| Logitech| 111222333        |
| Dell    | 999888777        |

`Sklad`

| název_zboží | výrobce  | cena | množství |
|------------|----------|-----:|--------:|
| Myš        | Logitech | 500  | 20      |
| Klávesnice | Logitech | 900  | 10      |
| Monitor    | Dell     | 5000 | 5       |

---

## Úloha 4 (2NF) – Kurzy a studenti

### Špatná tabulka
`Zápis(student_id, kurz_id, student_jméno, kurz_název, lektor, známka)`  
PK: `(student_id, kurz_id)`

| student_id | kurz_id | student_jméno | kurz_název | lektor  | známka |
|----------:|--------:|---------------|------------|---------|------:|
| 1         | 10      | Anna          | DB         | Novák   | 1     |
| 1         | 11      | Anna          | ALG        | Svoboda | 2     |
| 2         | 10      | Petr          | DB         | Novák   | 3     |

### FD
- `student_id → student_jméno`
- `kurz_id → kurz_název, lektor`
- `(student_id, kurz_id) → známka`

### Problém
- částečné závislosti na části klíče ⇒ porušení 2NF.

### Rozklad
- `Student(student_id PK, student_jméno)`
- `Kurz(kurz_id PK, kurz_název, lektor)`
- `Zápis(student_id FK, kurz_id FK, známka, PK(student_id, kurz_id))`

### Data po rozkladu
`Student`

| student_id | student_jméno |
|----------:|---------------|
| 1         | Anna          |
| 2         | Petr          |

`Kurz`

| kurz_id | kurz_název | lektor  |
|-------:|------------|---------|
| 10     | DB         | Novák   |
| 11     | ALG        | Svoboda |

`Zápis`

| student_id | kurz_id | známka |
|----------:|--------:|------:|
| 1         | 10      | 1     |
| 1         | 11      | 2     |
| 2         | 10      | 3     |

---

## Úloha 5 (3NF) – Objednávky a zákazník (tranzitivní závislost)

### Špatná tabulka
`Objednávka(obj_id, datum, zákazník_id, zákazník_jméno, zákazník_město)`  
PK: `obj_id`

| obj_id | datum     | zákazník_id | zákazník_jméno | zákazník_město |
|------:|-----------|------------:|----------------|----------------|
| 100   | 2026-03-01| 10          | Novák          | Brno           |
| 101   | 2026-03-02| 10          | Novák          | Brno           |

### FD
- `obj_id → datum, zákazník_id`
- `zákazník_id → zákazník_jméno, zákazník_město`

### Problém
- `obj_id → zákazník_id → (jméno, město)` ⇒ tranzitivní závislost ⇒ porušení 3NF.

### Rozklad
- `Objednávka(obj_id PK, datum, zákazník_id FK)`
- `Zákazník(zákazník_id PK, zákazník_jméno, zákazník_město)`

### Data po rozkladu
`Zákazník`

| zákazník_id | zákazník_jméno | zákazník_město |
|-----------:|----------------|----------------|
| 10         | Novák          | Brno           |

`Objednávka`

| obj_id | datum      | zákazník_id |
|------:|------------|------------:|
| 100   | 2026-03-01 | 10          |
| 101   | 2026-03-02 | 10          |

---

## Úloha 6 (3NF) – Zaměstnanci: funkce → tarif

### Špatná tabulka
`Zaměstnanec(os_č, jméno, funkce, tarif)`  
PK: `os_č`

| os_č | jméno | funkce   | tarif |
|----:|-------|----------|------:|
| 1   | Jana  | Junior   | 35000 |
| 2   | Petr  | Junior   | 35000 |
| 3   | Eva   | Senior   | 65000 |

### FD
- `os_č → jméno, funkce`
- `funkce → tarif`

### Problém
- `os_č → funkce → tarif` ⇒ porušení 3NF.

### Rozklad
- `Zaměstnanec(os_č PK, jméno, funkce_id FK)`
- `Funkce(funkce_id PK, název, tarif)`

### Data po rozkladu (ukázka)
`Funkce`

| funkce_id | název  | tarif |
|---------:|--------|------:|
| 1        | Junior | 35000 |
| 2        | Senior | 65000 |

`Zaměstnanec`

| os_č | jméno | funkce_id |
|----:|-------|----------:|
| 1   | Jana  | 1         |
| 2   | Petr  | 1         |
| 3   | Eva   | 2         |

---

## Úloha 7 (BCNF) – Učebna určuje předmět (klíč je složený)

### Špatná tabulka
`Rozvrh(učitel, učebna, předmět)`

| učitel  | učebna | předmět |
|---------|--------|---------|
| Novák   | A1     | DB      |
| Svoboda | A2     | ALG     |
| Novák   | A1     | DB      |

### FD
- `(učitel, předmět) → učebna`
- `učebna → předmět`

### Kandidátní klíče
- `(učitel, předmět)`
- `(učitel, učebna)`

### Proč to není BCNF
- `učebna → předmět` a `učebna` není superklíč ⇒ porušení BCNF.

### Rozklad do BCNF
- `Učebna(učebna PK, předmět)`
- `Rozvrh(učitel, učebna FK, PK(učitel, učebna))`

---

## Úloha 8 (mix 2NF + 3NF) – Faktura a položky v jedné tabulce

### Špatná tabulka
`FakturaPoložka(faktura_id, datum, zákazník_id, zákazník_jméno, produkt_id, produkt_název, cena, množství)`  
PK: `(faktura_id, produkt_id)`

| faktura_id | datum      | zákazník_id | zákazník_jméno | produkt_id | produkt_název | cena | množství |
|----------:|------------|------------:|----------------|-----------:|---------------|-----:|--------:|
| 200       | 2026-03-01 | 10          | Novák          | 7          | Myš           | 500  | 2       |
| 200       | 2026-03-01 | 10          | Novák          | 8          | Klávesnice    | 900  | 1       |

### FD
- `faktura_id → datum, zákazník_id`
- `zákazník_id → zákazník_jméno`
- `produkt_id → produkt_název, cena`
- `(faktura_id, produkt_id) → množství`

### Problémy
- 2NF: atributy závisí na části klíče (`faktura_id` / `produkt_id`)
- 3NF: tranzitivně `faktura_id → zákazník_id → zákazník_jméno`

### Rozklad do 3NF/BCNF
- `Faktura(faktura_id PK, datum, zákazník_id FK)`
- `Zákazník(zákazník_id PK, zákazník_jméno)`
- `Produkt(produkt_id PK, produkt_název, cena)`
- `Položka(faktura_id FK, produkt_id FK, množství, PK(faktura_id, produkt_id))`

---

## Úloha 9 (3NF) – Knihovna: výpůjčky (hlavička + položky)

### Špatná tabulka
`Výpůjčka(výpůjčka_id, datum, čtenář_id, čtenář_jméno, exemplář_id, název_knihy, datum_vrácení)`

| výpůjčka_id | datum      | čtenář_id | čtenář_jméno | exemplář_id | název_knihy     | datum_vrácení |
|------------:|------------|----------:|--------------|------------:|-----------------|--------------|
| 500         | 2026-03-01 | 1         | Jana         | 100         | Databáze 1      | 2026-03-10   |
| 500         | 2026-03-01 | 1         | Jana         | 101         | Databáze 1      | NULL         |

### FD (typicky)
- `výpůjčka_id → datum, čtenář_id`
- `čtenář_id → čtenář_jméno`
- `exemplář_id → název_knihy`
- `(výpůjčka_id, exemplář_id) → datum_vrácení`

### Problém
- míchá se hlavička výpůjčky, čtenář, exemplář a položka ⇒ 2NF/3NF potíže (redundance, tranzitivita)

### Rozklad do 3NF
- `Čtenář(čtenář_id PK, čtenář_jméno)`
- `Kniha(kniha_id PK, název_knihy)`
- `Exemplář(exemplář_id PK, kniha_id FK)`
- `Výpůjčka(výpůjčka_id PK, datum, čtenář_id FK)`
- `Položka(výpůjčka_id FK, exemplář_id FK, datum_vrácení, PK(výpůjčka_id, exemplář_id))`

---

## Úloha 10 (BCNF) – “Zní to nevinně”: směny a oddělení

### Špatná tabulka
`Směna(zaměstnanec, oddělení, vedoucí)`

| zaměstnanec | oddělení | vedoucí |
|------------|----------|---------|
| Jana       | IT       | Novák   |
| Petr       | IT       | Novák   |
| Eva        | HR       | Svoboda |

### FD
- `oddělení → vedoucí` (oddělení má právě jednoho vedoucího)
- klíč pro řádky je typicky `(zaměstnanec, oddělení)`

### Problém
- `oddělení → vedoucí` a `oddělení` není superklíč ⇒ porušení BCNF.
- redundance vedoucího u každého zaměstnance v oddělení.

### Rozklad do BCNF
- `Oddělení(oddělení PK, vedoucí)`
- `Zařazení(zaměstnanec, oddělení FK, PK(zaměstnanec, oddělení))`

---
