---
layout: page
title: 05 – Metody tematické kartografie pro geologická data
---

# 05 – Metody tematické kartografie pro geologická data

## Cíle lekce

- znáte základní metody tematické kartografie a víte, pro jaká data se hodí,
- rozlišujete data vázaná na plochu, linii a bod,
- umíte vybrat správnou územní jednotku pro geologický kartogram,
- chápete normalizaci a víte, proč se kartogram nedělá z absolutních hodnot.

---

# 1. Přehled metod

Systematiku metod tematické kartografie podává v českém prostředí Kaňok (1999) a Voženílek, Kaňok et al. (2011), v mezinárodní literatuře Slocum et al. (2009) a Dent et al. (2009). Problém volby územní jednotky (tzv. *modifiable areal unit problem*) je v geografii známý od Openshawa (1984); pro geologická data je jeho důsledkem nutnost volit jednotky s přírodním, nikoli administrativním vymezením.

| Metoda | Typ dat | Geologický příklad |
|---|---|---|
| bodová značka | kvalitativní bod | typ výchozu, lom |
| liniová značka | kvalitativní linie | zlom, hranice |
| areálová (plošná) | kvalitativní plocha | geologické jednotky |
| **kartogram** | relativní hodnota v území | průměrná mocnost kvartéru v rajonu |
| **kartodiagram** | absolutní hodnoty v bodě/území | složení hornin v lomu |
| tečková | absolutní počty | počet vrtů |
| izolinie | spojité pole | hladina podzemní vody |
| dasymetrická | spojité, s pomocnými daty | zranitelnost podzemních vod |

Základní geologická mapa je téměř čistě **areálová** – proto jste ji doteď považovali za jediný typ geologické mapy. Tento kurz je o těch ostatních.

---

# 2. Územní jednotky pro geologa

Kartogram potřebuje územní jednotky. Obecná kartografie používá okresy, obce. To je pro geologická data téměř vždy **špatně**: geologie nerespektuje administrativní hranice.

Smysluplné jednotky:

- geologické jednotky (polygony z mapy),
- hydrogeologické rajony (ČGS/ČHMÚ),
- povodí (ČHMÚ),
- inženýrskogeologické rajony,
- mapové listy (pouze pro organizační data, např. hustota průzkumu),
- pravidelná síť (grid) pro bodová data s náhodným rozložením.

Volba jednotky je odborné rozhodnutí. Zdůvodněte ji v tiráži nebo textu.

---

# 3. Normalizace

Kartogram zobrazuje **relativní** hodnotu, jinak vyjadřuje jen velikost území.

Špatně: počet vrtů v rajonu.
Správně: vrty na km² (hustota průzkumu).

Špatně: celková mocnost kvartéru (součet z vrtů).
Správně: průměrná mocnost, medián, nebo mocnost vážená plochou.

Špatně: počet vzorků nad limitem.
Správně: podíl vzorků nad limitem (%).

Výjimka: kartogram může zobrazovat průměr nebo medián jako takový – to je už normalizovaná veličina.

---

# 4. Bodová data → plošná

Většina geologických dat vzniká v bodech (vrty, vzorky, měření). Cesty k ploše:

- **agregace** do jednotek (průměr za rajon) → kartogram,
- **interpolace** → izolinie/rastr (lekce 09),
- ponechat jako bodová → kartodiagram (lekce 07).

Agregace skrývá rozptyl. Vždy uvádějte počet bodů v jednotce – rajon s jedním vrtem není totéž co rajon s padesáti.

---

# Cvičení v hodině

Z vrtné databáze (výřez, viz [Data]({{ '/geocartography/data/' | relative_url }})) a vrstvy hydrogeologických rajonů:

1. spojte vrty s rajony (*Spatial Join*, nebo *Summarize Within*, které rovnou spočítá statistiky),
2. spočítejte za rajon: počet vrtů, průměrnou a mediánovou mocnost kvartéru,
3. vytvořte kartogram mediánu; rajony s méně než 5 vrty vyšrafujte jako „nedostatečná data",
4. porovnejte s kartogramem průměru – kde se liší a proč.

---

# Shrnutí

- Areálová metoda je jen jedna z mnoha. Geolog potřebuje i kartogram, kartodiagram a izolinie.
- Územní jednotka musí mít geologický smysl.
- Normalizujte. A říkejte, z kolika bodů hodnota vznikla.

---

# Literatura

- DENT, B. D., TORGUSON, J. S., HODLER, T. W. (2009): *Cartography: Thematic Map Design*. 6. vyd. McGraw-Hill, New York.
- KAŇOK, J. (1999): *Tematická kartografie*. Ostravská univerzita, Ostrava.
- OPENSHAW, S. (1984): *The Modifiable Areal Unit Problem*. CATMOG 38. Geo Books, Norwich.
- SLOCUM, T. A., MCMASTER, R. B., KESSLER, F. C., HOWARD, H. H. (2009): *Thematic Cartography and Geovisualization*. 3. vyd. Pearson Prentice Hall, Upper Saddle River.
- VOŽENÍLEK, V., KAŇOK, J. a kol. (2011): *Metody tematické kartografie: vizualizace prostorových jevů*. Univerzita Palackého, Olomouc.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/geocartography/literatura/' | relative_url }}).
