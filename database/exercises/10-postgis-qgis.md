
## Část 1: Lehké úlohy (základy)

### Úloha 1 — Vypiš fakulty jako WKT

Vypiš ID, název, město a geometrii každé fakulty v lidsky čitelném formátu **WKT**.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT id, nazev, mesto, ST_AsText(geom) AS wkt
FROM   fakulta
ORDER BY id;
```

Funkce `ST_AsText` převede binární geometrii na textový formát Well-Known Text (např. `POINT(16.6068 49.1951)`).
</details>

---

### Úloha 2 — Vzdálenost dvou fakult v metrech

Spočítej vzdálenost mezi fakultou č. 1 a č. 20 v metrech.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT ROUND(
    ST_Distance(f1.geom::geography, f2.geom::geography)::numeric, 1
) AS vzdalenost_m
FROM   fakulta f1, fakulta f2
WHERE  f1.id = 1 AND f2.id = 20;
```

PostGIS pro EPSG:4326 počítá vzdálenost ve stupních. Přetypování na `geography` přepne výpočet na sférický a vrací metry.
</details>

---

### Úloha 3 — Fakulty v Brně podle zeměpisné šířky

Vypiš všechny fakulty v Brně seřazené podle zeměpisné šířky sestupně.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT id, nazev, ST_Y(geom) AS lat, ST_X(geom) AS lon
FROM   fakulta
WHERE  mesto = 'Brno'
ORDER BY ST_Y(geom) DESC;
```

`ST_X` a `ST_Y` extrahují souřadnice z bodu.
</details>

---

### Úloha 4 — Centroid fakult v Brně

Najdi geografický střed všech brněnských fakult.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT ST_AsText(ST_Centroid(ST_Collect(geom))) AS stred_brno
FROM   fakulta
WHERE  mesto = 'Brno';
```

`ST_Collect` agreguje body do MULTIPOINTu, `ST_Centroid` vrátí jejich těžiště.
</details>

---

### Úloha 5 — Bounding box všech fakult

Najdi obdélník, který obklopuje všechny fakulty.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT ST_AsText(ST_Extent(geom)) AS bbox
FROM   fakulta;
```

`ST_Extent` vrací box typu `box2d` — pro plnohodnotnou geometrii lze použít `ST_Envelope(ST_Collect(geom))`.
</details>

---

## Část 2: Středně těžké úlohy (joiny + prostor)

### Úloha 6 — Studenti a zaměstnanci na fakultu

Pro každou fakultu spočítej počet studentů a zaměstnanců.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f.id, f.nazev, f.mesto,
        COUNT(DISTINCT s.id) AS studentu,
        COUNT(DISTINCT z.id) AS zamestnancu
FROM    fakulta f
LEFT JOIN student     s ON s.id_fakul = f.id
LEFT JOIN zamestnanec z ON z.id_fakul = f.id
GROUP BY f.id, f.nazev, f.mesto
ORDER BY studentu DESC;
```

`COUNT(DISTINCT ...)` je nutné kvůli kartézskému součinu mezi studenty a zaměstnanci v rámci jedné fakulty.
</details>

---

### Úloha 7 — Tři nejbližší fakulty k fakultě č. 1

Najdi tři nejbližší fakulty k fakultě s ID 1 (k-NN dotaz).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f2.id, f2.nazev, f2.mesto,
        ROUND(ST_Distance(f1.geom::geography, f2.geom::geography)::numeric, 0) AS m
FROM    fakulta f1, fakulta f2
WHERE   f1.id = 1 AND f2.id <> 1
ORDER BY f1.geom <-> f2.geom
LIMIT 3;
```

Operátor `<->` v `ORDER BY` provede k-NN dotaz a využívá GIST index — je výrazně rychlejší než řazení podle `ST_Distance`.
</details>

---

### Úloha 8 — Páry fakult do 1 km

Najdi všechny dvojice fakult, které jsou od sebe vzdálené méně než 1 km.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f1.nazev AS fakulta_a,
        f2.nazev AS fakulta_b,
        ROUND(ST_Distance(f1.geom::geography, f2.geom::geography)::numeric, 0) AS m
FROM    fakulta f1
JOIN    fakulta f2 ON f1.id < f2.id
WHERE   ST_DWithin(f1.geom::geography, f2.geom::geography, 1000)
ORDER BY m;
```

