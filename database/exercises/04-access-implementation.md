# Cvičení 4 – Implementace relačního modelu v MS Access

## Cíl cvičení

Po absolvování tohoto cvičení bude student schopen:

- vytvořit databázi v MS Access
- založit tabulky podle relačního modelu
- správně nastavit datové typy
- nastavit primární klíče
- nastavit cizí klíče
- zapnout referenční integritu
- pochopit vztah mezi teoretickým návrhem a praktickou implementací

---

# Teoretická část (minimálně 20 minut)

## 1. Relační model vs. implementace

V minulém cvičení jsme vytvořili relační model:

- určili tabulky
- určili primární klíče
- určili cizí klíče

Dnes tento model převedeme do konkrétní databáze.

Důležité:
Model je návrh.
Access je implementace.

---

## 2. Primární klíč

Primární klíč:

- jednoznačně identifikuje záznam
- nesmí být NULL
- musí být unikátní

V Access:
- označit pole
- kliknout na „Primary Key“

---

## 3. Cizí klíč

Cizí klíč:

- odkazuje na primární klíč jiné tabulky
- vytváří vztah mezi tabulkami

V Access:
- nastavuje se pomocí nástroje „Relationships“

---

## 4. Referenční integrita

Referenční integrita zajišťuje, že:

- nelze vložit FK bez existujícího PK
- nelze smazat rodiče, pokud existují závislé záznamy

Bez referenční integrity je databáze nekonzistentní.

---

# Praktická část – Univerzitní databáze

Budeme implementovat model z Cvičení 3.

---

# Krok 1 – Vytvoření databáze

1. Otevři MS Access.
2. Klikni na „Blank Database“.
3. Název: univerzita.accdb.
4. Klikni Create.
![F0F39853-31D4-46B1-BED3-41C538EB97E8_1_105_c](https://github.com/user-attachments/assets/922dc781-1035-4869-bbed-93bad039fd5a)

---

# Krok 2 – Vytvoření tabulek

## Tabulka FAKULTA

Pole:

- faculty_id – AutoNumber – Primary Key
- nazev – Short Text

Ulož jako: FAKULTA

---

## Tabulka STUDENT

Pole:

- student_id – AutoNumber – Primary Key
- jmeno – Short Text
- faculty_id – Number (Long Integer)

Ulož jako: STUDENT

---

## Tabulka ZAMESTNANEC

Pole:

- employee_id – AutoNumber – Primary Key
- jmeno – Short Text
- faculty_id – Number (Long Integer)

Ulož jako: ZAMESTNANEC

---

## Tabulka PREDMET

Pole:

- subject_id – AutoNumber – Primary Key
- nazev – Short Text
- employee_id – Number (Long Integer)

Ulož jako: PREDMET

---

## Tabulka ZAPIS

Pole:

- student_id – Number (Long Integer)
- subject_id – Number (Long Integer)
- znamka – Number

Primární klíč:
Označ student_id + subject_id
Klikni „Primary Key“ (složený klíč)

Ulož jako: ZAPIS

---

# Krok 3 – Nastavení vztahů

1. Klikni Database Tools → Relationships.
2. Přidej všechny tabulky.
3. Přetáhni:

FAKULTA.faculty_id → STUDENT.faculty_id  
FAKULTA.faculty_id → ZAMESTNANEC.faculty_id  
ZAMESTNANEC.employee_id → PREDMET.employee_id  
STUDENT.student_id → ZAPIS.student_id  
PREDMET.subject_id → ZAPIS.subject_id  

4. Zaškrtni „Enforce Referential Integrity“.
5. Klikni Create.

---

# Krok 4 – Test integrity

## Test 1

Zkus vložit STUDENT s faculty_id, které neexistuje.

Očekávání:
Access odmítne vložení.

---

## Test 2

Zkus smazat FAKULTA, která má studenty.

Očekávání:
Access odmítne smazání.

---

# Krok 5 – Vložení testovacích dat

Vlož minimálně:

- 2 fakulty
- 3 studenty
- 2 zaměstnance
- 3 předměty
- 5 zápisů

Ověř, že vazby fungují.

---

# Kontrolní seznam

- Každá tabulka má primární klíč.
- Vztahy jsou vytvořeny.
- Referenční integrita je zapnutá.
- Složený primární klíč funguje.
- Datové typy FK odpovídají PK.

---

# Nejčastější chyby

- FK má jiný datový typ než PK.
- Není zapnutá referenční integrita.
- ZAPIS nemá složený primární klíč.
- FK je nastaven jako AutoNumber (nesmí být).

---

# Úkol k odevzdání

Odevzdej:

1. Screenshot diagramu vztahů.
2. Screenshot tabulky ZAPIS s primárním klíčem.
3. Stručné vysvětlení:
   - proč mají FK datový typ Number (Long Integer)
   - proč ZAPIS používá složený klíč

---

# Co bude následovat

V příštím cvičení budeme nad touto databází vytvářet dotazy.
