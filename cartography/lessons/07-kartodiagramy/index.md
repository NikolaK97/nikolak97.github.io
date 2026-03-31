# 07 – Kartogramy a dasymetrické mapování


## Úvod
Tento přehled shrnuje dvě důležité kartografické metody pro znázornění prostorově vázaných statistických dat:

- **choropleth mapu**  
- **dasymetrické mapování**

Obě metody využívají plošné znaky a barevnou intenzitu, liší se ale především tím, jak pracují s územními jednotkami.

---

# 1. Choropleth mapa

## 1.1 Historie metody
První doložené použití této metody pochází z roku **1826**.  
Autorem byl **Pierre Charles François Dupin (1784–1873)**, který vytvořil tematickou mapu znázorňující úroveň gramotnosti ve Francii pomocí různých odstínů.

Tato mapa je považována za základ dnešních choropleth map, protože ukázala, že prostorově rozložená statistická data lze přehledně vizualizovat pomocí intenzity výplně územních jednotek.

---

## 1.2 Název metody
Pojem **choropleth map** pochází z řečtiny:

- **choros** = místo, oblast, areál  
- **plethos** = množství, význam, intenzita  

Název tedy vyjadřuje znázornění intenzity jevu v jednotlivých oblastech.

Samotné pojmenování zavedl **J. K. Wright** v roce **1938**.

### Česká terminologie
V českém prostředí není terminologie jednotná. Setkáváme se s více názvy:

- **metoda kvantitativních areálů**
- **metoda kartogramu**
- **choropletová mapa**

V praxi se nejčastěji používá označení **choropletová mapa** nebo **kartogram**.

---

## 1.3 Vymezení metody
Podle Mezinárodní kartografické asociace jde o metodu, která využívá barvu nebo stínování k vyjádření hodnot v jednotlivých areálech.

Důležité je, že tyto areály nejsou vymezeny podle samotného jevu, ale většinou podle:

- administrativních jednotek
- statistických jednotek

Choropleth mapa tedy pracuje s daty agregovanými za předem vymezená území.

---

## 1.4 Princip metody
Choropleth mapa využívá **plošné znaky**:

- **výplň** – intenzita výplně odpovídá intenzitě sledované veličiny
- **obrys** – odděluje jednotlivé areály

Metoda pracuje s předem známými územními celky, tzv. kartografickými areály, které mohou být:

- **přirozené** – např. povodí, klimatické oblasti, geomorfologické jednotky
- **umělé** – např. obce, okresy, kraje, sčítací obvody, pravidelné sítě

---

## 1.5 Normalizace dat
U choropleth map nestačí zobrazovat absolutní hodnoty.  
Je nutné data **normalizovat**, tedy převést na relativní ukazatele, aby byla území mezi sebou porovnatelná.

Normalizace se provádí jako podíl dvou statistických veličin.

### Příklad
**hustota zalidnění = počet obyvatel / rozloha**

Tento přepočet eliminuje vliv velikosti územních jednotek a umožňuje zobrazit skutečnou intenzitu jevu.

---

## 1.6 Přístupy k normalizaci
Používá se několik základních přístupů:

### Hustota veličiny vztažená k plošné jednotce
Např. hustota zalidnění.

### Procentuální vyjádření podílu jevu na rozloze areálu
Např. podíl lesních ploch v rámci okresu.

### Podíl dvou veličin, které nereprezentují rozlohu
Např. spotřeba alkoholu na 1 obyvatele.

### Statistické charakteristiky
Např. průměrná rozloha obcí v okresech.

### Poznámka
V české odborné komunitě je nejvíce zdůrazňována hustota veličiny. Ostatní přístupy bývají někdy označovány jako tzv. **nepravé kartogramy**.

---

## 1.7 Typy a volba areálů
Volba areálů má zásadní vliv na interpretaci mapy.

### Typy areálů
- **přirozené** – vhodné pro fyzickogeografické jevy
- **umělé** – vhodné pro socioekonomické jevy

### Kritéria volby
Důležitá je především:

- **velikost areálu**
- **tvar areálu**

Nejvhodnější jsou areály s co nejmenšími rozdíly ve velikosti a tvaru, protože pak jsou lépe porovnatelné.

---

## 1.8 Vhodná a nevhodná data
### Vhodná data
Data, která jsou v rámci areálu relativně homogenní a mění se skokově na hranicích areálů.  
Např. administrativně stanovené hodnoty.

