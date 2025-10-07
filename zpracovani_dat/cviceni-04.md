#  Možnosti mapové algebry

Mapová algebra představuje univerzální rámec pro **analýzu a modelování rastrových dat** v GIS.  
Umožňuje provádět různé typy výpočtů, transformací a kombinací vrstev na základě matematických a logických vztahů.  
Její síla spočívá v tom, že všechny operace probíhají **systematicky pro každou buňku rastru**, což umožňuje přesné, plošné a opakovatelné prostorové analýzy.

---

##  1. Typy operací v mapové algebře

Klasifikace podle **C. D. Tomlina (1983)** rozlišuje čtyři základní typy operací mapové algebry podle rozsahu dat, se kterými pracují:

| Typ operace | Charakteristika | Příklad využití |
|--------------|----------------|-----------------|
| **Lokální (local)** | Každá buňka výstupu je vypočtena pouze z odpovídajících buněk vstupních rastrových vrstev. | výpočet sklonu <br> kombinace vrstev pomocí podmínek (`slope < 5 AND aspect > 270`) |
| **Fokální (focal)** | Hodnota výstupní buňky se určuje na základě okolí (okna, kernelu). | vyhlazení (mean filter), výpočet sklonu, směru odtoku |
| **Zonální (zonal)** | Operace se provádí pro celé zóny – skupiny buněk se stejnou hodnotou (např. typ půdy, povodí). | průměrná výška v každé zóně, součet srážek na území |
| **Globální (global)** | Každá buňka závisí na všech ostatních buňkách v rastru. | výpočet kumulativní vzdálenosti, normalizace hodnot, model nákladů (cost distance) |

Každý typ operace tedy umožňuje jiný způsob pohledu na prostorová data – od jednoduchých pixelových výpočtů po komplexní modely vztahů v krajině.

---

##  2. Matematické a logické operace

Mapová algebra nabízí široké spektrum **aritmetických**, **relačních** a **logických** operací, které lze libovolně kombinovat:

###  Aritmetické operace
Umožňují provádět běžné matematické výpočty:
- Sčítání / odčítání: `("layer1@1" + "layer2@1")`
- Násobení / dělení: `("layer1@1" * 0.5)`
- Normalizace: `("layer@1" / max("layer@1"))`

###  Relační operace
Slouží k porovnávání hodnot:
- Menší než `<`, větší než `>`, rovno `=`
- Příklad: `("slope@1" < 5)`

###  Logické operace
Umožňují spojovat více podmínek:
- `AND`, `OR`, `NOT`
- Příklad: `("slope@1" < 5) AND (("aspect@1" < 90) OR ("aspect@1" > 270))`

Výstupem bývá **binární rastr** (0/1), který lze dále analyzovat nebo kombinovat.

---

##  3. Analytické možnosti mapové algebry

Mapová algebra umožňuje širokou škálu analytických a modelovacích úloh:

###  Analýzy terénu
- Výpočet **sklonu**, **orientace (aspect)**, **zakřivení**, **stínování (hillshade)**
- Klasifikace terénu podle sklonu, expozice nebo nadmořské výšky

###  Hydrologické modelování
- Výpočet **směru odtoku (flow direction)** a **akumulace odtoku**
- Určení **povodí**, **depresí**, **povrchových toků**
- Modelování **erozních procesů**

###  Solární a klimatické analýzy
- Výpočet **potenciálu slunečního záření** podle orientace a sklonu
- Modelování **mikroklimatických podmínek** podle expozice

###  Územní a environmentální modelování
- Hodnocení vhodnosti území (např. pro výstavbu, obnovitelné zdroje)
- Kombinace kritérií pomocí vážených výrazů (např. `0.5*slope + 0.3*distance + 0.2*soil`)
- Tvorba **rizikových map** (povodně, sesuvy, kontaminace)

###  Krajinné a ekologické analýzy
- Modelování **stanovišť organismů** (např. jižní svahy s malým sklonem)
- Výpočet **indexů diverzity**, **fragmentace**, **viditelnosti (viewshed)**

