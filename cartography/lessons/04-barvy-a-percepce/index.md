# 04 – Barvy v tematické kartografii

## Cíle lekce

Po této lekci:

- rozumíte fyzikální a psychologické podstatě barvy,
- rozlišujete tón, sytost a jas,
- chápete pojem optická váha barvy,
- umíte zvolit vhodné barevné schéma podle typu dat,
- víte, kdy použít sekvenční a kdy divergentní stupnici.

---

# 1. Fyzikální podstata barvy

Barva vzniká rozkladem bílého světla.

Viditelné spektrum:
- přibližně 380–780 nm.

Barva je optická vlastnost kartografického znaku.

Barevné vidění umožňuje rozlišovat:

- tón (hue),
- sytost (saturation),
- jas (lightness).

---

# 2. Barevný vjem

Barevný vjem je:

- fyzikální (podráždění oka),
- psychologický (interpretace mozku).

Člověk:

- pojmenovává barvy subjektivně,
- vnímá je rozdílně podle kontextu.

Barva tedy není pouze technický parametr,
ale i nástroj komunikace.

---

# 3. Základní složky barvy

## 3.1 Tón (Hue)

Tón je konkrétní spektrální barva.

Například:
- červená,
- zelená,
- modrá.

Tón používáme hlavně pro kvalitativní odlišení jevů.

---

## 3.2 Sytost (Saturation)

Sytost určuje:

- jak „čistá“ je barva,
- kolik šedi obsahuje.

Syté barvy:
- výrazné,
- silné,
- dominantní.

Bledé barvy:
- méně výrazné,
- vhodné pro podklad.

---

## 3.3 Jas (Lightness)

Jas určuje množství světla v barvě.

Světlé barvy:
- působí lehce,
- mají menší optickou váhu.

Tmavé barvy:
- působí těžce,
- mají větší optickou váhu.

Jas je klíčový pro kvantitativní stupnice.

---

# 4. Psychologické účinky barev

Barvy mohou působit jako:

## Teplé
- červená
- oranžová
- žlutá

Vyvolávají pocit aktivity, energie.

## Studené
- modrá
- zelená
- fialová

Působí klidněji, vzdáleněji.

---

# 5. Optická váha barvy

Optická váha je síla, kterou barva přitahuje pozornost.

Vyšší optickou váhu mají:
- tmavé barvy,
- syté barvy,
- teplé barvy.

Nižší optickou váhu mají:
- světlé,
- méně syté,
- studené barvy.

Optická váha musí odpovídat významu prvku.

---

# 6. Funkce barev v tematické mapě

Barvy plní tři hlavní funkce:

## 1) Rozlišovací (kvalita)
Odlišují typy jevů.

## 2) Klasifikační (kvantita)
Vyjadřují intenzitu.

## 3) Estetická
Podporují jednotu a čitelnost mapy.

---

# 7. Barevné stupnice

Barevná stupnice je grafická podoba číselné stupnice.

Musí odpovídat:
- charakteru dat,
- zvolené klasifikaci.

---

## 7.1 Sekvenční schéma

Používá se pro:

- stupňované veličiny,
- hodnoty od minima k maximu.

Postup:
- jeden tón,
- mění se jas a/nebo sytost.

Příklad:
světle modrá → tmavě modrá.

---

## 7.2 Divergentní schéma

Používá se pro:

- data s kritickou hodnotou,
- data s nulou nebo středem.

Postup:
- dvě odlišné barvy,
- přechod přes neutrální střed.

Příklad:
modrá → bílá → červená.

---

# 8. Standardizované barevné palety

V kartografii existují ustálené konvence:

- voda – modrá,
- vegetace – zelená,
- výškopis – hnědá,
- nížiny – zelená,
- vyšší polohy – hnědá.

Konvence usnadňují čtení mapy.

---

# 9. Typické chyby

- použití náhodných barev,
- příliš mnoho tónů,
- nedostatečný kontrast,
- nevhodné divergentní schéma,
- příliš sytý podklad.

---

# Shrnutí

Barva je:

- fyzikální jev,
- psychologický nástroj,
- klíčový prvek kartografické komunikace.