### Podmíněně vhodná data
Data, která jsou sice uvnitř území proměnlivá, ale relativně rovnoměrně rozložená.  
Např. hustota zalidnění.

### Nevhodná data
Spojité jevy, které se mění plynule v prostoru a nerespektují hranice areálů.  
Např. nadmořská výška.

V těchto případech choropleth mapa zkresluje realitu.

---

## 1.9 Klasifikace dat
Normalizovaná data lze zobrazit dvěma způsoby:

- **neklasifikovaně**
- **klasifikovaně**

### Neklasifikovaná mapa
Hodnoty zůstávají neroztříděné a každé hodnotě odpovídá přímo určitá intenzita barvy.

#### Výhody
- věrnější znázornění dat
- zachování detailu

#### Nevýhody
- horší čitelnost
- menší možnost řídit interpretaci mapy

### Klasifikovaná mapa
Hodnoty jsou rozděleny do určitého počtu tříd.

#### Výhody
- přehlednost
- snadnější porovnávání oblastí

#### Nevýhody
- zjednodušení reality
- ztráta jemných rozdílů

---

## 1.10 Počet intervalů
Při klasifikaci je třeba určit:

- **počet intervalů**
- **způsob klasifikace**

Doporučený počet tříd je přibližně **3 až 6, maximálně 7**.

Příliš málo tříd:
- velké zjednodušení

Příliš mnoho tříd:
- ztráta přehlednosti

Počet tříd musí být kompromisem mezi čitelností a zachycením detailu.

---

## 1.11 Metody klasifikace
Výsledná mapa závisí nejen na počtu tříd, ale i na způsobu jejich vytvoření.

### Přirozené zlomy (Jenks)
- hledají přirozené shluky v datech
- dobře vystihují strukturu hodnot

### Kvantily
- v každé třídě je stejný počet jednotek
- mapa působí vyváženě, ale může spojovat různé hodnoty

### Stejné intervaly
- všechny intervaly mají stejnou šířku
- jednoduché, ale často nevhodné pro nerovnoměrně rozložená data

### Geometrické intervaly
- vhodné pro data s velkým rozptylem
- lépe pracují s extrémy

### Shrnutí
Různé metody klasifikace nevytvářejí jen jinou mapu, ale i jiný pohled na data.

---

# 2. Dasymetrické mapování

## 2.1 Úvod
Dasymetrická metoda je pokročilejší přístup k mapování prostorově vázaných statistických dat.

Anglicky se označuje jako **dasymetric mapping**.

Jejím cílem je odstranit jeden z hlavních problémů choropleth map, tedy to, že pracují s celými administrativními jednotkami, i když se sledovaný jev uvnitř nich nevyskytuje rovnoměrně.

---

## 2.2 Historie metody
Historie dasymetrické metody sahá do počátku 20. století.

- **1911** – první představení metody v článku
- **1919** – první doložené použití
- **1920** – vznik dasymetrické mapy evropského Ruska
- **1936** – popularizace metody díky J. K. Wrightovi

Dasymetrická metoda se postupně prosadila jako přesnější alternativa k choropleth mapám.

---

## 2.3 Vymezení metody
Dasymetrická metoda je kartografická metoda, která používá barvy nebo stínování podobně jako choropleth mapa, ale nepracuje s předem danými areály.

Místo toho využívá **účelově vytvořené areály**, tedy jednotky vytvořené **ad hoc**, které lépe odpovídají skutečné distribuci jevu.

Vyjadřuje především:

- **intenzitu jevu**
- **prostorovou proměnlivost jevu**

Typickým příkladem použití je opět **hustota zalidnění**.

---

## 2.4 Princip metody
Stejně jako u choropleth mapy se používají plošné znaky:

- intenzita výplně odpovídá intenzitě veličiny
- obrys odděluje jednotlivé areály

Hlavní rozdíl spočívá v tom, že areály nejsou předem dané, ale vytvářejí se jako **subjednotky**, které lépe odpovídají skutečné distribuci jevu.

K jejich vytváření se používá **areálová interpolace**.

---

## 2.5 Areálová interpolace
Areálová interpolace je proces, při kterém se hodnoty z původních areálů přerozdělují do nových, vhodnějších jednotek.

Řeší mimo jiné problém **MAUP (Modifiable Areal Unit Problem)**, tedy skutečnost, že výsledky analýzy mohou záviset na způsobu územního členění.

### Princip
- máme zdrojové areály s agregovanou hodnotou
- vytvoříme cílové areály nebo subjednotky
- podle překryvu nebo doplňkových informací se původní hodnota přerozdělí do nových jednotek