Podmínka `f1.id < f2.id` zajistí, že každý pár se objeví jen jednou. `ST_DWithin` na `geography` bere vzdálenost v metrech a využívá index — preferuj ji před `ST_Distance(...) < x`.
</details>

---

### Úloha 9 — Fakulty do 5 km od centra Brna

Najdi všechny fakulty v okruhu 5 km od souřadnic 16.6068, 49.1951.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  id, nazev, mesto,
        ROUND(ST_Distance(
            geom::geography,
            ST_SetSRID(ST_MakePoint(16.6068, 49.1951), 4326)::geography
        )::numeric, 0) AS m_od_centra
FROM    fakulta
WHERE   ST_DWithin(
            geom::geography,
            ST_SetSRID(ST_MakePoint(16.6068, 49.1951), 4326)::geography,
            5000)
ORDER BY m_od_centra;
```

`ST_MakePoint` + `ST_SetSRID` vytvoří bod z dvojice souřadnic se zadaným souřadnicovým systémem.
</details>

---

### Úloha 10 — Studijní průměr podle města

Spočítej průměr studijního průměru studentů podle města, kde se nachází jejich fakulta.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f.mesto,
        COUNT(s.id)                       AS pocet_studentu,
        ROUND(AVG(s.studijni_prumer), 2)  AS prumer
FROM    fakulta f
JOIN    student s ON s.id_fakul = f.id
GROUP BY f.mesto
ORDER BY prumer;
```

</details>

---

## Část 3: Těžší úlohy (kombinované)

### Úloha 11 — Konvexní obal fakult

Vytvoř konvexní obal („obrys“) všech fakult univerzity.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT ST_AsText(ST_ConvexHull(ST_Collect(geom))) AS obrys
FROM   fakulta;
```

`ST_ConvexHull` vrátí nejmenší konvexní polygon, který obsahuje všechny vstupní body. Pro vizualizaci v QGISu vrať přímo geometrii bez `ST_AsText`.
</details>

---

### Úloha 12 — Centroid a počet aktivních studentů na město

Pro každé město vypiš centroid jeho fakult, počet fakult a počet aktivních studentů.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f.mesto,
        COUNT(DISTINCT f.id)                                AS pocet_fakult,
        ST_AsText(ST_Centroid(ST_Collect(f.geom)))          AS stred,
        SUM(CASE WHEN s.aktivni THEN 1 ELSE 0 END)          AS aktivnich_studentu
FROM    fakulta f
LEFT JOIN student s ON s.id_fakul = f.id
GROUP BY f.mesto
ORDER BY pocet_fakult DESC;
```

</details>

---

### Úloha 13 — Sousedi do 500 m

Pro každou fakultu vypiš seznam fakult v okruhu 500 m.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f1.id, f1.nazev,
        STRING_AGG(f2.nazev, ', ' ORDER BY f2.nazev) AS sousedi_500m
FROM    fakulta f1
JOIN    fakulta f2
            ON f1.id <> f2.id
            AND ST_DWithin(f1.geom::geography, f2.geom::geography, 500)
GROUP BY f1.id, f1.nazev
ORDER BY f1.id;
```

`STRING_AGG` spojí názvy do jednoho řetězce. Fakulty bez souseda v daném okruhu nebudou ve výsledku — pro zachování všech použij `LEFT JOIN`.
</details>

---

### Úloha 14 — Top 5 fakult podle průměrného platu na plný úvazek

Najdi 5 fakult s nejvyšším průměrným platem zaměstnanců přepočteným na plný úvazek.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f.id, f.nazev, f.mesto,
        ROUND(AVG(z.plat_kc / NULLIF(z.uvazek, 0))::numeric, 0) AS prum_plat_full_uvazek
FROM    fakulta f
JOIN    zamestnanec z ON z.id_fakul = f.id
GROUP BY f.id, f.nazev, f.mesto
ORDER BY prum_plat_full_uvazek DESC
LIMIT 5;
```

`NULLIF(z.uvazek, 0)` chrání před dělením nulou.
</details>

---

### Úloha 15 — Nejbližší jiná fakulta pro každého studenta

Pro každého studenta najdi nejbližší fakultu, na které **nestuduje**.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  s.id, s.jmeno, s.prijmeni, s.id_fakul AS fakulta_kde_studuje,
        nf.id AS nejblizsi_jina_fakulta, nf.nazev,
        ROUND(nf.m::numeric, 0) AS metru
