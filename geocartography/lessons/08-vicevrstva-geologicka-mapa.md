---
layout: page
title: 08 – Kombinace metod a mapové konflikty
---

# 08 – Kombinace metod a mapové konflikty

## Cíle lekce

- umíte kombinovat více kartografických metod v jedné mapě,
- poznáte, kdy je mapa přeplněná, a víte, jak to řešit,
- umíte rozhodnout, co zůstane v hlavní mapě a co půjde do vedlejší,
- chápete, jak průhlednost a pořadí vrstev mění sdělení.

---

# 1. Typická komplexní geologická mapa

Kombinace kartografických metod v komplexních mapách a s tím spojené konflikty vizuální hierarchie rozebírají Voženílek, Kaňok et al. (2011) a Brewer (2016). Pro hydrogeologické mapy existuje mezinárodní standard legendy (Struckmeier & Margat 1995), který je vhodným příkladem, jak lze v jedné mapě kombinovat geologický podklad s hydrogeologickým obsahem bez ztráty čitelnosti.

V jedné mapě se běžně potkávají:

- předkvartérní podloží (areálová metoda, barvy),
- kvartérní pokryv (areálová, šrafa nebo průhledná výplň),
- tektonika (linie),
- strukturní měření (orientované body),
- dokumentační body a vrty (body),
- hydrogeologie (izolinie hladiny, prameny),
- topografický podklad.

To je pět až sedm vrstev tematického obsahu. Bez hierarchie vznikne šum.

---

# 2. Pravidla kombinace

1. **Jedna dominantní metoda.** Ostatní jsou doplňkové.
2. **Každá grafická proměnná jednou.** Pokud barva plochy = stáří, nesmí barva bodu = typ vrtu (jiný tón, jiná logika – čtenář je spojí).
3. **Plocha → linie → bod** je přirozené pořadí vykreslování (zdola nahoru).
4. Max. **3 tematické vrstvy** v jedné mapě pro bakalářskou úroveň. Zbytek do vedlejších map nebo přílohy.

---

# 3. Mapové konflikty a řešení

| Konflikt | Řešení |
|---|---|
| kvartér zakrývá podloží | odkrytá mapa + kvartér jen obrysem nebo průhlednou šrafou |
| shluk strukturních značek | generalizace: vybrat reprezentativní, zbytek do růžice |
| izolinie kříží hranice jednotek | izolinie tenké, jiný tón, nebo samostatná mapa |
| popisky překrývají značky | prioritizace popisků, vodicí čáry, zmenšení fontu |
| dva areálové jevy najednou | jeden barvou, druhý šrafou; nebo dvě mapy |
| ortofoto pod geologií | průhlednost 60–70 %, desaturace |

---

# 4. Vedlejší mapy

Když se něco do hlavní mapy nevejde, patří do vedlejší mapky menšího měřítka nebo detailu:

- tektonické schéma (zlomy bez geologie),
- mapa dokumentačních bodů,
- detail lomu / sesuvu,
- pozice území v ČR.

Vedlejší mapa musí mít vlastní měřítko a vyznačený rámec v hlavní mapě.

---

# 5. Rozbor problematického příkladu

Ukázka z bakalářské práce (anonymizovaná): geologie + kvartér plnou barvou + 200 strukturních měření + vrstevnice černé + ortofoto + popisky všech vrtů. Diskutujte:

- co má být dominantní,
- které tři vrstvy zůstanou,
- kam půjde zbytek.

---

# Cvičení v hodině

Načtěte pro studijní výřez: podloží, kvartér, zlomy, strukturní měření, vrty, izolinie hladiny podzemní vody.

1. Vyberte tři vrstvy do hlavní mapy a zdůvodněte to jednou větou pro každou vynechanou.
2. Sestavte mapu podle pravidel kombinace.
3. Zbývající vrstvy dejte do jedné vedlejší mapy.

---

# Shrnutí

- Jedna dominantní metoda, max. tři tematické vrstvy.
- Každá grafická proměnná nese jednu informaci.
- Co se nevejde, jde do vedlejší mapy. Ne do hlavní.

---

# Literatura

- BREWER, C. A. (2016): *Designing Better Maps: A Guide for GIS Users*. 2. vyd. Esri Press, Redlands.
- KRYGIER, J., WOOD, D. (2016): *Making Maps: A Visual Guide to Map Design for GIS*. 3. vyd. Guilford Press, New York.
- STRUCKMEIER, W. F., MARGAT, J. (1995): *Hydrogeological Maps: A Guide and a Standard Legend*. International Contributions to Hydrogeology 17. IAH / Heise, Hannover.
- VOŽENÍLEK, V., KAŇOK, J. a kol. (2011): *Metody tematické kartografie: vizualizace prostorových jevů*. Univerzita Palackého, Olomouc.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/geocartography/literatura/' | relative_url }}).
