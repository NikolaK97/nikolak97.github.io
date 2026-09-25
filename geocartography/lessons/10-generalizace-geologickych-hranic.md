---
layout: page
title: 10 – Generalizace geologických hranic
---

# 10 – Generalizace geologických hranic

## Cíle lekce

- rozumíte tomu, proč se mapa při zmenšení měřítka musí zjednodušit,
- znáte operace generalizace a umíte je aplikovat na geologické polygony a linie,
- umíte slučovat jednotky podle stratigrafické hierarchie,
- chápete minimální zobrazitelnou plochu a její důsledky.

---

# 1. Proč generalizovat

Kartografická generalizace je teoreticky zpracována v klasické práci McMastera & Shea (1992) a v přehledu Robinsona et al. (1995); algoritmus zjednodušení linií, dodnes používaný v GIS, navrhli Douglas & Peucker (1973). V české literatuře se generalizaci věnují Voženílek, Kaňok et al. (2011) a Miklín et al. (2018). Pro geologické mapy je specifické, že generalizace obsahu se opírá o stratigrafickou hierarchii, nikoli pouze o geometrii (Maltman 1998).

Mapa 1:10 000 zmenšená na 1:200 000 není mapa 1:200 000. Je to nečitelný shluk. Generalizace je řízené zjednodušení, které **zachová podstatu** a **odstraní detail**.

Vazba na lekci 01: to, co jste porovnávali mezi 1:50 000 a 1:500 000, byl výsledek generalizace.

---

# 2. Operace generalizace

| Operace | Co dělá | Geologický příklad |
|---|---|---|
| **výběr** | vynechání objektů | drobné výchozy, malé zlomy |
| **zjednodušení** | méně vrcholů linie | vyhlazení hranice jednotky |
| **slučování** | spojení objektů | souvrství → skupina; drobná tělesa → okolní jednotka |
| zvětšení | zdůraznění malého, ale důležitého | úzká žíla, tenká vrstva vůdčího horizontu |
| přesun | odsunutí kolidujících objektů | značky měření |
| typizace | nahrazení skupiny reprezentativním vzorem | shluk suťových kuželů → jeden symbol |

Zvětšení je v geologii důležité: vůdčí horizont o mocnosti 2 m se v 1:50 000 nezobrazí, ale bez něj mapa ztrácí strukturní informaci. Zobrazí se jako **linie** místo plochy.

---

# 3. Slučování podle stratigrafie

Geologie má výhodu: hierarchie jednotek je dána (vrstva → souvrství → skupina; stupeň → oddělení → útvar). Generalizace tedy není libovolná:

- 1:25 000 – souvrství, facie,
- 1:50 000 – souvrství,
- 1:200 000 – skupiny, útvary,
- 1:500 000 a menší – útvary, komplexy.

Sloučená jednotka dostane barvu a index nadřazené jednotky. Legenda se přepíše. Nikdy nenechte původní 40 položek legendy u sloučené mapy.

---

# 4. Minimální zobrazitelná plocha

Pravidlo palce: **2 × 2 mm v tisku** je nejmenší čitelná plocha, u linie 0,5 mm délky mezi lomy.

| Měřítko | 2 mm = | Min. plocha v terénu |
|---|---|---|
| 1:10 000 | 20 m | 0,04 ha |
| 1:50 000 | 100 m | 1 ha |
| 1:200 000 | 400 m | 16 ha |

Menší tělesa: sloučit s okolím, nebo zobrazit bodovou značkou (výskyt), nebo zvětšit (pokud jsou důležitá).

---

# 5. Generalizace hranic v ArcGIS Pro

- *Simplify Polygon* (Douglas–Peucker / Bend simplify) – tolerance podle měřítka (cca 0,2 mm v tisku),
- *Smooth Polygon* (PAEK) – vyhlazení po zjednodušení,
- *Dissolve* podle atributu nadřazené jednotky,
- *Eliminate* – malá tělesa (vybraná dotazem na plochu) do souseda s nejdelší společnou hranicí,
- **topologie**: *Simplify Polygon* a *Smooth Polygon* umí zachovat sdílené hranice (volba *Keep collapsed points* / topologicky konzistentní zpracování); ověřte *Check Geometry* a topologická pravidla *Must Not Overlap*, *Must Not Have Gaps*.

---

# Cvičení v hodině

Geologická mapa 1:25 000 studijního výřezu (polygony s polem `souvrstvi`, `skupina`, `utvar`).

1. *Dissolve* podle `skupina` → mapa 1:100 000.
2. Vyberte polygony menší než min. plocha pro 1:100 000 (Select by Attributes na `Shape_Area`) a spusťte *Eliminate*.
3. *Simplify Polygon* + *Smooth Polygon* s odpovídající tolerancí.
4. Přepište legendu. Porovnejte s originálem: co se ztratilo, a jestli to bylo správně.

---

# Shrnutí

- Generalizace není zmenšení, je to odborný výběr.
- V geologii máte oporu ve stratigrafické hierarchii – používejte ji.
- Minimální plocha rozhoduje, co je plocha, co bod a co zmizí.

---

# Literatura

- DOUGLAS, D. H., PEUCKER, T. K. (1973): Algorithms for the reduction of the number of points required to represent a digitized line or its caricature. *Cartographica*, 10(2), 112–122.
- MALTMAN, A. (1998): *Geological Maps: An Introduction*. 2. vyd. Wiley, Chichester.
- MCMASTER, R. B., SHEA, K. S. (1992): *Generalization in Digital Cartography*. Association of American Geographers, Washington.
- MIKLÍN, J., DUŠEK, R., KRTIČKA, L., KALÁB, O. (2018): *Tvorba map*. Ostravská univerzita, Ostrava.
- ROBINSON, A. H., MORRISON, J. L., MUEHRCKE, P. C., KIMERLING, A. J., GUPTILL, S. C. (1995): *Elements of Cartography*. 6. vyd. Wiley, New York.
- VOŽENÍLEK, V., KAŇOK, J. a kol. (2011): *Metody tematické kartografie: vizualizace prostorových jevů*. Univerzita Palackého, Olomouc.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/geocartography/literatura/' | relative_url }}).
