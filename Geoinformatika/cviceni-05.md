
## 🧭 Teoretická část

---

### 1. Úvod do prostorových funkcí

Prostorové funkce patří mezi základní nástroje geografických informačních systémů (GIS).  
Umožňují analyzovat, jak spolu jednotlivé geografické objekty **prostorově souvisejí** – tedy jaké mezi nimi existují vztahy (překrývání, dotyk, vzdálenost apod.).

Zatímco atributová data popisují **vlastnosti objektů** (např. počet obyvatel, typ půdy, třída silnice), **geometrická složka** dat určuje jejich **polohu a tvar** v prostoru. Kombinací obou lze provádět tzv. **prostorové analýzy** – získávat nové informace, které by jinak nebyly patrné.

Příklady prostorových dotazů:
- Kolik obyvatel žije do 500 m od železniční stanice?
- Jaká část města leží v záplavovém území?
- Které silnice protínají lesní oblasti?

---

### 2. Základní principy prostorových funkcí

#### 2.1 Topologické překrytí (overlay)

*Overlay* znamená porovnání **dvou nebo více vrstev** a analýzu jejich prostorového vztahu.  
Historicky se překrývaly mapy na fóliích; dnes se overlay provádí matematicky – GIS vyhodnocuje průniky, sjednocení či rozdíly geometrií.

**Výsledek overlay analýzy** → nová vrstva s kombinovanými informacemi z obou vstupních dat.

---

#### 2.2 Booleanovská logika v prostorových analýzách

Prostorové operace se řídí **logickými operátory**:

| Operátor | Význam | Logický příklad | Prostorový příklad |
|-----------|---------|----------------|--------------------|
| **AND (∩)** | průnik | město AND les | území, které je zároveň město i les |
| **OR (∪)** | sjednocení | město OR les | území, které je buď město, nebo les |
| **NOT (-)** | rozdíl | město NOT les | město bez lesních oblastí |

---

#### 2.3 Typy prostorových dat

| Typ geometrie | Popis | Příklad |
|----------------|--------|----------|
| **Bod (point)** | přesná poloha | studna, strom |
| **Linie (polyline)** | lineární prvek bez šířky | silnice, řeka |
| **Polygon (area)** | plošný prvek | obec, les |

Při kombinaci dat musí být typy geometrie **vzájemně kompatibilní** (např. CLIP může ořezávat linie polygonem).

---

### 3. Přehled základních prostorových funkcí

#### 3.1 CLIP – ořezání dat
- **Princip:** ořezává vstupní vrstvu podle ořezové.
- **Vstup:** libovolná vrstva + polygonová ořezová vrstva.  
- **Výstup:** objekty ležící uvnitř ořezové vrstvy.  
- **Příklad:** silnice pouze na území Poruby.

---

#### 3.2 BUFFER – obalová zóna
- **Princip:** vytváří území v určité vzdálenosti od objektu.  
- **Vstup:** body, linie nebo polygony + vzdálenost.  
- **Výstup:** polygonová vrstva zón.  
- **Příklad:** pásma 500 m a 3 km kolem železničních stanic.

---

#### 3.3 INTERSECT – průnik
- **Princip:** najde překrývající se části více vrstev.  
- **Výstup:** nová vrstva pouze s průnikem.  
- **Příklad:** lesy ležící uvnitř města Ostravy.

---

#### 3.4 UNION – sjednocení
- **Princip:** spojí dvě polygonové vrstvy, zachová i nepřekrývající se části.  
- **Výstup:** vrstva všech oblastí z obou vrstev.  
- **Příklad:** sjednocení lesů a vodních ploch.

---

#### 3.5 MERGE – spojení dat
- **Princip:** sloučí více vrstev stejného typu bez ohledu na polohu.  
- **Výstup:** jedna vrstva s daty ze všech vstupů.  
- **Příklad:** spojení vrstev Poruba a Zábřeh.

---

#### 3.6 DISSOLVE – sloučení podle atributu
- **Princip:** slučuje prvky se stejným atributem do jednoho celku.  
- **Výstup:** vrstva s menším počtem prvků.  
- **Příklad:** sloučení všech částí obce Ostrava.

---

#### 3.7 ERASE – odečtení dat
- **Princip:** odečte jednu polygonovou vrstvu od druhé.  
- **Výstup:** území zbylé po odečtení.  
- **Příklad:** území ČR dále než 1 km od silnic.

---

### 4. Význam prostorových funkcí v praxi

Prostorové funkce jsou klíčovým nástrojem GIS:
- modelují dopady jevů (povodně, hluk, doprava),  
- hodnotí dostupnost (např. škol nebo nemocnic),  
- určují překryvy územních limitů,  
- vytvářejí nové datové vrstvy z kombinovaných informací.

---

# CV05 – Praktická část: Prostorové funkce (GIS)

##  Cíl cvičení
- Seznámit se s vybranými prostorovými funkcemi GIS.
- Vyzkoušet jejich použití na datech ArcČR 500.
- Naučit se vytvářet a spravovat vlastní **File Geodatabase**.
- Provést základní překryvné analýzy a interpretovat výsledky.

