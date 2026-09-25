---
layout: page
title: 06 – Klasifikace a stupnice geochemických dat
---

# 06 – Klasifikace a stupnice geochemických dat

## Cíle lekce

- znáte metody klasifikace (equal interval, quantile, natural breaks, standardní odchylka, manuální),
- rozumíte tomu, proč geochemická data vyžadují zvláštní zacházení,
- umíte použít prahové hodnoty (limity, indexy) jako přirozené třídy,
- dokážete identifikovat a popsat mapu, jejíž klasifikace zkresluje rozdělení dat.

---

# 1. Proč klasifikovat

Klasifikace je klíčovým krokem tvorby kartogramu a zároveň nejčastějším zdrojem zavádějících map (Monmonier 1996). Metodu přirozených zlomů navrhl Jenks (1967); přehled metod a jejich vhodnosti pro různá rozdělení dat podávají Slocum et al. (2009). Specifika geochemických dat – asymetrická rozdělení, odlehlé hodnoty, hodnoty pod mezí detekce – a z toho plynoucí nároky na klasifikaci a vizualizaci rozebírají Reimann & Filzmoser (2000) a Reimann et al. (2008); metodiku geochemického mapování v evropském měřítku dokumentuje Salminen et al. (2005).

Spojitou hodnotu (koncentrace As v mg/kg) čtenář nerozliší v 200 odstínech. Klasifikace ji rozdělí do 4–7 tříd. Každá metoda ale řekne o datech **něco jiného** – a tady vzniká největší prostor pro manipulaci.

---

# 2. Metody klasifikace

| Metoda | Jak dělí | Hodí se pro |
|---|---|---|
| rovnoměrné intervaly | stejná šířka tříd | rovnoměrně rozložená data (vzácné) |
| kvantily | stejný počet prvků ve třídě | pořadové srovnání, percentily |
| přirozené zlomy (Jenks) | minimalizuje rozptyl uvnitř tříd | data se shluky |
| směrodatná odchylka | odchylky od průměru | normálně rozložená data |
| logaritmické intervaly | násobky (1, 3, 10, 30...) | **log-normální data = geochemie** |
| manuální | podle odborných prahů | limity, indexy, normy |

---

# 3. Geochemická data jsou log-normální

Obsahy prvků v půdách, horninách a vodách mají typicky rozdělení s dlouhým pravým chvostem: většina hodnot nízkých, pár extrémů.

Co se stane při **rovnoměrných intervalech**: 95 % území spadne do první třídy, mapa je jednobarevná, anomálie neuvidíte.

Co se stane při **kvantilech**: každá třída má stejný počet vzorků, mapa vypadá pestře, ale hranice tříd nemají žádný fyzikální význam a anomálie se rozmělní.

Správně:

- transformovat data (log₁₀) a pak klasifikovat, nebo
- použít logaritmické intervaly, nebo
- použít percentily s důrazem na chvost (např. 25, 50, 75, 90, 95, 98) – tak to dělá Geochemický atlas.

Vždy uveďte histogram dat vedle mapy. To je v geochemické kartografii standard.

---

# 4. Prahové hodnoty jako třídy

Když existuje odborný práh, je to nejlepší hranice třídy:

- limity kontaminace půd (vyhláška),
- **radonový index** pozemku: nízký / střední / vysoký (hranice dané metodikou),
- kategorie agresivity prostředí,
- třídy propustnosti.

Manuální klasifikace podle prahů je jediná, kterou čtenář-praktik umí přímo použít.

---

# 5. Stupnice a legenda

- třídy nesmí mít mezery ani překryvy: `< 5 · 5–10 · 10–20 · > 20`,
- uvádějte jednotky,
- u otevřených tříd (`> 20`) uveďte maximum v poznámce,
- sekvenční barvy pro sekvenční třídy (lekce 04),
- do legendy napište metodu klasifikace a počet vzorků.

---

# Cvičení v hodině

Vrstva půdních vzorků s obsahem As (studijní výřez, Geochemický atlas).

1. Zobrazte histogram (Symbology → Graduated colors → Histogram, nebo Data → Create Chart → Histogram).
2. Vytvořte tři mapy: rovnoměrné intervaly, kvantily, logaritmické intervaly / percentily.
3. Přidejte čtvrtou: manuální třídy podle limitu kontaminace.
4. Napište odstavec: která mapa nejvíce zkresluje rozdělení dat, pro jakého čtenáře, a která by byla vhodná pro odbornou zprávu.

---

# Shrnutí

- Klasifikace je interpretace. Volba metody je odborné rozhodnutí.
- Geochemie = log-normální rozdělení = log intervaly nebo percentily.
- Odborný práh je vždy lepší hranice než statistická.

---

# Literatura

- JENKS, G. F. (1967): The Data Model Concept in Statistical Mapping. *International Yearbook of Cartography*, 7, 186–190.
- MONMONIER, M. (1996): *How to Lie with Maps*. 2. vyd. University of Chicago Press, Chicago.
- REIMANN, C., FILZMOSER, P. (2000): Normal and lognormal data distribution in geochemistry: death of a myth. Consequences for the statistical treatment of geochemical and environmental data. *Environmental Geology*, 39(9), 1001–1014.
- REIMANN, C., FILZMOSER, P., GARRETT, R. G., DUTTER, R. (2008): *Statistical Data Analysis Explained: Applied Environmental Statistics with R*. Wiley, Chichester.
- SALMINEN, R. (ed.) et al. (2005): *Geochemical Atlas of Europe. Part 1: Background Information, Methodology and Maps*. Geological Survey of Finland, Espoo.
- SLOCUM, T. A., MCMASTER, R. B., KESSLER, F. C., HOWARD, H. H. (2009): *Thematic Cartography and Geovisualization*. 3. vyd. Pearson Prentice Hall, Upper Saddle River.

Úplný seznam literatury ke kurzu: [Literatura]({{ '/geocartography/literatura/' | relative_url }}).
