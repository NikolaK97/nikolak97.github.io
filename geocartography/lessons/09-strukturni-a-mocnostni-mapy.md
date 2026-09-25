---
title: 09 – Izolinie a interpolace: strukturní a mocnostní mapy
---

# 09 – Izolinie a interpolace: strukturní a mocnostní mapy

## Cíle lekce

- znáte typy izoliniových map v geologii,
- umíte interpolovat bodová data z vrtů (IDW, TIN, kriging) a znáte omezení každé metody,
- chápete vliv hustoty a rozložení vrtů na výsledek,
- umíte zobrazit nejistotu interpolace v mapě.

---

# 1. Izolinie v geologii

Izoliniová metoda je kartograficky popsána u Slocuma et al. (2009) a Voženílka, Kaňoka et al. (2011). Teoretický základ interpolace bodových dat a geostatistiky (kriging, variogram) podávají Isaaks & Srivastava (1989) a Webster & Oliver (2007); aplikaci v geologii shrnuje Davis (2002). Konstrukci strukturních map z vrtných dat a její úskalí popisují Spencer (2000) a Bennison et al. (2011).

Izolinie spojují místa stejné hodnoty spojitého pole. Geologie je má všude:

| Mapa | Izolinie čeho |
|---|---|
| strukturní (izohypsy) | nadmořská výška stropu/báze vrstvy |
| izopachová | mocnost vrstvy |
| hydroizohypsy | hladina podzemní vody |
| izolinie geofyzikálních polí | tíhové anomálie, magnetika |
| izokoncentrační | obsah prvku (spíš rastr než linie) |

Vstup jsou téměř vždy **vrty** nebo měřicí body → nerovnoměrná, řídká data.

---

# 2. Metody interpolace

**TIN (triangulace)** – lineární mezi trojicemi bodů. Poctivá, nevymýšlí nic mimo data, ale hranatá. Dobrá pro první pohled.

**IDW (inverzní vzdálenost)** – vážený průměr okolí. Jednoduchá, ale dělá „býčí oka" kolem bodů a nikdy nepřekročí min/max dat.

**Kriging** – geostatistika, model prostorové autokorelace (variogram). Nejlepší odhad + **mapu chyby**. Vyžaduje pochopení variogramu; pro bakaláře stačí ordinary kriging s automatickým variogramem (Geostatistical Analyst → Geostatistical Wizard, nebo nástroj *Kriging* ve Spatial Analyst).

**Spline** – hladký povrch, ale přestřeluje mimo rozsah dat – opatrně u mocností (záporná mocnost).

Pro strukturní mapy je standardem kriging; pro rychlý náhled TIN.

---

# 3. Omezení interpolace

- **Přes zlom.** Strop vrstvy je na obou stranách zlomu jinde. Interpolace to vyhladí. Řešení: interpolovat po blocích, zlomy jako bariéry.
- **Mimo data (extrapolace).** Ořízněte výsledek konvexní obálkou vrtů + malý buffer.
- **Nerovnoměrné vrty.** Hustý průzkum v údolí, nic na svazích → mapa je v údolí spolehlivá, na svazích vymyšlená.
- **Chybějící vrstva vs. nulová mocnost.** Vrt, který vrstvu nezastihl, není nulová hodnota – je to informace jiného typu.

---

# 4. Zobrazení nejistoty

Poctivá izoliniová mapa ukazuje, odkud data pocházejí:

- zobrazit vstupní vrty s hodnotou,
- izolinie čárkovaně tam, kde jsou daleko od vrtů,
- podložit mapou chyby krigingu (šedý rastr) nebo mapou vzdálenosti k nejbližšímu vrtu,
- oříznout oblast bez dat.

---

# 5. Kartografie izolinií

- interval izolinií konstantní, zvýrazněná každá pátá (nebo desátá),
- popisky na linii, orientované po spádu, ne vzhůru nohama,
- barva odlišná od geologických hranic (hydroizohypsy modře, izopachy hnědě),
- při kombinaci s geologií: izolinie tenké, hranice silnější, nebo naopak podle tématu mapy.

---

# Cvičení v hodině

Vrtná databáze studijního výřezu (~30 vrtů s polem `baze_kvarteru` v m n. m.).

1. Vytvořte izohypsy báze kvartéru metodou TIN (*Create TIN* → *TIN Contour*) a IDW (*IDW* → *Contour*).
2. Vytvořte je krigingem (Geostatistical Wizard → Ordinary Kriging) a exportujte i *Prediction Standard Error* rastr jako mapu chyby.
3. Ořízněte konvexní obálkou vrtů + 200 m (*Minimum Bounding Geometry* → *Buffer* → *Extract by Mask*).
4. V layoutu: kriging jako hlavní mapa, mapa chyby jako vedlejší, vrty s hodnotami. Označte oblast, kde byste výsledku nevěřili.

---

# Shrnutí

- Izolinie z vrtů jsou nejčastější geologická „tematická mapa" – a zároveň nejnáchylnější k dezinterpretaci.
- Metoda interpolace musí odpovídat datům; přes zlom se neinterpoluje.
- Zobrazte vstupní body a nejistotu; izopachová mapa bez nich není odborně obhajitelná.

---

# Literatura

- BENNISON, G. M., OLVER, P. A., MOSELEY, K. A. (2011): *An Introduction to Geological Structures and Maps*. 8. vyd. Hodder Education, London.
- DAVIS, J. C. (2002): *Statistics and Data Analysis in Geology*. 3. vyd. Wiley, New York.
- ISAAKS, E. H., SRIVASTAVA, R. M. (1989): *An Introduction to Applied Geostatistics*. Oxford University Press, New York.
- SLOCUM, T. A., MCMASTER, R. B., KESSLER, F. C., HOWARD, H. H. (2009): *Thematic Cartography and Geovisualization*. 3. vyd. Pearson Prentice Hall, Upper Saddle River.
- SPENCER, E. W. (2000): *Geologic Maps: A Practical Guide to the Preparation and Interpretation of Geologic Maps*. 2. vyd. Prentice Hall, Upper Saddle River.
- WEBSTER, R., OLIVER, M. A. (2007): *Geostatistics for Environmental Scientists*. 2. vyd. Wiley, Chichester.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/literatura/' | relative_url }}).
