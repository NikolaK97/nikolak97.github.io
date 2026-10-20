# Georeferencování rastrového souboru

Dostanete obrázek mapy, který „neví“, kde na Zemi leží. Vaším úkolem je mu pomocí kontrolních bodů přiřadit souřadnicový systém tak, aby přesně dosedl na podkladovou mapu areálu VŠB-TUO.

![Zdrojový obrázek mapa_S_JTSK – letecký snímek areálu VŠB-TUO bez souřadnicového systému](/download/mapa_S_JTSK.png)

*Zdrojový obrázek ke stažení: [mapa_S_JTSK.png](/download/mapa_S_JTSK.png). Kontrolní body volte tak, aby pokrývaly celý obrázek – čtyři poblíž rohů a jeden až dva uvnitř.*

## Krok 1 – Načtěte obrázek a ověřte, že nemá souřadnicový systém

1. Stáhněte si soubor [mapa_S_JTSK.png](/download/mapa_S_JTSK.png) a uložte ho do složky projektu.
2. Otevřete ArcGIS Pro a založte nový projekt s mapou (šablona **Map**).
3. V záložce **Map** klikněte na **Add Data** a vyberte soubor `mapa_S_JTSK.png`. Případný dotaz na výpočet pyramid potvrďte **Yes**.
4. Obrázek se přidá do mapy, ale pravděpodobně ho na mapě neuvidíte – leží někde u počátku souřadnic. To je v pořádku.
5. V panelu **Contents** klikněte na vrstvu pravým tlačítkem → **Properties** → záložka **Source** → rozbalte **Spatial Reference**.

> **Co máte vidět:** u položky Spatial Reference je **Unknown** (nebo prázdné pole). Právě to budeme opravovat. Pokud tam už nějaký systém je, máte jiný soubor než zadání – ověřte si název.

## Krok 2 – Zapněte nástroje pro georeferencování

1. V panelu **Contents** klikněte na vrstvu `mapa_S_JTSK.png`, aby byla vybraná.
2. V pásu karet se objeví kontextová záložka **Imagery**. Otevřete ji a klikněte na **Georeference**.
3. Objeví se nová záložka **Georeference** s nástroji. Budete používat hlavně skupiny **Prepare**, **Adjust**, **Review** a **Save**.

> **Záložka Imagery se nezobrazuje?** Nástroje se ukážou jen tehdy, když je v Contents vybraná *rastrová* vrstva. Klikněte na název obrázku ještě jednou.

## Krok 3 – Připravte si referenční vrstvu (podkladovou mapu)

Abyste mohli obrázku říct, kde leží, potřebujete něco, co stejné místo zobrazuje *a zároveň* má definovaný souřadnicový systém. Použijete podkladovou mapu ArcGIS Pro.

1. V záložce **Map** klikněte na **Basemap** a zvolte **World Topographic Map**. Pokud už ji projekt má, krok přeskočte. Lépe se ale klikají body na **World Imagery** – zkuste obě a vyberte si tu, kde snadněji poznáte stejné místo.
2. Do vyhledávacího pole **Locate** (ikona lupy v záložce Map) napište `VŠB-TUO, Ostrava` nebo `17. listopadu 2172, Ostrava-Poruba` a přibližte se tak, aby bylo vidět celý areál – přibližně od ulice Opavská na severu po Hlavní třídu na jihu.
3. V záložce **Georeference** klikněte na **Fit to Display**. Obrázek se natáhne přes aktuální výřez mapy, takže ho konečně uvidíte přes podklad. Zatím není umístěný přesně, jen zhruba.

> **Tip:** posuvníkem **Transparency** (záložka Georeference, skupina Prepare) nastavte obrázku průhlednost kolem 40–50 %. Uvidíte obě vrstvy najednou a snáz najdete společné body.

## Krok 4 – Přidávejte kontrolní body: vždy nejdřív na obrázku, pak na podkladu

Kontrolní bod je dvojice: místo na vašem obrázku a *totéž* místo na referenční mapě. Žádné předpřipravené body k dispozici nejsou, vytvoříte je ručně.