### Důležité
Celkový součet hodnot zůstává zachován, mění se pouze jejich prostorové rozložení.

---

## 2.6 Zdroje doplňkových informací
Pro kvalitní areálovou interpolaci jsou potřeba pomocná prostorová data, například:

- **využití půdy (land use)** – územní plán, katastr
- **půdní kryt (land cover)** – např. CORINE Land Cover
- **data z dálkového průzkumu Země**

Čím kvalitnější tato data jsou, tím přesnější bývá výsledná dasymetrická mapa.

---

## 2.7 Automatizace metody podle Eicher a Brewer
Eicher a Brewer navrhli postup, jak dasymetrickou metodu více zautomatizovat.

Zaměřili se zejména na mapování hustoty zalidnění s využitím dat o využití půdy, například z databází USGS.

Rozlišují různé typy ploch, například:

- sídla
- zemědělské plochy
- rozptýlené lesní porosty
- lesní porosty
- voda

Na základě těchto kategorií se obyvatelstvo přerozděluje realističtěji než při klasickém mapování.

---

## 2.8 Metody stanovení podílu obyvatelstva v dasymetrických zónách
Pro přerozdělení populace v dasymetrických zónách lze použít několik přístupů:

- **binární metoda**
- **trojtřídní metoda**
- **metoda omezující proměnné**

Přesnost roste spolu se složitostí metody.

---

## 2.9 Binární metoda
Nejjednodušší přístup.

Území se rozdělí na dvě kategorie:

- **obyvatelné plochy (100 % populace)**
- **neobyvatelné plochy (0 % populace)**

### Obyvatelné plochy
Např.:
- sídla
- zemědělské plochy
- rozptýlené lesní porosty

### Neobyvatelné plochy
Např.:
- lesní porosty
- voda

### Výhody
- jednoduchost
- rychlá aplikace

### Nevýhody
- velké zjednodušení
- nerozlišuje různou intenzitu osídlení
- může vytvářet chyby v mapách

---

## 2.10 Trojtřídní metoda
Pokročilejší varianta než binární metoda.

Území je rozděleno do tří kategorií s různým podílem populace:

- **sídla** – cca 70 % obyvatelstva
- **zemědělské plochy / rozptýlené lesní porosty** – cca 20 %
- **lesní porosty** – cca 10 %

### Výhody
- realističtější než binární metoda
- zohledňuje více typů prostředí

### Nevýhody
- stále zjednodušení reality
- pracuje s pevně danými podíly
- stále mohou vznikat chyby

---

## 2.11 Metoda omezující proměnné
Nejpokročilejší z uvedených přístupů.

Pracuje s doplňkovými proměnnými, které určují, kde a v jaké míře se obyvatelstvo může vyskytovat.

Například:

- **sídla** – vysoký podíl obyvatelstva
- **lesní porosty** – nízký podíl obyvatelstva

### Výhody
- vyšší přesnost
- lepší přizpůsobení skutečnému rozložení obyvatelstva

### Nevýhody
- vyšší náročnost
- závislost na kvalitě vstupních dat
- stále jde o model, takže může obsahovat chyby

---

# 3. Srovnání metod

## Choropleth mapa
### Výhody
- jednoduchá tvorba
- dobrá přehlednost
- vhodná pro agregovaná statistická data

### Nevýhody
- pracuje s předem danými administrativními jednotkami
- předpokládá rovnoměrné rozložení jevu uvnitř areálu
- může zkreslovat realitu

## Dasymetrické mapování
### Výhody
- přesnější prostorové rozložení dat
- lépe vystihuje skutečný výskyt jevu
- vhodné pro jevy, které nejsou v rámci areálu rovnoměrně rozložené

### Nevýhody
- vyšší datová a výpočetní náročnost
- potřeba doplňkových dat
- složitější interpretace i tvorba

---

# 4. Závěr
Choropleth mapa je základní a velmi rozšířená metoda kartografického znázorňování statistických dat v územních jednotkách. Je přehledná a snadno použitelná, ale může zkreslovat realitu, pokud jev uvnitř areálů není rozložen rovnoměrně.

Dasymetrická metoda se snaží tento problém řešit tím, že vytváří nové, realističtější prostorové jednotky a využívá doplňková data pro přesnější přerozdělení hodnot.

Obecně platí, že:

- **choropleth mapa** je vhodná pro rychlé a přehledné znázornění agregovaných dat
- **dasymetrické mapování** je vhodné tam, kde chceme přesnější obraz skutečného prostorového rozložení jevu