---

##  4. Praktické nástroje v QGIS

V QGIS se mapová algebra realizuje zejména prostřednictvím:

- **Raster Calculator**  
  – hlavní nástroj pro psaní výrazů a kombinaci vrstev.  
  Umožňuje využívat aritmetiku, logiku i podmíněné funkce (např. `if`, `else`).

- **Processing Toolbox – Raster Analysis**  
  – obsahuje specializované nástroje pro výpočty sklonu, aspectu, hillshade, curvature apod.

- **GDAL nástroje**  
  – pro pokročilé operace jako reprojekce, ořez, převzorkování, filtrace (Sieve).

---

##  5. Kombinace vrstev a vícekriteriální analýzy

Jednou z nejsilnějších možností mapové algebry je **vícekriteriální analýza (Multi-Criteria Evaluation, MCE)**.  
Ta umožňuje:
- přepočítat různé vrstvy na jednotnou škálu (např. 0–1),
- aplikovat váhy dle významu kritérií,
- kombinovat vrstvy do jednoho výsledného rastru.

Příklad:
0.5 * ("slope@1" <= 5) + 0.3 * ("distance@1" < 1000) + 0.2 * ("soil@1" = "suitable")


→ výsledný rastr reprezentuje **vhodnost území** (hodnoty 0–1).

---

##  6. Shrnutí

Mapová algebra poskytuje:
- **matematický jazyk pro práci s rastrovými daty**,  
- **nástroje pro analýzu, modelování a vizualizaci prostorových jevů**,  
- **možnost kombinovat různé tematické vrstvy** (terén, klima, půda, využití území, vzdálenost atd.),  
- a tím vytvářet **nové informace** potřebné pro rozhodování, plánování a výzkum.

Z praktického hlediska je mapová algebra základem většiny moderních prostorových analýz v GIS – od jednoduchého výběru oblastí až po komplexní modely procesů v krajině.

# CV04 – Mapová algebra v QGIS (analýzy terénu)

**Cíl cvičení:**  
Najít oblasti, které splňují tyto podmínky:
- orientace na **sever**
- **sklon ≤ 5°**, a pokud je **sklon ≤ 2°**, orientace nehraje roli

---

##  1. Příprava dat

