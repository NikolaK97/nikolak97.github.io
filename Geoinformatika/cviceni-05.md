
##  Teoretická část

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

## Společná část

Všechny kroky vedou ke stejnému vzoru: vybrat prvky (atributově nebo polohově) → exportovat → spustit prostorovou funkci. Výstupy pojmenovávejte jednotně, např. `Ostrava_casti`, `Poruba`, `Silnice_Poruba_clip`.

### 1. Přidání vrstev

1. Z administrativní geodatabáze přidejte **části obcí – polygony**, z topografické **silnice**.
2. Otevřete atributovou tabulku částí obcí a najděte pole s názvem obce (typicky něco jako `NAZ_OBEC`) a s názvem části obce (typicky `NAZEV` / `NAZ_CAST`).

### 2. Části obce Ostrava → nová vrstva

1. **Map → Select By Attributes**, Input Rows = části obcí.
2. Podmínka: *pole názvu obce* **is equal to** `Ostrava`. Spolehlivější je filtr přes kód obce – Ostrava má kód **554821**.
3. **Apply / OK** a ověřte počet vybraných prvků dole v tabulce.
4. Pravým na vrstvu → **Data → Export Features** → výstup `Ostrava_casti` do projektové geodatabáze. Export bere jen vybrané prvky.

### 3. Poruba → nová vrstva

1. **Select By Attributes** nad vrstvou `Ostrava_casti` (ne nad celou ČR – části obce jménem Poruba existují i jinde).
2. Podmínka: název části obce **is equal to** `Poruba`.
3. **Export Features** → `Poruba`.

### 4. Silnice křížící Porubu → nová vrstva

1. **Map → Select By Location**.
2. Input Features = **silnice**, Relationship = **Intersect**, Selecting Features = **Poruba**.
3. **Export Features** → `Silnice_Poruba`. Silnice jsou celé, tedy přesahují hranici Poruby – to je správně, ořez přijde v dalším kroku.

### 5. Ořez silnic na území Poruby (CLIP)

1. **Map → Clear** (zrušit výběr na silnicích).
2. **Analysis → Tools** → vyhledejte **Pairwise Clip** (rychlejší) nebo **Clip**.
3. Input Features = `Silnice_Poruba` (nebo rovnou celé silnice), Clip Features = `Poruba`, Output = `Silnice_Poruba_clip`.
4. Porovnejte délky: v tabulce výstupu se `Shape_Length` přepočítá jen na úseky uvnitř Poruby.

### 6. Sloučení částí Ostravy do jednoho polygonu (DISSOLVE)

1. V tabulce `Ostrava_casti` hledejte pole, které mají **všechny části stejné** – název obce nebo kód obce. To je odpověď na otázku zadání.
2. Nástroj **Dissolve** (nebo **Pairwise Dissolve**): Input = `Ostrava_casti`, Dissolve Field = *kód obce* (případně název obce), Output = `Ostrava_dissolve`.
3. Nechte zaškrtnuté **Create multipart features**. Výsledkem je 1 prvek = celá Ostrava. Tuto vrstvu budete používat i v úloze o lesích.

### 7. Zábřeh → nová vrstva

1. **Select By Attributes** nad `Ostrava_casti`.
2. V RÚIAN se část obce jmenuje **Zábřeh nad Odrou**, proto použijte podmínku **contains the text** `Zábřeh` (SQL: `NAZEV LIKE '%Zábřeh%'`).
3. **Export Features** → `Zabreh`.

### 8. Spojení Zábřehu a Poruby (MERGE)

1. Nástroj **Merge**: Input Datasets = `Poruba` + `Zabreh`, Output = `Poruba_Zabreh`.
2. V sekci **Field Map** můžete přidat zdrojové pole (*Add source information to output*) – uvidíte, ze které vrstvy prvek pochází.
3. Výstup má 2 samostatné prvky. Rozdíl oproti Dissolve: Merge spojuje **vrstvy**, Dissolve slučuje **prvky** podle atributu.

### 9. Lesy: INTERSECT vs. UNION s Ostravou

1. Přidejte vrstvu **lesů**.
2. **Pairwise Intersect**: Input = `lesy` + `Ostrava_dissolve`, Output = `Lesy_Ostrava_intersect`.
3. **Union**: Input = `lesy` + `Ostrava_dissolve`, Output = `Lesy_Ostrava_union`. Union nad lesy celé ČR trvá déle; můžete nejdřív vybrat lesy, které Ostravu protínají (Select By Location), a pustit ho jen na ně.
4. Porovnejte výsledky podle tabulky níže. V Union mají prvky bez překryvu v poli `FID_lesy` nebo `FID_Ostrava_dissolve` hodnotu **−1**.

