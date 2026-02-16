# 09 – Metoda izolinií

## Cíle lekce

Po této lekci:

- víte, co je izolinie,
- rozumíte pojmu 2,5D pole,
- znáte vlastnosti izolinií,
- umíte vysvětlit princip jejich konstrukce,
- dokážete posoudit vhodnost dat pro tuto metodu.

---

# 1. Co je izolinie?

Izolinie je:

> čára spojující body se stejnou hodnotou určité veličiny.

Izolinie vyjadřují spojité (kontinuální) rozložení jevu.

---

# 2. Příklady izolinií

Podle typu veličiny rozlišujeme například:

- vrstevnice (stejná nadmořská výška),
- izotermy (stejná teplota),
- izobary (stejný tlak),
- izohyety (stejné množství srážek),
- izochrony (stejný čas dostupnosti).

Název vždy vychází z řeckého „iso“ = stejný.

---

# 3. 2,5D pole

Metoda izolinií pracuje s tzv. 2,5D polem.

To znamená:

- jev má dvě prostorové souřadnice (x, y),
- a jednu hodnotu (z).

Hodnota se mění spojitě v prostoru.

Izolinie tedy nejsou vhodné pro jevy:

- které jsou skokové,
- které jsou vázané na administrativní jednotky.

---

# 4. Vlastnosti izolinií

Izolinie:

- se neprotínají,
- jsou uzavřené (nebo končí na hranici mapy),
- hustota izolinií vyjadřuje strmost změny,
- vyšší hodnota je vždy na jedné straně čáry.

Hustší izolinie = prudší změna hodnoty.

---

# 5. Ekvidistance

Ekvidistance je:

> rozdíl hodnot mezi dvěma sousedními izoliniemi.

Volba ekvidistance ovlivňuje:

- čitelnost mapy,
- míru detailu,
- interpretaci.

Příliš malá ekvidistance:

- přeplní mapu.

Příliš velká ekvidistance:

- zjednoduší realitu.

---

# 6. Konstrukce izolinií

Izolinie lze konstruovat:

- graficky (ručně),
- matematicky (interpolací),
- automatizovaně v GIS.

Postup:

1. Získání bodových hodnot.
2. Interpolace spojitého pole.
3. Generování izolinií podle zvolené ekvidistance.

---

# 7. Interpolace

Interpolace znamená:

> odhad hodnot mezi měřenými body.

Běžné metody:

- IDW,
- Kriging,
- spline.

Volba metody ovlivňuje výsledek.

Interpolace je model,
nikoli skutečná realita.

---

# 8. Vhodnost dat

Vhodná data:

- spojitě se měnící jevy,
- fyzickogeografické veličiny.

Nevhodná data:

- administrativní údaje,
- diskontinuální jevy,
- data s ostrými hranicemi.

Například:

Nezaměstnanost není spojitý jev v prostoru,
proto není vhodná pro izolinie.

---

# 9. Grafická úprava izolinií

Izolinie mohou být:

- tenké,
- barevně odstupňované,
- doplněné hodnotou.

Důležité:

- hlavní izolinie zvýraznit,
- zachovat čitelnost,
- nepřehustit mapu.

---

# Shrnutí

Metoda izolinií:

- vyjadřuje spojité pole,
- pracuje s interpolací,
- vyžaduje správnou volbu ekvidistance,
- není vhodná pro diskontinuální jevy.

Izolinie nejsou jen čáry.
Jsou model prostoru.
