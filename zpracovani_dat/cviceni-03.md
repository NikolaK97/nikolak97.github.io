---
layout: page
title: Cvičení 03 — Mapová algebra v ArcGIS Pro
permalink: /zpracovani_dat/cviceni-03/
---

**CV03 – Mapová algebra v ArcGIS Pro**  
**Téma:** výběr míst vhodných pro FVE v obci **Albrechtice (Karviná)**  
**Kritéria:** sklon ≤ 15°, solární expozice ≥ 1000 kWh/m², mimo zastavěná území a lesy (CLC).

---

## 0) Příprava projektu a prostředí
- **Nový projekt:** `Map` → pojmenuj např. `CV03_MapAlgebra_Albrechtice`.
- **Geodatabáze:** ve výchozí `CV03_MapAlgebra_Albrechtice.gdb` vytvoř *Feature Dataset* `S_JTSK` (volitelné) v **S-JTSK / Krovák East North (EPSG:5514)**.
- **Nastavení prostředí (důležité!):**
  - `Geoprocessing` panel → `Environments` (globálně pro projekt):
  - **Output coordinate system:** `EPSG:5514`
  - **Processing extent:** `Same as layer` → později **Albrechtice**
  - **Snap Raster:** nastavíme až po vytvoření prvního rastru (*DMR5G ořez*)
  - **Cell Size:** `Maximum of Inputs` nebo pevně **5 m** (*DMR5G má ~5 m*)
  - **Mask:** nastavíme na polygon obce (po jeho vytvoření)

> Tím zajistíš stejné rozsahy a mřížku pro všechny výstupy.

---

## 1) Hranice obce Albrechtice
**Data:** *ArcČR 4.3* (vrstva **Obce** a případně **Obce – definiční body**).

1. Přidej ArcČR 4.3 do projektu (balík nebo sdílená GDB).
2. Pokud **Obce** obsahuje názvy, proveď **Select By Attributes**:  
   `NAZEV = 'Albrechtice'` (nebo jiný odpovídající atribut/jméno).
3. Pokud názvy chybí: **Spatial Join** mezi polygonovou vrstvou *Obce* (**Target**) a *Obce – definiční body* (**Join Features**) → získáš textové názvy do polygonů. Pak vyber **Albrechtice**.
4. **Export** vybrané obce: pravým na *Obce* → **Data → Export Features** → `gdb: Albrechtice_boundary`.
5. Nastav symbologii a ulož si vrstvu; tuto vrstvu použij jako **Mask** a **Processing Extent** v *Environments*.

---

## 2) DMR5G – připojení, zobrazení a sklon
- **Zdroj:** ImageServer ČÚZK `https://ags.cuzk.cz/arcgis2/rest/services/dmr5g/ImageServer`.
1. **Připojení serveru:** `Insert` → `Connections` → `Server` → **Add ArcGIS Server Connection** → vlož URL. Případné přihlašovací okno zavři křížkem.
2. V **Catalog → Servers** přetáhni `dmr5g` do mapy.
3. Ve **vlastnostech** vrstvy `dmr5g` → **Processing Template** nastav `None` (uvidíš výšky, ne jen stínování).
4. **Ořez na obec:** *Data Management → Raster Processing → Clip Raster*
   - *Input Raster:* `dmr5g`
   - *Output Extent / Feature geometry:* `Albrechtice_boundary` (zaškrtni **Use Input Features for Clipping Geometry**)
   - *Output name:* `DMR5G_albrechtice` (rastr v GDB)
   - *Pozn.:* U ImageServeru je výpočet *on-the-fly*. Pro vyšší výkon můžeš udělat **Copy Raster** po klipu, aby sis vytvořila lokální raster dataset.
5. **Nastav Snap Raster:** `Environments` → `Snap Raster = DMR5G_albrechtice`; `Mask = Albrechtice_boundary`; `Cell size = 5`.
6. **Sklon (Slope):** *Spatial Analyst → Surface → Slope*
   - *Input raster:* `DMR5G_albrechtice`
   - *Output measurement:* `DEGREE` (stupně)
   - *Z-Factor:* `1` (výšky v metrech, horizont v metrech – Křovák)
   - *Output raster:* `Slope_deg`

---

## 3) CLC 2018 – ořez, převod na rastr, reklasifikace
**Zdroje:** *CLC 2018 ČR* + legenda (kódy `CODE_18`). **Kritéria: nevhodné →** urban `112`; les `311, 312, 313`.