|                       | Intersect                                    | Union                                        |
| --------------------- | -------------------------------------------- | -------------------------------------------- |
| Co ve výstupu zůstane | jen plochy, kde je les **a zároveň** Ostrava | vše z obou vrstev, rozřezané podle hranic    |
| Booleovská logika     | AND                                          | OR                                           |
| Atributy              | z obou vrstev                                | z obou, u nepřekrytých částí prázdné / −1    |
| Typ vstupů            | polygony, linie i body                       | jen polygony                                 |

### 10. Buffery kolem železničních stanic

1. Přidejte vrstvu **železničních stanic**.
2. Nástroj **Pairwise Buffer** (nebo **Buffer**): Input = stanice, Distance = **500 Meters**, Dissolve Type = **Dissolve all** (překrývající se zóny se sloučí), Output = `Stanice_buffer_500m`.
3. Zopakujte s **3 Kilometers** → `Stanice_buffer_3km`.
4. Alternativa: **Multiple Ring Buffer** s hodnotami `500; 3000` a jednotkou Meters vytvoří obě zóny v jedné vrstvě (vícevrstevná obalová zóna).

## Analytické úlohy

Každá úloha končí výpočtem plochy nebo počtu. Plochu v km² získáte tak, že do výsledné vrstvy přidáte pole `Plocha_km2` (Double) a v tabulce dáte pravým na něj **Calculate Geometry** → Property **Area**, Area Unit **Square kilometers**. Pokud má výstup víc prvků, sečtěte je: pravým na pole → **Statistics** (ukáže Sum). Konkrétní čísla zde nejsou uvedena, protože se liší podle verze dat – u ArcČR 4.3 vyjdou jinak než u v3.3.

### Úloha 1 – Výměra území ČR do 500 m od dálnic

1. **Select By Attributes** na silnicích: pole třídy silnice = *dálnice*. Přes **Field → Unique values** si nejdřív ověřte přesné hodnoty – od roku 2016 jsou bývalé rychlostní silnice vedené jako dálnice II. třídy, takže může jít o více hodnot.
2. **Pairwise Buffer**: Input = silnice (s výběrem), Distance = **500 Meters**, Dissolve Type = **Dissolve all** – jinak by se překryvy zón u křižovatek započítaly dvakrát. Output = `Dalnice_buf500`.
3. **Map → Clear**, pak **Pairwise Clip**: Input = `Dalnice_buf500`, Clip Features = polygon **ČR / stát**, Output = `Dalnice_buf500_CR`. Tím odříznete části bufferu za hranicí (např. u D5, D8, D1 u Polska).
4. Spočítejte plochu v km².

### Úloha 2 – Obce celé do 10 km od řeky Moravy a jejich obyvatelé

1. **Select By Attributes** na vodních tocích: název **is equal to** `Morava`. Nepoužívejte *contains* – chytili byste Moravici, Morávku, Moravskou Sázavu či Moravskou Dyji. Zkontrolujte, že vybrané úseky vedou od Králického Sněžníku po soutok s Dyjí.
2. **Pairwise Buffer**: Distance = **10 Kilometers**, Dissolve Type = **Dissolve all**, Output = `Morava_buf10km`.
3. **Map → Clear**, pak **Select By Location**: Input = **obce**, Relationship = **Completely within** (případně **Within**), Selecting Features = `Morava_buf10km`.
4. Počet obcí odečtěte dole v tabulce (Selected …).
5. Počet obyvatel: v tabulce obcí pravým na pole s počtem obyvatel (u SLDB 2021 / ČSÚ statistik – ověřte název) → **Statistics** → hodnota **Sum** se počítá jen z vybraných prvků. Alternativa: nástroj **Summary Statistics** se statistikou SUM.

### Úloha 3 – Křížení dálnic, rychlostních silnic a silnic I. třídy se železnicí

1. **Select By Attributes** na silnicích: třída = dálnice **OR** rychlostní silnice (pokud v datech existuje) **OR** silnice I. třídy. Export → `Silnice_D_R_I`.
2. **Pairwise Intersect**: Input = `Silnice_D_R_I` + **železnice**, **Output Type = Point**, Output = `Krizeni_body`. Bez přepnutí na Point dostanete linie, ne místa křížení.
3. **Multipart To Singlepart** → `Krizeni_single`. Intersect může do jednoho prvku sloučit víc bodů.
4. Volitelně **Delete Identical** (pole Shape) – odstraní duplicitní body vzniklé tam, kde je silnice či železnice v místě křížení rozdělená na dva úseky.
5. Celkový počet = počet řádků `Krizeni_single`.
6. Podíl dálnic: **Select By Attributes** na `Krizeni_single`, třída = dálnice (atributy silnic se do výstupu přenesou). Podíl = počet dálničních křížení / celkový počet × 100.
7. Poznámka do protokolu: ve 2D se počítá každé mimoúrovňové i úrovňové křížení stejně, mosty se nerozlišují.

### Úloha 4 – Společná vrstva letišť a železničních stanic

