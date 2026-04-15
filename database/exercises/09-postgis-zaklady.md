---
title: 6. PostGIS — setup
nav_order: 7
---

# 6. PostGIS — aktivace a setup

## 6.1 Co je PostGIS

PostGIS je rozšíření PostgreSQL, které přidává:

- datové typy `GEOMETRY` a `GEOGRAPHY` pro ukládání prostorových dat
- přes 500 prostorových funkcí (`ST_Distance`, `ST_Within`, `ST_Intersects`, …)
- prostorové indexy (GIST) pro výkonné dotazy
- podporu souřadnicových systémů (SRID)

Bez PostGIS jsou sloupce `x` a `y` jen čísla — databáze neví, že jde o souřadnice.

---

## 6.2 Aktivace

```sql
-- vyžaduje superuser nebo CREATE privilege na databázi
-- stačí jednou per databáze
CREATE EXTENSION IF NOT EXISTS postgis;

-- ověření verze
SELECT PostGIS_Full_Version();
```

---

## 6.3 GEOMETRY vs GEOGRAPHY

| | GEOMETRY | GEOGRAPHY |
|---|---|---|
| Soustava | planární (rovná plocha) | sférická (kulová plocha Země) |
| Vzdálenost | ve stupních nebo metrech (záleží na SRID) | vždy v metrech |
| Přesnost na velké vzdálenosti | nižší | vyšší |
| Výkon | rychlejší | pomalejší |
| Typické použití | malé oblasti, CAD | celosvětová data, GPS |

```sql
-- GEOMETRY s SRID 4326 (WGS84 = GPS souřadnice)
geom GEOMETRY(Point, 4326)

-- cast na geography pro metrické výpočty
ST_Distance(geom::geography, cil::geography)   -- → metry
ST_Distance(geom, cil)                          -- → stupně (nevhodné pro vzdálenosti)
```

---

## 6.4 Přidání sloupce geometrie

```sql
-- přidání sloupce Point s SRID 4326 (WGS84)
ALTER TABLE fakulty
ADD COLUMN IF NOT EXISTS geom GEOMETRY(Point, 4326);
```

Typy geometrií:

| Typ | Popis |
|---|---|
| `Point` | bod (lon, lat) |
| `LineString` | linie |
| `Polygon` | polygon (oblast) |
| `MultiPoint` | více bodů |
| `MultiPolygon` | více polygonů |

---

## 6.5 Naplnění geometrie ze souřadnic

```sql
-- ST_MakePoint(longitude, latitude) — pořadí: LON první, LAT druhá!
UPDATE fakulty
SET    geom = ST_SetSRID(ST_MakePoint(x, y), 4326)
WHERE  x IS NOT NULL AND y IS NOT NULL;
```

> **Pozor na pořadí:** `ST_MakePoint(longitude, latitude)` = `ST_MakePoint(x, y)`.  
> Záměna způsobí, že bod skončí na nesprávném místě.

```sql
-- ověření — WKT výstup
SELECT id, nazev,
       ST_AsText(geom) AS wkt,
       ST_X(geom)      AS lon,
       ST_Y(geom)      AS lat,
       x, y            -- porovnání s originálem
FROM   fakulty
ORDER BY id;
```

---

## 6.6 Prostorový index

```sql
-- GIST index na sloupec geometrie
CREATE INDEX IF NOT EXISTS idx_fakulty_geom
ON fakulty USING GIST (geom);
```

Bez indexu jsou prostorové dotazy sekvenční sken (`Seq Scan`).  
S indexem PostgreSQL použije `Index Scan` a dotaz je řádově rychlejší na velkých tabulkách.

```sql
-- ověření indexu
SELECT indexname, indexdef
FROM   pg_indexes
WHERE  tablename = 'fakulty';
```

---

## 6.7 Přehled základních funkcí

### Konstruktory

```sql
ST_MakePoint(lon, lat)              -- bod ze souřadnic
ST_SetSRID(geom, 4326)              -- přiřazení souřadnicového systému
ST_GeomFromText('POINT(16.6 49.2)', 4326)  -- bod z WKT
```

### Konverze výstupu

```sql
ST_AsText(geom)        -- WKT:  POINT(16.6068 49.1951)
ST_AsGeoJSON(geom)     -- GeoJSON: {"type":"Point","coordinates":[16.6068,49.1951]}
ST_X(geom)             -- extrakce longitude
ST_Y(geom)             -- extrakce latitude
ST_SRID(geom)          -- ID souřadnicového systému (4326 = WGS84)
```

### Vzdálenost a okolí

```sql
ST_Distance(a, b)                   -- vzdálenost (::geography → metry)
ST_DWithin(a, b, radius)            -- TRUE/FALSE: je b do radius metrů od a?
ST_ClosestPoint(a, b)               -- nejbližší bod na a k b
```

### Vztahy mezi geometriemi

```sql
ST_Within(a, b)         -- leží a celé uvnitř b?
ST_Contains(a, b)       -- obsahuje a celé b?
ST_Intersects(a, b)     -- překrývají se a a b?
ST_Touches(a, b)        -- dotýkají se hranicí (ne překrytí)?
```

### Transformace

```sql
ST_Buffer(geom, radius)             -- obalová zóna (::geography → metry)
ST_Centroid(geom)                   -- těžiště
ST_Collect(geom)                    -- agregace geometrií (aggregate funkce)
ST_Union(geom)                      -- sloučení do jedné geometrie
ST_Transform(geom, srid)            -- převod mezi souřadnicovými systémy
```

---

## 6.8 Časté chyby

### ERROR: function st_distance(geometry, geography) does not exist

Obě geometrie musí mít stejný typ — buď obě `::geometry`, nebo obě `::geography`:

```sql
-- správně
ST_Distance(geom::geography, ST_SetSRID(ST_MakePoint(16.6, 49.2), 4326)::geography)

-- špatně — smíchané typy
ST_Distance(geom, ST_SetSRID(ST_MakePoint(16.6, 49.2), 4326)::geography)
```

### Výsledek ST_Distance je v stupních, ne metrech

Chybí cast `::geography`:

```sql
-- špatně — vrátí stupně
ST_Distance(geom, cil)

-- správně — vrátí metry
ST_Distance(geom::geography, cil::geography)
```

### ST_DWithin vrací FALSE pro blízké body

Radius je v metrech při `::geography`, ale ve stupních bez něj:

```sql
-- hledám v okruhu 50 km
ST_DWithin(geom::geography, cil::geography, 50000)   -- 50000 metrů ✓
ST_DWithin(geom, cil, 50000)                         -- 50000 stupňů  ✗
```
