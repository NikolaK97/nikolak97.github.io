# Aktivace PostGIS v databázi

1. Připoj se do konkrétní databáze (např. `geo_projekt`)
2. Spusť:
```sql
CREATE EXTENSION postgis;
```

3. Ověř:
```sql
SELECT PostGIS_Version();
```