FROM    student s
JOIN    fakulta home ON home.id = s.id_fakul
CROSS JOIN LATERAL (
    SELECT  f.id, f.nazev,
            ST_Distance(home.geom::geography, f.geom::geography) AS m
    FROM    fakulta f
    WHERE   f.id <> s.id_fakul
    ORDER BY home.geom <-> f.geom
    LIMIT 1
) nf
ORDER BY s.id
LIMIT 20;
```

`LATERAL` umožňuje vnitřnímu poddotazu odkazovat na sloupce z vnějšího — pro každého studenta tak proběhne samostatný k-NN dotaz.
</details>

---

### Úloha 16 — Lokální vs. dojíždějící studenti

Roztřiď studenty na lokální (bydlí ve městě své fakulty) a dojíždějící, a porovnej jejich průměrné známky.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
WITH s_local AS (
    SELECT s.*, f.mesto AS mesto_fakulty
    FROM   student s
    JOIN   fakulta f ON f.id = s.id_fakul
)
SELECT
    CASE WHEN mesto = mesto_fakulty THEN 'lokální' ELSE 'dojíždějící' END AS kategorie,
    COUNT(*) AS pocet,
    ROUND(AVG(studijni_prumer), 2) AS prum_znamka
FROM   s_local
GROUP BY 1
ORDER BY 1;
```

</details>

---

### Úloha 17 — Nejúspěšnější předměty

Najdi 10 předmětů s nejvyšším podílem známek A a B (jen předměty s alespoň 20 zápisy).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  p.id, p.nazev,
        COUNT(*) FILTER (WHERE h.znamka IN ('A','B'))::float
            / COUNT(*)              AS podil_dobre,
        COUNT(*)                    AS pocet
FROM    predmet p
JOIN    zapis z      ON z.id_pred = p.id
JOIN    hodnoceni h  ON h.id_zap  = z.id
GROUP BY p.id, p.nazev
HAVING  COUNT(*) >= 20
ORDER BY podil_dobre DESC
LIMIT 10;
```

`FILTER (WHERE ...)` je elegantní syntax pro podmíněnou agregaci.
</details>

---

### Úloha 18 — Tepelná mapa úspěšnosti

Pro každou fakultu spočítej průměrnou známku jejích studentů (A=1, B=2, … F=6) — vhodné pro tepelnou mapu.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  f.id, f.nazev, f.mesto, f.geom,
        ROUND(AVG(ASCII(h.znamka) - ASCII('A') + 1)::numeric, 2) AS prum_znamka
FROM    fakulta f
JOIN    student s    ON s.id_fakul = f.id
JOIN    zapis z      ON z.id_stu   = s.id
JOIN    hodnoceni h  ON h.id_zap   = z.id
GROUP BY f.id, f.nazev, f.mesto, f.geom
ORDER BY prum_znamka;
```

`ASCII(h.znamka) - ASCII('A') + 1` převede znak A → 1, B → 2, … F → 6.
</details>

---

### Úloha 19 — Voronoiho diagram fakult

Vytvoř Voronoiho diagram (spádové oblasti) všech fakult.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT (ST_Dump(ST_VoronoiPolygons(ST_Collect(geom)))).geom AS oblast
FROM   fakulta;
```

Pro přiřazení každého polygonu k „jeho“ fakultě:

```sql
WITH vor AS (
    SELECT (ST_Dump(ST_VoronoiPolygons(ST_Collect(geom)))).geom AS poly
    FROM   fakulta
)
SELECT  f.id, f.nazev, vor.poly
FROM    vor
JOIN    LATERAL (
            SELECT id, nazev FROM fakulta f
            ORDER BY f.geom <-> ST_Centroid(vor.poly)
            LIMIT 1
        ) f ON TRUE;
```

Vyžaduje PostGIS ≥ 2.3.
</details>

---

### Úloha 20 — Export do GeoJSONu

Vyexportuj fakulty jako GeoJSON FeatureCollection s počtem studentů jako property.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT jsonb_build_object(
    'type',     'FeatureCollection',
    'features', jsonb_agg(feat)
)
FROM (
    SELECT jsonb_build_object(
        'type',       'Feature',
        'geometry',   ST_AsGeoJSON(f.geom)::jsonb,
        'properties', jsonb_build_object(
            'id',       f.id,
            'nazev',    f.nazev,
            'mesto',    f.mesto,
            'studentu', COUNT(s.id)
        )
    ) AS feat
    FROM    fakulta f
    LEFT JOIN student s ON s.id_fakul = f.id
    GROUP BY f.id
) sub;
```