### 1.1 Stažení DMT (SRTM 30m)
1. Otevři [(https://portal.opentopography.org/raster?opentopoID=OTSRTM.082015.4326.1)]
2. Vyhledej oblast zájmu (např. podle souřadnic nebo mapy)
3. Stáhni soubor ve formátu **GeoTIFF (.tif)**

### 1.2 Import do QGIS
1. V QGIS zvol: **Layer → Add Layer → Add Raster Layer**  
2. Načti stažený soubor `.tif`  
3. Zkontroluj souřadnicový systém — má být **EPSG:4326 (WGS84)**

---

##  2. Převod do projekce S-JTSK (EPSG:5514)

### 2.1 Použij nástroj Warp (Reproject)
**Cesta:** `Raster → Projections → Warp (Reproject)`

**Parametry:**
- Input layer: `SRTM raster`
- Target CRS: `EPSG:5514 – S-JTSK / Krovak East North`
- Resampling method: *Bilinear (pro plynulý povrch)*
- Output file: `DMT_5514.tif`

**Výsledek:** nový rastr ve správné projekci.

---

##  3. Stínovaný reliéf (Hillshade)

### 3.1 Otevři nástroj
`Raster → Analysis → Hillshade`

### 3.2 Parametry:
- Input layer: `DMT_5514.tif`
- Azimuth (°): `315` *(osvětlení ze severozápadu)*
- Vertical angle (°): `45` *(výška „slunce“ nad horizontem)*
- Output: `hillshade.tif`

### 3.3 Vysvětlení parametrů:
- **Azimuth:** směr, odkud dopadá světlo  
  `0° = sever, 90° = východ, 180° = jih, 270° = západ`
- **Vertical angle:** výška světla nad obzorem  
  menší úhel → delší stíny, větší úhel → plošší osvětlení

---

## 4. Sklon svahu (Slope)

**Cesta:** `Raster → Analysis → Slope`

**Parametry:**
- Input layer: `DMT_5514.tif`
- Z factor: `1` *(pokud je DMT v metrech)*
- Output measurement: *degrees*
- Output: `slope.tif`

**Výsledek:** světlé = strmé, tmavé = ploché oblasti.

---

##  5. Orientace svahu (Aspect)

**Cesta:** `Raster → Analysis → Aspect`

**Parametry:**
- Input layer: `DMT_5514.tif`
- Output: `aspect.tif`

**Interpretace:**
- `0° → sever`
- `90° → východ`
- `180° → jih`
- `270° → západ`

---

##  6. Výběr severně orientovaných svahů

**Cesta:** `Raster → Raster Calculator`

**Výraz:**
("aspect@1" < 90) OR ("aspect@1" > 270)

**Výstup:**
- Output raster: `north.tif`
- Hodnoty: `1 = severní orientace`, `0 = jiná orientace`

---

##  7. Výběr podle sklonu

### 7.1 Sklon ≤ 2°
("slope@1" <= 2)
→ uložit jako `slope_2.tif`

### 7.2 Sklon ≤ 5°
("slope@1" <= 5)
→ uložit jako `slope_5.tif`

---

##  8. Kombinace podmínek

### 8.1 Logický výraz

Kombinujeme:
- **Podmínka A:** sklon ≤ 5° a severní orientace  
- **Podmínka B:** sklon ≤ 2° (orientace nehraje roli)

**Výraz:**
(("slope@1" <= 5) AND (("aspect@1" < 90) OR ("aspect@1" > 270))) OR ("slope@1" <= 2)

### 8.2 Výstup
- Output raster: `kombinace.tif`
- Hodnota `1 = splňuje podmínky`, `0 = nesplňuje`

---

##  9. Vyčištění malých oblastí (Sieve)

**Cesta:** `Raster → Analysis → Sieve`

**Parametry:**
- Input layer: `kombinace.tif`
- Threshold: `20`
- Connectivity: `8`
- Output: `kombinace_sieve.tif`

Tím odstraníš malé izolované pixely (< 20 px).

---

##  10. Odstranění záporných hodnot

Pokud výstup obsahuje záporné hodnoty, použij Raster Calculator:

("kombinace_sieve@1" > 0)

→ Výsledek:  
`1 = splňuje`, `0 = nesplňuje`  
→ uložit jako `final.tif`

---

##  11. Vizualizace výsledku

### 11.1 Přidej vrstvy:
- `hillshade.tif` *(stínovaný reliéf)*
- `final.tif` *(výsledný rastr)*

### 11.2 Nastavení zobrazení:
- `hillshade.tif` jako podklad  
- `final.tif` zobraz s průhledností (např. **50 %**)  
- barevná paleta: **zelená = 1**

---

##  12. Výsledek

Máš vizuálně zobrazené oblasti, které:
- jsou orientované na **sever**,
- mají **sklon ≤ 5°**,  
- nebo mají **sklon ≤ 2° bez ohledu na orientaci**,  
- a které jsou dostatečně velké *(odfiltrovány pomocí Sieve)*.
  
---

##  ZADÁNÍ DOMÁCÍHO ÚKOLU
použijte nástroje mapové algebry pro vyhledání míst potenciálně vhodných pro umístění fotovoltaických panelů v **obci vašeho bydliště** 
(pokud nepocházíte z ČR, máte obec Žďár nad Sázavou)
zajímají nás místa se sklonitostí do 15 stupňů, sluneční expozicí nad 1000 kWh/m² a mimo zastavěná území a lesy
výstupem programu bude mapový výstup, který bude obsahovat 3 mezivýsledky podmínek (rastry po klasifikaci) a výsledek
dále bude program obsahovat stručný komentář k získaným výsledkům
program zašlete na mail vedoucího do termínu požadovaném vedoucím cvičení
