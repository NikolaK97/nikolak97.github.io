# 07 – Kartodiagramy a proporcionální znaky

## Cíle lekce

Po této lekci:

- rozumíte principu kartodiagramu,
- dokážete vysvětlit rozdíl mezi proporcionální a intervalovou stupnicí,
- víte, že velikost symbolu musí odpovídat ploše, ne poloměru,
- rozumíte percepčnímu zkreslení velikosti,
- znáte princip Flanneryho korekce.

---

# 1. Princip metody

Kartodiagram:

- znázorňuje kvantitativní hodnoty,
- používá bodové znaky,
- velikost znaku je funkcí hodnoty jevu.

Zásadní pravidlo:

> Čím větší hodnota, tím větší znak.

Metoda je vhodná pro:
- absolutní hodnoty,
- srovnávání konkrétních jednotek.

---

# 2. Parametr znaku

U dvourozměrných znaků (např. kruh):

Vnímáme plochu, ne poloměr.

Obsah kruhu:
A = πr²

Pokud zdvojnásobíme poloměr,
plocha se nezvětší 2×, ale 4×.

Typická chyba:

Škálování podle poloměru místo plochy.

---

# 3. Proporcionální vs. intervalový kartodiagram

## 3.1 Proporcionální stupnice

- velikosti znaků jsou v přesném poměru k datům,
- zachovává proporce.

Příklad:
Hodnoty 20 : 60 : 80 : 120
Velikosti musí být v poměru 1 : 3 : 4 : 6

---

## 3.2 Intervalová (gradovaná) stupnice

- hodnoty jsou rozděleny do tříd,
- každá třída má jednu velikost znaku.

Výhoda:
- vyšší přehlednost.

Nevýhoda:
- ztráta přesné proporcionality.

---

# 4. Percepce velikosti

Lidé systematicky podhodnocují velikost ploch.

Menší rozdíly mezi symboly jsou špatně rozlišitelné.

Proto:

- malé symboly musí být dostatečně velké,
- rozdíly mezi třídami musí být vizuálně výrazné.

---

# 5. Flanneryho korekce

Flannery zjistil, že:

lidé nevnímají plochu lineárně.

Navrhl korekci,
která upravuje výpočet velikosti symbolů tak,
aby odpovídala vizuálnímu vjemu.

ArcGIS umožňuje:

- použít perceptuální škálování,
- nebo klasické matematické škálování.

Perceptuální škálování často zlepšuje čitelnost.

---

# 6. Umístění znaků

Zásady:

- symbol musí být čitelný,
- velké symboly nesmí zakrývat malé,
- větší symboly patří vizuálně „dole“.

Konflikty překrývání jsou častý problém.

GIS nástroje řeší překryv omezeně,
někdy je nutná manuální úprava.

---

# 7. Kombinace proměnných

Je možné kombinovat:

- velikost + barvu,
- velikost + strukturu.

Ale:

Příliš mnoho vizuálních proměnných snižuje čitelnost.

Jedna mapa by měla mít jasnou dominantu.

---

# 8. Typické chyby

- škálování podle poloměru,
- příliš malé symboly,
- příliš mnoho tříd,
- překrývání bez řešení,
- kombinace více kvantitativních proměnných.

---

# Shrnutí

Kartodiagram:

- je silný nástroj pro absolutní hodnoty,
- vyžaduje přesný výpočet velikosti,
- musí respektovat percepci čtenáře,
- musí zůstat čitelný.

Proporce nejsou jen matematika.
Jsou i psychologie.