`ST_AsGeoJSON` převede geometrii do GeoJSON formátu. `jsonb_build_object` a `jsonb_agg` poskládají kompletní FeatureCollection.
</details>

---

## Část 4: OGC operace

Cvičení k základním OGC funkcím podle specifikace OpenGIS: `Dimension`, `GeometryType`, `SRID`, `Envelope`, `AsText`, `AsBinary`, `IsEmpty`, `IsSimple`, `Boundary`. V PostGIS mají prefix `ST_`.

> **Poznámka:** Tabulka `fakulta` obsahuje pouze body, takže pro funkce smysluplné na liniích a polygonech (`IsSimple`, `Boundary`, …) odvozujeme geometrie z dat — spojnice fakult, konvexní obaly měst, buffery atp.

### Úloha 21 — Inventarizace fakult

Pro každou fakultu vrať typ geometrie, dimenzi a SRID.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  id, nazev, mesto,
        ST_GeometryType(geom) AS typ,
        ST_Dimension(geom)    AS dim,
        ST_SRID(geom)         AS srid
FROM    fakulta
ORDER BY id;
```

Očekávaný výsledek: `ST_Point`, dim `0`, SRID `4326` u všech.
</details>

---

### Úloha 22 — Export do WKT a WKB

Vyexportuj geometrii fakulty č. 1 v textovém i binárním formátu.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  id, nazev,
        ST_AsText(geom)   AS wkt,
        ST_AsBinary(geom) AS wkb
FROM    fakulta
WHERE   id = 1;
```

WKT je čitelný řetězec (`POINT(...)`), WKB binární reprezentace pro efektivní přenos.
</details>

---

### Úloha 23 — Envelope: jeden bod vs. kolekce

Porovnej envelope jednoho bodu (degenerovaný) s envelope všech fakult v Brně (skutečný BBOX).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
-- Envelope jednoho bodu
SELECT ST_AsText(ST_Envelope(geom)) AS env_jeden_bod
FROM   fakulta WHERE id = 1;

-- Envelope všech fakult v Brně
SELECT ST_AsText(ST_Envelope(ST_Collect(geom))) AS env_brno
FROM   fakulta WHERE mesto = 'Brno';
```

Envelope jednoho bodu je vlastně bod (nulová plocha), envelope kolekce je obdélník.
</details>

---

### Úloha 24 — Validace: žádná prázdná geometrie

Ověř, že žádná fakulta nemá prázdnou geometrii.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT id, nazev, ST_IsEmpty(geom) AS prazdna
FROM   fakulta
WHERE  ST_IsEmpty(geom);
-- Pokud nic nevrátí, data jsou v pořádku.
```

</details>

---

### Úloha 25 — IsSimple na trase přes Brno

Vytvoř linii procházející všemi brněnskými fakultami v pořadí podle ID a otestuj, zda je „simple“ (sama sebe nikde neprotíná).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
WITH cesta AS (
    SELECT ST_MakeLine(geom ORDER BY id) AS lin
    FROM   fakulta
    WHERE  mesto = 'Brno'
)
SELECT  ST_GeometryType(lin) AS typ,
        ST_NPoints(lin)      AS pocet_bodu,
        ST_IsSimple(lin)     AS jednoducha
FROM    cesta;
```

`ST_MakeLine` s `ORDER BY` v agregaci vytvoří linii v zadaném pořadí.
</details>

---

### Úloha 26 — Boundary: bod vs. polygon

Porovnej hranici bodu (= prázdná geometrie) s hranicí konvexního obalu fakult v Brně (= obvodová linie).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  'fakulta'                     AS co,
        ST_GeometryType(geom)         AS vstup,
        ST_AsText(ST_Boundary(geom))  AS hranice
FROM    fakulta WHERE id = 1

UNION ALL

SELECT  'obal Brna' AS co,
        ST_GeometryType(ST_ConvexHull(ST_Collect(geom))) AS vstup,
        ST_AsText(ST_Boundary(ST_ConvexHull(ST_Collect(geom)))) AS hranice
FROM    fakulta WHERE mesto = 'Brno';
```

</details>

---

### Úloha 27 — Bounding box pro každé město