1. Klikněte na **Add Control Points**.
2. **První klik:** na zdrojovém obrázku klikněte přesně na vybrané místo (třeba roh křižovatky). Pro přesnost použijte kolečko myši k přiblížení – kliknutí „někam do okolí“ dělá velké chyby.
3. **Druhý klik:** klikněte na totéž místo na podkladové mapě. Obrázek se po každém bodu přepočítá a posune.
4. Opakujte, dokud nemáte alespoň 4 body, ideálně 6–8.
5. Seznam bodů s jejich chybami zobrazíte tlačítkem **Control Point Table**.

### Jak vybírat dobré body

| Vhodné | Nevhodné |
|---|---|
| Křižovatky a středy kruhových objezdů | Střed louky nebo parkoviště – není co trefit |
| Rohy výrazných budov, které jsou na obou mapách (Aula, rotunda, geologický pavilon) | Stromy, auta, stíny – na podkladu z jiného data tam nejsou |
| Rohy sportovišť (tenisové kurty, fotbalové hřiště) | Textové popisky a ikony – nejsou součástí terénu |
| Konce mostků, lávek, ostré zlomy cest | Body nahuštěné v jednom rohu obrázku |

Návrh rozmístění pro tento obrázek: roh tenisových kurtů (vlevo nahoře), křižovatka 17. listopadu × Opavská (vpravo nahoře), křižovatka Studentská × Dr. Slabihoudka (vlevo dole), kruhový objezd u Hlavní třídy (vpravo dole), kruhová budova a geologický pavilon (uvnitř).

> **Pravidlo rozmístění:** body volte tak, aby **pokrývaly celý obrázek** – jeden poblíž každého rohu a jeden či dva uprostřed. Pokud se body sejdou v jedné části, bude zbytek obrázku zkreslený, i když RMSE vypadá dobře.

**Klikli jste vedle?** Špatný bod odstraňte: v **Control Point Table** řádek vyberte a klikněte na **Delete**. Nebo bod přesuňte nástrojem **Select / Move**. Můžete také vypnout jen jeho zaškrtávátko **On** – bod zůstane, ale do výpočtu se nepočítá.

## Krok 5 – Sledujte chybu RMSE a snižujte ji

Od čtvrtého bodu začne ArcGIS Pro počítat **RMSE** (Root Mean Square Error, střední kvadratická chyba). Udává v mapových jednotkách (zde v metrech), jak moc se v průměru vaše body po transformaci „netrefí“ do míst, kam jste je umístili na podkladu. Vidíte ji ve stavovém řádku a v **Control Point Table**, kde má navíc každý bod svou vlastní odchylku (**Residual**).

### Postup minimalizace

1. Seřaďte tabulku podle sloupce **Residual**. Bod s největší odchylkou je hlavní podezřelý.
2. Přibližte se na něj a posuňte ho nástrojem **Select / Move** tak, aby seděl na obou vrstvách přesně. Pokud nejde opravit, smažte ho a vyberte jiné místo.
3. Přidejte další body v částech obrázku, kde zatím žádné nejsou.
4. Zkontrolujte výsledek vizuálně: použijte **Swipe** nebo přepínejte **Transparency** a dívejte se, zda cesty a budovy sedí všude, ne jen u bodů.

### Typ transformace

Výchozí **1st Order Polynomial (Affine)** umí obrázek posunout, otočit, zvětšit a zkosit. Pro mapu v jednom zobrazení (jako je tato) je to správná volba a nechte ji. Vyšší řády (2nd, 3rd order) RMSE uměle sníží, ale mezi body obrázek „zvlní“ – pro toto cvičení je nepoužívejte, pokud vyučující neřekne jinak.

> **Jaká hodnota je dobrá:** pro mapu areálu v tomto měřítku se snažte dostat **pod přibližně 3–5 m**. Menší hodnota je lepší, ale důležitější je, aby po vizuální kontrole obrázek seděl na podklad po celé ploše. Zapište si konečnou hodnotu RMSE – budete ji odevzdávat.

