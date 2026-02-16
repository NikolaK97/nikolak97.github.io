# Nastavení PostgreSQL (lokálně)

## Varianta A: pgAdmin (GUI) – vytvoření databáze
1. Otevři pgAdmin
2. Servers → PostgreSQL → Databases
3. Pravé tlačítko na Databases → Create → Database
4. Name: `projekt_db`
5. Owner: `postgres`
6. Save

## Varianta B: SQL
1. V pgAdmin otevři Query Tool (např. nad databází `postgres`)
2. Spusť:
```sql
CREATE DATABASE projekt_db;