Pro každé město spočítej bounding box, jeho typ a dimenzi.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  mesto,
        COUNT(*)                                          AS pocet_fakult,
        ST_AsText(ST_Envelope(ST_Collect(geom)))          AS bbox_mesta,
        ST_GeometryType(ST_Envelope(ST_Collect(geom)))    AS typ_bboxu,
        ST_Dimension(ST_Envelope(ST_Collect(geom)))       AS dim_bboxu
FROM    fakulta
GROUP BY mesto
ORDER BY pocet_fakult DESC;
```

Pozor: u města s 1 fakultou bude bbox bod (dim 0), u 2 fakult linie (dim 1), u 3+ polygon (dim 2).
</details>

---

### Úloha 28 — Obvod konvexního obalu Brna

Spočítej obvod konvexního obalu brněnských fakult v metrech.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  ROUND(
            ST_Length(
                ST_Boundary(
                    ST_ConvexHull(ST_Collect(geom))
                )::geography
            )::numeric, 0
        ) AS obvod_obalu_brno_m
FROM    fakulta
WHERE   mesto = 'Brno';
```

Skládá se: `Collect` → `ConvexHull` → `Boundary` → `Length::geography`.
</details>

---

### Úloha 29 — Buffer 1 km a jeho obvod

Pro prvních 5 fakult vytvoř buffer 1 km, jeho envelope a obvod.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  id, nazev,
        ST_AsText(
            ST_Envelope(ST_Buffer(geom::geography, 1000)::geometry)
        ) AS env_1km_wkt,
        ST_GeometryType(
            ST_Boundary(ST_Buffer(geom::geography, 1000)::geometry)
        ) AS typ_hranice,
        ROUND(
            ST_Length(
                ST_Boundary(ST_Buffer(geom::geography, 1000)::geometry)::geography
            )::numeric, 0
        ) AS obvod_m
FROM    fakulta
ORDER BY id
LIMIT 5;
```

Obvod kruhu o poloměru 1000 m vyjde cca **6283 m** (2π·1000) — dobrý sanity check.
</details>

---

### Úloha 30 — Hygienický přehled fakult

Najdi geometrie, které jsou prázdné, nevalidní nebo mají špatné SRID.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  id, nazev, mesto,
        ST_GeometryType(geom)  AS typ,
        ST_SRID(geom)          AS srid,
        ST_IsEmpty(geom)       AS prazdna,
        ST_IsSimple(geom)      AS simple,
        ST_IsValid(geom)       AS valid,
        ST_IsValidReason(geom) AS duvod
FROM    fakulta
WHERE   ST_IsEmpty(geom)
   OR   NOT ST_IsValid(geom)
   OR   ST_SRID(geom) <> 4326;
```

V reálu velmi užitečné při importu nových dat.
</details>

---

### Úloha 31 — Univerzální VIEW pro debug

Vytvoř pohled `v_fakulta_info`, který popisuje každou fakultu všemi OGC funkcemi naráz.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
CREATE OR REPLACE VIEW v_fakulta_info AS
SELECT  id, nazev, mesto, dekan, rok_zalozeni,
        ST_GeometryType(geom) AS typ,
        ST_Dimension(geom)    AS dim,
        ST_SRID(geom)         AS srid,
        ST_IsEmpty(geom)      AS prazdna,
        ST_IsSimple(geom)     AS simple,
        ST_AsText(geom)       AS wkt,
        ST_AsText(ST_Envelope(geom)) AS bbox
FROM    fakulta;

-- použití:
SELECT * FROM v_fakulta_info WHERE mesto = 'Brno';
```

</details>

---

### Úloha 32 — Závěrečná kombinovaná úloha

Napiš **jeden dotaz**, který pro každé město vrátí: počet fakult, bbox jako WKT, jeho dimenzi (0/1/2), obvod bboxu v metrech a údaj, zda je prázdný.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  mesto,
        COUNT(*) AS pocet,
        ST_AsText(ST_Envelope(ST_Collect(geom)))    AS bbox_wkt,
        ST_Dimension(ST_Envelope(ST_Collect(geom))) AS dim,
        ROUND(
            ST_Length(
                ST_Boundary(ST_Envelope(ST_Collect(geom)))::geography
            )::numeric, 0
        ) AS obvod_m,
        ST_IsEmpty(ST_Envelope(ST_Collect(geom)))   AS prazdny
FROM    fakulta
GROUP BY mesto
ORDER BY pocet DESC;
```

</details>

---