## Krok 6 – Uložte obrázek s přiřazeným souřadnicovým systémem

1. V záložce **Georeference**, skupina **Save**, klikněte na **Save as New**.
2. Zvolte složku (ideálně složku projektu), název např. `mapa_S_JTSK_georef` a formát **TIFF**. TIFF uloží souřadnicový systém přímo do souboru.
3. Potvrďte. Nový rastr se přidá do mapy.
4. Klikněte na **Close Georeference**.
5. Ověřte výsledek stejně jako v kroku 1: **Properties** → **Source** → **Spatial Reference**. Nyní už tam nesmí být Unknown, ale souřadnicový systém mapy (typicky WGS 1984 Web Mercator, protože ten používá podkladová mapa).

> **Save vs. Save as New:** **Save** pouze zapíše pomocný soubor (`.pgw` / `.aux.xml`) vedle původního PNG – originál zůstane bez změny a pomocný soubor se snadno ztratí. Zadání požaduje **Save as New**, který vytvoří nový, samostatně georeferencovaný soubor.

## Co odevzdat

- [ ] Georeferencovaný rastr `mapa_S_JTSK_georef.tif`
- [ ] Snímek obrazovky s **Control Point Table**, kde je vidět počet bodů a výsledná RMSE
- [ ] Snímek obrazovky s vrstvou nad podkladovou mapou (např. s poloviční průhledností), aby bylo vidět, že obrázek sedí
- [ ] Krátký komentář: kolik bodů jste použili, jaká vyšla RMSE a který bod dělal největší problém

## Časté problémy

**Po přidání obrázku ho nikde nevidím.**
Obrázek bez souřadnic leží u počátku souřadnicového systému mapy. Použijte **Fit to Display** v záložce Georeference (krok 3), nebo na vrstvu klikněte pravým tlačítkem → **Zoom To Layer**.

**Obrázek po přidání bodu „uletěl“ nebo se zdeformoval.**
Nejspíš jste prohodili pořadí kliknutí (nejdřív podklad, pak obrázek) nebo klikli na jiné místo. Poslední bod odstraňte v Control Point Table a přidejte ho znovu – vždy první klik na obrázek, druhý na podklad.

**RMSE klesá, ale obrázek vizuálně nesedí.**
Body jsou pravděpodobně nahuštěné v jedné části. Přidejte body do zbývajících rohů. Pokud máte zapnutý vyšší řád transformace, vraťte se na 1st Order (Affine).

**Nemůžu najít stejné místo na podkladu.**
Přepněte podkladovou mapu na **World Imagery** (letecké snímky) – váš obrázek je také letecký snímek, takže si budou podobnější. Areál se v detailech mění (nové budovy, parkoviště), proto volte dlouhodobě stabilní prvky: křižovatky a hlavní budovy.

## Slovníček pojmů

| Pojem | Význam |
|---|---|
| Georeferencování | Přiřazení souřadnic obrázku, který je původně jen mřížkou pixelů, tak aby ho bylo možné zobrazit na správném místě na mapě. |
| Kontrolní bod (GCP) | Dvojice souřadnic: pozice v pixelech obrázku a odpovídající pozice v souřadnicovém systému referenční vrstvy. |
| Residual | Vzdálenost mezi tím, kam bod po transformaci dopadne, a kam jste ho umístili. Počítá se pro každý bod zvlášť. |
| RMSE | Souhrnná chyba za všechny body – odmocnina z průměru druhých mocnin residuálů. Jedno číslo, které shrnuje kvalitu transformace. |
| Afinní transformace | Transformace 1. řádu: posun, otočení, změna měřítka a zkosení. Potřebuje minimálně 3 body, RMSE se počítá od 4. |
| S-JTSK | Systém jednotné trigonometrické sítě katastrální – český národní souřadnicový systém (EPSG 5514). Název souboru napovídá, v jakém systému byla mapa původně vytvořena. |
