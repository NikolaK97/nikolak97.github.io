# Cvičení 1 – Úvod do ERA modelování

## Cíl
- entita vs atribut
- kardinalita (1:1, 1:N, M:N)
- parcialita (povinná/volitelná účast)
- řešení M:N pomocí asociativní entity

---

## Teorie (0–25 min)

### Entita
Objekt reality, který evidujeme (Student, Zákazník, Objednávka).

### Atribut
Vlastnost entity (email, cena, datum).

### Primární klíč (PK)
Jednoznačně identifikuje entitu (student_id, order_id).

### Kardinalita
- 1:1
- 1:N
- M:N

### Parcialita
Pomůcka:
- „může“ → volitelné
- „musí/každý“ → povinné

---

## Řízený příklad (25–40 min)

Text:
> Zákazník může vytvořit více objednávek.  
> Každá objednávka patří právě jednomu zákazníkovi.

1) entity: Zákazník, Objednávka  
2) vztah: „vytváří / patří“  
3) kardinalita: 1:N  
4) parcialita: zákazník volitelně, objednávka povinně

---

## Hlavní úloha (40–80 min) – Mini e-shop

Zadání:
1. Zákazník může vytvořit více objednávek.
2. Objednávka obsahuje více produktů.
3. Produkt může být v mnoha objednávkách.
4. Evidujeme množství produktu v objednávce.
5. Zákazník: email, jméno.
6. Produkt: název, cena.

### Postup
1) entity: Zákazník, Objednávka, Produkt  
2) atributy + PK  
3) vztahy  
4) kardinality  
5) řešení M:N (vznik PoložkaObjednávky)  
6) parciality

### Kontrolní checklist
- [ ] každá entita má PK
- [ ] neexistuje přímá M:N Objednávka–Produkt
- [ ] množství je u PoložkaObjednávky
- [ ] kardinality jsou doplněné
- [ ] parcialita je doplněná aspoň u 2 vztahů

---

## Výstup
- ERA diagram (PNG/PDF)
- 3–5 vět: proč vznikla PoložkaObjednávky + příklad kardinality + parciality