1. Přidej CLC 2018 **vektor (polygon)**.
2. **Ořez na obec:** `Analysis → Tools → Clip`
   - *Input Features:* `CLC_2018`
   - *Clip Features:* `Albrechtice_boundary`
   - *Output:* `CLC18_clip`
3. **Feature to Raster:** *Conversion → To Raster → Feature To Raster*
   - *Input Features:* `CLC18_clip`
   - *Field:* `CODE_18`
   - *Cell Size:* `5` (shodně s DMR)
   - *Output raster:* `CLC18_r5`
4. **Reklasifikace CLC:** *Spatial Analyst → Reclassify*
   - *Input raster:* `CLC18_r5`
   - Vytvoř pravidla:
     - `112, 311, 312, 313 → 0` *(nevhodné)*
     - **Vše ostatní → 1**
     - *(Tip: použij **Unique values** → vyber uvedené třídy do 0; ostatní hromadně na 1.)*
   - *Output raster:* `CLC_ok` *(binární 0/1)*

---

## 4) Reklasifikace sklonu
- `0–15° → 1`, `>15° → 0`  
- *(Pomocí **Classify** → **Manual break** na 15)*  
- **Output raster:** `Slope_ok` *(binární 0/1)*

---

## 5) Analýza slunečního záření (Area Solar Radiation)
**Nástroj:** *Spatial Analyst → Solar Radiation → Area Solar Radiation*  
*Pozn.: Výpočet je náročný — běž spíše na ořezaném DMR5G.*

**Doporučené parametry (pro srovnatelný výsledek):**
- *Input raster:* `DMR5G_albrechtice`
- *Time configuration:* **Whole Year**
- *Date/time interval:* ponech výchozí (např. 14-day / Monthly podle verze)
- *Calculation directions / sky size:* výchozí hodnoty (např. sky size ~200)
- *Z-factor:* `1`
- *Slope/Aspect:* **From DEM** (automaticky)
- *Diffuse/Direct model parameters:* výchozí hodnoty
- *Output raster:* `Solar_Whm2`

> Výstup je v **Wh/m²**. Potřebujeme **kWh/m²** a prahovat na **≥ 1000 kWh/m²**.

### 5.1 Převod jednotek a prahování
Máš dvě pohodlné cesty:

**A) Raster Calculator (v jednom kroku)**
Solar_kWh_ok = Con( (Solar_Whm2 / 1000.0) >= 1000, 1, 0 )


**nebo** rozdělit na dva kroky:

**B1) Raster Calculator – převod:**

Solar_kWh = Solar_Whm2 / 1000.0


**B2) Raster Calculator – prahování:**
Solar_kWh_ok = Con( Solar_kWh >= 1000, 1, 0 )


*Výsledek `Solar_kWh_ok` je binární 0/1.*

---

## 6) Kombinace kritérií – Raster Calculator
Chceme pixely splňující **všechny tři** podmínky (**logické AND**):

- `Slope_ok = 1`
- `CLC_ok = 1`
- `Solar_kWh_ok = 1`

**Raster Calculator:**
PV_suitable = Con( (Slope_ok == 1) & (CLC_ok == 1) & (Solar_kWh_ok == 1), 1, 0 )

**Output:** `PV_suitable` *(binární 0/1).*

> Díky `Mask = Albrechtice_boundary` a `Snap = DMR5G_albrechtice` už máš vše zarovnané a ořezané.

---

## 7) Kontroly kvality a čištění
- **NoData:** zkontroluj, že v binárních rastrů mimo Albrechtice není nic (*mask to řeší*). Uvnitř obce by NoData být nemělo; pokud je, může to být v CLC (díra) — vyřeš nastavením **Reclassify** pro **All other values**.
- **Pixel alignment:** otevři *Layer Properties → Source* a ověř u `Slope_ok`, `CLC_ok`, `Solar_kWh_ok`, `PV_suitable` stejné **Cell Size**, **Extent** a **Origin**.
- **Rychlá vizuální kontrola:**
  - `Slope_ok:` světlé/rovné plochy = 1
  - `CLC_ok:` nezastavěné a nelesní = 1
  - `Solar_kWh_ok:` vyšší osvit = 1
  - `PV_suitable:` průnik výše.

---

## 8) (Volitelné) Vektorová generalizace a plošné filtry
Pokud chceš odstranit šum a velmi malé plochy:

