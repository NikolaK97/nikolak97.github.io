# Cvičení 2 – Pokročilé ERA modelování

## Cíl cvičení

Po absolvování tohoto cvičení bude student schopen:

- systematicky analyzovat zadání
- správně identifikovat entity a atributy
- určit kardinalitu vztahů
- určit parcialitu (povinná / volitelná účast)
- rozpoznat atribut vztahu
- vyřešit M:N vztah pomocí asociativní entity

---

# Teoretická část (minimálně 20–25 minut)

## 1. Systematická analýza zadání

Při práci se zadáním vždy postupuj v těchto krocích:

1. Podtrhni podstatná jména – kandidáti na entity.
2. Zakroužkuj slovesa – kandidáti na vztahy.
3. Najdi slova „může, musí, každý, právě, alespoň“ – určují parcialitu.
4. Najdi slova „více, mnoho, několik, jeden“ – určují kardinalitu.

Zásada:
Entita je objekt se vztahy.  
Atribut je vlastnost entity.

---

## 2. Kardinalita

Kardinalita odpovídá na otázku:

Kolik instancí jedné entity může být spojeno s instancí druhé entity?

Typy:

- 1:1
- 1:N
- M:N

Příklad:
„Fakulta má více zaměstnanců.“
→ 1:N

---

## 3. Parcialita

Parcialita odpovídá na otázku:

Musí entita být ve vztahu, nebo může existovat samostatně?

Testovací otázka:
Může existovat X bez Y?

Pokud ne → povinná účast.  
Pokud ano → volitelná účast.

---

## 4. Atribut vztahu

Některé informace vznikají až ve vztahu.

Příklad:
Student se zapisuje na předmět.  
Výsledkem je známka.

Známka:

- nepatří obecně studentovi
- nepatří obecně předmětu
- patří konkrétnímu zápisu studenta na předmět

Řešení:
Vzniká asociativní entita.

---

# Řízený příklad

## Zadání

Zaměstnanec může, ale nemusí vyučovat předmět.  
Každý předmět je vyučován právě jedním zaměstnancem.

---

## Analýza

Entity:
- Zaměstnanec
- Předmět

Vztah:
- vyučuje

Kardinalita:
- Zaměstnanec 1:N Předmět

Parcialita:
- Zaměstnanec – volitelná
- Předmět – povinná

---

# Hlavní úloha – Univerzitní informační systém

## Zadání

Univerzita má několik fakult.  
Každý student studuje právě na jedné fakultě.  
Fakulta má více zaměstnanců.  
Zaměstnanec může, ale nemusí vyučovat předmět.  
Každý předmět je vyučován právě jedním zaměstnancem.  
Student se zapisuje na předměty.  
Ze zápisu vzniká hodnocení (známka).

---

# Postup řešení

## Krok 1 – Identifikace entit

Očekávané entity:

- Fakulta
- Student
- Zaměstnanec
- Předmět
- Zápis (asociativní entita)

---

## Krok 2 – Atributy a primární klíče

Minimální návrh:

Fakulta  
- faculty_id (PK)  
- název  

Student  
- student_id (PK)  
- jméno  

Zaměstnanec  
- employee_id (PK)  
- jméno  

Předmět  
- subject_id (PK)  
- název  

Zápis  
- student_id (FK)  
- subject_id (FK)  
- známka  

---

## Krok 3 – Vztahy

Fakulta – Student  
Fakulta – Zaměstnanec  
Zaměstnanec – Předmět  
Student – Předmět  

---

## Krok 4 – Kardinality

Fakulta 1:N Student  
Fakulta 1:N Zaměstnanec  
Zaměstnanec 1:N Předmět  
Student M:N Předmět  

---
## Krok 5 – Parcialita

Student musí být na fakultě – povinná.  
Zaměstnanec může, ale nemusí vyučovat – volitelná.  
Předmět musí mít zaměstnance – povinná.  
Zápis musí mít studenta i předmět – povinná na obou stranách.

---

## Krok 6 – Řešení M:N vztahu

Student a Předmět nelze ponechat jako přímé M:N.

Vzniká entita Zápis:

- propojuje studenta a předmět
- obsahuje atribut známka
- má složený primární klíč (student_id, subject_id)

Výsledné vztahy:

Fakulta 1:N Student  
Fakulta 1:N Zaměstnanec  
Zaměstnanec 1:N Předmět  
Student 1:N Zápis  
Předmět 1:N Zápis  

---

# Kontrolní seznam

- Každá entita má primární klíč.
- Neexistuje přímý M:N vztah.
- Známka je atributem Zápisu.
- Kardinality jsou správně určeny.
- Parcialita je určena alespoň u dvou vztahů.
