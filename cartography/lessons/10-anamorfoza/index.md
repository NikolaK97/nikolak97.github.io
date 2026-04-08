# 10 – Generalizace

# Kartografická generalizace – přehled metod

Tento přehled shrnuje základní metody kartografické generalizace, které slouží ke zjednodušení mapy při zachování její informační hodnoty.

---

## Přehled metod

### (a) Výběr
- odstranění méně důležitých prvků  
- zachování klíčových objektů  
- cílem je snížení přeplněnosti mapy  

---

### (b) Zjednodušení tvaru
- redukce složitosti linií a hranic  
- zachování základního tvaru objektu  
- typicky u řek, hranic, obrysů  

---

### (c) Agregace (seskupení)
- spojování menších ploch do větších celků  
- snížení detailu v plošných jevech  
- zlepšení přehlednosti  

---

### (d) Symbolizace
- převod ploch na bodové nebo liniové znaky  
- prostorová redukce  
- např. budovy → bod  

---

### (e) Výběr podle hustoty
- redukce počtu prvků v závislosti na jejich koncentraci  
- zachování rovnoměrné hustoty mapy  

---

### (f) Zvýraznění
- zesílení nebo zvýraznění důležitých prvků  
- zlepšení čitelnosti (např. hlavní silnice)  

---

### (g) Sloučení (klasifikace)
- spojování kategorií do menšího počtu tříd  
- zevšeobecnění kvalitativních charakteristik  
- změna legendy mapy  

---

### (h) Přeskupení / výběr podle rozmístění
- úprava rozmístění prvků  
- zachování logické struktury prostoru  
- eliminace nadbytečných prvků  

---

### (i) Posunutí
- změna polohy objektů pro lepší čitelnost  
- používá se při kolizích prvků  
- zachovává se relativní poloha  

---

## Shrnutí

Kartografická generalizace je soubor metod, které:
- snižují množství detailu  
- zvyšují přehlednost mapy  
- přizpůsobují obsah měřítku  

Cílem je zachovat podstatné informace při omezeném prostoru mapy.

# CVIČENÍ
## Studijní oblast

Vyberte si vlastní město (doporučeno: město, které dobře znáte).

---

## Vstupní data

Použijte vlastní nebo veřejně dostupná data z ArcGIS Online:

- zařízení (např. nemocnice, školy, hasiči)
- body poptávky (např. obyvatelstvo, sídliště)
- kandidátní lokality (pro nové objekty)

---

## Úkoly

###  Analýza dostupnosti (Drive-Time Areas)

Vytvořte dojezdové oblasti:

- 5 minut  
- 10 minut  
- 15 minut  

Použijte režim:

- **Driving Time**

#### Vyhodnoťte:

- Které oblasti nejsou pokryty do 10 minut  
- Kde se nacházejí mezery v dostupnosti  

---

###  Analýza tras (Find Routes)

Vyberte 3–5 důležitých cílů (např. nemocnice).

Proveďte:

- výpočet nejrychlejší trasy  
- porovnání alternativních tras  

#### Porovnejte:

- čas vs. vzdálenost  
- případný vliv dopravního režimu  

---

### Location-Allocation (Choose Best Facilities)

Použijte nástroj:

- **Choose Best Facilities**

Definujte:

- existující zařízení  
- kandidátní lokality  
- poptávkové body  

#### Úkol:

- Navrhněte umístění **alespoň 1 nového zařízení**  
- Minimalizujte dojezdový čas  


