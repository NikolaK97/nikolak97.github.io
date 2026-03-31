# Relace v databázi PostgreSQL (1:1, 1:N, M:N)

## 1. Vytvoření databázové struktury

### 1.1 Tabulka zakaznik

CREATE TABLE zakaznik (
    id SERIAL PRIMARY KEY,
    jmeno VARCHAR(100) NOT NULL
);

### 1.2 Tabulka adresa (vztah 1:1 k zakaznik)

CREATE TABLE adresa (
    id SERIAL PRIMARY KEY,
    ulice VARCHAR(100),
    mesto VARCHAR(100),
    psc VARCHAR(10),
    zakaznik_id INTEGER UNIQUE NOT NULL,
    FOREIGN KEY (zakaznik_id) REFERENCES zakaznik(id) ON DELETE CASCADE
);

### 1.3 Tabulka objednavka (vztah 1:N k zakaznik)

CREATE TABLE objednavka (
    id SERIAL PRIMARY KEY,
    datum DATE NOT NULL,
    zakaznik_id INTEGER NOT NULL,
    FOREIGN KEY (zakaznik_id) REFERENCES zakaznik(id)
);

### 1.4 Tabulka produkt

CREATE TABLE produkt (
    id SERIAL PRIMARY KEY,
    nazev VARCHAR(100),
    cena NUMERIC(10, 2)
);

### 1.5 Tabulka objednavka_produkt (vztah M:N mezi objednavka a produkt)

CREATE TABLE objednavka_produkt (
    objednavka_id INTEGER NOT NULL,
    produkt_id INTEGER NOT NULL,
    mnozstvi INTEGER DEFAULT 1 CHECK (mnozstvi > 0),
    PRIMARY KEY (objednavka_id, produkt_id),
    FOREIGN KEY (objednavka_id) REFERENCES objednavka(id),
    FOREIGN KEY (produkt_id) REFERENCES produkt(id)
);

---

## 2. Vložení testovacích dat

### 2.1 Zákazníci

INSERT INTO zakaznik (jmeno) VALUES
('Anna Hrušková'),
('Petr Mlynář'),
('Eva Novotná');

### 2.2 Adresy (pro 1. a 2. zákazníka)

INSERT INTO adresa (ulice, mesto, psc, zakaznik_id) VALUES
('Hlavní 1', 'Bratislava', '81101', 1),
('Jarní 5', 'Košice', '04001', 2);

### 2.3 Objednávky

INSERT INTO objednavka (datum, zakaznik_id) VALUES
('2024-05-01', 1),
('2024-05-02', 2),
('2024-05-03', 2);

### 2.4 Produkty

INSERT INTO produkt (nazev, cena) VALUES
('Notebook', 899.99),
('Myš', 19.90),
('Monitor', 199.00);

### 2.5 Propojení objednávek a produktů

INSERT INTO objednavka_produkt (objednavka_id, produkt_id, mnozstvi) VALUES
(1, 1, 1),
(1, 2, 2),
(2, 3, 1),
(3, 1, 1),
(3, 2, 1);

---

## 3. Úlohy

### 3.1 Jednoduché SELECT dotazy

1. Zobraz všechny zákazníky  
2. Zobraz zákazníky s adresou (použij JOIN)  
3. Zobraz všechny objednávky zákazníka „Petr Mlynář“  
4. Zobraz všechny produkty, které byly objednány  

### 3.2 Agregační dotazy

1. Kolik objednávek vytvořil každý zákazník  
2. Kolik produktů obsahuje každá objednávka (součet množství)  
73. Která objednávka má celkovou hodnotu větší než 500  

### 3.3 Komplexní dotazy

1. Zobraz názvy produktů, které byly objednány vícekrát (celkové množství > 1)  
2. Zobraz produkty, které mají cenu vyšší než průměrná cena všech produktů  
3. Zobraz jména zákazníků a jejich nejdražší objednané produkty  

---

## 4. Vysvětlení pojmů

- **PRIMARY KEY** – jedinečný identifikátor řádku  
- **FOREIGN KEY** – sloupec odkazující na jinou tabulku (vytváří relaci)  
- **ON DELETE CASCADE** – při smazání rodičovského záznamu se smažou i navázané záznamy  
- **M:N vztah** – vyžaduje pomocnou tabulku s kombinovaným primárním klíčem  
- **CHECK** – podmínka na hodnotu sloupce (např. množství > 0)  
- **NUMERIC(10,2)** – číslo s maximálně 10 číslicemi, z toho 2 desetinná místa  
