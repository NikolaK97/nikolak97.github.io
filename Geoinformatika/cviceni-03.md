---
layout: default
title: "CV03 – Externí data a mapový výstup"
---

# CV03 – Připojení externích dat, tvorba mapového výstupu


> **Výstup cvičení:** kartogram míry nezaměstnanosti v okresech ČR (PNG), který splňuje základní kartografická pravidla  




V tomto cvičení se naučíte:

- připojit k prostorové vrstvě externí tabulku (Excel) přes společný atribut – tzv. **join**,
- vykreslit z připojeného atributu **kartogram** (choropleth),
- sestavit v ArcGIS Pro **mapový výstup (Layout)** se všemi kompozičními prvky,
- exportovat mapu do rastrového formátu.


## Data ke cvičení

Data si stahujete sami – obě sady jsou volně dostupné.

| Data | Zdroj | Poznámka |
|---|---|---|
| Polygonová vrstva okresů ČR | [ArcČR 4](https://www.arcdata.cz/produkty/geograficka-data/arccr) (ARCDATA PRAHA, zdarma po registraci) | vrstva `Okresy`; klíčový atribut je kód okresu (LAU 1, např. `CZ0806`) – název pole ověřte ve stažené verzi |
| Míra nezaměstnanosti podle okresů | [Veřejná databáze ČSÚ](https://vdb.czso.cz/vdbvo2/) | ve VDB vyhledejte *nezaměstnanost* → tabulka nezaměstnanosti v okresech (ukazatel je ve VDB veden jako *podíl nezaměstnaných osob*, zdroj MPSV); zvolte poslední dostupné období a exportujte do xlsx nebo csv <a href="/download/UD-1790691580864.xlsx" download>Stáhnout Excel</a>|

Alternativa k ArcČR: administrativní hranice z [RÚIAN / ČÚZK](https://services.cuzk.cz/shp/stat/epsg-5514/) (soubor `1.zip`, vrstva `OKRESY_P`).

Před prací tabulku z ČSÚ upravte v Excelu tak, aby byla připojitelná:

- odstraňte úvodní řádky s nadpisem a poznámky pod tabulkou, **první řádek = názvy sloupců**,
- sloupce pojmenujte bez diakritiky a mezer, např. `KOD_OKRESU`, `NAZEV`, `MIRA_NEZAM`,
- ponechte **jeden řádek na okres** (77 řádků), smažte řádky za kraje a ČR celkem,
- čísla uložte jako čísla (desetinná čárka podle nastavení systému, bez znaku `%`),
- kód okresu uložte ve stejném formátu (text × číslo), jako je ve vrstvě okresů.

---

## Postup cvičení

### 1. Založení projektu a načtení dat

1. Založte nový projekt ArcGIS Pro (**Map**), pojmenujte ho `CV03_prijmeni`.
2. **Map → Add Data** – přidejte vrstvu okresů.
3. Stejným způsobem přidejte upravený list z Excelu (vyberte konkrétní list, např. `List1$`). Tabulka se objeví v panelu *Contents* v sekci *Standalone Tables*.
4. Otevřete atributovou tabulku vrstvy i externí tabulky (pravé tlačítko → **Attribute Table**) a **najděte společný atribut** – typicky kód okresu. Název okresu je jako klíč nespolehlivý (diakritika, mezery, „Praha-východ" vs. „Praha - východ").

### 2. Připojení tabulky (Join)

1. Pravé tlačítko na vrstvu okresů → **Joins and Relates → Add Join**.
2. Vyplňte:
   - *Input Table*: vrstva okresů
   - *Input Join Field*: kód okresu ve vrstvě
   - *Join Table*: list z Excelu
   - *Join Table Field*: kód okresu v tabulce
3. Použijte **Validate Join** – zkontroluje počet spárovaných záznamů. Musí být **77** (počet okresů ČR).
4. Otevřete atributovou tabulku vrstvy: na konci přibyly sloupce z Excelu. Pokud mají některé řádky `<Null>`, join selhal – nejčastěji kvůli rozdílnému typu klíčového pole nebo překlepu.
5. Join je jen dočasný. Uložte výsledek trvale: pravé tlačítko → **Data → Export Features** → `okresy_nezam` do geodatabáze projektu. Dál pracujte s touto vrstvou.

### 3. Kartogram

1. Vyberte vrstvu `okresy_nezam` → **Feature Layer → Symbology → Graduated Colors**.
2. *Field*: `MIRA_NEZAM`.
3. *Method*: **Natural Breaks (Jenks)** nebo **Quantile**, 5 tříd. Zkuste obě metody a porovnejte, jak mění obraz mapy – volbu zdůvodníte v odevzdání.
4. *Color scheme*: **sekvenční** jednobarevná škála (světlá = nízké hodnoty, tmavá = vysoké). Nepoužívejte duhovou škálu.
5. V záložce *Classes* upravte popisky tříd (zaokrouhlení, jednotka `%`, správný oddělovač).
6. Kartogram zobrazuje **relativní** hodnoty (míra, podíl). Absolutní počty (např. počet uchazečů o zaměstnání) se do kartogramu nedávají – pro ty slouží kartodiagram.

### 4. Mapový výstup (Layout)

1. **Insert → New Layout** – zvolte formát (např. A4 na šířku).
2. **Insert → Map Frame** – vložte mapový rámec s vaší mapou a roztáhněte ho tak, aby mapové pole využilo většinu plochy.
3. Nastavte měřítko mapového rámce na „kulatou" hodnotu (např. 1 : 2 000 000).
4. Přidejte kompoziční prvky (vše v záložce **Insert**): **Title/Text**, **Legend**, **Scale Bar**, **North Arrow**, textové pole pro tiráž.
5. Každý prvek upravte podle pravidel v následující kapitole – výchozí nastavení ArcGIS Pro pravidla neplní (anglický popis směrovky, nadpis „Legend", nevhodné dělení měřítka).
6. Zapněte **Guides** a zarovnejte prvky; pohlídejte, aby se nepřekrývaly s mapovým polem.

### 5. Export

**Share → Export Layout** → formát **PNG**, rozlišení **300 DPI**, název `CV03_kartogram_prijmeni.png`. Výsledný obrázek otevřete a zkontrolujte čitelnost popisků – co je čitelné v ArcGIS Pro, nemusí být čitelné v exportu.

---

## Pravidla pro tvorbu tematické mapy

Kompoziční prvky se dělí na **základní** (v mapě musí být vždy a mají pevně danou podobu) a **nadstavbové** (podle potřeb konkrétní mapy).

| Základní prvky | Nadstavbové prvky |
|---|---|
| název mapy | směrovka |
| mapové pole | textové pole |
| legenda | citace |
| měřítko | grafy, tabulky, vedlejší mapy |
| tiráž | |

### Název mapy

Odpovídá na tři otázky:

1. **CO** – věcné vymezení tématu (např. *Míra nezaměstnanosti*),
2. **KDE** – prostorové vymezení (*v okresech České republiky*),
3. **KDY** – časové určení, pokud je jev proměnlivý v čase (*k 31. 8. 2026*).

Dělí se na hlavní nadpis a podnadpis:

- hlavní nadpis: VELKÉ TISKACÍ BEZPATKOVÉ PÍSMO, největší velikost v celé mapě,
- podnadpis: malé tiskací bezpatkové písmo, menší než nadpis.

Do názvu **nepatří** slova *mapa, plánek* apod. ani vyjádření metody (*interpolovaná data, měřeno GPS*) – metoda se uvádí v tiráži.

### Měřítko

- V digitálních mapách má přednost **grafické** měřítko před číselným.
- Dělení na desítky, stovky, tisíce – ne 1 : 2 350 000 nebo dílky po 7 km.
- Jednotka (km, m) se uvádí zkratkou **jen za posledním** popiskem grafického měřítka.
- Pokud použijete i číselné měřítko, umístí se na opačnou stranu, než jsou popisky grafického měřítka. V číselném měřítku se tisíce oddělují mezerou: *1 : 2 000 000*.

### Legenda

- **Nepoužívá se nadpis „Legenda"** (ani „Legend"). Nadpisem legendy je název znázorňovaného jevu, např. *Míra nezaměstnanosti (%)*.
- Popisky prvků se uvádějí v jednotném čísle.
- Legenda musí být:
  - **úplná** – vše, co je v mapě, je i v legendě,
  - **jednoznačná** – dva různé jevy nemají stejné vyjádření,
  - **uspořádaná** – související jevy jsou seskupené, od nejdůležitějšího k méně důležitému,
  - **v souladu s mapou** – symbol v legendě vypadá naprosto stejně jako v mapě (tvar, velikost, odstín),
  - **srozumitelná** i pro uživatele, který mapu nevytvářel.

### Tiráž

Obsahuje vždy:

- jméno a PŘÍJMENÍ autora (příjmení velkými písmeny),
- místo sestavení mapy,
- rok (příp. datum) sestavení,
- kartografické zobrazení / souřadnicový systém,
- zdroje dat s **úplnou citací** – nestačí „data z adresy xyz"; uveďte vydavatele, oficiální název datové sady a rok, např. *ČSÚ: Podíl nezaměstnaných osob v okresech ČR k 31. 8. 2026, Veřejná databáze (zdroj MPSV); ARCDATA PRAHA: ArcČR 4*.

### Mapové pole

Mapové pole má využít **většinu plochy** média. Častou chybou začátečníků je malá mapa uprostřed velké prázdné plochy – „poštovní známka". Příklady rozvržení jsou na obr. 2.

### Směrovka

- Vždy ukazuje ke skutečnému severu.
- Není nutná, pokud mapa zobrazuje obecně známé území (např. Evropu) nebo obsahuje síť poledníků a rovnoběžek.
- **Pozor na S-JTSK:** sever není rovnoběžný s osou Y. Pro Ostravsko otočte směrovku o cca **6° po směru hodinových ručiček** (obr. 1). U žádného zobrazení používaného na území ČR není sever přesně nahoře.
- Popis směrovky musí být ve stejném jazyce jako mapa – nenechávejte anglické „N".

![Odchylka poledníků od os S-JTSK](https://geoscience.vsb.cz/wp-content/uploads/2016/07/cv04_01-1.jpg)

*Obr. 1: Odchylka poledníků od os souřadnicového systému S-JTSK*

### Textové pole a citace

Do textového pole patří vysvětlující texty, definice ukazatele, popis metody, aktuálnost dat. Pokud jste použili cizí data či podklady, je citace nedílnou součástí mapy.

### Obecná pravidla

- Celá mapa je v **jednom jazyce**.
- Maximálně **dva typy** nezdobného písma, jasně odlišitelné.
- Barvy: pro kartogram sekvenční škála, žádná duha; červená vyhrazená pro zvýraznění.

![Příklady kompozic tematické mapy](https://geoscience.vsb.cz/wp-content/uploads/2016/07/cv04_02-1.jpg)

*Obr. 2: Příklady kompozic tematické mapy. Převzato z VOŽENÍLEK (2002)*

---

## Kontrolní seznam před odevzdáním

- [ ] Join se spároval na všech 77 okresů, žádné `<Null>` hodnoty
- [ ] Kartogram vychází z relativního ukazatele (`MIRA_NEZAM`), sekvenční barvy, 4–6 tříd
- [ ] Název odpovídá na CO – KDE – KDY, bez slova „mapa"
- [ ] Legenda bez nadpisu „Legenda", popisky s jednotkou
- [ ] Grafické měřítko s kulatým dělením, jednotka za posledním popiskem
- [ ] Směrovka v češtině, otočená podle S-JTSK
- [ ] Tiráž: autor, místo, rok, zobrazení, úplná citace dat
- [ ] Mapové pole využívá plochu listu
- [ ] Export PNG 300 DPI, popisky čitelné

## Odevzdání

Zašlete / nahrajte:

- `CV03_kartogram_prijmeni.png`
- `CV03_prijmeni.txt` – 3–5 vět: zvolená metoda klasifikace a proč, problémy při úpravě tabulky a joinu

**Termín:** [dd. mm. 2026]

---

*Poslední aktualizace: září 2026*