## Část 5: Propojení s QGISem

QGIS dokáže pracovat s PostGIS vrstvou nativně — připojí se přímo do databáze a vizualizuje libovolný SQL dotaz nad našimi tabulkami. To je ideální způsob, jak si výsledky úloh z předchozích sekcí **vidět na mapě**.

### 5.1 Připojení QGISu k PostGIS databázi

1. Otevři QGIS → **Browser panel** (vlevo) → pravý klik na **PostgreSQL** → **New Connection…**
2. Vyplň:
   - **Name:** `univerzita`
   - **Host:** `localhost` (nebo IP serveru)
   - **Port:** `5432`
   - **Database:** `univerzita`
   - **Authentication:** Basic — vyplň uživatele a heslo, zaškrtni *Save Username* a *Save Password*
3. Klikni **Test Connection** → **OK**

V Browser panelu se objeví větev `univerzita` se schématem `public` a tabulkou `fakulta`. Stačí ji **přetáhnout na plátno** a fakulty se vykreslí jako body.

> **Tip:** Tabulky bez geometrie (`student`, `predmet`, …) se v QGISu zobrazí jako **needitované záznamy bez vrstvy** — můžeš je joinovat na fakultu v *Layer Properties → Joins* nebo (lépe) přes SQL.

### 5.2 DB Manager — spouštění dotazů přímo v QGISu

**Database → DB Manager → PostGIS → univerzita → SQL Window** (`F2`).

Sem zkopíruj SQL z kterékoli předchozí úlohy. Klíčové je zaškrtnout **„Load as new layer"** a vybrat sloupec s geometrií — QGIS pak výsledek dotazu načte přímo jako vrstvu.

---

### Úloha 33 — Centroidy měst jako vrstva

Načti centroidy fakult pro každé město jako bodovou vrstvu v QGISu.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

V DB Manageru spusť:

```sql
SELECT  ROW_NUMBER() OVER (ORDER BY mesto) AS gid,
        mesto,
        COUNT(*) AS pocet_fakult,
        ST_Centroid(ST_Collect(geom))::geometry(Point, 4326) AS geom
FROM    fakulta
GROUP BY mesto;
```

V dialogu zaškrtni:
- **Load as new layer**
- **Column with unique values:** `gid`
- **Geometry column:** `geom`
- **Layer name:** `centroidy_mest`

Centroidy můžeš stylovat podle `pocet_fakult` (Properties → Symbology → Graduated).
</details>

---

### Úloha 34 — Konvexní obal univerzity jako polygonová vrstva

Vykresli konvexní obal všech fakult jako polygon na mapě.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  1 AS gid,
        'Univerzita ČR' AS popis,
        ST_ConvexHull(ST_Collect(geom))::geometry(Polygon, 4326) AS geom
FROM    fakulta;
```

Načti jako vrstvu, nastav `gid` jako klíč. V Layer Properties → Symbology zvol *Simple fill* s průhledností 30 %, pak **přidej i původní vrstvu `fakulta`** nahoru, ať vidíš body uvnitř obalu.
</details>

---

### Úloha 35 — Voronoiho diagram s atributy

Načti Voronoiho polygony, kde každý polygon má atributy „své" fakulty (název, město, počet studentů).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
WITH vor AS (
    SELECT (ST_Dump(ST_VoronoiPolygons(ST_Collect(geom)))).geom AS poly
    FROM   fakulta
),
prirazeno AS (
    SELECT  v.poly,
            f.id, f.nazev, f.mesto
    FROM    vor v
    JOIN    LATERAL (
                SELECT id, nazev, mesto FROM fakulta f
                ORDER BY f.geom <-> ST_Centroid(v.poly)
                LIMIT 1
            ) f ON TRUE
)
SELECT  p.id AS gid,
        p.nazev,
        p.mesto,
        COUNT(s.id) AS studentu,
        p.poly::geometry(Polygon, 4326) AS geom
FROM    prirazeno p
LEFT JOIN student s ON s.id_fakul = p.id
GROUP BY p.id, p.nazev, p.mesto, p.poly;
```

V QGISu nastav stylování *Categorized* podle `mesto` — uvidíš spádové oblasti barvené podle města fakulty. Přes vrstvu pak hodíš `fakulta` jako body.
</details>

---

### Úloha 36 — Tepelná mapa úspěšnosti

Načti vrstvu fakult s atributem průměrná známka a obarvi je od zelené (nejlepší) po červenou (nejhorší).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