---

##  1. Příprava prostředí v ArcGIS Pro

### Krok 1 – Založení projektu
1. Otevři **ArcGIS Pro** a vytvoř **nový projekt** (např. `CV05_ProstoroveFunkce`).
2. Zkontroluj, že se vytvořila výchozí geodatabáze (`CV05_ProstoroveFunkce.gdb`).

### Krok 2 – Vytvoření vlastní geodatabáze
1. Otevři **Catalog** → pravým tlačítkem → *New → File Geodatabase*.
2. Pojmenuj např. `MojeData_CV05.gdb`.

### Krok 3 – Přidání zdrojových dat
1. Připoj složku s daty **ArcČR 500**.
2. Přidej vrstvy:
   - Části obce (polygony)
   - Silnice (linie)
   - Železniční stanice (body)
   - Lesy (polygony)

---

##  2. Práce s vrstvami a výběry

### Krok 4 – Výběr částí obce Ostrava
```sql
NAZ_OBEC = 'Ostrava'
```
- Exportuj jako `Ostrava_casti`.

### Krok 5 – Vytvoření vrstvy Poruba
```sql
NAZ_CAST = 'Poruba'
```
- Exportuj jako `Poruba`.

---

##  3. Prostorové operace

### Krok 6 – Výběr silnic, které kříží Porubu
- **Select by Location**
  - *Input:* Silnice  
  - *Relationship:* Intersect  
  - *Selecting Features:* Poruba  
- Exportuj jako `Silnice_Poruba`.

### Krok 7 – Ořezání silnic na území Poruby (CLIP)
- **Clip Tool**
  - *Input Features:* Silnice  
  - *Clip Features:* Poruba  
  - *Output:* `Silnice_Poruba_clip`

### Krok 8 – Sloučení částí Ostravy (DISSOLVE)
- **Dissolve Tool**
  - *Input:* Ostrava_casti  
  - *Dissolve Field:* NAZ_OBEC  
  - *Output:* `Ostrava_dissolved`

### Krok 9 – Spojení Poruby a Zábřehu (MERGE)
1. Vyber `NAZ_CAST = 'Zábřeh'` → exportuj `Zabreh`.
2. **Merge Tool**
   - *Input Datasets:* Poruba, Zabreh  
   - *Output:* `Poruba_Zabreh`

### Krok 10 – Porovnání INTERSECT a UNION
- **Intersect:** Lesy + Ostrava_dissolved → `Lesy_Ostrava_intersect`
- **Union:** Lesy + Ostrava_dissolved → `Lesy_Ostrava_union`

### Krok 11 – Vytvoření bufferů
- **Buffer Tool**
  - 500 m → `Buffer_500m`
  - 3 000 m → `Buffer_3km`

---

##  4. Aplikační úlohy

### Úloha 1 – Území do 500 m od dálnic
- Buffer 500 m → Clip s hranicí ČR → Calculate Geometry (km²).

### Úloha 2 – Obce do 10 km od řeky Morava
- Buffer 10 km → Select by Location → součet obyvatel.

### Úloha 3 – Křížení silnic a železnic
- Intersect *Dálnice + Silnice I. třídy + Železnice*.  
- Vyhodnoť počet průsečíků a podíl dálnic (%).

### Úloha 4 – Spojení letišť a železničních stanic
- Merge *Letiště + Železniční stanice* → zjisti počet objektů.

### Úloha 5 – Území dále než 1 km od silnic
- Buffer 1 km → Erase od území ČR → oblasti bez dostupnosti.

### Úloha 6 – Území do 100 km od hranic Prahy
- Vyber Prahu → Buffer 100 km → Clip s ČR → výpočet výměry.

### Úloha 7 – Lesní pokryv Ostravy
- Intersect Lesy × Ostrava_dissolved → výpočet plochy a procent.

---

##  5. Editace dat a atributů

1. Panel **Edit** → *Select* okres → *Delete Selected* (např. Frýdek-Místek).  
2. V *Attribute Table* uprav hodnoty nebo přidej nové záznamy.

---

##  6. Shrnutí funkcí

| Funkce | Typ operace | Účel |
|--------|--------------|------|
| CLIP | Ořez | vymezení území podle jiné vrstvy |
| BUFFER | Vzdálenostní analýza | analýza okolí objektů |
| INTERSECT | Překryv | nalezení společného území |
| UNION | Sjednocení | kombinace všech oblastí |
| MERGE | Spojení vrstev | sloučení více vstupů |
| DISSOLVE | Sloučení atributové | tvorba větších celků |
| ERASE | Rozdíl | odečtení území |

---

##  7. Závěr
Prostorové funkce jsou základem analytické práce v GIS.  
Díky nim lze efektivně:
- analyzovat vztahy mezi objekty,
- vytvářet nové vrstvy kombinující různé informace,
- a podporovat rozhodování v územním plánování, dopravě i ekologii.

---







