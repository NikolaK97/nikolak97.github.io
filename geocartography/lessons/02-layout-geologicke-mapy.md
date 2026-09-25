---
title: 02 – Kompozice a layout geologické mapy
---

# 02 – Kompozice a layout geologické mapy

## Cíle lekce

- znáte povinné a volitelné prvky mapového listu,
- umíte sestavit legendu geologické mapy ve stratigrafickém pořadí,
- chápete vztah mapového pole, řezu a stratigrafického sloupce,
- umíte vytvořit vizuální hierarchii tak, aby geologie dominovala.

---

# 1. Prvky geologického mapového listu

Kompozice mapy je v české kartografické literatuře systematicky popsána Voženílkem, Kaňokem et al. (2011) a Miklínem et al. (2018); obecné zásady vizuální hierarchie a rozvržení listu shrnují Dent et al. (2009) a Brewer (2016). Pro geologické mapy platí navíc oborové konvence uspořádání legendy a doprovodných grafických prvků (řez, sloupec), kodifikované např. standardem FGDC (2006) a v české praxi edicí map ČGS.

**Základní (povinné):**

- mapové pole,
- název (území, téma, měřítko, typ – např. „Geologická mapa zakrytá, 1:25 000"),
- legenda,
- měřítko (grafické!),
- tiráž: autor, datum, zdroje dat, souřadnicový systém.

**Nadstavbové (u geologické mapy prakticky vždy):**

- geologický řez s vyznačenou linií řezu v mapě,
- stratigrafický sloupec,
- směrová růžice (pokud sever není nahoře),
- vedlejší mapky (tektonické schéma, pozice území).

---

# 2. Legenda geologické mapy není seznam

V obecné tematické mapě je legenda výčet značek. V geologii je legenda **sama nositelem informace**:

- jednotky se řadí **od nejmladší nahoře po nejstarší dole** (superpozice),
- v rámci stejného stáří se řadí podle geneze (sedimenty, vulkanity, intruziva),
- ke každé jednotce patří: barevné pole, index (např. *Q*, *D₂*), stručný litologický popis a stáří,
- liniové a bodové značky následují **až za** plošnými.

Stratigrafický sloupec je rozšířená legenda: přidává mocnost a vztahy (diskordance, transgrese).

---

# 3. Vizuální hierarchie

Čtenář má vidět v tomto pořadí:

1. geologické jednotky (barva, plocha),
2. tektoniku (silné linie, zlomy),
3. hranice jednotek a strukturní značky,
4. topografický podklad (šedé, tenké, potlačené),
5. rám, síť, tiráž.

Nástroje hierarchie: **kontrast, tloušťka linie, barva, velikost, pozice**.

Typické chyby:

- vrstevnice černé a silnější než geologické hranice,
- popisky sídel tučné, popisky jednotek žádné,
- řez umístěn mimo list, linie řezu v mapě chybí,
- rastrový podklad (ortofoto) plnou sytostí pod geologií.

---

# 4. Rozvržení listu

Doporučené rozvržení pro formát A3 na šířku:

- mapové pole zabírá 60–70 % plochy, vlevo,
- legenda vpravo, ve stratigrafickém pořadí,
- řez pod mapovým polem, ve stejném délkovém měřítku jako mapa (převýšení uvést!),
- tiráž vpravo dole.

Řez musí mít stejné barvy a indexy jako mapa. Pokud se liší, čtenář vám přestane věřit.

---

# Cvičení v hodině

Dostanete list geologické mapy rozstříhaný na prvky (mapové pole, legenda v náhodném pořadí, řez, sloupec, tiráž, měřítko, název). Ve dvojicích:

1. seřaďte položky legendy podle superpozice,
2. sestavte list,
3. označte, který prvek by měl být vizuálně nejsilnější a proč.

---

# Shrnutí

- Legenda geologické mapy nese informaci sama o sobě – pořadí není libovolné.
- Mapa, řez a sloupec jsou jeden dokument se společnou symbolikou.
- Podklad je vždy potlačen. Geologie dominuje.

---

# Literatura

- BREWER, C. A. (2016): *Designing Better Maps: A Guide for GIS Users*. 2. vyd. Esri Press, Redlands.
- DENT, B. D., TORGUSON, J. S., HODLER, T. W. (2009): *Cartography: Thematic Map Design*. 6. vyd. McGraw-Hill, New York.
- FGDC (2006): *FGDC Digital Cartographic Standard for Geologic Map Symbolization*. FGDC-STD-013-2006. Federal Geographic Data Committee, U.S. Geological Survey, Reston.
- MIKLÍN, J., DUŠEK, R., KRTIČKA, L., KALÁB, O. (2018): *Tvorba map*. Ostravská univerzita, Ostrava.
- SPENCER, E. W. (2000): *Geologic Maps: A Practical Guide to the Preparation and Interpretation of Geologic Maps*. 2. vyd. Prentice Hall, Upper Saddle River.
- VOŽENÍLEK, V., KAŇOK, J. a kol. (2011): *Metody tematické kartografie: vizualizace prostorových jevů*. Univerzita Palackého, Olomouc.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/literatura/' | relative_url }}).