SQL je rozšíření úlohy 18:

```sql
SELECT  f.id AS gid,
        f.nazev,
        f.mesto,
        ROUND(AVG(ASCII(h.znamka) - ASCII('A') + 1)::numeric, 2) AS prum_znamka,
        COUNT(*) AS pocet_znamek,
        f.geom
FROM    fakulta f
JOIN    student s    ON s.id_fakul = f.id
JOIN    zapis z      ON z.id_stu   = s.id
JOIN    hodnoceni h  ON h.id_zap   = z.id
GROUP BY f.id, f.nazev, f.mesto, f.geom;
```

V QGISu: **Properties → Symbology → Graduated**, sloupec `prum_znamka`, color ramp `RdYlGn` *invertovaný* (nízká = zelená = lepší známka), velikost bodů podle `pocet_znamek` (Symbology → *Data defined override* na velikosti).
</details>

---

### Úloha 37 — Buffery 5 km kolem fakult

Vytvoř polygonovou vrstvu s 5km buffery kolem všech fakult — užitečné pro vizualizaci „spádových oblastí" v městech s více fakultami (překryvy).

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
SELECT  id AS gid,
        nazev,
        mesto,
        ST_Buffer(geom::geography, 5000)::geometry(Polygon, 4326) AS geom
FROM    fakulta;
```

Přetypování přes `geography` zajistí buffer v metrech. Polygon vrácený zpět do EPSG:4326 se v QGISu zobrazí korektně. Pro průhlednost: Symbology → *Simple fill* → *Opacity* 25 %.
</details>

---

### Úloha 38 — Spojnice studentů s domácí fakultou

Pro studenty, jejichž bydliště se shoduje s městem, kde je nějaká fakulta univerzity, vykresli linie z města bydliště na jejich fakultu. Pokud bydliště není město fakulty, student se vynechá.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

Studenti nemají souřadnice bydliště, ale můžeme použít **centroid fakult ve městě bydliště** jako proxy:

```sql
WITH centroidy_mest AS (
    SELECT mesto, ST_Centroid(ST_Collect(geom)) AS bydliste_geom
    FROM   fakulta
    GROUP BY mesto
)
SELECT  s.id AS gid,
        s.jmeno || ' ' || s.prijmeni AS student,
        s.mesto AS bydli,
        f.mesto AS studuje,
        ROUND(ST_Distance(c.bydliste_geom::geography, f.geom::geography)::numeric, 0) AS km_cesty,
        ST_MakeLine(c.bydliste_geom, f.geom)::geometry(LineString, 4326) AS geom
FROM    student s
JOIN    fakulta f         ON f.id = s.id_fakul
JOIN    centroidy_mest c  ON c.mesto = s.mesto
WHERE   s.mesto <> f.mesto;   -- jen dojíždějící
```

V QGISu zobraz s tloušťkou linky podle `km_cesty` — uvidíš dojížděcí toky studentů.
</details>

---

### Úloha 39 — Materializovaný pohled pro mapu

Pro výkonnostně náročné úlohy (např. tepelná mapa) je lepší vytvořit **materializovaný pohled** v PostgreSQL a v QGISu pracovat s ním.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

```sql
CREATE MATERIALIZED VIEW mv_fakulta_uspech AS
SELECT  f.id AS gid,
        f.nazev,
        f.mesto,
        COUNT(DISTINCT s.id) AS pocet_studentu,
        ROUND(AVG(ASCII(h.znamka) - ASCII('A') + 1)::numeric, 2) AS prum_znamka,
        f.geom
FROM    fakulta f
LEFT JOIN student s    ON s.id_fakul = f.id
LEFT JOIN zapis z      ON z.id_stu   = s.id
LEFT JOIN hodnoceni h  ON h.id_zap   = z.id
GROUP BY f.id, f.nazev, f.mesto, f.geom;

CREATE INDEX ON mv_fakulta_uspech USING GIST (geom);