# CVIČENÍ 

Tento postup popisuje, jak přerozdělit populaci z administrativních jednotek (obcí) do menších, realističtějších prostorových jednotek pomocí dat o využití území.

Cílem je vytvořit dasymetrickou mapu hustoty obyvatelstva.

---

## 1. Příprava administrativní vrstvy obcí

Nejprve připravte vstupní vrstvu obcí:

- importujte polygonovou vrstvu obcí 
- ponechte pouze potřebná pole:
  - **ID_OBEC** (jedinečný identifikátor)
  - **POP** (počet obyvatel)

Cílem je mít čistou, jednoduchou vrstvu bez zbytečných atributů.

---

## 2. Příprava vrstvy využití území (land use)

- importujte vrstvu využití území (např. CORINE Land Cover)
- sjednoťte souřadnicový systém s vrstvou obcí
- odstraňte chybné geometrie
- zkontrolujte atributové pole s kategoriemi (např. typ využití půdy)

Obě vrstvy musí být ve stejné projekci, jinak nebude fungovat překryv.

---

## 3. Reklasifikace land use do dasymetrických tříd

Původní kategorie využití půdy převedeme na váhy (koeficienty), které vyjadřují pravděpodobnost výskytu obyvatel.

Příklad:

| Kategorie            | Váha (WEIGHT) |
|---------------------|--------------|
| obytná              | 1.0          |
| smíšená             | 0.7          |
| komerční            | 0.2          |
| průmyslová          | 0.1          |
| ostatní (lesy, voda)| 0            |

- vytvořte nové pole **WEIGHT**
- přiřaďte hodnoty podle typu využití

Čím vyšší váha, tím větší koncentrace obyvatel.

---

## 4. Provedení překryvu (Intersect)

- použijte nástroj **Intersect**
- vstupy:
  - vrstva obcí
  - vrstva land use

Výsledkem budou dílčí segmenty, které vzniknou kombinací obou vrstev.

Každý segment patří do jedné obce a má konkrétní typ využití.

---

## 5. Výpočet plochy segmentů

- přidejte nové pole **AREA_M2**
- vypočítejte plochu každého segmentu (např. v m²)

Tato hodnota říká, jak velká část obce připadá na daný typ využití.

---

## 6. Výpočet vážené plochy

- přidejte pole **W_AREA**
- vypočítejte:

*W_AREA = AREA_M2 × WEIGHT*

Vážená plocha zohledňuje nejen velikost segmentu, ale i jeho význam pro osídlení.

---

## 7. Součet vážených ploch za obec

- použijte nástroj **Summary Statistics**
- seskupení podle **ID_OBEC**
- spočítejte:
  - **SUM_W_AREA** (součet vážených ploch)

Získáte celkovou „osidlitelnou kapacitu“ každé obce.

---

## 8. Připojení součtů zpět k segmentům

- použijte **Join Field**
- propojte tabulky pomocí **ID_OBEC**
- připojte pole **SUM_W_AREA** ke každému segmentu

Každý segment nyní obsahuje informaci o celkové hodnotě za obec.

---

## 9. Výpočet podílu segmentu

- přidejte pole **SHARE**
- vypočítejte:

*SHARE = W_AREA / SUM_W_AREA*

Tato hodnota udává, jaký podíl populace připadá na daný segment.

---

## 10. Výpočet přidělené populace

- přidejte pole **POP_DASY**
- vypočítejte:

*POP_DASY = POP × SHARE*

Tím dojde k přerozdělení obyvatelstva do menších prostorových jednotek.

---

## 11. Kontrola správnosti výsledků

Je nutné ověřit, že nedošlo ke ztrátě nebo přičtení populace.

Postup:

1. znovu použijte **Summary Statistics**
2. seskupte podle **ID_OBEC**
3. sečtěte hodnoty **POP_DASY**

Výsledek musí odpovídat původní hodnotě **POP** v každé obci.

Pokud ne, je chyba ve výpočtu nebo spojení dat.

---

## Výstup

Výsledkem je vrstva segmentů, kde:

- populace je rozložena podle realističtější struktury krajiny
- lze vytvořit:
  - dasymetrickou mapu hustoty
  - detailnější analýzu osídlení

---

## Shrnutí principu

- původní data: agregovaná za obec  
- pomocná data: využití území  
- výsledek: detailnější rozdělení populace  

Čím kvalitnější vstupní data a váhy, tím přesnější výsledek.
