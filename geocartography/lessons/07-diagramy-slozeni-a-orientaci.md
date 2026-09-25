---
layout: page
title: 07 – Kartodiagramy: složení a orientace
---

# 07 – Kartodiagramy: složení a orientace

## Cíle lekce

- znáte typy kartodiagramů a umíte vybrat správný pro geologická data,
- umíte zobrazit složení (minerální, chemické) koláčovým nebo sloupcovým diagramem v mapě,
- umíte zobrazit orientační data růžicovým diagramem,
- chápete, jak škálovat velikost diagramu podle počtu vzorků.

---

# 1. Kartogram vs. kartodiagram

Kartodiagramy jsou v české terminologii vymezeny Kaňokem (1999) a Voženílkem, Kaňokem et al. (2011); v anglosaské literatuře odpovídají *proportional symbol* a *chart maps* (Slocum et al. 2009). Obecné principy grafického zobrazení kvantitativních dat, včetně zásady proporcionality plochy, formuloval Tufte (2001). Zobrazení orientačních dat růžicovými diagramy a stereografickou projekcí popisují Ragan (2009) a Marshak & Mitra (1988); statistickému zpracování směrových dat v geologii se věnuje Davis (2002).

- **kartogram**: relativní hodnota, vyplňuje plochu jednotky barvou,
- **kartodiagram**: absolutní hodnota nebo struktura, diagram umístěný do bodu nebo do těžiště plochy.

Kartodiagram umí to, co kartogram ne: zobrazit **více proměnných najednou** a **složení**.

---

# 2. Typy diagramů a geologické použití

| Diagram | Zobrazuje | Příklad |
|---|---|---|
| kruhový (velikost) | jednu absolutní hodnotu | vydatnost pramene, hloubka vrtu |
| koláčový | strukturu celku | modální složení horniny, zrnitost sedimentu |
| sloupcový | více hodnot vedle sebe | obsah Fe, Mn, As ve vrtu |
| hvězdicový / radarový | vícerozměrný profil | chemismus podzemní vody (hlavní ionty) |
| **růžicový** | směrové rozdělení | orientace puklin, směry transportu |
| Stiffův / Piperův | chemismus vody (specializované) | hydrogeochemie |

---

# 3. Škálování velikosti

Velikost diagramu nese informaci (počet vzorků, celkový objem). Pravidla:

- plocha diagramu úměrná hodnotě, ne poloměr (oko vnímá plochu),
- rozsah velikostí 1:5 až 1:10 – větší rozdíly už nejsou čitelné,
- do legendy tři referenční velikosti (min, střed, max),
- příliš malé diagramy (< 4 mm) nečitelné – slučte body nebo použijte jinou metodu.

Pokud velikost nenese informaci, nechte všechny stejné a řekněte to v legendě.

---

# 4. Růžicový diagram v mapě

Orientační data (směry puklin, lineací, směry paleotransportu) se zobrazují **růžicí**: polární histogram, sektory po 10° nebo 15°. V mapě se umístí do místa měření nebo do těžiště lokality.

Bezorientační data (pukliny: 30° = 210°) se zobrazují **symetrickou** růžicí. Orientovaná data (směr transportu) **asymetrickou**.

Nástroje: ArcGIS Pro *Polar Plot* (Create Chart → Bar chart s polárním rozložením) nebo Stereonet (Allmendinger) a vložení jako obrázek do layoutu.

---

# 5. Umístění a překryvy

- diagram umístit do těžiště plochy nebo přesně na bod; při překryvech odsunout s vodicí čárou,
- diagram nesmí zakrývat důležitou geologii pod ním – použijte mírnou průhlednost nebo bílý okraj,
- max. 6 kategorií v koláči, jinak sloučit do „ostatní".

---

# Cvičení v hodině

Vrstva lomů ve studijním výřezu s poli `qtz`, `fsp`, `mica`, `other` (modální %) a `n_samples`.

1. Symbology → *Charts* → Pie, čtyři pole.
2. Velikost diagramu podle `n_samples` (*Vary size using a field*, rozsah 4–12 mm, škálovat plochou).
3. Legenda diagramu se třemi referenčními velikostmi (legenda koláčů se v layoutu přidá automaticky, velikosti doplňte).
4. Bonus: z tabulky měření puklin vytvořte růžici pro jednu lokalitu a vložte ji do layoutu.

---

# Shrnutí

- Kartodiagram zobrazuje složení a více proměnných; kartogram jen jednu relativní hodnotu.
- Velikost = plocha, ne poloměr.
- Růžice je kartodiagram pro orientační data – specialita geologie.

---

# Literatura

- DAVIS, J. C. (2002): *Statistics and Data Analysis in Geology*. 3. vyd. Wiley, New York.
- KAŇOK, J. (1999): *Tematická kartografie*. Ostravská univerzita, Ostrava.
- MARSHAK, S., MITRA, G. (1988): *Basic Methods of Structural Geology*. Prentice Hall, Englewood Cliffs.
- RAGAN, D. M. (2009): *Structural Geology: An Introduction to Geometrical Techniques*. 4. vyd. Cambridge University Press, Cambridge.
- SLOCUM, T. A., MCMASTER, R. B., KESSLER, F. C., HOWARD, H. H. (2009): *Thematic Cartography and Geovisualization*. 3. vyd. Pearson Prentice Hall, Upper Saddle River.
- TUFTE, E. R. (2001): *The Visual Display of Quantitative Information*. 2. vyd. Graphics Press, Cheshire.
- VOŽENÍLEK, V., KAŇOK, J. a kol. (2011): *Metody tematické kartografie: vizualizace prostorových jevů*. Univerzita Palackého, Olomouc.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/geocartography/literatura/' | relative_url }}).