1. **Region Group** (*Spatial Analyst → Generalization*) na `PV_suitable` (consider only **4-neighbors**).
2. **Set Null** malé regiony (např. `< 10` pixelů = `< 250 m²`):  
   `Con( (PV_suitable == 1) & (RegionSize >= 10), 1 )` (přes kombinaci s výstupy **Region Group**).
3. **Raster to Polygon** a **Eliminate / Eliminate Polygon Part** pro odstranění drobků a úzkých částí.  
   Výsledek ulož jako `PV_suitable_poly`.

---

## 9) Mapové výstupy (co odevzdat)

### 9.1 Kompoziční mapa
- Vytvoř **Layout A4/A3**.
- Zobraz čtyři panely:
  - `Slope_ok` (0/1 symbologie)
  - `CLC_ok` (0/1)
  - `Solar_kWh_ok` (0/1)
  - `PV_suitable` (0/1, zvýrazni **1** zeleně)
- Přidej **legendu, měřítko, severku, popis datových zdrojů a souřadnicový systém**.
- **Exportuj** jako PDF a PNG (300 dpi). Pojmenuj např.:
  - `CV03_Albrechtice_mezivysledky.pdf`
  - `CV03_Albrechtice_vysledek.pdf`

### 9.2 Stručný komentář (příklad osnovy)
- **Metodika:** DEM (*DMR5G, 5 m*), *Area Solar Radiation* (celý rok), CLC 2018 (kódy nevhodných tříd), reklasifikace na 0/1, logický průnik.
- **Zjištění:** plocha vhodných pixelů (m²/ha), hlavní lokality (např. podél X, plochy polí), omezení (stínění lesy, sklony svahů).
- **Limitace:** přesnost CLC v měřítku, sezónnost modelu, nezohledněné stínění objekty/vegetací lokálního měřítka, technické a právní faktory (OÚP, sítě).
- **Doporučení:** případně přidat další kritéria (dist. k sítím, vzdál. od zástavby, orientace SV–JZ, aspekty).

---

## 10) „Samostatný program" (aplikuj na obec bydliště)
Stejný postup; pouze v kroku **1** vyber *tvoji* obec.  
Kritéria zůstávají: **Slope ≤ 15°**, **Solar ≥ 1000 kWh/m²**, **CLC ≠ {112, 311, 312, 313}**.  
Odevzdej mapu se 3 mezivýsledky (`Slope_ok`, `CLC_ok`, `Solar_kWh_ok`) a výsledkem (`PV_suitable`) + stručný komentář.  
Souborový titul a text v mapě změň na název **tvojí obce** a **datum**.

---

## Shrnutí klíčových nástrojů (rychlá taháková verze)
- **Clip (Features)** → `Albrechtice_boundary`
- **Clip Raster / Copy Raster** → `DMR5G_albrechtice`
- **Slope** → `Slope_deg`
- **Reclassify (Slope)** → `Slope_ok` (≤15 → 1; jinak 0)
- **Clip (CLC)** → `CLC18_clip`
- **Feature to Raster (CODE_18, 5 m)** → `CLC18_r5`
- **Reclassify (CLC)** → `CLC_ok` (`112,311–313 → 0`; ostatní → 1)
- **Area Solar Radiation (Whole Year)** → `Solar_Whm2`
- **Raster Calculator** → `Solar_kWh_ok = Con((Solar_Whm2/1000) >= 1000, 1, 0)`
- **Raster Calculator (finále)** → `PV_suitable = Con((Slope_ok==1) & (CLC_ok==1) & (Solar_kWh_ok==1), 1, 0)`

---

## Tipy a časté záseky (ušetří nervy)
- **Environments** nastav hned po vytvoření `DMR5G_albrechtice` (**Snap, Cell size, Mask, Extent**).
- **S-JTSK všude:** vyvaruj se mixu S-JTSK (m) a WGS84 (stupně); *Slope* i *Solar* pak sedí se **Z-faktorem 1**.
- **CLC detail:** pokud máš jen kategorie bez `CODE_18`, připoj si legendu tabulkou (**Join**) a vezmi příslušné kódy.
- **Výkon Solar:** pokud to trvá moc, sniž *sky size*, zvyšte interval (měsíční) a ověř, že počítáš jen ořez obce.
- **Symbologie 0/1:** jednoduché 2-třídní barvy, ať je kompozice čitelná.
- **Reproducibilita:** pojmenuj výstupy přesně jako výše; exportuj **Layer Files (.lyrx)** a přilož do odevzdání.