-- Refresh po změně dat:
-- REFRESH MATERIALIZED VIEW mv_fakulta_uspech;
```

V QGISu Browseru se materializovaný pohled chová jako tabulka — **přetáhni přímo na plátno**. Pro QGIS je důležité mít primární klíč: pokud nemá, přidej:

```sql
ALTER TABLE mv_fakulta_uspech ADD CONSTRAINT mv_pk PRIMARY KEY (gid);
```
</details>

---

### Úloha 40 — Export vrstvy do GeoPackage / Shapefile

Po načtení vrstvy z dotazu ji exportuj do souboru pro offline použití.

<details>
<summary><strong>Zobrazit řešení</strong></summary>

V QGISu:
1. Pravý klik na vrstvu (např. `voronoi_fakult`) → **Export → Save Features As…**
2. **Format:** GeoPackage (`.gpkg`) — moderní formát, podporuje delší názvy sloupců a UTF-8
3. **File name:** `univerzita.gpkg`
4. CRS: ponech `EPSG:4326`

Alternativně přes příkazovou řádku PostgreSQL nástrojem `ogr2ogr`:

```bash
ogr2ogr -f GPKG univerzita.gpkg \
        PG:"host=localhost dbname=univerzita user=postgres" \
        -sql "SELECT * FROM mv_fakulta_uspech"
```

Shapefile **nedoporučuju** — má omezení 10 znaků na název sloupce a problém s českou diakritikou.
</details>

---

### 5.3 Stylování přes QML

Pokud chceš sdílet stylování vrstvy s kolegy, ulož ho jako QML:

1. Pravý klik na vrstvu → **Properties → Symbology** → nastav vzhled
2. Dole **Style → Save Style → As QGIS QML Style File**
3. Soubor `.qml` můžeš commitnout do repozitáře vedle SQL

QGIS si pak při dalším načtení stejné PostGIS vrstvy styl automaticky natáhne (pokud je `.qml` ve stejné složce nebo uložený přímo v databázi přes *Save Style → In Database*).

### 5.4 Tipy pro práci s PostGIS v QGISu

- **Vždy přetypovávej geometrii v dotazu** přes `::geometry(TYP, SRID)` — QGIS pak správně rozpozná typ vrstvy a SRID bez ručního nastavování.
- **Primární klíč je nutnost** — bez něj QGIS vrstvu načte jen jako read-only. Pokud dotaz nemá `id`, přidej `ROW_NUMBER() OVER () AS gid`.
- **Filtruj v PostGIS, ne v QGISu** — pošli na server `WHERE mesto = 'Brno'`, ne aby QGIS stáhl všechny řádky a pak je filtroval lokálně.
- **Atlas a tisk:** Materializované pohledy + Print Layout v QGISu = automatické generování map (např. „pro každé město jeden A4 s fakultami").

---

## Tipy a dobré praktiky

### Indexy

Vždy vytvoř GIST index na geometrii, bez něj jsou prostorové dotazy pomalé:

```sql
CREATE INDEX fakulta_geom_idx ON fakulta USING GIST (geom);
```

### Vzdálenosti v metrech

Pro EPSG:4326 (stupně) přetypuj geometrii na `geography`:

```sql
ST_DWithin(a.geom::geography, b.geom::geography, 1000)  -- 1000 m
```

`ST_DWithin` na `geography` přebíjí `ST_Distance(...) < x` — využije index a vrací metry.

### k-NN dotazy

Operátor `<->` v `ORDER BY` provede k-NN s GIST indexem:

```sql
ORDER BY a.geom <-> b.geom LIMIT 5
```

### Projekce pro Česko

Pro Česko se hodí **S-JTSK / EPSG:5514** — pak jsou jednotky metry a vzdálenosti i bufferování fungují správně:

```sql
ST_Buffer(ST_Transform(geom, 5514), 1000)  -- 1 km buffer v metrech
```

### Srovnávací tabulka OGC funkcí

| Funkce          | Použití v praxi                                                |
|-----------------|----------------------------------------------------------------|
| `Dimension`     | Filtr „chci jen body / jen polygony“ bez znalosti přesného typu |
| `GeometryType`  | Větvení logiky (multi vs. single, point vs. linestring)        |
| `SRID`          | Hlídání kompatibility geometrií před joinem / vzdáleností       |
| `Envelope`      | Rychlý bounding box pro náhled mapy nebo hrubé filtry          |
| `AsText`        | Debug, export do textu, předání do jiných nástrojů             |
| `AsBinary`      | Efektivní přenos do klienta nebo souboru (WKB)                 |
| `IsEmpty`       | Validace dat, vyloučení „nic“ z agregací                       |
| `IsSimple`      | Detekce sebeprotínajících linií (problémy v topologii)          |
| `Boundary`      | Získání obrysu polygonu, koncových bodů linie                  |

---