1. Ověřte typ geometrie obou vrstev (pravým → **Properties → Source**). Merge vyžaduje stejný typ.
2. Pokud jsou letiště polygony, převeďte je nástrojem **Feature To Point** (zaškrtnout *Inside*) → `Letiste_body`.
3. **Merge**: Inputs = letiště (body) + železniční stanice, Output = `Letiste_Stanice`.
4. Počet objektů = počet řádků výstupu a musí se rovnat součtu počtů obou vstupů – to je kontrola správnosti.

### Úloha 5 – Výměra ČR dále než 1 km od nejbližší silnice (ERASE)

1. **Map → Clear** na silnicích (bereme všechny třídy).
2. **Pairwise Buffer**: Input = silnice, Distance = **1 Kilometers**, Dissolve Type = **Dissolve all**, Output = `Silnice_buf1km`. Vrstva silnic je velká, výpočet může trvat několik minut.
3. **Pairwise Erase**: Input = polygon **ČR**, Erase Features = `Silnice_buf1km`, Output = `CR_dal_1km_od_silnic`.
4. Spočítejte plochu v km². Kontrola: výsledek + plocha bufferu oříznutého na ČR ≈ rozloha ČR (78 871 km²).

### Úloha 6 – Výměra ČR do 100 km od hranic Prahy

1. **Select By Attributes** na krajích (nebo obcích): název = `Hlavní město Praha`. Export → `Praha`.
2. **Buffer**: Input = `Praha`, Distance = **100 Kilometers**, Dissolve Type = **Dissolve all**, Output = `Praha_buf100km`.
3. Rozhodněte, zda se má započítat i území Prahy samotné. Výchozí buffer polygon zahrnuje; parametr **Side Type = Exclude the input polygon** (`OUTSIDE_ONLY`, jen v nástroji Buffer, ne v Pairwise Buffer) ho vynechá. Do protokolu napište, kterou variantu jste zvolili.
4. **Pairwise Clip** podle polygonu ČR – 100 km od Prahy přesahuje do Německa.
5. Spočítejte plochu v km².

### Úloha 7 – Kolik procent Ostravy pokrývá les

1. Použijte `Ostrava_dissolve` ze společné části a vrstvu lesů.
2. **Pairwise Clip**: Input = lesy, Clip Features = `Ostrava_dissolve`, Output = `Lesy_v_Ostrave` (stejný výsledek jako `Lesy_Ostrava_intersect` z kroku 9).
3. Pokud se polygony lesů mohou překrývat, sloučte je: **Pairwise Dissolve** bez Dissolve Field → `Lesy_v_Ostrave_diss`.
4. Spočítejte plochu lesů a plochu `Ostrava_dissolve` (stejnou metodou).
5. Podíl = plocha lesa / plocha Ostravy × 100. Výpočet lze napsat i v **Calculate Field**, ale jednodušší je kalkulačka.

## Tvorba vlastní geodatabáze a editace

Editujte vždy jen exportovanou kopii ve vlastní geodatabázi, nikdy původní data ArcČR – jinak si je poškodíte pro další cvičení.

### Vytvoření geodatabáze

1. V panelu **Catalog** klikněte pravým na připojenou složku → **New → Folder**, pojmenujte např. `CV05_vlastni`.
2. Pravým na novou složku → **New → File Geodatabase**, název např. `Moje_data.gdb`.

### Import okresů

1. Přidejte do mapy vrstvu **okresů** z ArcČR.
2. Pravým na vrstvu v Contents → **Data → Export Features**.
3. Output Location = `Moje_data.gdb`, Output Name = `Okresy`. Zkontrolujte, že na okresech není aktivní výběr – jinak by se exportoval jen ten.
4. Nová vrstva se sama přidá do mapy. Další možnost importu: v Catalogu pravým na `Moje_data.gdb` → **Import → Feature Class(es)**.

### Smazání okresu Frýdek-Místek

1. **Select By Attributes** na vrstvě `Okresy` (ta z vaší gdb): název okresu **is equal to** `Frýdek-Místek`.
2. Karta **Edit** → **Delete** (případně klávesa Delete).
3. Karta **Edit** → **Save** → potvrďte uložení. Bez uložení změna po zavření projektu zmizí; **Discard** ji naopak zahodí.

### Změna hodnot v atributové tabulce

1. Otevřete atributovou tabulku `Okresy`.
2. Dvojklikem do buňky ji přepíšete přímo; pro více prvků najednou použijte **Edit → Attributes** (panel s formulářem vybraného prvku) nebo pravým na hlavičku pole → **Calculate Field**.
3. Nové pole přidáte přes **Add Field** v horní liště tabulky (Data Design → Fields), pak **Save** v pásu Fields.
4. Nakonec **Edit → Save**. Ověřte v tabulce, že změna platí a počet okresů je o 1 nižší než v originálu.






