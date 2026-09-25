---
title: 03 – Kartografický znak a geologické značky
---

# 03 – Kartografický znak a geologické značky

## Cíle lekce

- rozumíte grafickým proměnným (Bertin) a umíte je použít vědomě,
- znáte standardní bodové, liniové a plošné značky geologické mapy,
- umíte v ArcGIS Pro nastavit značku s rotací podle naměřeného azimutu,
- chápete rozdíl mezi jistotou a nejistotou vyjádřenou typem linie.

---

# 1. Grafické proměnné

Teorii grafických proměnných formuloval Bertin (1967, angl. 1983) a dodnes tvoří základ sémiologie mapového znaku; její rozšíření a kritické zhodnocení podává MacEachren (1995). Standardizovaná symbolika geologických map je definována ve standardu FGDC (2006), který je i mezinárodně nejčastěji citovanou referencí; principy konstrukce strukturních značek vysvětlují Lisle et al. (2011) a Ragan (2009).

Podle Bertina může znak nést informaci pomocí:

- **tvaru**, **velikosti**, **orientace**,
- **barvy (tónu)**, **jasu (sytosti)**, **textury (šrafy)**,
- polohy.

Každá proměnná se hodí na jiný typ dat:

| Data | Vhodná proměnná |
|---|---|
| kvalitativní (litologie) | tvar, barva, textura |
| pořadová (stupeň zvětrání) | jas, velikost |
| kvantitativní (mocnost) | velikost, jas |
| směrová (sklon vrstev) | **orientace** |

Geologie je jeden z mála oborů, kde **orientace** značky nese primární informaci.

---

# 2. Bodové značky

- **sklon a směr vrstev** (T-značka: dlouhá čára = směr, krátká = sklon, číslo = velikost sklonu),
- foliace, lineace, osy vrás (různé varianty T-značky),
- horizontální a vertikální uložení (kříž, kroužek s čárou),
- **vrt** (kroužek, index vrtu, případně hloubka),
- výchoz, lom (činný/opuštěný), pramen, štola, sesuv.

Značka sklonu/směru musí být **orientována přesně** – ± 5° je už chyba, kterou strukturní geolog pozná.

---

# 3. Liniové značky

- geologická hranice: **zjištěná / předpokládaná / zakrytá** (plná / čárkovaná / tečkovaná),
- zlom: zjištěný / předpokládaný; typ (normální, přesmyk, horizontální posun – trojúhelníčky, šipky),
- násun (trojúhelníky na nadložním bloku),
- osa antiklinály / synklinály,
- linie řezu, hranice sesuvu, okraj lomu.

Tloušťka linie vyjadřuje hierarchii: zlomy > hranice jednotek > hranice facií.

---

# 4. Plošné značky

Dvě strategie, často kombinované:

- **barevná výplň** – primární rozlišení jednotek (viz lekce 04),
- **litologická šrafa** – vzor uvnitř plochy (tečky = pískovec, cihly = vápenec, vlnky = břidlice, křížky = granit...).

Šrafa je záloha pro černobílý tisk a pro barvoslepé čtenáře. Nikdy ne obojí v plné síle – šrafa se dává jemná, řídká, ve stejném tónu jako výplň.

---

# 5. Čitelnost

- minimální velikost bodové značky: 2 mm v tisku,
- minimální tloušťka linie: 0,1 mm (jemná hranice) – 0,5 mm (zlom),
- popisky indexů jednotek: 6–8 pt, vždy uvnitř plochy, bez halo, pokud to jde,
- shluky značek (strukturních měření) řešte generalizací: ponechte reprezentativní, zbytek do tabulky.

---

# Cvičení v hodině

Načtěte bodovou vrstvu strukturních měření ze studijního výřezu (tabulka s poli `dip_dir`, `dip`).

1. Nastavte symbol typu **sklon/směr** ze stylu *Geology 24K* (Symbology → Gallery, přidat styl).
2. Rotaci značky napojte na pole `dip_dir` (Symbology → Vary symbology by attribute → Rotation; nastavte *Geographic* – azimut po směru hodin od severu).
3. Popisek nastavte na hodnotu `dip`, umístěte vedle krátkého ramene.
4. Exportujte výřez 1:10 000 a zkontrolujte čitelnost při 100 %.

---

# Shrnutí

- Grafická proměnná se vybírá podle typu dat, ne podle vkusu.
- Geologické značky jsou standardizované – používejte knihovnu, ne vlastní kresbu.
- Typ linie vyjadřuje jistotu. To je vaše nejdůležitější poctivost vůči čtenáři.

---

# Literatura

- BERTIN, J. (1983): *Semiology of Graphics: Diagrams, Networks, Maps*. University of Wisconsin Press, Madison. (Franc. orig. *Sémiologie graphique*, 1967.)
- FGDC (2006): *FGDC Digital Cartographic Standard for Geologic Map Symbolization*. FGDC-STD-013-2006. Federal Geographic Data Committee, U.S. Geological Survey, Reston.
- LISLE, R. J., BRABHAM, P., BARNES, J. W. (2011): *Basic Geological Mapping*. 5. vyd. Wiley-Blackwell, Chichester.
- MACEACHREN, A. M. (1995): *How Maps Work: Representation, Visualization, and Design*. Guilford Press, New York.
- RAGAN, D. M. (2009): *Structural Geology: An Introduction to Geometrical Techniques*. 4. vyd. Cambridge University Press, Cambridge.
- SLOCUM, T. A., MCMASTER, R. B., KESSLER, F. C., HOWARD, H. H. (2009): *Thematic Cartography and Geovisualization*. 3. vyd. Pearson Prentice Hall, Upper Saddle River.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/literatura/' | relative_url }}).
