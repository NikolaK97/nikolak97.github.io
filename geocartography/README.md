# Kartografie pro geology

Jekyll web s materiály ke cvičení z tematické kartografie pro bakaláře geologie.
Struktura vychází z kurzu *Kartografie* (nikolak97.github.io/cartography), obsah je přizpůsoben geologickým datům a konvencím.

## Nasazení na GitHub Pages

1. Vytvořte nové repo, např. `geocartography`, a nahrajte obsah této složky.
2. V `_config.yml` upravte `baseurl` (název repa) a `url` (váš účet).
3. Settings → Pages → Source: *Deploy from a branch*, branch `main`, folder `/ (root)`.
4. Web poběží na `https://UZIVATEL.github.io/geocartography/`.

## Struktura

- `index.md` – rozcestník
- `hodnoceni.md` – podmínky ukončení
- `_lessons/` – 10 lekcí
- `_assignments/` – 3 úkoly + zápočet
- `data.md` – zdroje geologických dat
- `literatura.md` – seznam literatury kurzu
- `arcgis-nastroje.md` – přehled nástrojů ArcGIS Pro použitých v kurzu

Lokální náhled: `bundle install && bundle exec jekyll serve`.
