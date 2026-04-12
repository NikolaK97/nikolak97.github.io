# Nastavení PostgreSQL (lokálně)


------------------------------------------------------------------------

## 1. Úvod

PostgreSQL je relační databázový systém (RDBMS), který umožňuje
ukládání, správu a zpracování dat pomocí jazyka SQL.

Používá se v: - webových aplikacích - informačních systémech - datové
analytice

------------------------------------------------------------------------

## 2. Localhost

-   vlastní počítač
-   IP adresa `127.0.0.1`
-   databáze není dostupná z internetu

**Výhody:** - jednoduché nastavení - bezpečné prostředí - vhodné pro
vývoj a výuku

------------------------------------------------------------------------

## 3. Instalace PostgreSQL

### 3.1 Windows

https://www.postgresql.org/download/windows/

-   stáhni instalační balíček
-   spusť instalaci
-   nastav heslo pro `postgres`
-   port `5432`
-   doporučeno: pgAdmin

### 3.2 macOS

    brew install postgresql
    brew services start postgresql

### 3.3 Linux (Ubuntu)

    sudo apt update
    sudo apt install postgresql postgresql-contrib -y

------------------------------------------------------------------------

## 4. Ověření instalace

    psql --version
    pg_isready

------------------------------------------------------------------------

## 5. Přihlášení

### Windows / macOS

    psql -U postgres

### Linux

    sudo -i -u postgres
    psql

------------------------------------------------------------------------

## 6. Základní pojmy

-   databáze
-   tabulka
-   řádek
-   sloupec
-   uživatel (role)

------------------------------------------------------------------------

## 7. Vytvoření databáze

    CREATE DATABASE moje_databaze;

    \l

------------------------------------------------------------------------

## 8. Vytvoření uživatele

    CREATE USER muj_uzivatel WITH ENCRYPTED PASSWORD 'heslo123';

------------------------------------------------------------------------

## 9. Oprávnění

    GRANT ALL PRIVILEGES ON DATABASE moje_databaze TO muj_uzivatel;

------------------------------------------------------------------------

## 10. Připojení

    psql -h localhost -U muj_uzivatel -d moje_databaze

------------------------------------------------------------------------

## 11. Tabulka

    CREATE TABLE studenti (
        id SERIAL PRIMARY KEY,
        jmeno VARCHAR(100),
        email VARCHAR(255)
    );

------------------------------------------------------------------------

## 12. Vložení dat

    INSERT INTO studenti (jmeno, email)
    VALUES ('Jan Novak', 'jan@example.com');

------------------------------------------------------------------------

## 13. Výpis

    SELECT * FROM studenti;

------------------------------------------------------------------------

## 14. SQL operace

### SELECT

    SELECT * FROM studenti WHERE jmeno = 'Jan Novak';

### UPDATE

    UPDATE studenti
    SET email = 'novy@email.cz'
    WHERE id = 1;

### DELETE

    DELETE FROM studenti WHERE id = 1;

------------------------------------------------------------------------

## 15. Connection string

    postgresql://muj_uzivatel:heslo123@localhost:5432/moje_databaze

------------------------------------------------------------------------

## 16. pgAdmin

-   Host: localhost
-   Port: 5432
-   User: postgres
-   Password: dle instalace

------------------------------------------------------------------------

## 17. Chyby

### psql není rozpoznán

→ PATH problém

### password authentication failed

→ špatné přihlášení

### could not connect to server

→ PostgreSQL neběží

