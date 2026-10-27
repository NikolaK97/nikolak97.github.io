# CV07 – Webové mapové služby a ArcGIS Online

**Zadání cvičení**

---

## Co se dnes naučíte

Na konci cvičení budete umět:

1. vysvětlit vlastními slovy, čím se liší **WMS** a **WFS**,
2. ručně sestavit dotaz na mapový server a zobrazit si odpověď v prohlížeči,
3. připojit WMS i WFS službu do ArcGIS Pro,
4. vytvořit a sdílet jednoduchou webovou mapu v ArcGIS Online.

**Budete potřebovat:** ArcGIS Pro, webový prohlížeč, školní účet do ArcGIS Online a připojení k internetu.

**Odhadovaný čas:** 90 minut.

---

## Teorie v kostce (přečtěte si před začátkem, cca 10 minut)

### Proč webové služby?

Data nemusíte stahovat k sobě do počítače. Leží na serveru jejich správce (např. ČÚZK) a vy si je jen „půjčíte“ přes internet. Výhoda: máte vždy aktuální verzi a nezabíráte místo na disku.

Komunikují spolu dvě strany:

- **server** – počítač, na kterém jsou data uložena a který odpovídá na dotazy,
- **klient** – program, který se ptá (ArcGIS Pro, QGIS, webový prohlížeč).

Klient posílá dotaz jako obyčejnou **URL adresu** (protokol HTTP), server vrátí odpověď.

### WMS vs. WFS – hlavní rozdíl

| | **WMS** (Web Map Service) | **WFS** (Web Feature Service) |
|---|---|---|
| Co dostanete | **obrázek** mapy (PNG, JPEG…) | **skutečné objekty** – body, linie, polygony (formát GML) |
| Datový model | rastr | vektor |
| Můžu vybírat objekty, dělat atributové dotazy? | ne, je to jen obrázek | ano |
| Můžu změnit symbologii? | ne (jen vybrat styl, který server nabízí) | ano |
| Typické použití | podkladová mapa | analýzy, práce s atributy |

**Pomůcka:** WMS je jako *fotka* mapy. WFS je jako *krabice s jednotlivými kostkami*, ze kterých je mapa poskládaná – každou kostku můžete vzít do ruky a podívat se, co na ní je napsáno.

### Tři základní dotazy WMS

Představte si mapový server jako restauraci:

| Dotaz | Co dělá | V restauraci |
|---|---|---|
| **GetCapabilities** | vrátí XML s popisem služby: jaké vrstvy má, v jakých souřadnicových systémech a formátech umí odpovídat | přečtu si **jídelní lístek** |
| **GetMap** | vrátí obrázek mapy pro zadané území, vrstvy, formát a souřadnicový systém | **objednám** konkrétní jídlo |
| **GetFeatureInfo** | vrátí atributy objektu v místě, kam kliknete | zeptám se číšníka, **co v tom jídle je** |

Klient se vždy nejdřív zeptá přes GetCapabilities – bez „jídelního lístku“ neví, co si může objednat.

### Souřadnicový systém a kód EPSG

Každá služba musí vědět, v jakém souřadnicovém systému (CRS) chcete mapu dostat. Systémy se označují číselným **kódem EPSG**. Pro ČR budete nejčastěji potkávat:

- **EPSG:5514** – S-JTSK / Krovak East North (dnes nejběžnější varianta S-JTSK v GIS; souřadnice jsou záporné),
- **EPSG:4326** – WGS 84 (zeměpisné souřadnice, GPS),
- **EPSG:3857** – Web Mercator (používají webové mapy včetně ArcGIS Online).

---

## Úkol 1 – Zeptejte se serveru přímo z prohlížeče (15 minut)

Cílem je pochopit, že „připojení služby“ v GIS není žádná magie – program jen skládá URL adresy, stejně jako to teď uděláte vy.

### 1a) GetCapabilities

Zkopírujte do adresního řádku prohlížeče tuto adresu (služba Ortofoto ČR od ČÚZK):