Volba barvy není estetické rozhodnutí.
Je to metodické rozhodnutí.

---

# Kartografické cvičení: Barvy a kartografická dostupnost

## Cíl cvičení

Cílem cvičení je vytvořit tematickou mapu dostupnosti služeb, která bude:

- kartograficky čitelná
- využívat vhodnou barevnou škálu
- přístupná i pro uživatele s poruchami barevného vidění

Studenti si procvičí práci se symbologií v ArcGIS Pro a principy kartografické hierarchie a barev.

---

## Vstupní data

Pro práci použijte následující vrstvy:

- hranice obcí nebo městských částí
- silniční síť
- lokace služeb (např. zdravotnická zařízení, školy nebo knihovny)
- populační data

---

## Zadání úkolu

### 1. Výpočet dostupnosti služeb

Vytvořte mapu dostupnosti zvolených služeb.

Možné přístupy:

- bufferová analýza (např. 2 km, 5 km, 10 km)
- síťová analýza – Service Area

---

### 2. Klasifikace dostupnosti

Rozdělte území do kategorií dostupnosti.

Příklad klasifikace:

| Kategorie | Popis |
|-----------|------|
| Dobrá dostupnost | do 5 minut |
| Střední dostupnost | 5–10 minut |
| Špatná dostupnost | více než 10 minut |

---

### 3. Kartografické zpracování

Vytvořte tematickou mapu dostupnosti.

Mapa musí obsahovat:

- vhodnou barevnou škálu
- jasnou vizuální hierarchii
- čitelnou legendu

---

### 4. Kontrola přístupnosti

Zkontrolujte, zda je mapa čitelná i pro uživatele s poruchou barevného vidění.

Zvažte například:

- deuteranopii
- protanopii

Pokud je to nutné, upravte barevnou škálu.

---

### 5. Vytvoření mapového layoutu

Finální mapa musí obsahovat:

- název mapy
- legendu
- měřítko
- zdroj dat
- autora mapy

---

## Doporučený pracovní postup v ArcGIS Pro

### Výpočet dostupnosti pomocí bufferu
Analysis → Buffer


Příklad vzdáleností:

- 2 km
- 5 km
- 10 km

---

### Výpočet dostupnosti pomocí síťové analýzy
Network Analyst → Service Area


Příklad časových intervalů:

- 5 minut
- 10 minut
- 15 minut

---

## Možné varianty řešení

### Varianta 1 – sekvenční barevná škála

Příklad:

| Kategorie | Barva |
|-----------|------|
| Špatná dostupnost | světle žlutá |
| Střední dostupnost | oranžová |
| Dobrá dostupnost | tmavě červená |

Výhody:

- silná vizuální hierarchie
- intuitivní interpretace

---

### Varianta 2 – modrá sekvenční škála

Příklad:

| Kategorie | Barva |
|-----------|------|
| Špatná dostupnost | světle modrá |
| Střední dostupnost | střední modrá |
| Dobrá dostupnost | tmavě modrá |

Výhody:

- dobrá čitelnost
- vhodné pro uživatele s poruchami barevného vidění

---

### Varianta 3 – divergenční škála

Příklad:

| Kategorie | Barva |
|-----------|------|
| Špatná dostupnost | červená |
| Střední dostupnost | šedá |
| Dobrá dostupnost | zelená |

Poznámka:  
Tato kombinace může být problematická pro některé typy color vision deficiency.!!!

https://www.google.com/url?sa=t&source=web&rct=j&url=https%3A%2F%2Fwww.geoinformatics.upol.cz%2Fdprace%2Fbakalarske%2Fvodicka22%2F&ved=0CBYQjRxqFwoTCKi8wtmZlpMDFQAAAAAdAAAAABAI&opi=89978449<img width="2339" height="1654" alt="image" src="https://github.com/user-attachments/assets/e5ead3cc-7825-4113-957e-3e130c9a8383" />

---

## Typické chyby

Při tvorbě mapy se často objevují tyto problémy:

- použití příliš velkého množství barev
- použití duhové barevné škály
- nedostatečný kontrast
- problematická kombinace červené a zelené
- legenda neodpovídá mapovým symbolům

---


