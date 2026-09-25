---
layout: page
title: 04 – Barvy a percepce: barvy stratigrafie
---

# 04 – Barvy a percepce: barvy stratigrafie

## Cíle lekce

- rozumíte tomu, jak lidské oko vnímá barvu a co z toho plyne pro mapy,
- znáte stratigrafické barevné konvence (ICS, ČGS, USGS) a víte, kdy se jich držet,
- chápete, proč jsou chronostratigrafické barvy nominální, ne pořadové,
- umíte ověřit mapu pro barvoslepé čtenáře.

---

# 1. Barva jako grafická proměnná

Percepční základy užití barvy v mapách shrnují Slocum et al. (2009) a Brewer (2016); empiricky ověřené barevné stupnice pro kvalitativní, sekvenční a divergentní data publikovali Brewer et al. (2003). Problém duhových a percepčně nelineárních stupnic ve vědecké vizualizaci analyzují Crameri et al. (2020). Stratigrafické barevné konvence vycházejí z Mezinárodní chronostratigrafické tabulky (Cohen et al. 2013, průběžně aktualizováno ICS).

Tři složky: **tón** (hue), **sytost**, **jas**.

- tón rozlišuje **kvalitativní** třídy (litologie, stáří),
- jas a sytost vyjadřují **pořadí a množství** (mocnost, koncentrace).

Percepce není lineární: oko rozliší asi 7–10 tónů bez legendy, u jasu 5–7 stupňů. Geologická mapa s 40 jednotkami tedy nikdy nebude čitelná „na první pohled" – a to je v pořádku, čtenář ji čte s legendou.

---

# 2. Konvence: mezinárodní stratigrafická škála

Mezinárodní komise pro stratigrafii (ICS) definuje barvy chronostratigrafických jednotek (kvartér žlutá, neogén žlutooranžová, křída zelená, jura modrá, trias fialová, perm červenohnědá, karbon šedá, devon hnědá, silur šedozelená...). ČGS má vlastní tradici, částečně odlišnou; USGS rovněž.

Pravidlo pro tento kurz:

- **základní geologická mapa** → držet konvenci ČGS/ICS,
- **účelová mapa** (hydrogeologie, radon, inženýrská geologie) → barvy volíte podle sdělení, konvence neplatí.

---

# 3. Konvence vs. percepce

Chronostratigrafické barvy jsou **nominální**: modrá jura není „víc" než zelená křída. Nesmíte je proto číst ani stavět jako gradient.

Kde jde konvence proti kartografickým pravidlům:

- sousední jednotky mají někdy podobný tón (dva odstíny hnědé devonu),
- sytá barva kvartéru (žlutá) opticky vystupuje víc než tmavé podloží,
- historické barvy nejsou navrženy pro barvoslepé.

Řešení: šrafy, silnější hranice mezi podobnými tóny, indexy uvnitř ploch.

---

# 4. Barvy pro kvantitativní geologická data

Pro mocnosti, koncentrace, hladiny podzemní vody:

- **sekvenční** stupnice (světlá → tmavá) pro hodnoty od nuly nahoru,
- **divergentní** (dva tóny se světlým středem) pro odchylky od prahu (např. limit kontaminace, průměr),
- nikdy duhová (rainbow) – vytváří falešné hranice tam, kde v datech nejsou.

Ověřené sady: ColorBrewer, Viridis, Scientific colour maps (Crameri) – ColorBrewer je součástí stylů ArcGIS Pro, ostatní lze importovat jako `.stylx` nebo zadat RGB ručně.

---

# 5. Barvoslepost

Asi 8 % mužů. Červeno-zelená kombinace (typická pro perm × křída) je nejhorší možná. Ověření:

- ArcGIS Pro → View → *Color Vision Deficiency Simulator* (protanopie/deuteranopie),
- doplňte šrafy nebo indexy,
- kontrola v šedé škále: pokud se dvě sousední plochy slijí, přidejte kontrast.

---

# Cvičení v hodině

Vezměte polygonovou vrstvu geologických jednotek studijního výřezu.

1. Obarvěte ji podle **chronostratigrafie** (ICS, styl ke stažení v [Data]({{ '/geocartography/data/' | relative_url }})).
2. Obarvěte ji podle **litologie** (vlastní kvalitativní paleta, max. 8 tónů).
3. Zapněte simulaci deuteranopie a opravte problémová místa.
4. Napište tři věty: co která verze sděluje a komu je určena.

---

# Shrnutí

- Tón = kvalita, jas = množství.
- Základní geologická mapa dodržuje konvenci; účelová mapa ji odkládá.
- Konvence není záruka čitelnosti – ověřujte šrafou, hranicí, simulací barvosleposti.

---

# Literatura

- BREWER, C. A. (2016): *Designing Better Maps: A Guide for GIS Users*. 2. vyd. Esri Press, Redlands.
- BREWER, C. A., HATCHARD, G. W., HARROWER, M. A. (2003): ColorBrewer in Print: A Catalog of Color Schemes for Maps. *Cartography and Geographic Information Science*, 30(1), 5–32.
- COHEN, K. M., FINNEY, S. C., GIBBARD, P. L., FAN, J.-X. (2013; průběžně aktualizováno): The ICS International Chronostratigraphic Chart. *Episodes*, 36(3), 199–204. Aktuální verze: stratigraphy.org.
- CRAMERI, F., SHEPHARD, G. E., HERON, P. J. (2020): The misuse of colour in science communication. *Nature Communications*, 11, 5444.
- HARROWER, M., BREWER, C. A. (2003): ColorBrewer.org: An Online Tool for Selecting Colour Schemes for Maps. *The Cartographic Journal*, 40(1), 27–37.
- SLOCUM, T. A., MCMASTER, R. B., KESSLER, F. C., HOWARD, H. H. (2009): *Thematic Cartography and Geovisualization*. 3. vyd. Pearson Prentice Hall, Upper Saddle River.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/geocartography/literatura/' | relative_url }}).