```
https://ags.cuzk.gov.cz/arcgis1/services/ORTOFOTO/MapServer/WMSServer?service=WMS&request=GetCapabilities
```

Zobrazí se vám dlouhý XML dokument. Neděste se, nemusíte rozumět všemu. Pomocí **Ctrl+F** najděte a zapište si:

- [ ] název služby (značka `<Title>` hned na začátku),
- [ ] název vrstvy, kterou služba nabízí (značka `<Name>` uvnitř `<Layer>`) – u služeb ČÚZK to bývá číslo, např. `0`,
- [ ] zda služba podporuje **EPSG:5514** (hledejte text `5514`),
- [ ] jaké obrazové formáty umí vrátit (značka `<Format>` u GetMap).

### 1b) GetMap

Teď si objednáte konkrétní obrázek. Zkopírujte do prohlížeče:

```
https://ags.cuzk.gov.cz/arcgis1/services/ORTOFOTO/MapServer/WMSServer?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&LAYERS=0&STYLES=&CRS=EPSG:5514&BBOX=-743300,-1043500,-742300,-1042500&WIDTH=800&HEIGHT=800&FORMAT=image/png
```

Měl by se zobrazit letecký snímek Staroměstského náměstí v Praze.

Rozeberte si adresu – každá část za `&` je jeden parametr:

| Parametr | Hodnota | Význam |
|---|---|---|
| `REQUEST` | `GetMap` | chci obrázek mapy |
| `LAYERS` | `0` | kterou vrstvu (název z GetCapabilities) |
| `CRS` | `EPSG:5514` | v jakém souřadnicovém systému |
| `BBOX` | `-743300,-1043500,-742300,-1042500` | výřez území: min X, min Y, max X, max Y |
| `WIDTH`, `HEIGHT` | `800` | velikost obrázku v pixelech |
| `FORMAT` | `image/png` | formát obrázku |

**Vyzkoušejte:**

- [ ] Změňte `WIDTH` a `HEIGHT` na `400`. Co se stalo?
- [ ] Zvětšete BBOX – ke každé hodnotě „min“ odečtěte 2000 a ke každé hodnotě „max“ přičtěte 2000. Co teď vidíte?
- [ ] Změňte `FORMAT` na `image/jpeg`. Liší se něco?

> **Zapamatujte si:** Odpověď na GetMap je obyčejný obrázek. Na budovy v něm nemůžete „kliknout“ – obrázek žádné objekty neobsahuje.

---

## Úkol 2 – Připojení WMS ZABAGED do ArcGIS Pro (15 minut)

ZABAGED® je Základní báze geografických dat ČR. Její WMS vypadá jako Základní mapa 1 : 10 000 a hodí se jako topografický podklad pro tematické mapy.

1. Spusťte ArcGIS Pro a vytvořte nový projekt **Map** (pojmenujte ho `CV07_Prijmeni`).
2. Na kartě **Insert** klikněte ve skupině **Project** na **Connections → Server → New WMS Server**.
3. Do pole **Server URL** vložte:

   ```
   https://ags.cuzk.gov.cz/arcgis/services/ZABAGED_POLOHOPIS/MapServer/WMSServer
   ```

   Pole pro uživatelské jméno a heslo nechte **prázdná** – služba je veřejná.
4. Klikněte na **OK**.
5. Otevřete panel **Catalog** (karta **View → Catalog Pane**). Připojení najdete v části **Servers**.
6. Rozbalte ho a přetáhněte službu do mapy.
7. Přibližte si libovolnou obec.

**Pozor:** služba je určena pro měřítka zhruba od **1 : 10 000** a podrobněji. Pokud vidíte prázdnou mapu, nejspíš jste moc oddálení – zkuste se přiblížit.

- [ ] Rozbalte vrstvu v panelu **Contents** a prohlédněte si, jaké podvrstvy obsahuje. Zkuste některé vypnout.
- [ ] Zjistěte, zda můžete u WMS vrstvy otevřít atributovou tabulku (pravé tlačítko na vrstvě). Proč to (ne)jde?

