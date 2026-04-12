# Připojení PostGIS do QGIS -- podrobný návod

Tento návod popisuje kompletní postup, jak připojit databázi PostgreSQL
s rozšířením PostGIS do QGIS.

## 1. Co je PostGIS

PostGIS je rozšíření pro PostgreSQL, které umožňuje práci s prostorovými
daty (GIS).

## 2. Předpoklady

-   PostgreSQL
-   PostGIS
-   QGIS

## 3. Instalace PostGIS

### Windows

Použij Stack Builder → Spatial Extensions → PostGIS

### Linux

sudo apt install postgis postgresql-15-postgis-3

### macOS

brew install postgis

## 4. Aktivace PostGIS

psql -U postgres `\c m`{=tex}oje_databaze CREATE EXTENSION postgis;
SELECT PostGIS_version();

## 5. Vytvoření tabulky

CREATE TABLE mesta ( id SERIAL PRIMARY KEY, nazev VARCHAR(100), geom
GEOMETRY(Point, 4326) );

## 6. Vložení dat

INSERT INTO mesta (nazev, geom) VALUES ('Brno',
ST_SetSRID(ST_MakePoint(16.6068, 49.1951), 4326));

## 7. QGIS připojení

Layer → Data Source Manager → PostgreSQL → New

Host: localhost\
Port: 5432\
Database: moje_databaze\
User: muj_uzivatel

## 8. Načtení dat

Connect → vyber tabulku → Add

## 9. Problémy

-   authentication failed → špatné heslo\
-   could not connect → PostgreSQL neběží

## 10. Tip

CREATE INDEX idx_geom ON mesta USING GIST (geom);
