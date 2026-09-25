---
title: Úkol 2 – Geologická mapa výřezu s řezem
---

# Úkol 2 – Geologická mapa výřezu s řezem

**Vazba na lekce:** 01, 02, 03, 04, 07, 08
**Body:** 25
**Odevzdání:** PDF A3 + projekt ArcGIS Pro jako Project Package (.ppkx)

## Zadání

Vytvořte **komplexní geologickou mapu** zvoleného výřezu (cca 5 × 5 km, měřítko 1:25 000 nebo 1:10 000) v úpravě odpovídající základní geologické mapě ČGS, doplněnou o **jednu další kartografickou metodu** (kartodiagram, izolinie nebo kartogram).

## Povinný obsah

- geologické jednotky (areálová metoda), barvy podle konvence ČGS/ICS, indexy v plochách,
- zlomy a hranice s rozlišením zjištěná / předpokládaná / zakrytá,
- strukturní měření s orientovanými značkami (min. 15),
- dokumentační body / vrty,
- **geologický řez** s linií řezu v mapě, stejné barvy a indexy, uvedené převýšení,
- legenda ve stratigrafickém pořadí,
- potlačený topografický podklad (vrstevnice / stínovaný reliéf, vodstvo),
- vedlejší mapka s pozicí území,
- tiráž s uvedením zdrojů dat a použitého symbolického standardu (FGDC 2006 / ČGS),
- **druhá metoda**: např. koláčové diagramy složení pro lomy, izohypsy báze kvartéru, růžice puklin pro lokality.

## Doporučený postup

1. Data: WMS/WFS ČGS pro geologii, vrtná databáze GDO, vlastní měření z terénního cvičení (pokud proběhlo), DMR 5G z ČÚZK pro reliéf.
2. Symbologie: styl *Geology 24K* / FGDC geologické značky (viz [Nástroje ArcGIS Pro]({{ '/arcgis-nastroje/' | relative_url }})).
3. Řez: *Interpolate Shape* + profil ve *Profile Graph* / *Elevation Profile*, nebo konstrukce v layoutu; alternativně ArcGIS Pro *Geological cross-section* nástroje (Geology Toolbox, pokud je dostupný).
4. Layout A3 podle lekce 02.
5. Kontrola: simulace barvosleposti, tisk na 100 % a kontrola čitelnosti.