> **Co se děje na pozadí:** ArcGIS Pro nejdřív poslal GetCapabilities (aby zjistil, jaké vrstvy jsou k dispozici), a při každém posunu nebo přiblížení mapy posílá nový GetMap s jiným BBOX – úplně stejně jako vy v úkolu 1.

---

## Úkol 3 – Další WMS z ČÚZK a dotaz na objekt (15 minut)

### 3a) Připojte další dvě služby

Stejným postupem jako v úkolu 2 připojte:

| Služba | URL |
|---|---|
| Ortofoto ČR | `https://ags.cuzk.gov.cz/arcgis1/services/ORTOFOTO/MapServer/WMSServer` |
| Katastrální mapa | `https://services.cuzk.cz/wms/local-km-wms.asp` |

Další služby najdete na Geoportálu ČÚZK (https://geoportal.cuzk.gov.cz) v sekci věnované síťovým službám – prohlížecí služby WMS.

- [ ] Uspořádejte vrstvy tak, aby **ortofoto bylo dole** a **katastrální mapa nahoře**. Na dostatečně podrobném měřítku uvidíte hranice parcel přes letecký snímek.
- [ ] U vrstvy ZABAGED nastavte v **Contents** průhlednost (karta **Raster Layer** nebo **Appearance → Transparency**) na 50 % a porovnejte ji s ortofotem.

### 3b) GetFeatureInfo – „co je tady?“

1. Na kartě **Map** vyberte nástroj **Explore**.
2. Zapněte vrstvu **ZABAGED** a klikněte na nějakou budovu, silnici nebo vodní tok.
3. Mělo by se otevřít okno s atributy objektu.

- [ ] Zapište si, jaké atributy jste u objektu získali.
- [ ] Zkuste totéž kliknutím na katastrální mapu. Dostali jste odpověď? (Ne každá služba GetFeatureInfo podporuje – podívejte se do GetCapabilities dané služby.)

> **Rozdíl oproti WFS:** Atributy jste dostali, ale jen pro jeden bod, na který jste klikli. Nemůžete s nimi dál pracovat – vybírat podle nich, počítat statistiky ani je exportovat.

---

## Úkol 4 – Připojení WFS a porovnání s WMS (15 minut)

Teď si vyzkoušíte službu, která vrací skutečné vektorové objekty. Použijete data **Agentury ochrany přírody a krajiny ČR (AOPK)** – památné stromy.

1. **Insert → Connections → Server → New WFS Server**.
2. Do **Server URL** vložte:

   ```
   https://gis.nature.cz/arcgis/services/PamatneStromy/PamStrom/MapServer/WFSServer
   ```

3. Potvrďte **OK**, v panelu **Catalog → Servers** najděte připojení a vrstvu přetáhněte do mapy.
4. Pokud se objeví dialog s nastavením (např. maximální počet prvků), ponechte výchozí hodnoty.

Teď proveďte stejné kroky jako s WMS a porovnejte výsledek:

- [ ] Otevřete **atributovou tabulku** vrstvy památných stromů. Jde to?
- [ ] Pomocí **Select By Attributes** (vzpomeňte si na CV02) vyberte stromy podle nějakého atributu (např. podle druhu nebo názvu).
- [ ] Změňte **symbologii** stromů (barva, velikost, tvar bodu).
- [ ] Najděte nejbližší památný strom ke své škole nebo bydlišti a zapište si jeho název.

Vyplňte tabulku:

| | WMS ZABAGED | WFS Památné stromy |
|---|---|---|
| Typ dat (rastr / vektor) | | |
| Lze otevřít atributovou tabulku? | | |
| Lze změnit symbologii? | | |
| Lze provést atributový dotaz? | | |
| Lze data exportovat do geodatabáze? | | |

---

## Úkol 5 – Webová mapa v ArcGIS Online (20 minut)

ArcGIS Online je cloudová služba (SaaS) – nic se neinstaluje, vše běží v prohlížeči. Slouží k vytváření, prohlížení a sdílení map a aplikací.

### 5a) Vytvořte webovou mapu

1. Otevřete https://www.arcgis.com a přihlaste se **školním účtem**.
2. V horní liště klikněte na **Map** – otevře se **Map Viewer**.
3. Vlevo klikněte na **Add → Add layer from URL**.
4. Vložte adresu WMS ZABAGED (stejnou jako v úkolu 2). Typ by se měl rozpoznat automaticky jako **WMS OGC**. Klikněte na **Add to map**.
5. Stejným způsobem přidejte WFS památných stromů (typ **WFS OGC**).
6. Přibližte se do svého okolí.

### 5b) Upravte a uložte mapu

- [ ] U vrstvy památných stromů zapněte **vyskakovací okna (Pop-ups)** a vyzkoušejte, že po kliknutí na strom vidíte jeho atributy.
- [ ] Vyberte vhodnou **podkladovou mapu (Basemap)**.
- [ ] Uložte mapu (**Save and open → Save as**):
  - **Title:** `CV07 – Prijmeni – Pamatne stromy`
  - **Tags:** `CV07, WMS, WFS, pamatne stromy`
  - **Summary:** jednou větou popište, co mapa zobrazuje.

### 5c) Sdílejte mapu

1. Klikněte na **Share map**.
2. Nastavte sdílení na **Organization** (nebo podle pokynu vyučujícího).
3. Zkopírujte odkaz na mapu – budete ho odevzdávat.

> **Tip:** Otevřete odkaz v anonymním okně prohlížeče. Pokud po vás chce přihlášení, sdílení je nastaveno jen pro organizaci – to je v pořádku. Pokud mapu nevidí nikdo, zapomněli jste ji sdílet.

---

## Když něco nefunguje

| Problém | Pravděpodobná příčina | Řešení |
|---|---|---|
| Mapa je prázdná, vrstva se nevykreslí | jste mimo měřítkový rozsah služby | přibližte se (ZABAGED od cca 1 : 10 000) |
| V prohlížeči vidím chybové XML místo obrázku | překlep v URL nebo chybějící parametr | zkontrolujte `&` mezi parametry a čárky v BBOX |
| Obrázek z GetMap je bílý / prázdný | BBOX je mimo území ČR nebo prohozené souřadnice | u EPSG:5514 musí být obě souřadnice záporné; zkontrolujte pořadí min X, min Y, max X, max Y |
| ArcGIS Pro hlásí, že se nelze připojit | špatná adresa nebo výpadek služby | zkopírujte URL znovu; otestujte GetCapabilities v prohlížeči |
| Vrstva se načítá velmi pomalu | server odpovídá na každý posun mapy | chvíli počkejte, nepřibližujte a neposouvejte zběsile |
| U WFS se nenačetly všechny objekty | služba má limit počtu prvků na jeden dotaz | přibližte se na menší území |
| ArcGIS Online vrstvu nepřidá | služba nepodporuje Web Mercator nebo URL nezačíná `https` | zkontrolujte adresu; vyzkoušejte jinou službu |

---

## Kontrolní otázky

Odpovězte stručně, vlastními slovy (1–3 věty).

1. Jaký je hlavní rozdíl mezi tím, co vrací WMS a co vrací WFS?
2. Proč klient jako první posílá dotaz GetCapabilities?
3. Které parametry musí obsahovat dotaz GetMap, aby server věděl, jaký obrázek má vrátit? Vyjmenujte alespoň čtyři.
4. Proč jste u WMS ZABAGED nemohli otevřít atributovou tabulku, ale u WFS památných stromů ano?
5. K čemu slouží dotaz GetFeatureInfo a v čem je jeho omezení oproti WFS?
6. Co znamená zkratka EPSG:5514 a proč je v ní uveden kód?
7. Kdy byste ve vlastním projektu zvolili WMS a kdy WFS? Uveďte ke každé jeden příklad.
8. Co znamená, že ArcGIS Online je služba typu SaaS?

---
