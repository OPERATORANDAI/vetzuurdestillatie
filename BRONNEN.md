# Bronnen — stofdata en correlaties (model 1, openbare versie)

Dit is de bronnenlijst bij **model 1** (en, in hoofdstuk 26, bij het bedieningsmodel, model 2) van de trainingsmodule *Vacuümdestillatie van vetzuren*: elke stofwaarde, wet en
correlatie die het model gebruikt, met **bron** en **geldigheidsbereik**. Het is een uitgedunde kopie van de bronnenlijst van
het project, met **dezelfde hoofdstuknummers**, zodat elke verwijzing in de module ("bronnenlijst §8.12") hier
klopt. Weggelaten is wat alleen bij het bouwen hoort: interne besluiten en verwijzingen naar werkafspraken en tests.
Hoofdstuk 26 hoort bij het bedieningsmodel (model 2): het bevat de paragrafen waar model 2 naar verwijst, en de twee stukken
die model 1 eruit gebruikt (26.29 en 26.34).

**Lees dit als een logboek.** Hoofdstuk 0 tot en met 2 beschrijven de eerste dampspanningsdata; hoofdstuk 23 beschrijft hoe
die door gemeten data zijn vervangen en wat dat met de uitkomsten deed. Waar de twee verschillen, geldt hoofdstuk 23. De
oudere tekst is bewaard omdat de module er zelf naar verwijst, onder meer bij de vervallen stofdata. Zo zijn ook hoofdstuk 4
tot en met 6 plaatshouders uit de eerste opzet gebleven: de dichtheid is nog altijd niet vastgelegd (zie 24, A5), de
afbraakkinetiek staat in hoofdstuk 15 en de pakking in hoofdstuk 14. De oorspronkelijke publicaties van de wetten staan in 7.1.

Regels die overal gelden:

- Geen bedrijfsspecifieke data, geen gegevens van een bestaande installatie. Uitsluitend literatuurwaarden en generieke,
  afgeronde procesgegevens.
- Kan een waarde niet met een bron worden onderbouwd, dan staat dat er expliciet bij en wordt de waarde in de code
  als open punt gemarkeerd zolang er nog een bron gezocht wordt; hier staat zij dan als aanname of als afleiding. Er wordt niets verzonnen.
- Wordt een correlatie buiten haar geldigheidsbereik gebruikt (extrapolatie), dan wordt dat hier én in de code en de UI
  vermeld.
- "De testsuite" is de eigen testsuite van het project. Die is niet openbaar; waar hier staat dat zij iets nagerekend heeft,
  zijn de getallen met de formules in dit bestand na te rekenen.

## Status

Module 1 (stofdata) is ingevuld: molmassa, Antoine-constanten en verdampingswarmte.
Dichtheid en afbraakkinetiek staan nog open (hoofdstuk 4 en 5).

Module 3 (evenwicht) voegt geen nieuwe stofwaarden toe: Raoult, Dalton en de relatieve
vluchtigheid zijn wetten, en alle getallen die eruit komen, komen uit de dampspanningsdata
van hoofdstuk 2. Wat er wel bij komt is een generieke voedingssamenstelling en de aanname
van een ideaal mengsel; zie hoofdstuk 9.

Module 4 (massabalans) voegt ook geen stofwaarden toe: behoud van massa is een behoudswet
en Rachford-Rice is de combinatie daarvan met het evenwicht. Zie hoofdstuk 10.

Module 5 (scheiding) voegt evenmin stofwaarden toe, maar introduceert wel iets nieuws: een
**correlatie zonder natuurwet eronder**. Zie hoofdstuk 11 en 8.10.

Module 6 (energiebalans) voegt wel iets toe: **smeltpunten** (12.4) en het temperatuurniveau
van de **verwarmingsmedia** (12.2). Dat laatste is geen stofeigenschap maar een
procesgegeven, en het blijkt mede te bepalen hoe diep het vacuüm moet zijn (8.12).

---

## 0. Herkomst van de dampspanningsdata — lees dit eerst

De Antoine-set is **gemengd**. Per component staat de herkomst in het veld
`antoine.herkomst` in `components.js`:

| Herkomst | Componenten | Wat het betekent |
|---|---|---|
| `primair` | C16:0, C18:0, C18:1 | Rechtstreeks overgenomen uit NIST WebBook, inclusief het door NIST opgegeven geldigheidsbereik |
| `deels-afgeleid` | C18:2 | Vorm (B, C) geleend van C18:1; alleen A gefit op één gepubliceerd kookpunt |
| `afgeleid` | C12:0, C14:0 | Tweepunts-fit op twee gepubliceerde kookpunten (C = 0) |

**De drie hoofdcomponenten van de kolom — C16:0, C18:0 en C18:1 — staan dus op primaire
data met een gepubliceerd geldigheidsbereik dat het hele werkgebied van de kolom dekt.**
Dat is de belangrijkste verbetering ten opzichte van de eerste versie van dit bestand,
waarin alles nog was afgeleid.

Voor C12:0 en C14:0 bestaat wél primaire data, maar die is hier bewust **niet** overgenomen.
De reden staat met cijfers in hoofdstuk 2.5.

---

## 1. Componenten en molmassa

Molmassa berekend uit de brutoformule met de conventionele IUPAC standaard
atoomgewichten (2021): C = 12,011; H = 1,008; O = 15,999 g/mol.
Deze berekening is exact reproduceerbaar; de testsuite controleert elke waarde
opnieuw tegen de formule.

| Component | Notatie | Formule | M (g/mol) | Bron | Opmerking |
|---|---|---|---|---|---|
| Laurinezuur | C12:0 | C12H24O2 | 200,322 | IUPAC 2021, uit formule | kleine fractie bij palmvoeding |
| Myristinezuur | C14:0 | C14H28O2 | 228,376 | IUPAC 2021, uit formule | lichte fractie in deze module |
| Palmitinezuur | C16:0 | C16H32O2 | 256,430 | IUPAC 2021, uit formule | hoofdproduct |
| Stearinezuur | C18:0 | C18H36O2 | 284,484 | IUPAC 2021, uit formule | hoofdproduct |
| Oliezuur | C18:1 | C18H34O2 | 282,468 | IUPAC 2021, uit formule | |
| Linolzuur | C18:2 | C18H32O2 | 280,452 | IUPAC 2021, uit formule | |
| Zwaar residu (pitch) | pseudo | C51H98O6 | 807,339 | benadering | **pseudo-component**, zie 1.1 |

### 1.1 Pseudo-component "zwaar residu"

Pitch is geen zuivere stof maar een verzamelpost: niet-gesplitst vet (tri-, di- en
monoglyceriden), polymeren en zware afbraakproducten. De samenstelling verschilt per
voeding en per bedrijfsvoering, dus er bestaat geen stofdata voor.

Gekozen benadering: de molmassa van **tripalmitine** (C51H98O6, 807,3 g/mol), het
triglyceride dat bij een palmgebaseerde voeding domineert. Dit is een representatieve
orde van grootte, **geen gemeten eigenschap van pitch**.

De component wordt behandeld als **niet-vluchtig**: dampspanning verwaarloosbaar bij
kolomcondities, dus y = 0 in het evenwicht. Dat is fysisch verdedigbaar — triglyceriden
hebben bij 10–30 mbar en 250 °C een dampspanning die vele ordes lager ligt dan die van
de vrije vetzuren — maar het is een **aanname**, niet met een meting onderbouwd.
Verdampingswarmte en dichtheid zijn daarom bewust op `null` gelaten.

---

## 2. Dampspanning

### 2.0 Vorm van de vergelijking

```
log10( p_sat [Pa] ) = A − B / ( T [K] + C )
```

met A dimensieloos, B in K en C in K.

Deze vorm is expliciet vastgelegd omdat bronnen sterk uiteenlopende conventies hanteren
(log10 vs ln; mmHg, kPa of bar; °C of K). Overnemen van constanten uit een andere bron
zonder omrekening geeft fouten van ordes van grootte.

> **Let op bij het overnemen uit NIST.** NIST WebBook geeft A voor **p in bar**.
> De omrekening naar Pa is `A_Pa = A_bar + 5` (want log10(10⁵) = 5); B en C veranderen
> niet. In `components.js` is dit al gedaan; de originele bar-waarde staat bewaard in
> `antoine.A_origineel_bar`, en de testsuite controleert de omrekening.

### 2.1 Overgenomen primaire sets (C16:0, C18:0, C18:1)

Bron: **NIST Chemistry WebBook**, rubriek *Phase change data*, Antoine-parameters.
Onderliggende referentie voor alle drie: **Stull, D.R. (1947)**, *Vapor Pressure of Pure
Substances. Organic Compounds*, Ind. Eng. Chem. 39. Voor C18:0 vermeldt NIST dat de
coëfficiënten door NIST zijn herberekend uit de data van de auteur.

| Component | A (bar) | A (Pa) | B (K) | C (K) | Geldig T (K) | Geldig T (°C) | Geldig p (mbar) |
|---|---|---|---|---|---|---|---|
| C16:0 | 5,35728 | 10,35728 | 3061,422 | −55,077 | 426,8 – 626,9 | 154 – 354 | 1,3 – 1008 |
| C18:0 | 5,72544 | 10,72544 | 3348,131 | −57,825 | 446,9 – 643,0 | 174 – 370 | 1,3 – 1009 |
| C18:1 | 5,04842 | 10,04842 | 2555,604 | −127,258 | 449,7 – 633,0 | 177 – 360 | 1,3 – 989 |

De drukgrenzen zijn niet uit de bron overgenomen maar berekend door de correlatie op
T_min en T_max te evalueren. Ze staan erbij omdat een operator in mbar denkt, niet in K.

**Het werkgebied van de kolom (10–30 mbar, 200–260 °C) valt hier volledig binnen.**

### 2.2 Afgeleide fits (C12:0, C14:0)

Voor deze twee is de correlatie afgeleid uit twee gepubliceerde kookpunten, met C = 0.
Daarmee is de vergelijking wiskundig identiek aan de geïntegreerde
Clausius-Clapeyron-vergelijking met een constante verdampingswarmte.

Uit twee punten (T₁, p₁) en (T₂, p₂) volgt:

```
B = ( log10(p₂) − log10(p₁) ) / ( 1/T₁ − 1/T₂ )
A = log10(p₁) + B / T₁
```

Gebruikte kookpunten (handboekwaarden; zie de waarschuwing onder 2.4):

| Component | Punt 1 | Punt 2 |
|---|---|---|
| C12:0 | 225,0 °C bij 100 mmHg (133 mbar) | 298,9 °C bij 760 mmHg |
| C14:0 | 250,5 °C bij 100 mmHg (133 mbar) | 326,2 °C bij 760 mmHg |

Resultaat:

| Component | A (Pa) | B (K) | C (K) | Geldig T (K) | Geldig T (°C) | Geldig p (mbar) |
|---|---|---|---|---|---|---|
| C12:0 | 10,9430 | 3396,4 | 0 | 498,15 – 572,05 | 225 – 299 | 133 – 1013 |
| C14:0 | 11,0984 | 3651,7 | 0 | 523,65 – 599,35 | 250 – 326 | 133 – 1013 |

Het punt van C14:0 bij 100 mmHg wordt bevestigd door NIST (523,2 K bij 0,133 bar, naar
Weast en Grasselli 1989); dat van C12:0 eveneens (498,2 K bij 0,133 bar, naar Aldrich 1990).

### 2.3 Deels afgeleid: C18:2 (linolzuur)

**NIST WebBook heeft geen Antoine-parameters voor linolzuur.** Er is één gepubliceerd
kookpunt: 502,7 K bij 0,021 bar (NIST WebBook, naar Weast en Grasselli 1989).

Met één punt kun je geen drie constanten fitten. Daarom:

- **B en C zijn overgenomen van C18:1.** Dit is een **aanname**: de vorm van de
  dampspanningskromme van linolzuur wordt gelijk verondersteld aan die van oliezuur.
  Fysisch verdedigbaar (zelfde ketenlengte, één dubbele binding verschil), maar geen meting.
- **Alleen A is gefit** op het ene meetpunt: A = 10,12914 (Pa).
- Geldigheidsbereik daarom smal: 490 – 520 K, oftewel circa 13 – 40 mbar.
- **Gevolg van de geleende vorm:** α(C18:2 / C18:1) is per constructie **constant** op
  1,20 en kan géén temperatuurafhankelijkheid weergeven. Dat is een artefact van de
  methode, geen eigenschap van de stof.
- Er is **geen normaal kookpunt** ingevuld. Extrapolatie naar 1013 mbar ligt ver buiten
  het fitbereik en zou schijnnauwkeurigheid suggereren.

Deze component is bruikbaar om te laten zien *dat* onverzadigde vetzuren in het
destillaat meelopen, maar niet om die scheiding te kwantificeren.

### 2.4 Validatie tegen onafhankelijk gepubliceerde kookpunten

Elke correlatie is getoetst aan gepubliceerde kookpunten. De tolerantie per punt staat in
`components.js` (`validatiepunten[].maxAfwijking_K`) en wordt in de testsuite afgedwongen.

| Component | Druk | Voorspeld | Gepubliceerd | Verschil | Tolerantie | Bron van het punt |
|---|---|---|---|---|---|---|
| C12:0 | 100 mmHg | 225,0 °C | 225,0 °C | 0,0 K | 0,5 K | fitanker |
| C12:0 | 760 mmHg | 298,9 °C | 298,9 °C | 0,0 K | 0,5 K | fitanker |
| C14:0 | 100 mmHg | 250,5 °C | 250,5 °C | 0,0 K | 0,5 K | fitanker |
| C14:0 | 760 mmHg | 326,2 °C | 326,2 °C | 0,0 K | 0,5 K | fitanker |
| **C16:0** | **15 mmHg** | **215,8 °C** | **215,0 °C** | **0,8 K** | 2 K | handboekwaarde |
| C16:0 | 0,133 bar | 273,1 °C | 271,6 °C | 1,5 K | 3 K | NIST, naar Aldrich 1990 |
| C16:0 | 760 mmHg | 354,0 °C | 351,5 °C | 2,5 K | 5 K | handboekwaarde |
| C18:0 | 15 mmHg | 235,6 °C | 232,0 °C | 3,6 K | 5 K | handboekwaarde |
| C18:0 | 760 mmHg | 370,0 °C | 361,0 °C | 9,0 K | 12 K | handboekwaarde |
| **C18:1** | **10 mmHg** | **223,2 °C** | **223,0 °C** | **0,2 K** | 2 K | handboekwaarde |
| C18:1 | 760 mmHg | 360,9 °C | 360,0 °C | 0,9 K | 3 K | handboekwaarde |
| C18:1 | 0,001 bar | 171,6 °C | 194,6 °C | 22,9 K | — | uitbijter, zie 2.7 |
| C18:2 | 0,021 bar | 229,6 °C | 229,6 °C | 0,0 K | 0,5 K | fitanker |

De twee vetgedrukte regels zijn de sterkste controle in dit bestand: een correlatie uit
Stull (1947) en een handboekkookpunt uit een heel andere bron vallen bij **kolomdruk**
samen binnen 1 K. Twee onafhankelijke bronnen die elkaar daar bevestigen, geven
vertrouwen in het deel van de kromme dat deze module daadwerkelijk gebruikt.

Twee kanttekeningen bij de toleranties:

- De ruime marge bij **C18:0 bij 760 mmHg (12 K)** is niet gekozen om de test te laten
  slagen, maar weerspiegelt de werkelijke spreiding in de bronnen: voor het normale
  kookpunt van stearinezuur circuleren 358 °C, 361 °C en 383 °C, en NIST noemt daarnaast
  631,15 K (358 °C) met een door TRC toegekende onzekerheid van 5 K. De gebruikte
  tolerantie is **smaller** dan die spreiding. Vetzuren ontleden bij 1 atm vóór of tijdens
  het koken, dus het normale kookpunt is intrinsiek slecht bepaald. Dat is precies de
  reden dat deze kolom onder vacuüm draait.
- De **fitankers zijn geen onafhankelijke validatie.** Dat ze exact reproduceren is
  triviaal. Ze staan in de tabel omdat de test dan ook de fit zelf bewaakt, maar ze
  bewijzen niets over de juistheid van de data. In `components.js` zijn ze gemarkeerd met
  `fitAnker: true`.

**Waarschuwing over de handboekwaarden.** De kookpunten die hierboven als
"handboekwaarde" staan, zijn de waarden zoals die in handboeken en stofdatabladen voor
deze vetzuren worden gegeven (CRC Handbook of Chemistry and Physics; Merck Index;
leveranciersspecificaties). Ze zijn onderling consistent en worden op de punten waar NIST
eigen data heeft door NIST bevestigd, maar er is **geen editie- en paginanummer
geverifieerd**. Controleer ze tegen een fysiek exemplaar voordat de module in gebruik gaat.

### 2.5 Waarom de primaire sets van C12:0 en C14:0 niet zijn overgenomen

NIST WebBook heeft voor beide wél Antoine-parameters, naar **Hammer en Lydersen (1957)**:

| Component | A (bar) | B (K) | C (K) | Geldig T (K) | Geldig T (°C) | Geldig p (mbar) |
|---|---|---|---|---|---|---|
| C12:0 | 5,55523 | 2510,999 | −106,676 | 379,52 – 449,00 | 106 – 176 | 0,2 – 17 |
| C14:0 | 1,44788 | 685,976 | −262,3 | 385,03 – 465,1 | 112 – 192 | 0,07 – 12 |

Deze sets zijn **niet** overgenomen. De redenen, met cijfers:

1. **Het geldigheidsbereik ligt te laag.** Beide stoppen bij 176 respectievelijk 192 °C.
   De kolom werkt tot 260 °C. Gebruik in de module zou voor deze componenten bijna
   overal extrapolatie betekenen, en juist die extrapolatie gaat mis (punt 2).
2. **De C14:0-set extrapoleert aantoonbaar slecht.** Doorgetrokken naar 523,2 K
   voorspelt hij 66 mbar, terwijl NIST op dezelfde pagina een gemeten kookpunt van
   523,2 K bij **133 mbar** vermeldt. Een factor 2 mis. De parameters (B = 686 K,
   C = −262,3 K) zijn over een interval van 80 K gefit en zijn sterk gecorreleerd;
   buiten dat interval is de vorm niet te vertrouwen.
3. **De C14:0-set is onderling strijdig met de aangenomen C16:0-set.** Bij 465,1 K, waar
   beide geldig zijn, geeft NIST α(C14:0 / C16:0) = 1,49. Verwacht op grond van de
   praktijk in de oleochemie is 2,0 – 2,3 per C2-stap, en bij een lágere temperatuur
   hoort α juist hóger te liggen, niet lager. De afgeleide fit geeft daar 2,27.
4. **De afgeleide fits passen wél in de reeks.** Bij 498,15 K geven zij
   α(C12:0 / C14:0) = 2,27 en α(C14:0 / C16:0) = 2,09, met C16:0 uit de primaire set.
   Dat is de verwachte ~2 per C2-stap.

Voor **C12:0** is het beeld milder: de NIST-set extrapoleert naar 498,15 K met 139 mbar
tegen 133 mbar gemeten (4 % verschil). Beide sets zijn daar dus in orde; ze lopen pas
boven 500 K uiteen. De set is niettemin niet overgenomen, om dezelfde reden als bij
C14:0 (bereik te laag) en om C12:0 en C14:0 op dezelfde manier te behandelen.

**Conclusie:** voor C12:0 en C14:0 bestaat er geen correlatie die over het héle werkgebied
van de kolom is gevalideerd. De gekozen fits dekken het bovenste deel (133–1013 mbar) en
worden bij kolomdruk geëxtrapoleerd; de NIST-sets dekken het onderste deel (0,1–17 mbar)
maar niet de temperaturen waar de kolom werkt. Dit is een open punt, geen opgelost punt.

### 2.6 C18:0 tegenover C18:1 — richting en omslagpunt

Met de primaire sets is **oliezuur iets vluchtiger dan stearinezuur**:
α(C18:1 / C18:0) = **1,13 bij 240 °C**. Dat is de richting die de literatuur aangeeft en
die ook uit de structuur volgt: de cis-dubbele binding in C18:1 knikt de keten, waardoor
de moleculen minder goed stapelen. Hetzelfde effect is te zien in het smeltpunt —
69 °C voor C18:0 tegen 13 °C voor C18:1.

*(In de eerste versie van deze module kwam deze verhouding andersom uit. Die fit was
afgeleid uit twee losse handboekkookpunten bij verschillende drukken en was daarvoor niet
nauwkeurig genoeg. De primaire data lost dit op, zonder dat er iets met de hand is
bijgesteld.)*

**Bekende beperking: de twee correlaties kruisen elkaar bij 202,1 °C.**

| T (°C) | 170 | 190 | 200 | **202,1** | 210 | 230 | 240 | 260 | 300 |
|---|---|---|---|---|---|---|---|---|---|
| α(C18:1/C18:0) | 0,84 | 0,94 | 0,99 | **1,00** | 1,03 | 1,10 | 1,13 | 1,18 | 1,23 |

Beneden 202,1 °C geeft de data stearinezuur als de vluchtigste — in strijd met de
literatuur. Dit is vrijwel zeker een **fit-artefact**: beide sets komen uit dezelfde
compilatie maar zijn onafhankelijk van elkaar gefit, met sterk verschillende C
(−57,8 tegen −127,3 K) en dus verschillende kromming.

**Waarom dit de module niet raakt:** bij 202 °C is de dampspanning van beide stoffen nog
maar 5,1 mbar, ruim onder de laagste kolomdruk van 10 mbar. Daar zijn ze in de praktijk
gewoon vloeistof; α is er geen sturende grootheid. In het gebied waar C18 daadwerkelijk
verdampt (boven circa 230 °C) klopt de richting wel.

**Regel:** gebruik α(C18:1 / C18:0) niet beneden 210 °C. Dit staat vastgelegd in
`NIET_SCHEIDBAAR_C18.beperking` in `components.js`, en de testsuite toetst zowel de
richting boven 210 °C als de ligging van het omslagpunt zelf, zodat het niet ongemerkt
kan verschuiven.

### 2.7 Uitbijter: C18:1 bij 0,001 bar

NIST vermeldt voor oliezuur een kookpunt van 467,7 K bij 0,001 bar (naar Aldrich 1990).
De Antoine-set van NIST zelf voorspelt daar 444,8 K — **22,9 K verschil**, en bovendien
onder de ondergrens van het geldigheidsbereik (449,7 K).

Het punt is dus in strijd met de correlatie op dezelfde NIST-pagina. Omdat de correlatie
bij 13 mbar wél tot op 0,2 K met een onafhankelijke handboekwaarde overeenkomt (2.4),
wordt het Aldrich-punt als uitbijter beschouwd. Het staat in `components.js` met
`buitenBereik: true`: het wordt in de testuitvoer gerapporteerd, maar niet getoetst.

### 2.8 Extrapolatie bij kolomdruk

Kooktemperatuur bij 20 mbar, met de vermelding of dat binnen het geldigheidsbereik valt:

| Component | T_kook bij 20 mbar | Binnen bereik? | Herkomst |
|---|---|---|---|
| C12:0 | 179,5 °C | binnen | primair-meting |
| C14:0 | 200,5 °C | binnen | primair-meting |
| C16:0 | 219,0 °C | binnen | primair-meting |
| C18:0 | 238,1 °C | binnen | primair-meting |
| C18:1 | 233,5 °C | binnen | primair-meting |
| C18:2 | 228,3 °C | binnen | deels-afgeleid |

**Sinds 24 september 2026 staat er geen enkele extrapolatie meer in deze tabel.** Dat is de
hele reden voor de overstap naar de gemeten Antoine-sets van hoofdstuk 23. Daarvoor stonden
C12:0 en C14:0 er wel buiten - C14:0 zelfs 35 K onder zijn venster - en waren de kookpunten
171,3 en 195,2 °C in plaats van 179,5 en 200,5.

Let op één nuance die met de smallere vensters is meegekomen. Elk gemeten venster ligt rond
het EIGEN kookgebied van zijn stof. Bij het bubbelpunt van het MENGSEL (224,7 °C) worden alle
componenten op dezelfde temperatuur bekeken, en daar staat C18:0 nog net onder zijn venster:
4,8 mol% van het mengsel. Voorheen was dat C14:0 met 1,2 mol%. Er wordt dus minder VER
geëxtrapoleerd dan vroeger, maar over een groter deel van het mengsel.

`isBinnenAntoineBereik()` in `components.js` signaleert dit; de testsuite controleert
dat de signalering werkt. **De UI moet deze melding tonen zodra er buiten bereik wordt
gerekend.**

---

## 3. Verdampingswarmte

Niet onafhankelijk gemeten, maar **afgeleid uit de Antoine-constanten** via
Clausius-Clapeyron:

```
d(ln p) / dT = ΔH_vap / (R · T²)        (Clausius-Clapeyron)
log10(p) = A − B / (T + C)              (Antoine)

  ⇒  ΔH_vap = R · ln(10) · B · T² / (T + C)²
```

Voor C = 0 valt dit terug op ΔH_vap = B · R · ln(10), onafhankelijk van de temperatuur.
De afleiding staat in `dHvapUitAntoine()` in `components.js`.

Waarden bij **240 °C (513,15 K)**, de gekozen referentietemperatuur (`DHVAP_REF_T_K`):

| Component | ΔH_vap (kJ/mol) | T-afhankelijk? | Geldig over (°C) | Bron |
|---|---|---|---|---|
| C12:0 | 65,0 | nee (C = 0) | 225 – 299 | afgeleid uit B |
| C14:0 | 69,9 | nee (C = 0) | 250 – 326 | afgeleid uit B |
| C16:0 | 73,6 | ja | 154 – 354 | afgeleid uit primaire set |
| C18:0 | 81,4 | ja | 174 – 370 | afgeleid uit primaire set |
| C18:1 | 86,5 | ja | 177 – 360 | afgeleid uit primaire set |
| C18:2 | 86,5 | ja | 217 – 247 | **aanname**, vorm van C18:1 |
| Zwaar residu | — | — | — | niet van toepassing: pitch verdampt in dit model niet, een aanname van het model. Ter orde, voor het triglyceridedeel: tripalmitine 160,8 kJ/mol bij 521 K (NIST Chemistry WebBook, SRD 69, uit R. M. Stephenson en S. Malanowski, *Handbook of the Thermodynamics of Organic Compounds*, Springer, 1987; de NIST-pagina ingezien op 19 november 2026) |

De reeks stijgt monotoon met de ketenlengte (65,0 → 69,9 → 73,6 → 81,4 kJ/mol), zoals het
hoort: een langere keten heeft meer vanderwaalscontact en kost meer energie om los te
maken. De testsuite toetst deze monotonie.

**Beperking.** Bij de componenten met C ≠ 0 is ΔH_vap temperatuurafhankelijk en neemt hij
af naarmate T stijgt — fysisch juist, want in het kritisch punt gaat hij naar nul. Bij
C12:0 en C14:0 is ΔH_vap door de vorm van de fit **constant**; dat is een vereenvoudiging.
Voor de reboiler- en condensorduty in module 6 is dit een bruikbare benadering. Wie meer
nauwkeurigheid nodig heeft, moet een aparte correlatie gebruiken (bijvoorbeeld Watson).

> **Verouderd (tweede doorloop, 26 september 2026).** De tabel en de beperking hierboven
> horen bij de vervallen Stull-sets. Met de gemeten sets van hoofdstuk 23 gelden andere
> waarden, en voor twee componenten klopt de beperking niet meer: zie 3.1. Niet
> bijgewerkt, want de oorzaak moet eerst worden opgelost.

### 3.1 De verdampingswarmte stijgt voor C18:1 en C18:2 — een modelfout

**Wat er gevonden is.** Bij de tweede doorloop draaide de energiebalans om: de stripsectie
kreeg meer damp dan de rectificatiesectie in plaats van minder. De oorzaak zat niet in de
balans maar in de verdampingswarmte die erin gaat. De testsuite toetst nu wat
hier al had moeten staan: een hogere temperatuur geeft een lagere verdampingswarmte, voor
elke component, over het bedrijfsbereik. Die test valt voor C18:1 en C18:2.

| Component | Antoine C (K) | ΔH_vap bij 200 / 220 / 240 / 260 °C (kJ/mol) | Meetbereik (°C) |
|---|---|---|---|
| C12:0 | −234,0 | 75,7 / 70,1 / 65,4 / 61,5 | 171,5 – 217,4 |
| C14:0 | −173,0 | 83,5 / 79,8 / 76,5 / 73,7 | 191,5 – 239,5 |
| C16:0 | −250,5 | 96,0 / 87,8 / 81,2 / 75,7 | 210,2 – 260,3 |
| C18:0 | −13,0 | 86,8 / 86,6 / 86,4 / 86,3 | 228,2 – 280,1 |
| **C18:1** | **+51,1** | **83,9 / 84,6 / 85,2 / 85,8 — stijgt** | 223,6 – 274,3 |
| **C18:2** | **+51,1** (geleend) | **idem — stijgt** | idem |

**De oorzaak.** De afleiding ΔH = R·ln(10)·B·T²/(T+C)² heeft een afgeleide naar T met het
teken van C: 2·T·C/(T+C)³. Een Antoine-set met een **positieve C** geeft dus altijd een
stijgende verdampingswarmte, hoe goed hij de dampspanning ook beschrijft. De gemeten
C18:1-set van Falleiro et al. 2012 heeft C = +51,1 K.

Van de twee verdachten die bij de doorloop zijn genoemd:

- **C18:2 leent B en C van C18:1.** Klopt, maar het is een gevolg, niet de bron: C18:2
  erft de fout. De bron is de C18:1-set zelf.
- **De fits worden ver buiten hun bereik gebruikt.** Niet de oorzaak voor C18:1. Het
  bedrijfsbereik (ongeveer 213 tot 255 °C) valt grotendeels binnen het meetbereik van de set,
  en ΔH stijgt ook daarbinnen.

**Het onderliggende probleem is breder dan C18:1.** Een Antoine-fit met drie parameters op
zeven punten over zo'n 50 K legt de dampspanning goed vast, maar de KROMMING (C) nauwelijks.
De verdampingswarmte is de afgeleide van de dampspanning, en hoe die met T verloopt hangt
helemaal aan C. Ook C16:0 is daarom verdacht: C = −250,5 K laat ΔH over 60 K met ongeveer
0,34 kJ/mol per kelvin dalen, en dat is ruwweg drie keer steiler dan voor stoffen van dit
type gebruikelijk is. De dampspanningen zelf zijn niet in het geding — die zijn gemeten en
gevalideerd — alleen de afgeleide.

**Wat het raakt.** Alles wat ΔH bij een andere temperatuur dan de referentie gebruikt: de
energiebalans (top en bodem op hun eigen temperatuur), en daarmee de richting van het verschil
tussen de dampstromen boven en onder de voeding. De uitlegtekst daarover wordt pas herschreven
als dit is opgelost.

**Mogelijke richtingen, nog niet gekozen:**

1. ΔH per component op één referentietemperatuur vastzetten en niet met T laten variëren.
   Eenvoudig en consistent, maar zonder temperatuurafhankelijkheid.
2. ΔH als koorde over het meetbereik: `dHvapUitTweePunten_Jmol()` in `vapor.js` op de
   eindpunten van de meting. Robuust, want het leunt op twee gemeten dampspanningen in plaats
   van op C.
3. Een correlatie met een fysische vorm (Watson), die monotoon daalt naar nul bij het
   kritische punt. Vraagt kritische temperaturen, en daar is nog geen bron voor.

**Gekozen: richting 2, de koorde** (tweede doorloop). Reden: drie
Antoine-parameters op zeven punten leggen de dampspanning goed vast maar de kromming nauwelijks,
en ΔH hangt juist aan die kromming. Een koorde gebruikt alleen wat de meting werkelijk vastlegt.

- `dHvapKoorde_Jmol()` in `components.js`: ΔH = R · ln(p₂/p₁) / (1/T₁ − 1/T₂) door het laagste
  en het hoogste meetpunt van de eigen reeks van Falleiro et al. 2012 (1333 en 9333 Pa).
- C18:2 heeft geen eigen reeks (één ankerpunt) en **leent** de eindpunten van C18:1
  (`koordeGeleendVan: 'C18_1'`) — dezelfde aanname als de geleende B en C.
- `verdampingswarmteMengsel()` in `energy.js` gebruikt de koorde; `dHvapUitAntoine()` blijft
  bestaan als afleiding, maar gaat de energiebalans niet meer in.
- De testsuite toetst twee dingen, omdat een van beide alleen een lege huls is:
  de verdampingswarmte van het model stijgt nergens over het bedrijfsbereik, én zij IS de
  koorde door de gemeten eindpunten (onafhankelijk nagerekend), niet de Antoine-afgeleide.

| Component | ΔH, koorde (kJ/mol) | Was (Antoine-afgeleide bij 240 °C) |
|---|---|---|
| C12:0 | 76,9 | 65,4 |
| C14:0 | 80,3 | 76,5 |
| C16:0 | 83,3 | 81,2 |
| C18:0 | 86,5 | 86,4 |
| C18:1 | 86,8 | 85,2 |
| C18:2 | 86,8 (geleend) | 85,2 |

De reeks stijgt nu weer monotoon met de ketenlengte, zoals hoort.

**Wat de koorde opgeeft: de temperatuurafhankelijkheid van ΔH binnen het bedrijfsbereik.** De
koorde is per component één getal, een gemiddelde over het meetbereik.

Hoe groot is wat er wordt opgegeven? **De meting zelf kan dat niet zeggen.** Halve koorden
(1333→4000 en 4000→9333 Pa) geven voor C16:0 87,5 en 78,4 kJ/mol, en voor C18:1 85,0 en 89,1 —
bij C18:1 zelfs stijgend. Die spreiding is meetruis van de DSC-reeks en geen fysica; ze is groter
dan het effect dat ze zou moeten tonen. Precies daarom is de koorde, over de hele reeks, de
betrouwbaarste uitspraak die deze data toelaten.

Een fysische ordegrootte, **zonder bron**: de Watson-vorm met de gebruikelijke exponent 0,38 en
een kritische temperatuur rond 750 tot 800 K geeft ongeveer −0,1 kJ/mol per kelvin. Over
213–255 °C is dat zo'n 4 tot 5 kJ/mol, ongeveer vijf procent — niet verwaarloosbaar. Tussen de
top (218 °C) en de bodem (233 °C) van de kolom scheelt het zo'n 1,5 kJ/mol, ongeveer twee procent.

**Wat blijft staan: de verschillen TUSSEN de componenten**, en daar hangt de energiebalans aan.
C16:0 (83,3) tegenover C18 (86,5 tot 86,8) is vier procent, en dat verschil ligt vast in de
meting: elke koorde komt uit dezelfde meetmethode over hetzelfde drukbereik. De **richting** van
het verschil in dampstromen (de stripsectie krijgt minder damp dan de top) houdt daarom stand.
De **grootte** (4 procent) is met de weggelaten temperatuurafhankelijkheid onzeker, en kan
ruwweg de helft kleiner zijn. De uitlegtekst van `WET_ENERGIEBALANS` zegt dat erbij.

Status: **opgelost met de koorde**; de temperatuurafhankelijkheid staat open voor v1.2 (vraagt
kritische temperaturen met bron, of een dampspanningsmeting met minder spreiding).

---

## 4. Vloeistofdichtheid

**Vastgelegd in de ronde van de dichtheid** (22 november 2026; opdracht van de gebruiker). Een lijn voor
de vloeistofdichtheid van het vetzuurmengsel als functie van de temperatuur, gefit op metingen. Code: `DICHTHEID_VETZUREN` en
`dichtheidVetzuren_kgm3()` in `components.js`.

### 4.1 De bron

X. Wang, T. Sun en A. S. Teja, "Density, Viscosity, and Thermal Conductivity of Eight Carboxylic Acids from (290.3 to 473.4) K",
*J. Chem. Eng. Data* 61 (8) (2016) 2651–2658, doi 10.1021/acs.jced.5b00971 (Crossref gecontroleerd). De meetpunten zijn ingezien in
het openbare ThermoML-bestand bij NIST TRC (https://trc.nist.gov/ThermoML/10.1021/acs.jced.5b00971.xml): pyknometer, 101,325 kPa,
uitgebreide onzekerheid 2 tot 2,1 kg/m³ per punt (95 %). De samenvatting van het artikel noemt 0,2 kg/m³; het model rekent met de 2,1
uit de meetpunten.

Tetradecaanzuur (C14:0), alle negen meetpunten:

| T (K) | 332,1 | 352,1 | 368,2 | 388,2 | 408,2 | 427,9 | 443,2 | 458,2 | 473,2 |
|---|---|---|---|---|---|---|---|---|---|
| ρ (kg/m³) | 861,3 | 846,9 | 835,6 | 821,4 | 806,7 | 791,7 | 780,9 | 769,3 | 758,0 |

Dodecaanzuur (C12:0) is in hetzelfde bestand gemeten van 322,1 tot 467,2 K (872,4 tot 762,0 kg/m³); C16 tot C18 staan er niet in.

### 4.2 De vorm

Lineair in T, een eigen kleinste-kwadratenfit op de negen punten van C14:0:

ρ = 1104,87 − 0,73181 · T   (ρ in kg/m³, T in K)

- De grootste afwijking van een meetpunt is 0,62 kg/m³, binnen de meetonzekerheid. Een kwadratische fit verandert binnen het
  meetbereik minder dan 0,6 kg/m³: de lineaire vorm past bij de metingen.
- **Soort:** een correlatie, geen natuurwet: een lijn door metingen, zonder theorie eronder.
- **Een lijn voor het mengsel, niet per component.** C14:0 is de langste keten die tot 200 °C gemeten is. C16 tot C18 zijn bij die
  temperatuur niet gemeten gevonden. Vlak boven hun smeltpunt liggen ze binnen ongeveer een procent van deze lijn: palmitinezuur 852,7
  kg/m³ bij 62 °C en stearinezuur 847 kg/m³ bij 70 °C, tegen 859,6 en 853,8 op de lijn (−0,8 %), en oliezuur 895 kg/m³ bij 25 °C (+1 %).
  Die drie waarden zijn secundair (handboekwaarden via een overzicht; het handboek zelf is niet ingezien). Tussen C12:0 en C14:0, beide
  gemeten, krimpt het verschil met de temperatuur: 3,6 kg/m³ bij 60 °C, 0,7 bij 200 °C. Daarom hebben de componenten in
  `components.js` geen eigen `rho_kgm3`.

### 4.3 Het meetbereik en de extrapolatie

**Gemeten van 58,95 tot 200,05 °C (332,1 tot 473,2 K).** In het ontwerppunt rekent het model de dichtheid op vier plekken, elk bij de
temperatuur van die vloeistof, en houdt hem daar vast (ook in een overgang: het niveau is in het model een massafractie, en een
dichtheid die meeloopt zou het niveau laten bewegen zonder dat er massa bijkomt):

| Plek | Temperatuur | Boven het meetbereik | ρ |
|---|---|---|---|
| bodem van K-01 | 237,8 °C | 37,8 K | 730,9 kg/m³ |
| bodem van K-02 | 250,1 °C | 50,0 K | 722,0 kg/m³ |
| refluxvat V-02 (de toptemperatuur van K-02) | 218,3 °C | 18,3 K | 745,2 kg/m³ |
| bed van K-02 (het midden tussen top en bodem) | 234,2 °C | 34,2 K | 733,6 kg/m³ |

Alle vier zijn dus **extrapolatie**, 18 tot 50 K boven het hoogste meetpunt.

### 4.4 De onzekerheid bij 240 tot 250 °C

Een schatting, ongeveer **±1,5 %** (±11 kg/m³), opgebouwd uit:

- de vorm: kwadratisch geëxtrapoleerd ligt 2 tot 3 kg/m³ lager dan lineair (0,3 %);
- de ketenlengte en de onverzadiging: −0,8 % tot +1 %, vlak boven het smeltpunt gezien; bij hogere temperatuur kleiner, zoals tussen
  C12:0 en C14:0;
- de meting zelf: 2,1 kg/m³ (0,3 %).

**Niet in die schatting: de pitch in de bodem van K-01** (niet-gesplitst vet, polymeren). Daarvoor is geen dichtheid bij temperatuur
gevonden; pitch is waarschijnlijk zwaarder dan vetzuur.

### 4.5 Wat ermee verandert

De bodeminhoud M = ρ · V, en daarmee elke verblijftijd τ = M/B en de afbraakdosis, schalen met ρ. De grootste inhoud M_max van de
bodems, en daarmee de afstemming van de niveaukringen (lambda-regel, Kp = 2 · M_max/λ) en de tijd van alarm tot trip, ook. De
kleppen en de pomp van V-02 rekenen met de dichtheid van hun eigen vloeistof. De massastroom in het ontwerppunt verandert niet.

---

## 5. Afbraakkinetiek (Arrhenius)

**Nog niet vastgelegd.** Hoort bij module 9 (productkwaliteit).

| Grootheid | Waarde | Geldigheidsbereik | Bron | Betrouwbaarheid |
|---|---|---|---|---|
| Activeringsenergie E_a | — | — | nog invullen | |
| Pre-exponentiële factor A | — | — | nog invullen | |

---

## 6. Pakking en drukval

**Nog niet vastgelegd.** Hoort bij module 8.

| Grootheid | Waarde | Voorwaarden | Bron |
|---|---|---|---|
| — | — | — | nog invullen |

---

## 7. Natuurconstanten en definities

| Grootheid | Waarde | Bron |
|---|---|---|
| Gasconstante R | 8,314462618 J/(mol·K) | exacte SI-waarde sinds de herdefinitie van 2019 (R = N_A · k_B) |
| Standaardversnelling van de zwaartekracht g_n | 9,80665 m/s² | definitie, exact; vastgelegd door de 3e Conférence générale des poids et mesures (1901), zoals de SI-brochure van het BIPM haar geeft. Alleen gebruikt voor het vrije verval op het P&ID (27.2) |
| 1 bar | 100 000 Pa | definitie |
| 1 atm | 101 325 Pa | definitie |
| 1 mmHg (torr) | 101 325 / 760 Pa | definitie |
| Nulpunt Celsius | 273,15 K | definitie |
| Atoomgewichten C, H, O | 12,011 / 1,008 / 15,999 g/mol | IUPAC standaard atoomgewichten 2021 |

### 7.1 De wetten en correlaties van model 1: waar ze vandaan komen

De wetten zelf zijn geen stofdata en hebben geen geldigheidsbereik in de zin van een meting; hun aannames staan in het
hoofdstuk waar ze rekenen (9.1, 10.1, 11.1, 12.1, 13.1, 14.1, 15.1, 16.1). Hier staat per wet de oorspronkelijke publicatie.

- **DOI:** waar er een bestaat, is hij gecontroleerd bij doi.org (25 oktober 2026): hij verwijst naar de uitgever.
- **Nagelezen:** "ja" betekent dat de publicatie zelf is ingezien (een scan), met de vergelijking erin. "Nee" betekent dat de
  gegevens zijn nagezocht via Crossref en referentielijsten, maar dat de tekst niet is gelezen.
- **Handboek:** een wet zonder eigen publicatie (een definitie, een behoudswet, een afgeleide vorm) heeft een handboek waarin zij
  staat, op boekniveau: zonder hoofdstuk of bladzijde, want die zijn niet nagegaan (besluit van de gebruiker, 18 november 2026).

| Wet of correlatie | Hoofdstuk | Publicatie | DOI | Nagelezen |
|---|---|---|---|---|
| Antoine | 2 | C. Antoine, "Tensions des vapeurs; nouvelle relation entre les tensions et les températures", *Comptes rendus hebdomadaires des séances de l'Académie des sciences* 107 (1888) 681–684, 778–780 en 836–837 | geen | nee; de scan bij de Bibliothèque nationale de France was niet bereikbaar |
| Clausius-Clapeyron | 3 | É. Clapeyron, "Mémoire sur la puissance motrice de la chaleur", *Journal de l'École Royale Polytechnique* 14 (23e cahier) (1834) 153–190 | geen | de titelpagina, in een scan van 39 bladzijden; de jaargang uit referentielijsten |
| | | R. Clausius, "Ueber die bewegende Kraft der Wärme und die Gesetze, welche sich daraus für die Wärmelehre selbst ableiten lassen", *Annalen der Physik* 155 (1850) 368–397 en 500–524 | 10.1002/andp.18501550306 en 10.1002/andp.18501550403 | nee |
| Raoult | 9 | F.-M. Raoult, "Loi générale des tensions de vapeur des dissolvants", *Comptes rendus hebdomadaires des séances de l'Académie des sciences* 104 (1887) 1430–1433 | geen | nee |
| Dalton | 9 | J. Dalton, "Experimental essays" (vier essays, waarvan het eerste over de samenstelling van gemengde gassen), *Memoirs of the Literary and Philosophical Society of Manchester* 5 (2) (1802) 535 en verder | geen | nee |
| Relatieve vluchtigheid | 9 | een definitie, zonder eigen publicatie; de verhouding volgt uit Raoult en Dalton. Handboek: J. D. Seader, E. J. Henley en D. K. Roper, *Separation Process Principles*, 3e druk, Wiley, 2011 | geen | nee: op boekniveau, zonder hoofdstuk of bladzijde |
| Behoud van massa en energie | 10, 12, 16 | behoudswetten, zonder eigen publicatie. Handboek: J. D. Seader, E. J. Henley en D. K. Roper, *Separation Process Principles*, 3e druk, Wiley, 2011 | geen | nee: op boekniveau, zonder hoofdstuk of bladzijde |
| Ophoping en ophoping per component | 16 | behoud van massa in de tijd, zonder eigen publicatie. Handboek: D. E. Seborg, T. F. Edgar, D. A. Mellichamp en F. J. Doyle III, *Process Dynamics and Control*, 4e druk, Wiley, 2016 | geen | nee: op boekniveau, zonder hoofdstuk of bladzijde |
| Rachford-Rice | 10 | H. H. Rachford jr. en J. D. Rice, "Procedure for use of electronic digital computers in calculating flash vaporization hydrocarbon equilibrium", *Journal of Petroleum Technology* 4 (10) (1952), sectie 1, blz. 19, en sectie 2, blz. 3 | 10.2118/952327-G | nee |
| Fenske | 11 | M. R. Fenske, "Fractionation of straight-run Pennsylvania gasoline", *Industrial and Engineering Chemistry* 24 (5) (1932) 482–485 | 10.1021/ie50269a003 | ja: vergelijking (1), het minimum aantal trappen bij totale reflux |
| Underwood | 11 | A. J. V. Underwood, "Fractional distillation of multicomponent mixtures", *Chemical Engineering Progress* 44 (8) (1948) 603–614 | geen | ja: vergelijking (5) en de minimale reflux |
| McCabe-Thiele | 11 | W. L. McCabe en E. W. Thiele, "Graphical design of fractionating columns", *Industrial and Engineering Chemistry* 17 (6) (1925) 605–611 | 10.1021/ie50186a023 | nee |
| Gilliland (correlatie) | 11, 8.10 | E. R. Gilliland, "Multicomponent rectification: estimation of the number of theoretical plates as a function of the reflux ratio", *Industrial and Engineering Chemistry* 32 (9) (1940) 1220–1223 | 10.1021/ie50369a035 | nee |
| | | De vorm van Molokanov: Yu. K. Molokanov, T. P. Korablina, N. I. Mazurina en G. A. Nikiforov, "An approximate method of calculating the basic parameters of multicomponent fractionation", *Chemistry and Technology of Fuels and Oils* 7 (2) (1971) 129–133; vaak geciteerd als *International Chemical Engineering* 12 (1972) 209 | 10.1007/BF00718702 | nee |
| Kirkbride (correlatie) | 11.4 | zie 11.4 | — | — |
| Warmteoverdracht Q = U·A·ΔT | 12.2 | de definitie van de warmteoverdrachtscoëfficiënt; alleen kwalitatief gebruikt (12.2). Handboek: F. P. Incropera, D. P. DeWitt, T. L. Bergman en A. S. Lavine, *Fundamentals of Heat and Mass Transfer*, 7e druk, Wiley, 2011 | geen | nee: op boekniveau, zonder hoofdstuk of bladzijde |
| Ideale gaswet | 13 | Clapeyron (1834), zie boven | geen | de titelpagina en de eerste alinea, die de wetten van Mariotte en Gay-Lussac als vertrekpunt noemt. De vergelijking staat er als pv = R(267 + t), met t in graden Celsius: de ideale gaswet, met 267 uit de uitzetting die Gay-Lussac mat, dus een absoluut nulpunt bij −267 °C. Bron van die 267: J. L. Gay-Lussac, "Recherches sur la dilatation des gaz et des vapeurs", *Annales de chimie* 43 (1802) 137, voorgelezen op 11 pluviôse an X: tussen smeltend ijs en kokend water zet elk gas "100/266.66" van zijn volume uit op de honderddelige thermometer, dus ongeveer 1/267 per graad. Die deling per graad is van ons; Gay-Lussac geeft het hele traject. Ingezien in de Engelse vertaling van het fragment op Classic Chemistry van C. Giunta (web.lemoyne.edu/GIUNTA), naar W. F. Magie, *A Source Book in Physics* (1935); het origineel niet. Bron: W. B. Jensen, "The universal gas constant R", *Journal of Chemical Education* 80 (2003) 731–732, doi:10.1021/ed080p731. Daar staat dat Clapeyron in zijn memoire van 1834 pv = R(267 + t) schreef, met t in graden Celsius, en dat Clausius in 1850 273 invulde (pv = R(273 + t)) en in 1864 de absolute temperatuur T (pv = RT). De tekst van Jensen is ingezien via de herdruk op zijn eigen pagina bij de University of Cincinnati (homepages.uc.edu/~jensenwb, reprints, "100. Gas Constant", met een bijwerking uit 2009), 19 november 2026 |
| Arrhenius | 15 | S. Arrhenius, "Über die Reaktionsgeschwindigkeit bei der Inversion von Rohrzucker durch Säuren", *Zeitschrift für physikalische Chemie* 4 (1889) 226–248 | 10.1515/zpch-1889-0416 (de uitgever heeft hetzelfde artikel ook onder 10.1515/zpch-1889-0116; beide gecontroleerd) | nee |
| Drukopbouw over de kolom | 14.1 | een boekhouding van de druk, zonder eigen publicatie. Handboek: H. Z. Kister, *Distillation Design*, McGraw-Hill, 1992 | geen | nee: op boekniveau, zonder hoofdstuk of bladzijde |
| F-factor en floodinggrens (correlatie) | 14 | F = u·√ρ is een afspraak; de grens van 2,5 in dit model is een rond praktijkgetal (14.2). Handboek: H. Z. Kister, *Distillation Design*, McGraw-Hill, 1992 | geen | nee: op boekniveau, zonder hoofdstuk of bladzijde |
| Drukval, HETP (correlaties) | 14 | zie 14.2 | — | — |
| PI-regelaar en lambda-afstemming | 16 | zie 16.5 | — | — |
| Profielvertraging (modelaanname) | 20 | de vorm, een eerste-orde-vertraging. Handboek: D. E. Seborg, T. F. Edgar, D. A. Mellichamp en F. J. Doyle III, *Process Dynamics and Control*, 4e druk, Wiley, 2016. De 25 minuten bij ontwerpbelasting zijn een aanname van dit model (20.3) | geen | nee: op boekniveau, zonder hoofdstuk of bladzijde |

**Verder lezen.** J. D. Seader, E. J. Henley en D. K. Roper, *Separation Process Principles*, derde druk, Wiley, Hoboken NJ,
2011, ISBN 978-0-470-48183-7: een leerboek waarin evenwicht, flash, Fenske, Underwood, Gilliland, McCabe-Thiele en de balansen
samen behandeld worden. Sinds 18 november 2026 staat het in de tabel als handboek voor de behoudswetten en de relatieve
vluchtigheid; voor een getal in de module is het geen bron.

---

## 8. Bekende afwijkingen en vereenvoudigingen

Deze staan als **data** in `BEKENDE_AFWIJKINGEN`, zodat de UI ze kan tonen en latere
modules ernaar kunnen verwijzen. Het zijn geen voetnoten: een operator die ze niet kent,
trekt verkeerde conclusies uit de getallen.

Elke fysicamodule houdt zijn eigen lijst bij: `components.js` heeft er drie (8.1 – 8.3),
`vapor.js` één (8.4), `vle.js` twee (8.5 – 8.6), `balance.js` twee (8.7 – 8.8) en
`stages.js` drie (8.9 – 8.11), `energy.js` vier (8.12 – 8.15) en `vacuüm.js` twee (8.16 – 8.17) en `packing.js` vijf (8.18 – 8.22;
de andere twee notities van module 7 horen bij 8.2 en 13.2). De testsuite scant alle modules in
`src/physics/`, dus een notitie in een latere module valt er automatisch onder.

### 8.1 `NIET_SCHEIDBAAR_C18` — C18:0 en C18:1 zijn niet te destilleren (module 5)

α(C18:1 / C18:0) ligt over het werkgebied tussen **1,08 en 1,13**. In de noemer van Fenske
staat ln(α):

```
N_min = ln[ (x_D/(1−x_D)) · ((1−x_B)/x_B) ] / ln(α)
```

Gaat α naar 1, dan gaat ln(α) naar 0 en N_min naar oneindig.

**Vergelijking bij gelijke uitgangspunten.** N_min hangt van twee dingen af: de
zuiverheidseis (de teller) en α (de noemer). Om de twee scheidingen te kunnen vergelijken
zijn ze allebei doorgerekend met **dezelfde eis en over hetzelfde temperatuurbereik** —
alleen α verschilt, en dat is precies het punt.

- Specificatie: **x_D = 0,99 en x_B = 0,01** (99 mol % lichte sleutel in de top,
  1 mol % in de bodem; kortweg 99/99). Teller: ln(0,99/0,01 · 0,99/0,01) = ln(9801) = 9,190.
- Temperatuurbereik: **223,2 °C** (bubbelpunt van `VOEDING_GENERIEK` bij 20 mbar, module 3)
  tot **240 °C** (representatieve bodemtemperatuur; dezelfde waarde als `DHVAP_REF_T_K`).
  Beide liggen ruim boven het omslagpunt van 2.6.

**Er staat bewust geen enkel getal zonder temperatuur.** N_min hangt via α van de
temperatuur af, en bij een paar zó dicht bij 1 is die afhankelijkheid groot:

| Paar | α bij 223,2 °C | N_min | α bij 240 °C | N_min | α gemiddeld | N_min |
|---|---|---|---|---|---|---|
| C16:0 / C18:0 | 2,134 | **12,1** | 2,004 | **13,2** | 2,068 | **12,7** |
| C18:1 / C18:0 | 1,081 | **118** | 1,132 | **74** | 1,106 | **91** |

De gemiddelde α is het **meetkundig** gemiddelde van de twee eindpunten,
α_gem = √(α_onder · α_boven). Meetkundig en niet rekenkundig, omdat α in Fenske als
logaritme voorkomt; zie `alfaGemiddeld_geometrisch()` in `vle.js`. Dat maakt de aanname die
hieronder als beperking stond nu onderdeel van de berekening zelf.

**De conclusie is robuust, het getal niet.** Bij het C16-paar schuift N_min over het hele
bereik maar 9 % (12,1 → 13,2). Bij het C18-paar is het een factor 1,6 (118 → 74) over
nauwelijks 17 K. Maar 74 en 118 zijn allebei volstrekt onhaalbaar, dus het antwoord op de
vraag *"kan dit met destillatie?"* is over het hele bereik hetzelfde. Dat onderscheid —
een gevoelig getal bij een ongevoelige conclusie — is de leerstof.

**Verhouding tussen beide scheidingen.** De C18-scheiding kost vijf tot tien keer zoveel
schotels; met de gemiddelde α's ruim zeven keer. Die verhouding hangt *niet* van de
zuiverheidseis af: de teller is voor beide paren dezelfde en valt weg.

| Specificatie | N_min C16/C18 (gem.) | N_min C18:1/C18:0 (gem.) | Verhouding |
|---|---|---|---|
| 95/95 | 8,1 | 58,4 | 7,2 |
| 99/99 | 12,7 | 91,1 | 7,2 |
| 99,9/99,9 | 19,0 | 137,0 | 7,2 |

Maar wél van de **temperatuur**: de verhouding loopt van 9,7 bij 223 °C naar 5,6 bij
240 °C, omdat de twee paren tegengesteld op temperatuur reageren. Wie deze factor citeert,
hoort er de temperatuur of het bereik bij te noemen.

Geen enkele kolom is hoog genoeg, en méér reflux helpt niet: N_min is het aantal schotels
bij *oneindige* reflux en dus de ondergrens. Het probleem zit in de stofeigenschappen,
niet in de bedrijfsvoering.

#### De richting waarin α met de temperatuur beweegt is hier níet onderbouwd

Over 223 → 240 °C bewegen de twee paren **tegengesteld**:

| Paar | α van → naar | Richting |
|---|---|---|
| C16:0 / C18:0 | 2,134 → 2,004 | daalt |
| C18:1 / C18:0 | 1,081 → 1,132 | stijgt |

Beide waarden staan hier omdat een α zonder temperatuur niets waard is. Er hoort echter
**geen regel** uit te worden afgeleid, en dat is een uitspraak over de data en niet over de
fysica. De drie Antoine-sets komen uit dezelfde compilatie maar zijn onafhankelijk van
elkaar gefit, met sterk verschillende C: −55,1 K (C16:0), −57,8 K (C18:0) en −127,3 K
(C18:1). Juist die C bepaalt hoe de kromme buigt, en dus hoe de *verhouding* tussen twee
krommen met de temperatuur verandert. Bij een paar zó dicht bij α = 1 is het verschil
tussen de twee dampspanningen kleiner dan de onzekerheid in de fits zelf. Dat de twee
correlaties elkaar bij 202,1 °C zelfs **kruisen** (2.6) is het duidelijkste bewijs dat de
fijnstructuur hier niet te vertrouwen is.

Wat de data wél dragen: α van het C18-paar blijft over het hele werkgebied dicht bij 1, en
de scheiding is daarmee onhaalbaar bij elke temperatuur in dat gebied.

Deze cijfers staan als data in `NIET_SCHEIDBAAR_C18.fenskeVergelijking` in `components.js`,
met één gedeelde specificatie en één gedeeld temperatuurbereik voor beide paren, zodat ze
niet uiteen kunnen lopen. De testsuite herberekent α én N_min op elk punt én de
meetkundig gemiddelde waarde uit de stofdata, toetst dat de verhouding bij 95/95, 99/99 en
99,9/99,9 dezelfde blijft, en controleert dat de vastgelegde richtingen met de data kloppen
en tegengesteld zijn.

In de oleochemie gebeurt deze scheiding daarom met **kristallisatie of fractionering** —
daar benut je het verschil in *smeltpunt* (69 °C tegen 13 °C) in plaats van in kookpunt.
De destillatiekolom scheidt op **ketenlengte** (C14 / C16 / C18), niet op verzadigingsgraad.

> **Aanname die blijft staan.** Twee eindpunten met een meetkundig gemiddelde ertussen is
> nog steeds een benadering: in werkelijkheid verloopt de temperatuur over de hoogte van de
> kolom en verandert de samenstelling per schotel mee. De eindpunten zelf zijn generiek
> gekozen en niet uit een kolomberekening afgeleid; die volgt in module 5.

Beperking van de data: zie 2.6 (omslagpunt bij 202,1 °C).

### 8.2 `DIMERISATIE_DAMPFASE` — vetzuurdamp bestaat deels uit dubbelmoleculen (module 7)

De carboxylgroep (−COOH) van een vetzuur vormt twee waterstofbruggen met die van een
tweede molecuul. In de damp zweeft daardoor een deel van de moleculen rond als **dimeer**.
Hoe lager de temperatuur en hoe hoger de druk, hoe groter dat deel.

Twee moleculen die als één deeltje reizen, tellen in de ideale gaswet `p·V = n·R·T` als
één mol, niet als twee. De damp bevat dus **minder mol** dan je op grond van de
monomeer-molmassa zou verwachten, en neemt bij dezelfde druk en temperatuur **minder
volume** in.

**Wat dit betekent voor module 7 (vacuüm, dampvolume, lekdebiet):** die module rekent met
de monomeer-molmassa (256,4 g/mol voor C16:0) en met de ideale gaswet. Het berekende
dampvolume en de berekende belasting van het vacuümsysteem zijn daarmee een
**bovengrens**. Wie de werkelijke belasting wil, heeft een associatie-evenwicht nodig;
dat zit niet in deze module en er is **geen associatiegraad vastgelegd**.

De dampspanningscorrelaties zelf zijn hier ongevoelig voor: die zijn gemeten als druk
tegen temperatuur, ongeacht hoe de damp is samengesteld.

### 8.3 `PITCH_VIA_MEESLEUREN` — pitch komt er wel in, maar niet door te verdampen (module 8)

In de stofdata staat pitch als niet-vluchtig: `p_sat = 0`, dus `y = 0` in het evenwicht.
Volgens de VLE-berekening komt er dus **nooit** pitch in het destillaat. Als
verdampingsverschijnsel klopt dat ook.

In de praktijk komt het er wel in, via **meesleuren**: bij schuimen, te hoge dampbelasting
en flooding rukt de damp vloeistofdruppels uit de bodem mee naar boven. Die druppels hebben
de samenstelling van de bodemvloeistof, inclusief pitch. Dat is **mechanisch transport, geen
vluchtigheid** — er zit geen dampspanningswet onder.

Waarom dit leerstof is: de operator ziet geen samenstelling maar een kwaliteitsafwijking
(verkleuring, slechtere koude test), en de reflex is dan naar de temperatuur te kijken. De
oorzaak zit echter in de belasting. De twee zijn aan hun gedrag te onderscheiden:

| | Meesleuren | Afbraak (module 9) |
|---|---|---|
| Schaalt met | dampbelasting, F-factor, bodemniveau, schuim | temperatuur en verblijftijd |
| Gedrag in de tijd | komt plots op bij doorzetten, verdwijnt bij terugnemen | bouwt langzaam op, gaat niet weg door minder belasting |
| Begeleidend signaal | hogere drukval over de pakking | hogere bodemtemperatuur |

**Onderbouwing:** dit is praktijkkennis over het gedrag van een vacuümkolom, **geen
literatuurwaarde**. Er hoort geen getal bij en er valt niets te valideren. Er is **geen
meesleurmodel en geen entrainmentfactor** vastgelegd; de VLE-berekening blijft dus zeggen
dat er geen pitch in het destillaat zit. Dat is een eigenschap van het model, niet van de
kolom. Kwantitatief wordt dit pas in **module 8** (drukval, F-factor, flooding).

### 8.4 `ROLVERDELING_WETTEN` — Antoine rekent, Clausius-Clapeyron legt uit (module 2)

Staat in `vapor.js`, niet in `components.js`: het gaat over de wetten, niet over de stofdata.

- **Antoine** is een **fit**: drie constanten op meetwaarden afgeregeld. Verklaart niets,
  maar geeft binnen zijn fitbereik het nauwkeurigste getal dat er is.
- **Clausius-Clapeyron** is **afgeleid** uit de thermodynamica. Minder nauwkeurig, maar
  vertelt *waarom* de kromme loopt zoals hij loopt: de verdampingswarmte bepaalt de
  steilheid, en daarom hoort bij een grotere ketenlengte een hoger kookpunt.

Praktische regel: **reken met Antoine, redeneer met Clausius-Clapeyron.**

**De beperking, met cijfers.** Clausius-Clapeyron neemt ΔH_vap constant. Hoe groter de stap,
hoe harder die aanname sneuvelt. Beide intervallen hieronder eindigen in het werkgebied van
de kolom; alleen de stap verschilt (C16:0, ΔH_vap = 73,55 kJ/mol bij 240 °C):

| Interval | Afwijking t.o.v. Antoine | Oordeel |
|---|---|---|
| 133 → 20 mbar | **0,06 K** | kleine stap: verwaarloosbaar |
| 1013,25 → 10 mbar | **1,27 K** | grote stap: ruim twintig keer zo groot |

**Oorzaak:** ΔH_vap is niet constant. Voor C16:0 loopt hij van circa 75,2 kJ/mol bij 198 °C
naar 70,4 kJ/mol bij 354 °C — bijna 7 % over het bereik, terwijl de schatting met één waarde
rekent. De testsuite rekent zowel de twee afwijkingen als deze twee ΔH_vap-waarden opnieuw
uit de stofdata uit, zodat de leerstof niet los kan raken van de data.

**Waarom dit leerstof is:** niet de formule is fout, de **stap** is te groot voor de aanname
eronder. Een handberekening vanaf een kookpunt vlak bij het werkgebied is prima; dezelfde som
vanaf het normale kookpunt bij 1013 mbar is dat niet.

---

### 8.5 `IDEAAL_MENGSEL_AANNAME` — Raoult rekent met γ = 1 (module 3)

Staat in `vle.js`. De volledige vorm van Raoult luidt p_i = γ_i · x_i · p_sat,i; deze module
laat de activiteitscoëfficiënt γ weg en rekent dus met γ = 1 voor alle componenten.

**Waarom dat hier verdedigbaar is.** De vetzuren vormen een homologe reeks: dezelfde
COOH-kop met dezelfde waterstofbruggen, alleen een verschillende staartlengte. Dat is
ongeveer het gunstigste geval dat er voor Raoult bestaat, en een heel andere situatie dan
water/ethanol, waar de aanname onderuit gaat en er een azeotroop ontstaat.

**Waar hij wringt.** Twee plaatsen:

1. **De pitch** is geen vetzuur maar een triglyceride — een veel groter molecuul. Dat een
   vetzuur zich tussen triglyceriden net zo gedraagt als tussen vetzuren, is een stuk minder
   waarschijnlijk. En juist in de bodem is de pitchfractie hoog, dus daar is de aanname het
   zwakst. Zie 8.6.
2. **De dampzijde.** Dalton telt hier mol *monomeer*, terwijl vetzuurdamp deels uit
   dubbelmoleculen bestaat (8.2). Beide afwijkingen zitten in dezelfde berekening.

**Er hoort geen getal bij.** Er zijn geen activiteitscoëfficiënten vastgelegd en er is geen
model om ze uit te rekenen; UNIFAC, NRTL of Wilson zouden de gebruikelijke keuzes zijn, maar
geen ervan is hier onderbouwd beschikbaar. Wat in de notitie staat is de **richting** van de
onzekerheid en waar hij het grootst is, niet de grootte. Een verzonnen γ zou
schijnnauwkeurigheid zijn.

---

### 8.6 `PITCH_VERDUNT_DE_BODEM` — pitch verdampt niet en maakt de bodem juist daarom warmer (module 9)

Staat in `vle.js`. Pitch heeft p_sat = 0 en levert in de som van Dalton dus géén bijdrage aan
de druk — maar hij bezet wel plaats in de vloeistof, en die plaats gaat af van de vetzuren
die wél kunnen verdampen. De kolomdruk staat vast, dus moet het verschil uit de
**temperatuur** komen.

Bij 20 mbar, met in elke regel dezelfde vetzuurverhouding als `VOEDING_GENERIEK` en alleen
een andere pitchfractie:

| x_pitch (mol%) | Bubbelpunt | Erbij t.o.v. de referentie |
|---|---|---|
| 0 | 223,0 °C | referentie |
| 10 | 225,8 °C | +2,8 K |
| 20 | 228,9 °C | +5,9 K |
| 30 | 232,5 °C | +9,5 K |
| 50 | 241,8 °C | +18,8 K |

**Let op de basis.** Dit zijn **molfracties**, geen massaprocenten. Dat verschil is hier
groot: de 2 massa% pitch van de generieke voeding is maar **0,68 mol%**, omdat een
triglyceridemolecuul ruim drie keer zo zwaar is als een vetzuurmolecuul. Zie hoofdstuk 9.2.

**Waarom dit leerstof is.** De reflex bij een oplopende bodemtemperatuur is om naar het
vacuüm of naar de reboiler te kijken. Dit is een derde oorzaak die met geen van beide te maken
heeft: de **samenstelling** van de bodem zelf. Staat de druk stabiel en is de belasting
ongewijzigd, maar kruipt de bodemtemperatuur toch omhoog, dan concentreert de bodem op. De
ingreep is dan niet meer warmte of meer vacuüm, maar meer pitch uitspuien.

Het is bovendien een keten die zichzelf voedt: pitch ontstáát door een te warme bodem
(Arrhenius, module 9) en maakt die bodem vervolgens nog warmer. In module 9 moet die
koppeling expliciet worden gelegd.

**Beperking.** De berekening neemt pitch als volledig niet-vluchtig; dat is een aanname, geen
meting (1.1). Daarbij belast een molfractie van 30 of 50 % pitch de ideaal-mengselaanname het
zwaarst (8.5). De richting van het effect staat vast en de orde van grootte is bruikbaar,
maar de laatste regels van de tabel zijn geen precieze voorspelling.

De testsuite rekent de hele tabel opnieuw uit de stofdata uit.

---

### 8.7 `BODEMAFNAME_IS_GEEN_KNOP` — de bodemafname staat op niveauregeling (module 6 en 10)

Staat in `balance.js`. Module 4 vraagt om een **bodemstroom** en rekent daaruit alles verder
uit. Dat is bewust: het is de grootheid die de splitsing bepaalt en die op het scherm van de
operator staat. Maar het is **geen vrij instelbare waarde**.

In bedrijf staat de bodemafname vrijwel altijd op **niveauregeling**: een regelkring houdt
het vloeistofniveau in de kolombodem op setpoint en zet de afvoerklep zo ver open als daarvoor
nodig is. De bodemstroom is dus een gevolg.

Wat de operator werkelijk manipuleert is de **reboilerduty** — in deze installatie de thermische
olie naar E-01 (hoofdstuk 11; hier stond tot 7 oktober 2026 "de stoom", en dat klopte niet met wat
de software doet):

```
meer reboilerduty  →  meer damp omhoog  →  minder vloeistof beneden
                   →  niveauregelaar knijpt de bodemafvoer dicht
                   →  KLEINERE bodemstroom
                   →  pitch sterker opgeconcentreerd
                   →  hogere bodemtemperatuur (Raoult en Dalton, module 3)
                   →  snellere afbraak (Arrhenius, module 9)  →  meer pitch
```

**Waarom dit leerstof is.** Zonder deze notitie leert de cursist een knop die in werkelijkheid
een gevolg is — een misvatting van hetzelfde soort als 9.4. Erger nog: hij zou de verkeerde
*richting* kunnen onthouden. "Meer warmte om harder te koken" klinkt onschuldig, maar het is
precies de ingreep die de bodemstroom verkleint en de keten hierboven in gang zet. De keten
begint dus bij de **klep van de verwarming** en niet bij de bodemklep.

Er zit in deze module géén regelkring en géén niveaudynamiek: de bodemstroom gaat er als
gegeven in. Voor een stationaire balans is dat precies goed, maar de module kan daarmee niet
laten zien hóe snel of via welke weg die bodemstroom verandert. Module 6 legt het verband
tussen reboilerduty en dampstroom (kW tegen kg/h damp); module 10 voegt de niveauregeling
zelf toe.

---

### 8.8 `BALANS_SLUIT_NIET_DOOR_AFBRAAK` — de componentbalans sluit alleen zonder afbraak (module 9)

Staat in `balance.js`. Het **totale** massabehoud is onaantastbaar: F = D + B, altijd. De
**componentbalans** F·z_i = D·x_D,i + B·x_B,i is dat niet: die veronderstelt dat elke
component er als zichzelf in gaat en als zichzelf uit komt. In een vetzuurkolom breken
vetzuren bij bodemtemperatuur af en polymeriseren ze tot zwaar residu — op molbasis zelfs geen
één-op-één ruil.

Reken je een balans over een echte kolom door met labanalyses, dan sluit het totaal wel maar
de componenten niet. Dat is geen meetfout en geen rekenfout.

**Onderscheid aan het patroon.** Een analysefout of een verkeerde stroommeting geeft een
*willekeurig* teken: de ene component te veel, de andere te weinig. Afbraak geeft een
*systematisch* teken — vetzuren structureel tekort, zwaar residu structureel te veel — en het
loopt op met de bodemtemperatuur en de verblijftijd.

Er is géén afbraakterm in de balans opgenomen en geen reactiesnelheid vastgelegd.
`componentBalans()` rapporteert daarom een **sluitingsfout** en noemt hem een sluitingsfout,
niet een verlies: deze module kan die twee niet onderscheiden. Module 9 (Arrhenius) kan de
componentbalans een bronterm geven; dan wordt de sluitingsfout een berekende grootheid.

---

### 8.9 `PSEUDO_BINAIRE_SLEUTELS` — twee sleutels voor zes componenten (module 8)

Staat in `stages.js`. Fenske, Underwood en McCabe-Thiele zijn voor een **binair** mengsel
gemaakt; deze kolom heeft zes componenten. De uitweg is twee **sleutelcomponenten** aanwijzen
en de rest als meeloper behandelen.

**De keuze, en waarom.** De kolom scheidt op ketenlengte: C16 naar boven, de C18-groep naar
beneden. De sleutels zijn dus het aangrenzende paar op die grens. Bij 230 °C is de volgorde
van vluchtig naar zwaar:

| Component | p_sat bij 230 °C | Rol |
|---|---|---|
| C14:0 | 69,3 mbar | lichter dan de lichte sleutel → naar de top |
| **C16:0** | **33,5 mbar** | **lichte sleutel** |
| **C18:2** | **21,4 mbar** | **zware sleutel** |
| C18:1 | 17,8 mbar | zwaarder dan de zware sleutel → naar de bodem |
| C18:0 | 16,1 mbar | idem |
| Zwaar residu | 0 | niet-vluchtig → naar de bodem |

**De valkuil.** De verleiding is om C18:0 als zware sleutel te nemen — dat is de zwaarste
component en het is het paar uit 8.1. Maar de zware sleutel is niet de zwaarste component, het
is de **vluchtigste van de zware groep**, want die is het moeilijkst beneden te houden. Dat is
C18:2, met een dubbele binding meer en dus een hogere dampspanning.

| Paar | α bij 230 °C | Gevolg |
|---|---|---|
| C16:0 / C18:2 (juist) | 1,565 | — |
| C16:0 / C18:0 (onjuist) | 2,079 | N_min ruim een derde te laag |

Het aantal trappen schaalt met 1/ln(α); de verhouding ln(2,079)/ln(1,565) is ongeveer 1,63.
Met de verkeerde zware sleutel lijkt de kolom een stuk makkelijker dan hij is.

> **Waarschuwing bij de data.** De zware sleutel die het hele antwoord bepaalt, is uitgerekend
> de component met de **zwakste onderbouwing** van de hele set. Voor C18:2 bestaat geen gemeten
> Antoine-set: B en C zijn overgenomen van C18:1 en alleen A is op één gepubliceerd kookpunt
> gefit (2.3), met een geldigheidsbereik van 217 tot 247 °C. Alles wat module 5 uitrekent hangt
> via α aan die ene gefitte waarde. De **getallen** zijn dus minder hard dan hun nauwkeurigheid
> suggereert; het **verband** dat ze laten zien staat wel vast.

**Wat de pseudo-binaire stap kost.** De samenstellingen worden herschaald alsof de twee
sleutels samen 100 % vormen. Bij deze voeding valt daarmee **43,4 mol%** buiten beeld — en in
de **bodem 80,1 mol%**.

Dat knelt het hardst waar het er het meest toe doet. In de top klopt het beeld redelijk: daar
is C16:0 met ruim 97 % bijna de enige component. In de bodem zitten C18:1 en C18:0 samen voor
bijna 80 %, plus de pitch, en vormen de twee sleutels nog geen 20 %. Precies de stroom die de
bodemtemperatuur bepaalt — en daarmee de afbraak — is de stroom waarvoor de binaire
voorstelling het slechtst klopt. De pitch is er bovendien volledig onzichtbaar: hij staat niet
op de evenwichtslijn omdat hij niet verdampt, maar hij verhoogt wel de kooktemperatuur (8.6).
Het diagram toont dus een koelere bodem dan de echte.

**In cijfers.** Bij dezelfde specificatie en dezelfde reflux (1,3 × R_min) geeft de
pseudo-binaire McCabe-Thiele **26** trappen en geeft Gilliland er **31,5** — bijna 20 %
verschil. Een deel is de spreiding van Gilliland zelf (8.10), een deel is de prijs van de
binaire voorstelling. Het verschil tussen de twee is een eerlijker maat voor de onzekerheid dan
elk van beide afzonderlijk.

De module **weigert** bovendien een sleutelpaar waartussen nog een component ligt die in de
voeding voorkomt. Zo'n component verdeelt zich over beide producten, en dan liggen er extra
polen in het zoekinterval van Underwood; de enkele wortel die deze module zoekt is dan niet
meer de juiste. Kies je C16:0 met C18:0, dan zegt de module dat met zoveel woorden. Fenske
rekent er wél mee door — en geeft dan N_min = 10,4 in plaats van 15,8.

Er is géén trap-voor-trap berekening in deze module en géén model dat de vier niet-sleutels
over de kolom verdeelt. Dat zij niet verdelen is een **aanname**, geen uitkomst.

---

### 8.10 `GILLILAND_IS_GEEN_WET` — onder Gilliland zit geen natuurwet (module 5)

Staat in `stages.js`. Elke andere berekening in deze trainingsmodule is aan een natuurwet
gekoppeld: Antoine aan Clausius-Clapeyron, het evenwicht aan Raoult en Dalton, de balans aan
het behoud van massa. **Gilliland niet.**

Gilliland heeft een groot aantal doorgerekende kolommen in een grafiek gezet — het refluxgetal
X tegen het trapgetal Y — en daar een lijn doorheen getrokken; Molokanov heeft die lijn later
in een formule gevangen. Dat werkt, maar het verklaart niets. De getallen 54,4 en 117,2 in de
formule volgen nergens uit: het zijn **fitparameters**.

Twee gevolgen, en de cursist hoort ze allebei te kennen:

1. De punten liggen **zichtbaar** boven en onder de lijn. Een kolom die volgens Gilliland 31
   trappen nodig heeft, kan er in werkelijkheid een paar meer of minder vragen. Het is een
   schatting voor een eerste opzet, geen ontwerp.
2. **Buiten het gefitte gebied mag je hem niet gebruiken.** Bij een natuurwet kun je
   beredeneren wat er buiten het meetgebied gebeurt; bij een getrokken lijn niet.

**Geldigheidsbereik.** Vastgelegd in `CORRELATIE_GILLILAND.geldigheidsbereik`:

| Grootheid | Bereik |
|---|---|
| Aantal componenten | 2 – 11 |
| q | 0,28 – 1,42 |
| α | 1,11 – 4,05 |
| R_min | 0,53 – 9,09 |
| N_min | 3,4 – 60,3 |

> Deze grenzen worden in openbaar collegemateriaal toegeschreven aan J. D. Seader en E. J. Henley, *Separation Process
> Principles*, 2e druk, Wiley, 2006 (een collegepresentatie ingezien op 19 november 2026; daar staat ook een zesde grens, de
> druk, van vacuüm tot 42,4 bar). Het handboek zelf en de oorspronkelijke publicatie (Gilliland, 1940) zijn **niet ingezien**;
> dat de grenzen kloppen, is daarom een aanname van het model. Er is ook
> géén foutmarge vastgelegd: hoeveel de punten precies om de lijn spreiden is hier niet met een
> bron te onderbouwen, dus staat er geen getal bij. Het geval in 11.3 valt binnen alle vijf de
> grenzen.

**Waarom dit leerstof is.** Dit is een les over de **status** van een getal, en die is voor een
operator net zo belangrijk als het getal zelf. Een uitkomst van Antoine en een uitkomst van
Gilliland zien er op een scherm hetzelfde uit — allebei een getal met decimalen — maar ze zijn
niet even hard. De UI hoort een correlatie daarom anders te tonen dan een wet; de testsuite
dwingt af dat elke correlatie zichzelf als niet-natuurwet aanmerkt en haar geldigheidsbereik
noemt.

Dezelfde soort notitie als 8.3: ook daar staat expliciet dat er **geen** wet onder zit, en ook
daar is dat het punt en geen omissie.

---

### 8.11 `PITCH_IS_NIET_VERDELEND` — pitch valt buiten de vergelijking van Underwood (module 5)

Staat in `stages.js`. Underwood gaat uit van een constante relatieve vluchtigheid per
component, en pitch heeft α = 0. Op het eerste gezicht breekt dat de vergelijking, want α staat
in de noemer:

```
som( α_i · z_i / (α_i − θ) ) = 1 − q
```

Dat valt mee, en het is de moeite waard om te zien waarom: **in de teller staat α ook**. De
hele term wordt 0 / (−θ), en dat is nul zolang θ zelf niet nul is. En dat is hij nooit — de
gezochte wortel ligt tussen α van de zware sleutel (per definitie 1) en α van de lichte
sleutel, dus altijd boven 1.

Pitch levert dus geen bijdrage aan de wortelvergelijking, en dat is precies goed: hij is een
**niet-verdelende zware component**. Hij komt volledig in de bodem terecht (10.3) en doet aan
de scheiding tussen de sleutels niet mee. De module haalt hem expliciet uit de som in plaats
van erop te vertrouwen dat 0 gedeeld door iets wel goed gaat — dat is duidelijker en het
voorkomt dat een afrondingsfout in α alsnog een pool oplevert. De testsuite toetst dat de
wortel met en zonder pitch dezelfde is, en dat meer pitch in de voeding de berekening niet
breekt.

**De echte beperking.** Pitch verdwijnt uit de *scheidingsberekening*, maar niet uit de kolom:
hij bezet plaats in de bodemvloeistof en verhoogt daar de kooktemperatuur (8.6). Fenske en
Underwood merken daar niets van, want die rekenen alleen met verhoudingen tussen vluchtigheden.
Het aantal trappen dat eruit komt klopt dus, maar de **temperatuur** waarbij die trappen werken
is hoger dan het binaire beeld suggereert — en daarmee ook de afbraak.

**Randgeval.** Een zware sleutel moet vluchtig zijn. Zou iemand pitch als zware sleutel
aanwijzen, dan is α niet te definiëren en is er geen scheiding te berekenen; de module weigert
dat met een expliciete foutmelding in plaats van een NaN.

---

### 8.12 `MEDIUM_BEPAALT_HET_PLAFOND` — het verwarmingsmedium bepaalt mede het vacuüm (module 7)

Staat in `energy.js`. Tot nu toe is het vacuüm in deze module verdedigd met een argument over
**productkwaliteit**: lagere druk → lagere kooktemperatuur → minder afbraak. Dat klopt, maar het
is de helft van het verhaal.

Warmte stroomt alleen van warm naar koud, dus de bodem kan nooit boven de aanvoertemperatuur van
het medium komen — en zelfs die haalt hij niet, want zonder werkbaar temperatuurverschil gaat er
geen warmte over. Er ligt dus een **plafond**: `T_bodem,max = T_medium − ΔT_min`.

Dat plafond werkt door naar de druk. De bodem moet koken, en bij welke temperatuur dat gebeurt
bepaalt de kolomdruk (hoofdstuk 9). Bij ΔT_min = 25 K en de bodemsamenstelling van 11.3:

| Medium | Aanvoer | Plafond bodem | Max. kolomdruk | Haalbaar voor deze kolom? |
|---|---|---|---|---|
| Thermische olie | 280 °C | 255 °C | **48,2 mbar** | ja, 23 K marge op 232 °C |
| Verzadigde stoom 40 bar | 250 °C | 225 °C | 14,9 mbar | **nee** — 7 K te koud |
| Verzadigde stoom 10 bar | 180 °C | 155 °C | 0,4 mbar | **nee** — 77 K te koud |

**Lees de derde en vierde kolom samen en de omkering springt eruit.** Met thermische olie mag de
kolom tot 48 mbar staan. Met stoom van 40 bar — een *kouder* medium — nog maar tot 15 mbar, want
alleen bij die lagere druk kookt de bodem nog onder het plafond. **Een kouder medium dwingt een
dieper vacuüm af.** Vacuüm en verwarming zijn dus geen twee losse ontwerpkeuzes maar één.

*Storingsbeeld:* zakt de aanvoertemperatuur van de olie weg, dan zakt het plafond mee en haalt de
kolom zijn bodemtemperatuur niet meer. De reflex is de klep verder openen, maar als de
*temperatuur* het probleem is en niet de hoeveelheid, helpt dat niet.

---

### 8.13 `WANDTEMPERATUUR_IS_HOGER` — de afbraak begint aan de wand (module 9)

Staat in `energy.js`. De bodemtemperatuur op het scherm is die van de **vloeistof**. Dat is niet
de heetste plek: om warmte over te dragen is een temperatuurverval nodig, en de wand van de
reboiler ligt daar middenin — kouder dan het medium, **heter dan de bulk**. Voor de kolom van
11.3 met thermische olie ligt de wand ergens in een band van 48 K tussen 232 en 280 °C.

Omdat de afbraaksnelheid exponentieel met de temperatuur loopt (hoofdstuk 5, module 9), gebeurt
de afbraak daar onevenredig hard. De verkleuring die je in de bodem terugvindt is daar niet
ontstaan: die is aan de wand gemaakt en weggemengd.

**Ontwerpregel die tegen de intuïtie in gaat:** een *ruime* warmtewisselaar met een klein
temperatuurverschil is beter voor de productkwaliteit dan een krappe met een groot verschil — ook
al dragen ze dezelfde warmte over. Bezuinigen op oppervlak betaal je in kleur.

De module **rekent de wandtemperatuur niet uit** en kan dat ook niet: daarvoor zijn U en A nodig
en die zijn nergens vastgelegd. `wandtemperatuurBereik()` geeft alleen de grenzen terug, met
`bekend: false` erbij. Een getal verzinnen zou schijnnauwkeurigheid zijn op precies het punt
waar het om gaat.

---

### 8.14 `STOLLEN_IN_DE_CONDENSOR` — te ver doorkoelen laat de condensor dichtlopen (module 11)

Staat in `energy.js`. Vetzuren zijn bij kamertemperatuur vast. Zakt de temperatuur ergens in of
achter de condensor onder het stolpunt van het destillaat, dan slaat product als vaste stof neer
op de koelbuis; die raakt geïsoleerd door zijn eigen aanslag, draagt minder over, en de laag
groeit tot de condensor dichtloopt.

**De harde ondergrens** is het smeltpunt van de hoogst smeltende component die noemenswaardig in
het destillaat zit. Voor het destillaat van 11.3 (97,1 mol% C16:0) is dat **62,9 °C**. Dat is
conservatief: een mengsel stolt *lager* dan zijn hoogst smeltende component
(vriespuntsverlaging), dus wie zich aan de zuivere waarde houdt zit veilig. De echte stolcurve
vraagt vast-vloeistofevenwicht, en dat zit niet in deze module.

**Let op waar het risico níet zit.** De condensatie zelf gebeurt bij de topdruk: bij 20 mbar is
dat 215,5 °C, ruim 150 K boven het stolpunt. Om het destillaat bij zijn smeltpunt te laten
condenseren zou je naar ongeveer **0,003 mbar** moeten, en dat haalt geen enkel vacuümsysteem. De
condensatietemperatuur is dus **nooit** het knelpunt. Het risico zit volledig aan de koude kant:
te koud koelmedium, lokaal doorgekoelde wand, dode hoeken, uitgevallen tracing, opstarten en uit
bedrijf nemen.

*Storingsbeeld:* de topdruk loopt langzaam op terwijl het vacuümsysteem niets mankeert, de
condensor draagt minder over en de destillaatstroom zakt zonder dat er minder gekookt wordt. Een
dichtslaande condensor gedraagt zich precies als een vacuümsysteem dat het niet bijhoudt — kijk
eerst naar de koelmediumtemperatuur en de tracing.

---

### 8.15 `CONSTANTE_MOLAIRE_OVERLOOP` — de aanname onder McCabe-Thiele en Underwood, becijferd (module 5)

Staat in `energy.js`, maar de aanname komt uit module 5: rechte werklijnen en vaste stromen per
sectie bestaan alleen als elke mol damp die condenseert precies één mol laat verdampen. Dat vraagt
dat alle componenten ongeveer dezelfde verdampingswarmte **per mol** hebben.

Dat is hier niet zo. Bij 230 °C, uit de eigen stofdata (hoofdstuk 3):

| Component | ΔH_vap |
|---|---|
| C14:0 | 69,9 kJ/mol |
| C16:0 | 73,9 kJ/mol |
| C18:0 | 81,8 kJ/mol |
| C18:1 | 87,7 kJ/mol |
| C18:2 | 87,7 kJ/mol |

Van 69,9 tot 87,7 kJ/mol: ruim **25 %** spreiding. Het knelt het hardst waar de samenstelling het
sterkst verandert, en dat is precies wat over de hoogte van de kolom gebeurt — top bijna zuiver
C16:0, bodem vol C18.

**Waar de aanname werkelijk breekt: in de dampstroom, niet in de duty’s.**

De warmte die een condenserende mol afgeeft, verdampt zoveel mollen als daarmee te verdampen
zijn. Kost een mol in de bodem méér energie, dan levert dezelfde warmte daar dus **minder**
mollen damp op. Energieconsistent geldt `V_strip · ΔH(bodem) = V_top · ΔH(top)`:

| Sectie | ΔH_vap | Dampstroom |
|---|---|---|
| Rectificatie (top) | 74,4 kJ/mol | 19,46 mol/s |
| Stripping (bodem) | 85,5 kJ/mol | **16,93 mol/s** |
| Verschil | | **13 %** |

Constante molaire overloop zou die twee gelijkstellen. Precies daarom zijn de werklijnen van
McCabe-Thiele in werkelijkheid licht **gebogen** en niet recht: de helling van een werklijn is
een verhouding van stromen, en die stromen veranderen over de kolom.

> **Correctie.** Een eerdere versie van dit hoofdstuk zette hier een verschil van **216 kW**
> tussen reboiler- en condensorduty neer (1664 tegen 1448 kW). Dat was fout. Het kwam uit een
> berekening die constante molaire overloop aanhield voor de *stromen* (V_strip = V_top) en
> tegelijk verwierp voor de *enthalpieën*. Zo sluit de energiebalans niet. Zie 12.3.

---

### 8.16 `CONDENSOR_DOET_HET_ZWARE_WERK` — de pomp haalt alleen weg wat niet condenseert (module 11)

Staat in `vacuüm.js`. De dampstroom uit de top is bij 20 mbar en 215 °C goed voor **142 000 m³/h**
— een volumestroom waar geen vacuümsysteem bij in de buurt komt. Toch draait de kolom, want het
vacuümsysteem hoeft die damp niet te verwerken: de **condensor** haalt hem eruit door hem vloeistof
te maken. Wat overblijft is alleen wat níét condenseert: het luchtlek plus de meegesleepte damp.
Bij 5 kg/h lek is dat **269 m³/h** — ruim **500 ×** minder.

Gevolg: condensor en vacuümsysteem bepalen elkaars conditie. Slaat de condensor dicht (8.14) of
valt zijn koeling weg, dan komt er damp door die de pomp nooit kan wegwerken en loopt de kolomdruk
op. Dat lijkt op een vacuümprobleem maar is het niet.

*Onderscheid aan de bijverschijnselen:* bij een vacuümprobleem (lek, versleten ejecteur) blijft de
condensor gewoon warmte afvoeren en verandert de koelzijde nauwelijks. Bij een condensorprobleem
zakt juist de warmteafgifte — het koelmedium wordt mínder warm terwijl de druk stijgt. **Kijk naar
het koelwater voordat je naar de pomp kijkt.**

---

### 8.17 `MEESLEEP_TEGENOVER_STOLLEN` — kouder afzuigen spaart product, maar niet te koud (module 11)

Staat in `vacuüm.js`. De afgezogen lucht verlaat de condensor niet droog: hij is **verzadigd** met
productdamp bij de uitlaattemperatuur en neemt dat mee naar buiten. Hoeveel volgt uit Dalton
(hoofdstuk 9): `n_product / n_lucht = p_sat(T) / (p − p_sat(T))`.

Bij 20 mbar en 5 kg/h lek, met C16:0 als hoofdcomponent:

| Uitlaat | p_sat | Productverlies | Zuigvolume |
|---|---|---|---|
| 80 °C | 0,0122 mbar | **0,027 kg/h** | 254 m³/h |
| 100 °C | 0,0540 mbar | 0,120 kg/h | 269 m³/h |
| 120 °C | 0,2003 mbar | 0,448 kg/h | 285 m³/h |
| 140 °C | 0,6420 mbar | **1,468 kg/h** | 306 m³/h |

Van 140 naar 80 °C daalt het productverlies met een **factor vijftig**, terwijl het zuigvolume
nauwelijks verandert. Kouder afzuigen is dus vrijwel gratis winst — het kost alleen koeling.

**Maar er is een bodem, en die komt uit module 6.** Onder het stolpunt van het destillaat — hier
**62,9 °C** — loopt de condensor dicht (8.14). Het optimum ligt dus *net boven* het stolpunt, met
marge. Twee modules die elkaar tegenwerken: te warm kost product via de vacuümafzuiging, te koud
kost de hele condensor.

*Voor de operator:* een condensoruitlaat die structureel te warm staat, verdwijnt als
productverlies door de vacuümleiding — langzaam, onzichtbaar en zonder alarm.

---

### 8.18 `DRUKVAL_PER_TRAP_IS_HET_CRITERIUM` — bij vacuüm beslist de drukval per trap (module 6)

Staat in `packing.js`. Bij een kolom op atmosferische druk is drukval een kostenpost. Bij 20 mbar
is hij van dezelfde orde als de druk zelf, en bepaalt hij dus hoeveel héter de bodem staat dan de
top. Het criterium is daarom niet de drukval per meter of per schotel maar **per theoretische
trap** — je koopt scheiding, niet meters.

Voor de kolom van 11.3 (31,5 trappen, top op 20 mbar), naast het mediumplafond van 255 °C uit 8.12:

| Intern | Δp/trap | Δp | p_bodem | T_bodem | Hoogte | Haalbaar? |
|---|---|---|---|---|---|---|
| **Gestructureerde pakking** | 0,5 mbar | 16 mbar | 36 mbar | **246,9 °C** | 14,2 m | **ja**, 8 K marge |
| Gestorte pakking | 1,5 mbar | 47 mbar | 67 mbar | 264,3 °C | 23,6 m | **nee**, 9 K te heet |
| Zeefschotels | 5,0 mbar | 157 mbar | 177 mbar | 294,3 °C | 34,6 m | **nee**, 39 K te heet |

**Dit is de sterkste conclusie van de module.** Gestorte pakking en zeefschotels zijn niet duurder
in bedrijf — ze zijn **onverwarmbaar**: de thermische olie krijgt die bodem niet aan de kook.
Gestructureerde pakking is dus niet de nette keuze maar de énige, en niet omdat zij beter scheidt
maar omdat zij per trap scheiding de minste bodemtemperatuur kost. Wie alleen naar de scheiding
kijkt, ziet dat argument niet.

*Waarom een schotel per trap zoveel duurder is:* de damp moet door een laag vloeistof heen
borrelen, en die laag weegt — enkele mbar per schotel, vrijwel onafhankelijk van de systeemdruk.
Bij 1000 mbar is dat niets; bij 20 mbar is het een groot deel van de druk. Een pakkingbed heeft die
vloeistoflaag niet: de vloeistof loopt als film langs het metaal en de damp gaat erlángs.

---

### 8.19 `LAGE_DICHTHEID_BEPAALT_DE_DIAMETER` — de damp is zo dun dat de kolom onverwacht groot wordt (module 11)

Staat in `packing.js`. Kijk naar de massastroom en de kolom lijkt bescheiden: 18 t/h damp. Kijk
naar het volume en het kantelt.

| | Bij 1013 mbar | Bij 20 mbar |
|---|---|---|
| Dampdichtheid | 6,386 kg/m³ | **0,126 kg/m³** |
| Volumestroom | 0,78 m³/s | **39,5 m³/s** |
| Snelheid bij F = 2,5 | 0,99 m/s | **7,04 m/s** |
| Doorsnede | 0,79 m² | **5,61 m²** |
| Diameter | 1,00 m | **2,67 m** |

Dezelfde massastroom vraagt bij 20 mbar een **zeven keer** zo grote doorsnede. De diameter van een
vacuümkolom wordt dus niet bepaald door hoeveel product er door moet, maar door hoeveel **plaats**
die damp inneemt.

**Waarom flooding dichterbij ligt dan het lijkt.** Op het scherm staat een massastroom, en tien
procent meer voeding lijkt niets. Maar flooding gaat over de F-factor, en die schaalt met de
damp*snelheid*; de drukval loopt ongeveer met het **kwadraat** mee. De marge tot flooding is dus
kleiner dan de voedingsmeter suggereert en loopt sneller weg dan lineair.

De 2,67 m hierboven is de diameter **op** de floodinggrens en dus geen ontwerp. Een ontwerp zit op
70–80 % van F_max, dus op F ≈ 1,9, en dan komt de diameter op ruim **3,1 m**.

---

### 8.20 `UITDROGING_BIJ_LAGE_BELASTING` — te weinig belasting droogt het bed uit (module 11)

Staat in `packing.js`. Flooding is bekend. Het spiegelbeeld is dat niet, en is verraderlijker.

Het gaat in twee stappen. Eerst haalt de **vloeistofverdeler** zijn minimumdebiet niet meer
(circa 40 % van de ontwerpstroom): sommige gaten lopen nog, andere niet, of het sproeipatroon valt
in elkaar. Daarna volgt de **pakking**: waar geen vloeistof komt trekt de film open en staat er
droog metaal. En dan gebeurt het ergste: de damp gaat juist **dóór** die droge baan, want daar is
de minste weerstand. Er stroomt onevenredig veel damp door een stuk bed waar niets wordt
uitgewisseld.

**Het resultaat is dat de scheiding wegvalt terwijl er niets kapot is.**

*Waarom het in trends slecht te zien is* — en dit is het didactische punt. De drukval is **laag**,
lager dan normaal, want droge banen geven weinig weerstand. De temperaturen zijn vlak, de niveaus
in orde, er staat geen alarm. Een operator is geoefend op afwijkingen die **omhoog** gaan; een te
lage drukval is geen alarm en ziet eruit als een kolom die het rustig aan doet.

Het kenmerk is de **combinatie**: lage belasting, lage drukval, scheiding die niet klopt. De
ingreep is contra-intuïtief: **opvoeren**, niet terugnemen.

Twee verschillende grenzen, en het onderscheid doet ertoe: de pakking heeft een minimale
bevochtiging nodig (hier 0,5 m³/(m²·h)) en de verdeler een minimale fractie van zijn ontwerpstroom
(hier 40 %). Bij deze kolom is de **verdeler** de eerste die het opgeeft — bij 30 % belasting is de
bevochtiging nog 0,88 m³/(m²·h), ruim boven het pakkingminimum. Beide grenzen zijn ronde
waarden, een aanname van het model, met de orde van grootte uit Perry's handboek (14.3); de werkelijke komen van de leverancier.

De notitie draagt een **animatiespecificatie** (`ANIMATIE_UITDROGING`): een doorsnede van het bed
met een schuifregelaar over de belasting, waarbij de droge wiggen groeien en de damppijlen zich
herverdelen, met de drukval en het scheidingsresultaat als meters ernaast. Die animatie is
functioneel en niet decoratief: het mechanisme is een **terugkoppeling**
in de tijd — meer damp door de droge baan betekent nog minder vloeistof daar — en dat is in een
statisch plaatje niet te vertellen.

---

### 8.21 `DRUKVAL_SLUIT_DE_KETEN` — meer belasting of fouling geeft een hetere bodem (module 9)

Staat in `packing.js`. Dit is de laatste schakel van de keten die door de hele trainingsmodule
loopt:

```
meer belasting of vuilere pakking
  → meer DRUKVAL over het bed
  → hogere BODEMDRUK           (p_bodem = p_top + Δp, hoofdstuk 14)
  → hogere bodemTEMPERATUUR    (Raoult en Dalton, hoofdstuk 9)
  → snellere AFBRAAK           (Arrhenius, module 9)
  → meer pitch
      ↳ verdringt de vetzuren en verhoogt de kooktemperatuur verder (8.6)
      ↳ vervuilt de pakking en drijft de drukval verder op
```

Met cijfers, bij 20 mbar top en gestructureerde pakking:

| Fouling | Δp | p_bodem | T_bodem | |
|---|---|---|---|---|
| ×1,0 (schoon) | 15,7 mbar | 35,7 mbar | 246,9 °C | 8 K onder het plafond |
| ×1,5 | 23,6 mbar | 43,6 mbar | 252,2 °C | 3 K onder |
| ×2,0 | 31,5 mbar | 51,5 mbar | **256,8 °C** | **plafond voorbij** |
| ×3,0 | 47,2 mbar | 67,2 mbar | 264,3 °C | 9 K erboven |

Een **verdubbeling** van de drukval — niet ongewoon bij een vervuild bed — zet de bodem voorbij het
mediumplafond van 255 °C (8.12). De kolom is dan onverwarmbaar geworden door vervuiling alleen; er
is niets kapot en toch zakt de scheiding weg. Vervuiling is in deze kolom dus geen onderhoudsdetail
maar een **bedrijfsgrens**.

En het is een terugkoppeling die zichzelf versterkt: een vervuilende kolom loopt niet gelijkmatig
achteruit maar steeds sneller.

*Wat de operator ziet:* de drukval loopt over weken op, de bodemtemperatuur kruipt mee, het
destillaat wordt donkerder — en de **topdruk blijft op setpoint**, want de weerstand zit binnen de
kolom. Wie alleen de topdruk in het oog houdt, ziet dit niet aankomen. Dezelfde drukvalmeter gaat
bij uitdroging (8.20) juist de andere kant op: **omhoog is vervuiling of overbelasting, omlaag is
uitdroging.**

---

### 8.22 `MEESLEUREN_BIJ_FLOODING` — pitch in het destillaat, zonder te verdampen (module 11)

Staat in `packing.js`, en lost de belofte in die `PITCH_VIA_MEESLEUREN` (8.3) aan module 8 deed.

Volgens het evenwicht komt er nooit pitch in het destillaat: p_sat = 0, dus y = 0. Bij hoge
dampbelasting gaat er iets anders gebeuren: de damp rukt vloeistof**druppels** uit de film en neemt
ze mee naar boven. Die druppels hebben de samenstelling van de bodemvloeistof — inclusief pitch —
en belanden als druppel in de condensor.

De **F-factor** is de maat die aangeeft wanneer dit begint: ver onder het floodingpunt
verwaarloosbaar, bij het naderen ervan snel oplopend. Waar precies het omslagpunt ligt, is
leveranciers- en stofsysteemafhankelijk en staat hier **niet** als getal vastgelegd. Er is géén
meesleurmodel en géén entrainmentfactor — precies zoals 8.3 al aankondigde. De module kan aangeven
hóe dicht de kolom bij flooding zit, niet hoeveel er meekomt.

Het **onderscheid** dat de operator moet kunnen maken, wordt hiermee concreet: meesleuren schaalt
met de **belasting** (komt plotseling op, verdwijnt bij terugnemen, gaat samen met hoge drukval);
afbraak schaalt met **temperatuur en verblijftijd** (bouwt langzaam op, gaat niet weg door
terugnemen).

---

### 8.23 `EA_IS_GEEN_ONDERBOUWDE_WAARDE` — de activeringsenergie is een omgekeerde vuistregel (module 12)

Staat in `degradation.js`. Dit is de **zwakst onderbouwde aanname van het hele project**, en dat
hoort hier met zoveel woorden te staan.

Onder de wet van Arrhenius zit een natuurwet. Onder het *getal* dat wij erin vullen zit niets. De
activeringsenergie is hier afgeleid uit de praktijkregel "de afbraak verdubbelt per 8 K":

    Ea = ln(2) · R / (1/T₁ − 1/T₂)

Bij het temperatuurpaar 223,18 → 231,27 °C uit 9.4 geeft dat **178,4 kJ/mol**. Dat is de omkering
van een vuistregel en geen meetwaarde. Zie hoofdstuk 15 voor de literatuurtoets die hem niet
bevestigt.

**De vuistregel kan geen wet zijn, en dat is met een rekensom te laten zien.** Arrhenius zegt dat
de verdubbelingsstap met de temperatuur *groeit*. Wie eist dat de stap overal 8 K is, heeft bij elke
temperatuur een andere Ea nodig:

| T | Ea die "factor 2 per 8 K" vraagt |
|---|---|
| 223,2 °C | 180,3 kJ/mol |
| 232,1 °C | 186,8 kJ/mol |
| 246,9 °C | 197,8 kJ/mol |
| 280,0 °C | 223,6 kJ/mol |

Een spreiding van 43 kJ/mol over het temperatuurgebied van deze kolom. Een natuurwet heeft één
waarde; de vuistregel is een **lokale benadering rond 500 K**. Omgekeerd: houd je Ea vast op 178,4
kJ/mol, dan verschuift de verdubbelingsstap van 8,0 K bij 223 °C naar ruim 9 K bij 280 °C.

**Gevolg voor de module.** *Vijfde doorloop: de band hieronder is vervangen door het gemeten
bereik voor vetzuurreacties, 31,0 tot 127,4 kJ/mol - zie 15.2. De tekst van deze paragraaf is het
verloop tot dan.* Er wordt niet met een getal gerekend maar met een **band** van 100 tot
178,4 kJ/mol (`EA_BAND`), en elke uitkomst is een bereik. Voor de 14,8 K uit 8.24 betekent dat een
factor tussen 2,0 en 3,3 — bijna 70 % verschil, alleen door de keuze van Ea. Alle conclusies van
module 9 zijn daarmee **kwalitatief hard** (warmer is exponentieel slechter, de fout valt altijd
dezelfde kant op) en **quantitatief zacht** (hoeveel precies is onbekend).

**Wat er nodig is:** een houdproef. Monsters op temperatuur houden bij 200–280 °C onder vacuüm, en
dimeergehalte en kleur tegen de tijd uitzetten. Zolang die er niet is, blijft openstaand punt 9
open.

---

### 8.24 `DRUKVAL_KOST_AFBRAAK` — wat de 14,8 K uit module 8 kost (module 11)

Staat in `degradation.js`, en dit is het getal dat de hele reeks sluit.

Modules 5 tot 7 rekenden met **één druk** over de hele kolom: 20 mbar in de top en 20 mbar in de
bodem, bodemtemperatuur 232,13 °C. Module 8 rekende het drukprofiel erbij — 0,5 mbar per
theoretische trap over 31,47 trappen, dus 15,7 mbar — en daarmee kookt de bodem bij 246,92 °C. Een
verschil van **14,79 K**.

| Aanname | Factor in afbraaksnelheid | Wat modules 5–7 niet in de boeken hadden |
|---|---|---|
| Vuistregel 2^(ΔT/8) | 3,60 | 72 % |
| Ea = 178,4 kJ/mol | 3,35 | 70 % |
| Ea = 130 kJ/mol | 2,41 | 58 % |
| Ea = 100 kJ/mol | 1,97 | 49 % |

Ook bij de voorzichtigste aanname **verdubbelt** de afbraak. De fout valt daarbij altijd dezelfde
kant op: wie de drukval vergeet, onderschat de afbraak en overschat hem nooit. Voor een kolom die
met 8 K marge onder zijn mediumplafond zit (14.4), is dat precies de verkeerde kant.

**Didactisch punt.** Dit laat zien dat een *aanname in een rekenmodel* — één druk in plaats van een
drukprofiel — doorwerkt tot een factor 3 in de productkwaliteit. De praktische consequentie voor de
operator: kijk niet alleen naar de topdruk maar naar het **verschil** tussen top en bodem, want dat
is de meter die de afbraak voorspelt.

---

### 8.25 `AFBRAAK_BEGINT_AAN_DE_WAND` — de bulktemperatuur is niet de afbraaktemperatuur (module 11)

Staat in `degradation.js`, en lost de belofte in die `WANDTEMPERATUUR_IS_HOGER` (8.14) aan module 9
deed.

De bodemtemperatuur op het scherm is de **bulk**temperatuur. De warmte komt binnen via de wand van
de reboiler, en daar moet een temperatuurverschil over staan, anders stroomt er geen warmte
(12.2). De wand is dus altijd heter dan de bulk.

| | T | Factor t.o.v. de bulk |
|---|---|---|
| Bulk | 246,9 °C | 1 |
| Medium (thermische olie) | 280,0 °C | 4,0 (Ea=100) tot 11,8 (Ea=178) |

Band: **33,1 K**. Dit is een **bovengrens** en geen voorspelling: zat de wand werkelijk op
mediumtemperatuur, dan stroomde er geen warmte meer. De wandtemperatuur zelf is **niet berekend** —
daarvoor zijn U en A nodig en die zijn in dit project niet vastgelegd (12.2, 8.14).

De orde is wel duidelijk: **afbraak is een wandverschijnsel, niet een bulkverschijnsel.** Dat
verklaart waarom aankoeking altijd op het warmteoverdragende oppervlak zit en niet in het vat, en
waarom een reboiler met een klein oppervlak sneller vervuilt dan een grote bij dezelfde duty:
minder oppervlak betekent een grotere ΔT bij dezelfde warmte, dus een hetere wand, dus snellere
afbraak juist daar.

**Praktische regel:** een grote reboiler op 260 °C olie is beter voor het product dan een kleine op
300 °C, ook al leveren ze dezelfde duty.

---

### 8.26 `AFBRAAK_IS_SNELHEID_MAAL_TIJD` — verblijftijd weegt even zwaar als temperatuur (module 10)

Staat in `degradation.js`.

De hoeveelheid afbraak is de snelheid **maal** de tijd op temperatuur. Die tijd is de verblijftijd
in de bodem: τ = vloeistofinhoud / bodemstroom. Daar zit een tegenwerking in die de afweging uit
10.4 compleet maakt:

wie de bodemafname dichtknijpt om meer terug te winnen (10.2), **verlengt de verblijftijd**
(halveer de stroom en τ verdubbelt) **en** concentreert de pitch sterker op, waardoor ook de
bodemtemperatuur stijgt. Twee kanten tegelijk de verkeerde kant op.

De afweging is dus niet "koel stoken" maar **"kort en warm tegenover lang en koel"**, en bij
vetzuren valt die bijna altijd uit op kort. Daarom worden in deze dienst verdampers met een kleine
vloeistofinhoud gebruikt — een vallende-film- of dunnefilmverdamper in plaats van een ketel met een
grote inhoud.

**Wat hier niet vastligt.** De vloeistofinhoud van het bodemcompartiment is een apparaatgegeven en
staat *niet* in dit project. `verblijftijd()` vraagt hem daarom expliciet als invoer, en rekent met
een aangenomen dichtheid zolang openstaand punt 6 open is. De uitkomst van `afbraakmaat()` is een
**verhouding** ten opzichte van een referentie, nooit een absolute hoeveelheid afbraak: daarvoor is
de pre-exponentiële factor A nodig, en die is voor dit systeem niet bekend.

Het gevaarlijkste geval is een kolom die **warm stilstaat**: geen doorstroming betekent τ → ∞.

**Twee knoppen die op elkaar lijken en niet hetzelfde doen.** Dit hoort erbij, want de conclusie
hierboven wordt anders te ruim gelezen (aangescherpt bij module 10, 8.28):

| Wat je verandert | Stationaire samenstelling x_B | Verblijftijd τ | Afbraakdosis |
|---|---|---|---|
| **Bodemstroom B** | verandert (F·z = B·x_B) | verandert (τ = M/B) | verandert |
| **Vloeistofinhoud M** | **onveranderd** — M staat niet in de balans | verandert | verandert |

Een kleinere sump geeft dus **minder schade en niet minder opconcentratie.** Pitch kan niet
verdampen, dus moet alles wat erin gaat er via de bodem uit, hoeveel er ook in de sump staat; in
de stationaire balans valt M weg zodra d*M*/d*t* = 0. Wie die twee verwart, verwart een balans met
een tijdconstante. Doorgerekend in 8.28 en hoofdstuk 16.

---

### 8.27 `FOULINGLUS_VERSTERKT_ZICHZELF` — en loopt vast tegen het medium (module 11)

Staat in `degradation.js`. Dit is de lus waar de ketens van 8.21 en 8.24 op uitkomen.

| Stap | Wat | Wet |
|---|---|---|
| 1 | aankoeking in de pakking en op de reboilerwand | gevolg van afbraak |
| 2 | de drukval loopt op | `CORRELATIE_DRUKVAL` (14.1) |
| 3 | p_bodem = p_top + Δp loopt op | `WET_DRUKPROFIEL` (14.1) |
| 4 | de bodemtemperatuur loopt op | kooklijn, Raoult + Dalton (9.1) |
| 5 | de afbraak versnelt exponentieel | `WET_ARRHENIUS` (15.1) |
| 6 | meer pitch en meer polymeer | gevolg van 5 |
| 7 | dat koekt aan → terug naar 1, maar erger | de lus is rond |

Doorgerekend met `foulinglus()`:

| Foulingfactor | Δp | T_bodem | Factor t.o.v. schoon | Onder het plafond? |
|---|---|---|---|---|
| 1 (schoon) | 15,7 mbar | 246,9 °C | 1 | ja |
| 1,5 | 23,6 mbar | 252,3 °C | 1,26–1,52 | ja, krap |
| 2 | 31,5 mbar | 256,8 °C | 1,54–2,16 | **nee** |
| 3 | 47,2 mbar | 264,4 °C | 2,12–3,81 | **nee** |

**De nuance is belangrijker dan de lus zelf.** Een zichzelf versterkende lus suggereert een op hol
geslagen proces, en dat gebeurt hier niet — om een saaie reden. Bij een verdubbelde drukval staat de
bodem op 256,8 °C, en dat is **boven het plafond van 255 °C** dat thermische olie van 280 °C met 25
K overdrachtsverschil kan leveren (12.5, 8.13). De lus loopt dus niet weg in de temperatuur maar
vast tegen het medium: de reboiler krijgt de bodem niet meer aan de kook, de dampstroom zakt, de
scheiding valt terug en de kolom verliest capaciteit.

**Zelfbegrenzend in de BULKtemperatuur** — en die toevoeging is essentieel. Wat er in bedrijf
gebeurt is geen thermische wegloop maar een langzame verstikking: elke week iets meer drukval, iets
minder doorzet, iets slechtere kleur, tot de kolom eruit moet.

De lus wordt op **stap 1** doorbroken en niet op stap 5. Zodra de drukval oploopt is de
temperatuurstijging een *gevolg* dat niet weg te regelen is: lager stoken kan niet, want dan kookt
de bodem niet.

#### Begrensd in temperatuur is niet begrensd in schade

De conclusie hierboven leest geruststellender dan hij is, en dat voorbehoud hoort er altijd bij
(`nietBegrensdInSchade` in `degradation.js`). **Het plafond van het medium zit op de
bulktemperatuur, niet op de wandtemperatuur.** De bovengrens van de wand is de mediumtemperatuur
zelf, en die wordt door de verstikking niet geraakt. De wandfilm blijft dus onverminderd coken
terwijl de kolom bulkmatig verstikt: de lus is begrensd in temperatuur en **niet** in
aankoekingssnelheid.

Op papier lijkt het zelfs te verbeteren, en dat cijfer moet je kunnen weerleggen:

| | Bulk | Wandband | Verhouding wand/bulk |
|---|---|---|---|
| Schoon | 246,9 °C | 33,1 K | 4,0–11,8× |
| Verstikt | 255,0 °C | 25,0 K | 2,8–6,3× |

De verhouding **daalt**. Dat is een rekenkundig artefact: hij daalt omdat de *noemer* stijgt — de
bulk is heter geworden — en niet omdat de teller daalt. De wandtemperatuur kan nog altijd tot 280 °C
oplopen, precies zoals eerst. Afgerekend tegen de schone bulk als vaste referentie:

| Grootheid aan de wand | Factor t.o.v. schone bulk | Verandering |
|---|---|---|
| Bovengrens (= mediumtemperatuur, 280 °C) | 4,0–11,8× | **ongewijzigd** |
| Ondergrens (= de opgelopen bulk, +8,1 K) | 1,4–1,9× | **gestegen** |

Er is dus **geen enkele grootheid aan de wand die door de verstikking gunstiger wordt.** Alleen de
bulk houdt op met warmer worden.

Daar komt de verblijftijd nog bij (8.26): de doorzet zakt, dus stijgt de tijd op temperatuur. Bij
**50 % doorzet** is de afbraak per kilogram product **2,9–3,8×** die van de schone kolom, bij een
bulk die maar 8,1 K heter staat. De kolom maakt minder product én slechter product, en de tweede
helft van die zin komt uit de tijd en niet uit de temperatuur.

**Voor de bediening betekent dit dat het ontbreken van een temperatuuralarm hier niets zegt.** Het
systeem is begrensd in precies de grootheid waar de alarmering op staat, en onbegrensd in de
grootheid die de schade doet. Het bijbehorende storingsscenario
(`VERSTIKKING_DOOR_AANKOEKING` in `src/content/storingen.js`) werkt dat uit, inclusief de
gevaarlijkste ingreep: het **setpoint** van de olie verhogen. Dat werkt — de bodem kookt weer — en
verschuift tegelijk de bovengrens van de wandtemperatuur even hard mee:

| Setpoint olie | Plafond bodem | Wandbovengrens t.o.v. schone bulk |
|---|---|---|
| 280 °C | 255 °C | 4,0–11,8× |
| 290 °C | 265 °C | 5,9–23,5× |
| 300 °C | 275 °C | 8,5–45,7× |

20 K setpoint erbij koopt 20 K ruimte en kost een **factor 3,9** aan de wand. Bovendien heeft de
olie zelf een maximale filmtemperatuur (`VERWARMINGSMEDIA.beperking`, 12.1): boven een bepaald
niveau kraakt hij in de eigen stoker. Een setpointverhoging is daarmee een **ontwerpbeslissing** en
geen bedrijfsingreep.

---

### 8.28 `HOLDUP_IS_GEEN_BALANS` — minder schade, niet minder opconcentratie (module 12)

Staat in `dynamics.js`, en scherpt de conclusie van 8.26 aan.

Module 9 stelde vast dat de verblijftijd de afbraak bepaalt en dat in deze dienst daarom
verdampers met een kleine vloeistofinhoud worden gebruikt. Daaruit volgt gemakkelijk een verkeerde
conclusie: dat een kleinere sump ook de **opconcentratie** van pitch zou verminderen. Dat is niet
zo, en het verschil is fundamenteel.

Pitch kan niet verdampen. Alles wat er met de voeding aan pitch binnenkomt moet er via de
bodemstroom uit, hoeveel of hoe weinig er ook in de sump staat. In de stationaire balans komt de
ophoping niet voor — zij valt eruit zodra d*M*/d*t* = 0:

    dm/dt = F·z_pitch − B·(m/M)     →  stationair:  x_pitch = F·z_pitch / B

Geen *M* in de uitkomst. Doorgerekend met `vergelijkConfiguraties()` bij F = 10 000 kg/h,
z_pitch = 2,00 massa% en B = 340 kg/h:

| Configuratie | V_sump | τ | x_pitch stationair | Afbraakdosis |
|---|---|---|---|---|
| Ruime sump | 3,00 m³ | 450 min | **58,82 massa%** | 30× |
| Vallende film (de bodem van K-01, 20.13) | 0,591 m³ | 88,7 min | **58,82 massa%** | 5,9× |
| Dunne film | 0,10 m³ | 15,0 min | **58,82 massa%** | 1× |

Identiek tot in de vierde decimaal, en de tests dwingen dat element voor element af. Wat verschilt
is de **dosis**, met een factor 30.

**De twee knoppen die op elkaar lijken:**

| Wat je verandert | x_B | τ | Dosis |
|---|---|---|---|
| Bodemstroom **B** | verandert | verandert | verandert |
| Vloeistofinhoud **M** | **onveranderd** | verandert | verandert |

Wie die twee verwart, verwart een balans met een tijdconstante. Het is dezelfde soort fout als de
misvatting van 9.4, waar de voeding met de bodem werd verward: in beide gevallen wordt een
grootheid uit het ene model in het andere gebruikt.

---

### 8.29 `TIJDSCHALEN_LOPEN_ORDES_UITEEN` — seconden tot uren in één apparaat (module 11)

**Met de dichtheid uit hoofdstuk 4**: bij 340 kg/h is de vallende film (de bodem van K-01, 730,9 kg/m³) in 76,3 min rond, de ruime sump van 3 m³ in 387 min en de dunne film in 12,9 min; de spreiding tegen de damp (3,3 s) is 1380 en ruim 7000. De getallen hieronder zijn met een eerdere, aangenomen dichtheid van 850 kg/m³ gerekend.

Staat in `dynamics.js`. Elke ophoping heeft zijn eigen tijdconstante τ = inhoud / stroom, en ze
lopen extreem uiteen:

| Ophoping | Inhoud | Stroom | τ |
|---|---|---|---|
| Damp in de kolom (druk) | 19,7 kg | 21 428 kg/h | **3,3 s** |
| Refluxvat | 1275 kg | 21 428 kg/h | 3,6 min |
| Bodemsump, vallende film (de bodem van K-01), pitchsnede | 502 kg | 340 kg/h | 88,7 min |
| Bodemsump, ruime sump, pitchsnede | 2550 kg | 340 kg/h | 450 min |

Het refluxvat in deze tabel is een oudere rekenwaarde (1275 kg). Refluxvat V-02 van het procesmodel heeft sinds
v1.1.5 1753 kg bij half vol, vijf minuten van reflux plus destillaat (26.29).

Dat is een spreiding van een factor 1600 tot ruim 8000 — ruim drie tot bijna vier ordes — binnen
één apparaat, en de *realistische* configuratie zit aan de lage kant daarvan. (Tot v1.1.6, met een vallende film
van 0,25 m³: 213 kg, 37,5 min, een factor 680.)

De dampinhoud is het kolomvolume maal de dampdichtheid aan de top: de bedhoogte van module 8
(17,6 m) maal de doorsnede van het ontwerp (3,38 m diameter, driekwart van F_max, module 8) maal
0,125 kg/m³. Tot de vierde doorloop stond hier 12,7 kg tegen 17 922 kg/h, met dezelfde methode maar
met de kolom van vóór de gemeten stofdata (31,5 trappen, 3,0 m). De testsuite rekent de
tabel nu uit het model.

**Voor de bediening is dit de belangrijkste eigenschap van de kolom.** Een drukingreep is binnen
een minuut te beoordelen; een ingreep die de bodemsamenstelling moet veranderen niet. Juist daar
gaat het mis: de operator ziet na tien minuten niets, concludeert dat zijn ingreep niet werkte, en
grijpt nog een keer in. Als beide ingrepen dan aankomen schiet de kolom door — **niet door
instabiliteit maar door ongeduld.**

Het verklaart ook waarom de drukregeling snel moet staan en de niveauregeling langzaam. Een scherp
afgestemde niveauregelaar zet elke voedingsschommeling om in een schommelende bodemstroom, en die
schommelt via de opconcentratie de bodemtemperatuur mee. **Een ruime, trage niveauregeling is hier
beter dan een scherpe** — tegen de intuïtie in.

---

### 8.30 `ONGECOMPENSEERDE_TEMPERATUUR_MASKEERT_LEK` — de regelkring verbergt de storing (module 11)

Staat in `dynamics.js`, en verbindt de modules 7, 8, 10 en 11.

De sleutel is dT/d*p*, en het verschil tussen atmosferisch en vacuüm is groot:

| Druk | dT/dp voor C16:0 |
|---|---|
| 1013,25 mbar | 0,046 K/mbar |
| 50 mbar | 0,600 K/mbar |
| 35,7 mbar | 0,804 K/mbar |
| **20 mbar** | **1,335 K/mbar** |

Een factor 29 tussen atmosferisch en 20 mbar. Een temperatuurmeting bij vacuüm is daarmee een
*mengsel* van samenstelling en druk, en zegt op zichzelf bijna niets over de scheiding.

**De keten.** Een luchtlek (13.2) laat de topdruk oplopen, van 20 naar 26 mbar. Het kookpunt op het
meetpunt stijgt 6 × 1,335 = **8,0 K**, zonder dat er iets aan de scheiding is veranderd. Een
ongecompenseerde temperatuurregelaar leest dat als "te warm" en knijpt de reboilerduty dicht tot de
temperatuur weer op setpoint staat. Daarmee is **precies die 8,0 K aan echte strippingtemperatuur
weggegeven** — geen schatting, maar de compensatie zelf.

Het beeld is daarna weer netjes: temperatuur op setpoint, geen alarm, regelaar doet zijn werk
uitstekend. Het enige symptoom is wegzakkende zuiverheid en donkerder product, via het lab, dagen
later.

Met drukcompensatie — T_gecomp = T_gemeten − (dT/dp)·(p − p_ref) — gebeurt het omgekeerde: de
regelaar ziet dat de gecompenseerde temperatuur gelijk is gebleven en laat de duty staan. De
temperatuur **loopt** dan op, en dat is precies het signaal dat je wilt zien.

**De paradox:** dit is de enige plek in de trainingsmodule waar een *beter* werkende regelkring een
*slechter* beeld geeft. Wie regelkringen beoordeelt op "staat de meting op setpoint", komt hier tot
de verkeerde conclusie. Uitgewerkt als scenario `LEK_GEMASKEERD_DOOR_REGELING`.

Bij een kolom op 20 mbar is een ongecompenseerde temperatuurregeling daarmee geen afstemkwestie
maar een **ontwerpfout**.

---

### 8.31 `QUASI_STATIONAIRE_SCHEIDING` — de scheiding loopt hier zonder vertraging in (module 12)

Staat in `dynamics.js`, en is de **zwakste aanname van module 10**.

De module integreert de *inhoud* van de vaten in de tijd, maar niet de scheiding zelf: elke
tijdstap levert module 5 de samenstellingen alsof het kolomprofiel al helemaal is ingelopen. De
fout gaat de kant op die een operator niet verwacht — het model **onderschat de traagheid van de
zuiverheid** terwijl het de traagheid van de *inhoud* wel goed heeft. Na een refluxverandering is
de topzuiverheid in werkelijkheid niet na één tijdconstante van het refluxvat op zijn nieuwe
waarde maar veel later.

De reden om het zo te doen: module 8 (14.1) stelde al vast dat theoretische trappen in een
pakkingbed niet bestaan. Een trap-voor-trap dynamisch model zou dus **schijnnauwkeurigheid** zijn —
het rekent iets nauwkeurig uit dat er niet is. De eerlijke keuze is een grof model met een
duidelijk benoemde grens.

Om dezelfde reden wordt de samenstelling op het temperatuurmeetpunt **lineair geïnterpoleerd**
tussen top en bodem (`meetpunt()`, veld `isBenadering: true`). Daar zit geen wet onder en een echt
profiel is krom; het getal is bruikbaar voor de *richting* en de *orde*, niet als meetwaarde.

**Waar het knelt:** bij snelle transiënten. Een dichtslaande condensor of een plotseling lek
verandert de druk in seconden, en dan is juist de drukdynamiek het verhaal — die hier algebraïsch
is weggewerkt (8.32). Voor die gevallen geeft het model de *eind*toestand goed en het verloop
ernaartoe niet.

---

### 8.32 `STIJF_STELSEL_DRUK_ALGEBRAISCH` — de druk wordt niet geïntegreerd (module 12)

Staat in `dynamics.js`. Een numerieke keuze, onderbouwd met de tijdconstanten van 8.29.

De dampinhoud van deze kolom is bij 20 mbar ongeveer 20 kg tegen een dampstroom van 21,4 ton
per uur: τ = 3,3 s (zie 8.29). De bodemsump zit op tientallen minuten tot uren. Een integrator met een stap
die de druk netjes volgt zou dus ordes meer stappen nodig hebben dan de langzame toestanden
vragen. Dat is geen nauwkeurigheid maar rekenwerk zonder opbrengst, en het is precies wat een
**stijf stelsel** genoemd wordt.

De druk wordt daarom **algebraïsch** behandeld: per tijdstap berekend als de waarde waarop
dampproductie en afvoer in evenwicht zijn, niet geïntegreerd. Dat is de standaard
ingenieursbenadering voor een stelsel met deze spreiding en nauwkeurig voor alles wat langzamer
verloopt dan een paar seconden.

De langzame toestanden worden geïntegreerd met **RK4** (`rk4Stap()`, `integreer()`). Vierde orde
betekent dat de fout bij een halve stap ongeveer zestien keer kleiner wordt; de testsuite meet
die factor in plaats van hem aan te nemen (gemeten: 19,7 / 17,8 / 16,9 bij opeenvolgende
halveringen van dy/dt = −y).

**Waar het knelt:** bij verschijnselen die juist op de drukschaal spelen — een klep die dichtslaat,
een condensor die in korte tijd dichtkoekt, een lek dat opent. Daar geeft het model de nieuwe
evenwichtstoestand goed en de eerste seconden niet. Voor de trainingsdoelen aanvaardbaar, want een
operator grijpt niet in op een tijdschaal van seconden — maar het hoort erbij te staan. Als module
12 een animatie maakt waarin de druk "meebeweegt", is dat een illustratie en geen berekening.

---

### 8.33 `TWEE_SNEDEN_IN_EEN_KOLOM` — modules 4 en 5 rekenen aan verschillende bedrijfspunten (module 12)

**Met de dichtheid uit hoofdstuk 4**: elke snede met de dichtheid van haar eigen bodem, 730,9 kg/m³ voor K-01 en 722,0 kg/m³ voor K-02. Dan is de bodem bij de pitchsnede in 76,3 min rond en bij de fractioneringssnede in 10,3 min. De getallen hieronder zijn met een eerdere, aangenomen dichtheid van 850 kg/m³ gerekend.

Staat in `dynamics.js`. **Aan het licht gekomen bij het opzetten van de dynamiek**, omdat een
tijdconstante door de stroom wordt gedeeld en die stroom tussen de modules een factor 16 verschilt.

| | Module 4 (`balansUitBodemstroom`) | Module 5 (`ontwerp`) |
|---|---|---|
| Soort snede | pitchverwijdering / strippen | fractionering C16 tegen C18 |
| Sleutels | — (één evenwichtstrap) | C16:0 / C18:2, 98 % elk |
| Bodemstroom | 2–15 % van de voeding | **55,7 %** van de voeding |
| Bij F = 10 000 kg/h | 200–1500 kg/h | 5568 kg/h |
| x(pitch) in de bodem | 6,8–58,8 massa% | **3,59 massa%** |

Beide zijn **echte bedrijfsvoeringen** en in een vetzuurfabriek staan ze achter elkaar: eerst een
stripkolom die pitch afscheidt, dan een fractioneerkolom die C16 van C18 scheidt. Er is geen
tegenspraak in de wetten — het zijn twee geldige bedrijfspunten van dezelfde wetten.

Maar het zijn niet dezelfde kolom op hetzelfde moment, en **modules 6 tot 9 hebben allemaal het
bedrijfspunt van module 5 gebruikt.** De bodemtemperatuur van 246,9 °C (14.4) hoort dus bij een
bodem met 3,6 massa% pitch, terwijl de opconcentratieketen van 10.4 over de andere snede gaat.

**Voor een tijdconstante is dat beslissend.** Elke snede rekent met de bodem van haar eigen kolom: de pitchsnede met de bodem van K-01, de fractioneringssnede met
die van K-02, beide V = A · h met h = 0,15 m (20.13). τ = V · ρ / B, met ρ = 850 kg/m³.

| Snede | B | Bodem | V | **τ bodem** | Refluxvat | τ refluxvat | Ruime sump (3 m³) |
|---|---|---|---|---|---|---|---|
| Pitchsnede | 340 kg/h | K-01 | 0,591 m³ | **88,7 min** | V-01 | 8,0 min | 450 min |
| Fractioneringssnede | 5568 kg/h | K-02 | 1,32 m³ | **12,1 min** | V-02 | 5,0 min | 27,5 min |

Eigen sommen: 0,591 · 850 / 340 · 60 = 88,7 min; 1,32 · 850 / 5568 · 60 = 12,1 min (8,81 m² · 0,15 m = 1,32 m³, de
doorsnede van K-02 zoals het model haar nu geeft; zie de stand onder de tabel van 20.13). Het refluxvat: V-01 1275 kg bij
9600 kg/h destillaat zonder reflux, 8,0 min; V-02 vijf minuten bij half vol, een ontwerpkeuze (26.29).

**In beide snedes is de bodem de traagste buffer**, langer dan het refluxvat. Wat verschilt is hoeveel: bij de pitchsnede een
factor elf, bij de fractioneringssnede ruim een factor twee. De stroom staat in de noemer.

**Wat dat voor de operator betekent:**
- **De bodem dempt verstoringen.** Een schommeling in de voeding of de verwarming wordt in de bodem uitgesmeerd; de
  bodemstroom en de bodemsamenstelling volgen haar traag.
- **Een verandering is er pas later te zien.** Na een ingreep duurt het een paar tijdconstanten voordat de bodem op zijn
  nieuwe waarde staat: bij K-01 uren, bij K-02 ruim een half uur. Wie na tien minuten niets ziet, heeft niet te weinig
  gedaan.
- **De vloeistof blijft langer heet.** Elke minuut in de bodem is een minuut op bodemtemperatuur, en daar breekt het product
  af (thermische afbraak, module 9). Daarom houdt een ontwerper de bodem zo klein als de niveauregeling en de pomp toelaten.

**De les van voor ronde 4, vervallen.** Tot ronde 4 rekenden beide snedes met dezelfde kleine bodem: eerst 0,25 m³ (37,5 en
2,3 min), in ronde 3 de bodem van K-01 (88,7 en 5,4 min). Daaruit volgde: "bij de fractioneringssnede is de sump in minuten
rond en is de bodem **niet** het trage element van de kolom; bij de pitchsnede wel." Dat klopte alleen met een bodem die voor
de stroom van de fractioneringssnede te klein is. In `dynamics.js` staat zij als `vervallenAanname` in de notitie.

`vergelijkSneden()` rekent beide snedes door, elk met de bodem die zij meekrijgt. De keuze op projectniveau is gemaakt: het
zijn twee kolommen in serie (`installatie.js`, 8.34), en de getallen van module 4 en die van 5 tot 9 horen elk bij hun eigen
kolom.

---

### 8.34 `TWEE_KOLOMMEN_TWEE_VERHALEN` — in beide kolommen is de bodem traag, maar niet in dezelfde orde (module 11)

**Met de dichtheid uit hoofdstuk 4**: K-01 64,8 min (432 kg bij 400 kg/h), K-02 10,9 min (954 kg bij 5231 kg/h), 5,9 keer korter. De getallen hieronder zijn met een eerdere, aangenomen dichtheid van 850 kg/m³ gerekend.

Staat in `installatie.js`.

Elke kolom rekent met de bodem van haar eigen kolom, zoals in 8.33: de doorsnede van de kolom maal 0,15 m vloeistof
(20.13), τ = V · ρ / B met ρ = 850 kg/m³. In beide kolommen is de bodem het trage element, maar niet in dezelfde orde, en de
oorzaak is de bodem**stroom** en niet het vat:

| Kolom | Doorsnede | Bodem | Bodemstroom | τ bodem | τ refluxvat | Traagste element |
|---|---|---|---|---|---|---|
| K-01 | 3,94 m² | 0,591 m³ | 400 kg/h (4 % van F) | **75,4 min** | 8,0 min | **de bodem** |
| K-02 | 8,81 m² | 1,32 m³ | 5231 kg/h (52 % van F) | **12,9 min** | 5,0 min | **de bodem** |

Eigen sommen: 0,591 · 850 / 400 · 60 = 75,4 min en 1,32 · 850 / 5231 · 60 = 12,9 min. Het refluxvat: V-01 1275 kg bij
9600 kg/h, V-02 vijf minuten bij half vol (26.29).

K-02 heeft 2,2 keer het volume van K-01 en is toch 5,9 keer sneller rond. Het verschil zit in de noemer van τ, en de test
dwingt dat af — dat is `HOLDUP_IS_GEEN_BALANS` (8.28) in zijn praktische vorm.

**Tot v1.1.6** (21 oktober 2026) stond hier een tabel met voor beide kolommen dezelfde sump van
0,25 m³: 31,9 min bij K-01 en 2,4 min bij K-02, zodat het refluxvat van K-02 (toen 4,4 min) het trage element leek. Die
formulering staat als `vervallenAanname` in de notitie.

**Voor de bediening:** een operator die van de ene kolom naar de andere loopt, moet zijn
geduldsinstelling omzetten. In K-02 mag je na een half uur oordelen; in K-01 pas na bijna vier uur (drie tijdconstanten). De fout die
hieruit volgt is voorspelbaar: wie K-02 gewend is grijpt in K-01 te snel opnieuw in, en wie K-01
gewend is laat in K-02 een afwijking te lang lopen. Niet de kolom gedraagt zich onverwacht — de
tijdconstante verandert.

---

### 8.35 `AFBRAAK_ZIT_IN_DE_KOUDSTE_KOLOM` — K-02 is heter, maar K-01 doet de schade (module 11)

Staat in `installatie.js`. Een misvatting, en een contra-intuïtief resultaat.

| | K-01 | K-02 |
|---|---|---|
| Bodemtemperatuur | 237,8 °C | **250,1 °C** (12,3 K heter) |
| Afbraak**snelheid** t.o.v. K-01 | 1× | 1,2–2,0× sneller |
| Verblijftijd (de bodem van de eigen kolom, 8.34) | **75,4 min** | 12,9 min (5,9× korter) |
| **Afbraak*dosis*** | **2,9–4,9×** | 1× |
| Bodemmateriaal | 50 massa% pitch | schoon C18-rijk product |

Dosis is snelheid **maal** tijd, en bijna zes keer langer weegt zwaarder dan hooguit twee keer sneller.
De conclusie houdt over de hele Ea-band stand: zelfs bij de hoogste activeringsenergie — die de
temperatuur het zwaarst laat wegen — blijft de dosis in K-01 bijna drie keer die van K-02. De getallen komen uit
`afbraakPerKolom()`, die met de bodem van de eigen kolom rekent.

**Tot v1.1.6** (21 oktober 2026) stond hier een tabel met voor beide kolommen een sump van
0,25 m³ (31,9 tegen 2,4 min, 13× korter) en de bodemtemperaturen van toen (237,1 en 246,5 °C): een dosis van 6,1 tot 8,5×.
De les is dezelfde; met de bodem van de eigen kolom is het verschil kleiner.

Daar komt bij dat het in K-01 om het **verkeerde materiaal** gaat: die bodem is 50 massa% pitch,
precies de fractie die polymeriseert en aankoekt. De bodem van K-02 is een schoon product.

**Waarom dit misgaat:** wie op temperatuur stuurt, kijkt naar K-02 en laat K-01 met rust. Dat is
omgekeerd. De ingreep die in K-01 het meeste oplevert is niet koeler stoken — dat kan nauwelijks,
de bodem moet koken — maar de **verblijftijd** korter maken: een kleinere sump, en de bodemafname
niet verder dichtknijpen dan nodig.

**Hoe je het ziet:** aan waar de kleur ontstaat. De verkleuring die in K-01 ontstond is al aanwezig
voordat K-02 begint. En aan de aankoeking: in K-01 zit die op de reboiler en in de bodemleiding,
niet in het pakkingbed.

---

### 8.36 `PITCH_MAG_K02_NIET_IN` — de volgorde is niet vrij (module 11)

Staat in `installatie.js`, en onderbouwt waarom K-01 vóór K-02 staat.

| | K-01 | K-02 |
|---|---|---|
| Theoretische trappen | 6 | 31,4 |
| Bedhoogte | 2,7 m | 14,1 m |
| Drukval | 3,0 mbar | 15,7 mbar |
| Bodemtemperatuur | 237,1 °C | 246,5 °C |
| **Marge tot het mediumplafond** | **17,9 K** | **8,5 K** |

Bij een **verdubbelde** drukval staat de bodem van K-02 op 256,4 °C — boven het plafond van 255 °C,
dus verstikking (8.27, scenario `VERSTIKKING_DOOR_AANKOEKING`). K-01 komt zelfs bij een
**viervoudige** drukval niet boven 247 °C uit: daar is verstikking geen bedrijfsrisico.

De kolom die pitch verdraagt is dus die met weinig trappen en veel marge; de kolom die je schoon
wilt houden is die met 31 trappen en 8,5 K marge. **Daarom staat de vuile stap eerst.**

**Gevolg voor de bediening:** de pitchafscheiding in K-01 is een **bescherming van K-02** en niet
alleen een productstap. Zakt de scheiding in K-01 weg — te hoge bodemafname, uitdroging, meesleuren
bij te hoge belasting — dan slipt er pitch naar K-02 en begint daar een foulinglus in de kolom die
er het slechtst tegen kan. Het effect komt bovendien **vertraagd**: eerst weken oplopende drukval in
K-02, dan pas een probleem. Te zien aan de drukval over K-02 op weken-termijn, en direct aan een
pitch- of asgehalte in het destillaat van K-01 — dat laatste hoort in de routineanalyse.

---

### 8.37 `WAAR_DE_DAMP_ONTSTAAT` — voor de energiebalans is een reboiler een doos (module 9)

Staat in `energy.js`. De duty-berekening behandelt de reboiler als een doos waar kilowatts in gaan:
Q = V · ΔH_vap, en verder niets. Voor de **energie** klopt dat precies. Voor de **productkwaliteit**
is het onvolledig, en het ontbrekende stuk is één vraag: *waar ontstaat de damp?*

| Type | Waar de damp ontstaat | Kookt aan de wand? | Wat eruit komt |
|---|---|---|---|
| Ketelreboiler | in de mantel, rond de pijpenbundel | **ja** | droge damp |
| Thermosifon | in de pijpen, tijdens het opstijgen | **ja** | tweefasenmengsel |
| Geforceerde circulatie | pas bij intrede in de kolom (flashen) | **nee** | vloeistof die flasht |
| Vallende film | op het oppervlak van de neerlopende film | **ja** | damp plus restfilm |

**Bij drie van de vier kookt de vloeistof op warm metaal**, en dat metaal is de heetste plek van de
installatie (8.25). Bij geforceerde circulatie is dat bewust anders: de pomp houdt de vloeistof
onder zoveel druk dat zij in de pijpen alleen wordt *opgewarmd*, en het flashen gebeurt pas waar de
druk terugvalt op de kolomdruk. Daar is geen warm oppervlak.

Wat dat scheelt, doorgerekend met `reboilerkeuzeEnAfbraak()` voor K-01 (bulk 237,1 °C, olie 280 °C):

| | Temperatuurverschil over de film | Afbraak t.o.v. de bulk |
|---|---|---|
| Wandkoker (ketel, thermosifon, vallende film) | tot **42,9 K** | 6,2 tot 26,1× |
| Geforceerde circulatie | ~5 K | 1,26 tot 1,50× |
| **Winst** | | **factor 4,9 tot 17,3** |

Dat is de reden dat geforceerde circulatie in vervuilende en temperatuurgevoelige diensten wordt
gekozen — en het is een afweging die uit de energiebalans **nooit** volgt, want die telt alleen
kilowatts. De 5 K over de film is een aanname en geen meetwaarde; het exacte getal vraagt U en A,
en die zijn hier niet vastgelegd (12.2).

**Wat dit betekent voor E-01 en E-03 van deze installatie.** E-01 is een vallende film, gekozen om
de verblijftijd kort te houden (8.26) — maar de film ligt *op* de wand, dus het wandeffect vervalt
daar **niet**. Dat is een eerlijke beperking van de gekozen configuratie: zij lost de tijd op, niet
de temperatuur. E-03 hoeft geen vallende film te zijn, want de bodem van K-02 is een schoon product
met ruim 5200 kg/h doorstroming en dus al een korte verblijftijd; een thermosifon volstaat daar. Die
kookt wel in de pijpen, en dat is precies waarom pitchslip uit K-01 daar zo duur is (8.36).

Eén gevolg voor de scheiding: een **ketelreboiler** levert droge damp en telt daarom als een extra
evenwichtstrap; een thermosifon en een geforceerde circulatie leveren een tweefasenmengsel en doen
dat niet. Module 5 rekent daar niet mee — het aantal trappen is daar het resultaat van Fenske,
Underwood en Gilliland, zonder correctie voor het reboilertype.

### 8.38 De volumevergroting bij het flashen

`volumevergroting()` in `energy.js`, op de ideale gaswet uit hoofdstuk 13.

Bij het verdampen neemt het volume enorm toe, en bij vacuüm veel meer dan bij atmosferische druk.
Berekend met de **dampsamenstelling** en niet met die van de vloeistof — pitch verdampt niet, dus is
de damp veel lichter dan de bodemvloeistof:

| | Molmassa |
|---|---|
| Bodemvloeistof K-01 (25,3 mol% pitch) | 408,5 g/mol |
| Evenwichtsdamp daarboven (0 % pitch) | **269,5 g/mol** |

| Waar | Druk | Dampdichtheid | 1 m³ vloeistof wordt |
|---|---|---|---|
| Bodem K-01 | 23,0 mbar | 0,146 kg/m³ | **5818 m³** |
| Bodem K-02 | 35,7 mbar | 0,223 kg/m³ | 3816 m³ |
| Ter vergelijking, atmosferisch | 1013,25 mbar | 6,437 kg/m³ | 132 m³ |

Een factor **44** tussen vacuüm en atmosferisch. Twee gevolgen die aan de installatie te zien zijn:

1. **De leiding van de reboiler naar de kolom is vele malen groter dan de toevoerleiding.** Dezelfde
   massastroom vraagt aan de dampzijde bijna zesduizend keer zoveel volume. Een leiding die bij
   atmosferische druk ruim zou zijn, is hier veel te krap — en te krap betekent drukval, en drukval
   betekent een hetere bodem (8.21).
2. **Waar het flashen gebeurt, is een mechanische kwestie.** Op de plek waar de vloeistof verdampt,
   neemt het volume in enkele meters duizenden keren toe en dus ook de snelheid. Gebeurt dat in een
   bocht, tegen een wand of in een te nauwe doorgang, dan is dat erosie en trilling. Bij geforceerde
   circulatie wordt het flashpunt bewust in de kolomintrede gelegd: daar is ruimte en daar staat
   geen warmtewisselaar in de weg. Het verplaatst het probleem naar een plek waar het te verdragen
   is, in plaats van het weg te nemen.

Beide getallen staan hier als afgeleide waarde en zijn door de testsuite herberekend; de
vloeistofdichtheid was toen een aanname; zij komt nu uit hoofdstuk 4.

---

## 9. Evenwicht en samenstelling (module 3)

### 9.1 Wetten, niet stofdata

Module 3 voegt **geen enkele nieuwe stofwaarde** toe. Raoult, Dalton en de relatieve
vluchtigheid zijn wetten; alle getallen die eruit komen, komen uit de dampspanningsdata van
hoofdstuk 2. Er is dus ook niets nieuws te valideren tegen literatuur — wat er te valideren
was, staat in 2.4.

| Wet | Vorm | Aanname |
|---|---|---|
| Raoult | p_i = x_i · p_sat,i(T) | ideaal vloeistofmengsel, γ = 1 (8.5) |
| Dalton | p_tot = Σ p_i, y_i = p_i / p_tot | ideaal gasmengsel; dimerisatie niet verwerkt (8.2) |
| Relatieve vluchtigheid | α_ij = p_sat,i / p_sat,j | volgt uit de twee bovenstaande |

α volgt uit Raoult en Dalton en erft dus hun aannames. De totale druk valt in de verhouding
weg: **α hangt alleen van de temperatuur af, niet van de druk.** Vacuüm is in deze kolom dus
geen middel om beter te scheiden, maar om bij een lagere temperatuur te kunnen werken.

**Numerieke methode.** Bubbel- en dauwpunt worden met **bisectie** opgelost, niet met
Newton-Raphson. Σ x_i · p_sat,i(T) loopt monotoon op met T, dus heeft de vergelijking precies
één oplossing en kan bisectie er niet naast grijpen. Newton is sneller maar kan bij deze
steile exponentiële krommen wegschieten, en een onzinnig getal op een scherm in de
controlekamer is erger dan een paar microseconden rekentijd.

### 9.2 Voedingssamenstelling

**Generiek en afgerond; geen bedrijfsdata en geen meting.** Vastgelegd in
`VOEDING_GENERIEK` in `components.js`, als samenstelling van een *stroom* en niet als
stofdata van een component.

De basis is **massaprocent**, want zo rapporteert een vetzuuranalyse. De molfracties worden
daaruit afgeleid met `massafractiesNaarMolfracties()` en staan nergens los opgeslagen, zodat
de twee sets niet uit elkaar kunnen lopen.

| Component | Massa% | Molfractie (mol%) |
|---|---|---|
| C14:0 | 1 | 1,20 |
| C16:0 | 44 | 46,86 |
| C18:0 | 5 | 4,80 |
| C18:1 | 38 | 36,74 |
| C18:2 | 10 | 9,74 |
| Zwaar residu (pitch) | 2 | 0,68 |
| **Totaal** | **100** | **100** |

Gemiddelde molmassa: **273,07 g/mol**. Dat is de brug die module 4 nodig heeft om een
voeding in kg/h naar mol/s om te rekenen.

**Waarom de pitchfractie erin hoort.** Vetsplitsing haalt een splitsingsgraad van 96 tot
99 %, dus er blijven altijd ongesplitste glyceriden achter. Zonder die fractie heeft de
bodemstroom van de kolom geen bestaansreden: er valt dan niets af te scheiden en de
driedeling top / destillaat / pitch valt weg.

**Let op welke basis waar geldt.** Tussen de vetzuren onderling schelen de molmassa's weinig,
dus daar verschuift de basis maar een paar procentpunt (44 massa% C16:0 wordt 46,9 mol%). Bij
pitch is het verschil bijna een factor drie: 2 massa% wordt 0,68 mol%, omdat tripalmitine
(807 g/mol) ruim drie keer zo zwaar is als palmitinezuur (256 g/mol). Wie een analyse in
massaprocent naast een evenwichtsberekening legt, moet dus eerst omrekenen.
De testsuite toetst de omrekening heen en terug en controleert dat beide sets op 100 %
sluiten.

**De vetzuren: binnen Codex CXS 210-1999**. Codex Alimentarius, *Standard for Named
Vegetable Oils* (CODEX STAN 210-1999), tabel 1, "Fatty acid composition of vegetable oils as determined by gas liquid
chromatography from authentic samples (expressed as percentage of total fatty acids)", kolom **Palm oil**, gelezen in de tekst van
de norm zoals de FAO die publiceert (www.fao.org/4/y2774e/y2774e04.htm), cel voor cel uit de HTML-tabel. De norm geeft de
samenstelling als percentage van de **vetzuren**; het model geeft massaprocent van de **voeding**, met 2 % pitch. Daarom staat
hieronder de modelwaarde ook genormaliseerd op de 98 % vetzuur.

| Vetzuur | Codex, palmolie (% van de vetzuren) | Model (massa% voeding) | Model, genormaliseerd op 98 % |
|---|---|---|---|
| C12:0 | ND – 0,5 | 0 | 0 |
| C14:0 | 0,5 – 2,0 | 1 | 1,02 |
| C16:0 | 39,3 – 47,5 | 44 | 44,90 |
| C16:1 | ND – 0,6 | 0 | 0 |
| C17:0 | ND – 0,2 | 0 | 0 |
| C18:0 | 3,5 – 6,0 | 5 | 5,10 |
| C18:1 | 36,0 – 44,0 | 38 | 38,78 |
| C18:2 | 9,0 – 12,0 | 10 | 10,20 |
| C18:3 | ND – 0,5 | 0 | 0 |
| C20:0 | ND – 1,0 | 0 | 0 |
| C20:1 | ND – 0,4 | 0 | 0 |
| C22:0 | ND – 0,2 | 0 | 0 |

(ND: niet aantoonbaar.) De vijf vetzuren van het model liggen alle binnen hun band. De kleine vetzuren die het model niet kent,
samen hoogstens ongeveer 3,4 % volgens de bovengrenzen van de norm, zitten in het model in de vijf grote. **Twee aannames:** dat de
vetzuurverdeling bij het splitsen niet verandert (de norm beschrijft de olie, de kolom krijgt de gesplitste vetzuren); en dat de
waarden van 1999 nog gelden. De norm is daarna meermaals gewijzigd (laatst in 2026, met in juli 2026 een correctie op juist de
rijen van palmolie in de nieuwe opmaak); de rij van palmolie in de huidige druk is hier niet cel voor cel gelezen. De oudere bron
uit 24 (`A4_VOEDINGSSAMENSTELLING`, Japir e.a. 2018) blijft staan als tweede, voor het mengsel na splitsing.

**De pitch: 2 massa%, een ontwerpkeuze**. De pitch staat voor de ongesplitste
glyceriden (en in werkelijkheid ook polymeren). Bij een splitsingsgraad van **98 %** blijft ongeveer 2 % ongesplitst; de
gebruikelijke band van de splitsingsgraad is 96 tot 99 %, dus de pitch ligt **tussen ongeveer 1 en 4 %**. Een openbare opgave voor
de bovenkant van continu splitsen onder hoge druk (250–260 °C, ongeveer 50 bar) is "up to 98 %" (octrooi CA 2,360,986; alleen uit
een samenvatting gelezen). **De koppeling met de bodemafname van K-01.** K-01 trekt 4 % van de voeding als bodem
(`B_procent` in `installatie.js`). Alle pitch gaat naar de bodem, dus het pitchgehalte van de bodem is w_pitch,bodem = z_pitch /
(B/F) = 2 / 4 = **50 massa%**. De twee getallen horen bij elkaar:
- meer pitch bij dezelfde bodemafname maakt de bodem rijker aan pitch, dus heter: bij 2,4 % pitch 60 % in de bodem en 3,1 K
  heter, en in het scenario voedingstrip loopt K-01 dan naar een grens van het model;
- bij 4 % pitch zou de bodem helemaal uit pitch bestaan, en dat is onhaalbaar: dan moet ook de bodemafname omhoog.
Wie de pitch verandert, beslist dus ook over de bodemafname.

### 9.3 De topdruk, en de afgeleide waarden bij kolomcondities

**De topdruk van K-01 en K-02: 20 mbar, een keuze van het model, niet van de praktijk**.
Echte vetzuurkolommen draaien vaak op 2 tot 10 mbar aan de top:
- US 4,595,461 (1986), achtergrond en voorbeeld 1: vetzuurdestillatie wordt "generally carried out at 2 to 10 mbars"; in het
  voorbeeld 3 mbar in de top en 9 mbar in de bodem. Gelezen via de samenvatting en de tekst van het octrooi.
- US 2006/0006056 A1: "between 1 and 50 mbar, preferably between 5 and 10 mbar". Gelezen.
- Schulz, Rietfort, Jansen en Olujić, *Chem. Eng. Trans.* 69 (2018) 763–768, §1: vetzuurkolommen werken "well below 0.1 bara".

**Waarom het model toch op 20 mbar ontwerpt.** De dampspanningsdata van hoofdstuk 2 (Falleiro 2012) zijn gemeten vanaf 1333 Pa,
ongeveer 13 mbar. Daaronder extrapoleert de Antoine-vergelijking, en dat doet zij hier onbetrouwbaar. Een
proefberekening heeft het model op 10 mbar doorgerekend: dan valt 98 % van de voeding
onder het gemeten venster; de relatieve vluchtigheid van C16:0 tegen C18:2 heeft in de doorgetrokken krommen een top rond 225 °C en
daalt daaronder, zodat R en N van K-02 bij een lagere druk **stijgen** (R 3,82 → 4,21, N 39 → 42), tegen de verwachting uit
Clausius-Clapeyron in; en bij 172,6 °C (C16:0 dan op 1,6 mbar) kruisen de krommen van C16:0 en C18:2, en draaien de sleutels om.
Dat is een grens van de data, niet van het proces. Op het scherm staat het bij het vacuümsysteem (`apparaten.js`). Een topdruk van
10 mbar vraagt dampspanningsdata onder 13 mbar; dat staat open voor een volgende versie.

**De afgeleide waarden.** Uit de bovenstaande samenstelling en de dampspanningsdata van hoofdstuk 2, bij **20 mbar**.
Alle waarden zijn door de testsuite herberekend; ze staan hier ter controle, niet als
invoer.

| Grootheid | Waarde |
|---|---|
| Bubbelpunt van de voeding (inclusief 0,68 mol% pitch) | 223,2 °C |
| Bubbelpunt van alleen de vetzuurfractie | 223,0 °C |
| Dauwpunt van die vetzuurfractie | 226,0 °C |
| y(C16:0) in de damp bij het bubbelpunt | 61,5 mol% (tegen 46,9 mol% in de vloeistof) |
| α(C16:0 / C18:0) bij 223,2 °C | 2,134 |
| α(C18:1 / C18:0) bij 223,2 °C | 1,081 |
| Bodemtemperatuur bij 10 → 30 mbar | 205,9 → 234,0 °C, dus 28,1 K |

Het bubbel- en het dauwpunt in de tabel horen bij de **pitchvrije** vetzuurfractie, want
een vloeistof met een niet-vluchtige component heeft in dit model geen dauwpunt: p_sat = 0
geeft K = 0, en daar loopt de dauwpuntvergelijking op vast. `dauwpunt_K()` weigert zo'n
damp dan ook met een expliciete foutmelding in plaats van een NaN. Om dezelfde reden is de
thermodynamische rondgang bubbelpunt → damp → dauwpunt alleen exact voor een pitchvrije
vloeistof; mét pitch komt het dauwpunt van de damp 0,17 K **lager** uit dan het bubbelpunt
waar hij van afkwam, omdat de eerste druppel geen pitch bevat en dus een ander mengsel is.
De testsuite toetst beide gevallen.

De onderste regel van de tabel is het mengselequivalent van de gevoeligheid uit hoofdstuk 2: bij een
echte voeding kost 20 mbar vacuümverlies ruim 28 K bodemtemperatuur, vergelijkbaar met de bijna
29 K die de zuivere stof C16:0 geeft.

**Extrapolatie.** Bij 223,2 °C valt **C14:0** buiten het geldigheidsbereik van zijn eigen
correlatie (2.8). Dat betreft 1,2 mol% van het mengsel. `bubbelpuntMetContext()` meldt welke
componenten het zijn en hoeveel mol% ze samen vormen; **de UI moet die melding tonen.**

---

### 9.4 De bodem is niet de voeding

Vastgelegd als misvatting in `src/content/misvattingen.js`
(`VOEDING_IS_NIET_DE_BODEM`), omdat het geen ontbrekende kennis is maar **foute** kennis:
de voedingsanalyse is het enige spectrum dat op papier staat, dus wordt de daaruit
berekende kooktemperatuur gelezen als de bodemtemperatuur.

Dat is niet zo. Elke hoeveelheid damp die omhoog gaat neemt verhoudingsgewijs meer van de
lichte componenten mee, dus is de bodem verarmd aan C14 en C16, opgeconcentreerd in C18 en
pitch, en kookt bij dezelfde druk **warmer**.

Doorgerekend bij 20 mbar vanaf `VOEDING_GENERIEK` (bubbelpunt 223,2 °C):

| Afgedestilleerd | Bubbelpunt van de rest | Erbij | x(C16:0) | x(pitch) |
|---|---|---|---|---|
| — (voeding) | 223,2 °C | — | 46,9 mol% | 0,68 mol% |
| 30 % | 224,5 °C | +1,3 K | 41,4 mol% | 0,97 mol% |
| 50 % | 225,6 °C | +2,4 K | 36,2 mol% | 1,35 mol% |
| 70 % | 227,4 °C | +4,2 K | 28,3 mol% | 2,25 mol% |
| 90 % | 231,3 °C | **+8,1 K** | 14,0 mol% | **6,8 mol%** |

**Rekenmethode, en wat het niet is.** Dit is een **Rayleigh**-berekening: er wordt
stapsgewijs damp weggehaald en de dampsamenstelling wordt elke stap opnieuw bepaald, want
hoe armer de rest aan lichte componenten raakt, hoe armer ook de damp die er nog afkomt.
Het is géén kolomberekening — een echte kolom heeft reflux en een continue balans, en de
werkelijke bodemsamenstelling volgt uit de massabalans van **module 4**. De tabel toont de
richting en de orde van grootte, niet meer. Het is bovendien een numerieke integratie, dus
het resultaat hangt licht van de stapgrootte af; het aantal stappen (400) staat daarom als
data vastgelegd. De testsuite rekent de hele tabel opnieuw uit.

**Waarom dit juist bij een goed werkende kolom misgaat.** Bij een ondiepe cut lijkt de
voedingstemperatuur nog een redelijke schatting (1,3 K ernaast bij 30 %). Bij 90 %, wat een
kolom hoort te halen, zit je er ruim 8 K naast — en is de pitchfractie een factor tien
hoger geworden, wat via 8.6 nog eens extra temperatuur kost. De twee effecten versterken
elkaar en wijzen dezelfde kant op: te optimistisch.

**De 8,1 K is een ondergrens, geen kolomresultaat.** Twee redenen. De rekenwijze is een
flashbenadering, en 90 % afgedestilleerd is voor een vetzuurkolom een **ondiepe** cut: de
bedoeling is juist zo veel mogelijk vetzuur als destillaat terug te winnen en zo weinig
mogelijk product met de pitch te verliezen, dus is de bodemstroom eerder een paar procent
van de voeding. Bij zo'n diepe cut concentreert de pitch veel sterker op:

| Afgedestilleerd | Bodemstroom | Bubbelpunt | Erbij | x(pitch) |
|---|---|---|---|---|
| 90 % | 11,5 massa% | 231,3 °C | +8,1 K | 6,8 mol% |
| 95 % | 6,4 massa% | 234,4 °C | +11,3 K | 13,5 mol% |
| 97 % | 4,4 massa% | 238,0 °C | +14,8 K | 22,5 mol% |
| 98 % | 3,4 massa% | 242,5 °C | **+19,3 K** | 33,8 mol% |

Let op de kolom **bodemstroom in massa% van de voeding**: dat is het getal waarin een
operator denkt. Bij een bodemstroom van een paar procent staat de bodem geen 8 maar 15 tot
20 K warmer dan de voedingsanalyse suggereert — een andere orde van grootte, geen correctie
in de marge. Het echte getal komt uit de massabalans van **module 4**.

Bij 22 tot 34 mol% pitch belast deze berekening wel de ideaal-mengselaanname het zwaarst
die er in de module zit (8.5): een vetzuurmolecuul zit daar voor een derde tussen
triglyceriden. Hoe dieper de cut, hoe zachter het getal.

**Wat die onderschatting kost — vooruitwijzing naar module 9.** Een verschil van 8 K op een
bodemtemperatuur ziet op een scherm uit als niets, maar de afbraaksnelheid loopt
exponentieel met de temperatuur. Over 8 K komt dat ruwweg neer op een **verdubbeling**. Wie
de bodemtemperatuur uit de voedingssamenstelling afleidt, onderschat dus ook ongeveer de
**helft** van de verkleuring en de pitchvorming. Daarmee sluit de keten zich: te warme bodem
→ meer pitch → pitch verdringt de vetzuren (8.6) → nog warmere bodem → nog sneller afbraak.

> **Deze factor 2 is een VUISTREGEL, geen berekening.** Er is in deze module geen
> activeringsenergie vastgelegd — hoofdstuk 5 is nog leeg. Een factor 2 over 8,09 K hoort bij
> Ea ≈ **178 kJ/mol** (ln 2 = Ea/R · (1/496,3 − 1/504,4)). Die orde van grootte is niet
> onwaarschijnlijk voor thermische afbraak en polymerisatie van vetzuren, maar is **hier niet
> met een bron onderbouwd**. Bij Ea = 100 kJ/mol is de factor 1,5; bij 250 kJ/mol is hij 2,6.
> De boodschap — een factor van die orde, geen paar procent — staat overeind; het precieze
> getal niet. De testsuite toetst wat hier wél te toetsen valt: dat de opgegeven Ea, de
> opgegeven factor en het opgegeven temperatuurverschil onderling consistent zijn, zodat
> module 9 het kan narekenen zodra er een Ea met bron is.

Gebruik de kooktemperatuur van de voeding daarom als **ondergrens** voor de bodem, en niet
als norm.

---

## 10. Massabalans (module 4)

### 10.1 Wetten, niet stofdata

Module 4 voegt **geen enkele nieuwe stofwaarde** toe. Behoud van massa is een behoudswet, en
Rachford-Rice is geen nieuwe wet maar de combinatie van die balans met het evenwicht uit
module 3.

| Wet | Vorm | Aanname |
|---|---|---|
| Behoud van massa | F = D + B; F·z_i = D·x_D,i + B·x_B,i | stationair; géén reactie (8.8) |
| Rachford-Rice | Σ z_i(K_i − 1) / (1 + Ψ(K_i − 1)) = 0 | één evenwichtstrap; alle aannames van 9.1 |

**Afleiding van Rachford-Rice.** De componentbalans over één trap is
z_i = Ψ·y_i + (1 − Ψ)·x_i; het evenwicht is y_i = K_i·x_i. Substitueren en x_i vrijmaken geeft
x_i = z_i / (1 + Ψ(K_i − 1)); eisen dat de x-en op 1 sluiten geeft de vergelijking. Er komt
dus geen nieuwe fysica bij.

**Numerieke methode.** Opgelost met **bisectie** op de temperatuur, niet met Newton-Raphson.
De residufunctie loopt monotoon op met T: elke K_i doet dat, en de afgeleide van elke term
naar K_i is 1/(1 + Ψ(K_i − 1))², dus positief. Bisectie kan er daarmee niet naast grijpen.
Zelfde keuze en zelfde reden als bij het bubbelpunt in 9.1.

**Basis.** Dit is de module waar massa en mol elkaar het hardst tegenkomen: de DCS toont
kg/h, het lab rapporteert massa%, het evenwicht rekent in molfracties. De voorspellende kant
rekent intern in mol en rapporteert daarnaast in kg/h; de controlerende kant
(`componentBalans()`) **eist** dat de basis expliciet wordt benoemd en weigert anders te
rekenen.

### 10.2 De bodemstroom als ingang

De module is om de **bodemstroom** heen gebouwd en niet om het terugwinningspercentage. Een
operator stelt nooit een terugwinning in — dat is de uitkomst van de labanalyse waarop hij
wordt afgerekend. De afname onderuit is wel een knop (met de kanttekening van 8.7).

De bodemstroom komt daarom altijd in **beide** eenheden terug: kg/h zoals de DCS hem toont,
en procent van de voeding zoals de operator denkt. De terugwinning komt ernaast te staan als
**resultaat**.

**Let op de basisverschuiving.** De bodem is veel zwaarder per mol dan de voeding — 372 tegen
273 g/mol, want hij is rijk aan pitch. Een bodemafname van 5 **massa**% is daarmee maar
3,7 **mol**% van de voeding, en de dampfractie dus 96,3 mol% en niet 95. Wie de twee
gelijkstelt, rekent de flash verkeerd.

### 10.3 De harde ondergrens uit het massabehoud

Pitch heeft K = 0 en kan niet verdampen, dus **moet** alle pitch die erin gaat er onderuit
weer uit. De bodemstroom kan daarmee nooit kleiner zijn dan de pitchstroom zelf:

```
B_min / F = Σ( z_i · M_i van de niet-vluchtige componenten ) / M_voeding
          = het massaprocent pitch in de voeding
```

Voor `VOEDING_GENERIEK` is dat **2,00 massa%** — bij 10 000 kg/h voeding dus 200 kg/h. Dit
getal volgt puur uit het massabehoud en heeft niets met de kwaliteit van de scheiding te
maken: geen enkele kolom komt eronder.

Bij die grens bestaat de bodem uit **zuivere pitch**, en zuivere pitch kookt niet — de
benodigde temperatuur loopt naar oneindig. Het is dus een limiet en geen werkpunt.
`dampfractieUitBodemfractie()` weigert een kleinere bodemstroom met een expliciete
foutmelding. Praktisch ligt de grens eerder, al is het maar om de pitch verpompbaar te
houden.

### 10.4 Afgeleide waarden: de afweging in cijfers

Bij 10 000 kg/h `VOEDING_GENERIEK` en **20 mbar**, met één evenwichtstrap. Alle waarden zijn
door de testsuite herberekend.

| Bodemafname | Bodemstroom | Bodemtemperatuur | t.o.v. voeding | x(pitch) | Terugwinning C16:0 |
|---|---|---|---|---|---|
| 15 % | 1500 kg/h | 226,9 °C | +3,7 K | 4,9 mol% | 90,5 % |
| 10 % | 1000 kg/h | 227,8 °C | +4,7 K | 7,8 mol% | 94,2 % |
| 5 % | 500 kg/h | 231,1 °C | +7,9 K | 18,4 mol% | 97,9 % |
| 3 % | 300 kg/h | 239,3 °C | +16,1 K | 40,4 mol% | 99,3 % |
| **2 %** | **200 kg/h** | — | — | 100 mol% | — (limiet, zie 10.3) |

Dit is de centrale afweging van de kolom: **de knop die de terugwinning verbetert, is dezelfde
knop die de bodem heter maakt.** Van 15 naar 3 % bodemafname gaat de terugwinning van 90,5
naar 99,3 %, en de bodemtemperatuur van 227 naar 239 °C. De laatste procenten terugwinning
zijn bovendien de duurste: per procentpunt kosten ze steeds meer graden.

**Deze waarden zijn een ondergrens.** Één evenwichtstrap strippt slechter dan differentieel
destilleren en veel slechter dan een kolom met pakking in tegenstroom. Bij dezelfde
bodemstroom liggen de Rayleigh-waarden van 9.4 dan ook 3,8 tot 6,2 K **hoger**:

| Bodemstroom | Eén trap (module 4) | Differentieel (9.4) | Verschil |
|---|---|---|---|
| 11,5 massa% | 227,5 °C | 231,3 °C | 3,8 K |
| 6,4 massa% | 229,5 °C | 234,4 °C | 5,0 K |
| 4,4 massa% | 232,3 °C | 238,0 °C | 5,7 K |
| 3,4 massa% | 236,3 °C | 242,5 °C | 6,2 K |

De ordening is te beredeneren en geen tegenspraak: bij één trap komt álle damp in één keer
met de eindvloeistof in evenwicht, dus blijft er relatief veel lichte component in de bodem
achter. Differentieel destilleren laat elke hoeveelheid damp vertrekken in evenwicht met een
steeds armere vloeistof en strippt dus beter. Een kolom in tegenstroom strippt beter dan
beide, dus ligt de **werkelijke** bodemtemperatuur boven alle waarden in deze tabellen.
Module 5 kwantificeert dat. Merk ook op dat het verschil tussen de modellen oploopt als de
bodemstroom krimpt: hoe dieper je strippt, hoe meer het uitmaakt *hoe* je strippt.

---

## 11. Scheiding (module 5)

### 11.1 Wetten, een correlatie, en geen stofdata

Module 5 voegt **geen enkele nieuwe stofwaarde** toe. Alle α-waarden komen uit de
dampspanningsdata van hoofdstuk 2, via het evenwicht van hoofdstuk 9.

| Methode | Vorm | Status |
|---|---|---|
| Fenske | N_min = ln[(x_LK/x_HK)_D · (x_HK/x_LK)_B] / ln(α) | afgeleid — **wet** |
| Underwood | Σ α_i z_i/(α_i − θ) = 1 − q; R_min + 1 = Σ α_i x_D,i/(α_i − θ) | afgeleid — **wet** |
| McCabe-Thiele | werklijnen uit de massabalans + de evenwichtslijn | afgeleid — **wet** |
| Gilliland | Y = 1 − exp[(1+54,4X)/(11+117,2X) · (X−1)/√X] | **gefit — géén wet** (8.10) |

Fenske en Underwood rekenen allebei een **grens** uit, en allebei een andere: Fenske neemt
oneindige reflux (dus zo min mogelijk trappen), Underwood oneindig veel trappen (dus zo min
mogelijk reflux). Geen van beide is een kolom die je kunt bouwen. De brug ertussen is Gilliland,
en dat is de zwakste schakel van de module.

**Numerieke methode.** De wortel θ van Underwood wordt met **bisectie** gezocht, strikt tussen
α van de zware sleutel (1) en α van de lichte sleutel. Tussen die twee polen loopt de functie
monotoon van −∞ naar +∞ — de afgeleide van elke term naar θ is α_i·z_i/(α_i−θ)², dus positief —
zodat er precies één wortel ligt.

**Dit zijn theoretische trappen.** Hoeveel meter pakking daarbij hoort, hangt af van het
rendement per trap; dat is module 8. De reboiler telt in Fenske en Gilliland als trap mee.

### 11.2 De sleutelkeuze

Zie 8.9. Lichte sleutel **C16:0**, zware sleutel **C18:2**, met de waarschuwing dat de zware
sleutel de zwakst onderbouwde component van de set is.

### 11.3 Afgeleide waarden: een kolom voor deze scheiding

Specificatie: 98 % terugwinning van C16:0 in het destillaat, 98 % van C18:2 in de bodem, bij
**20 mbar**, met de voeding als kokende vloeistof (q = 1). Alle waarden zijn door
de testsuite herberekend.

| Grootheid | Waarde |
|---|---|
| Destillaat / voeding | 0,473 mol/mol |
| Toptemperatuur | 215,1 °C |
| Bodemtemperatuur | 232,1 °C |
| α(C16:0 / C18:2), meetkundig gemiddeld | 1,638 |
| **N_min** (Fenske) | **15,8 trappen** |
| θ (Underwood) | 1,160 |
| **R_min** (Underwood) | **2,34** |
| R bij 1,3 × R_min | 3,04 |
| **N** (Gilliland) | **31,5 trappen** |
| N (McCabe-Thiele, pseudo-binair) | 26 trappen |

Het aantal trappen bij een gebruikelijke reflux komt hier op ruwweg **twee keer N_min** uit —
een bekende vuistregel, handig om een uitkomst op plausibiliteit te toetsen.

De afweging tussen reflux en hoogte, met Gilliland:

| R / R_min | R | N |
|---|---|---|
| 1,1 | 2,58 | 39,9 |
| 1,2 | 2,81 | 34,8 |
| 1,3 | 3,04 | 31,5 |
| 1,5 | 3,51 | 27,5 |
| 2,0 | 4,68 | 23,1 |

De eerste stappen boven R_min leveren het meest op; daarboven wordt het steeds minder. Dat
verband klopt ook al is het getal niet hard (8.10).

---

### 11.4 `CORRELATIE_KIRKBRIDE` — de plaats van de voeding

| Methode | Vorm | Status |
|---|---|---|
| Kirkbride | N_R / N_S = [(B/D) · (z_HK/z_LK) · (x_LK,B / x_HK,D)²]^0,206 | **gefit — géén wet** |

**Bron:** Kirkbride, C. G. (1944), *Process design procedure for multicomponent fractionators*,
Petroleum Refiner **23** (9). De oorspronkelijke publicatie is **niet ingezien**; de vorm en de
exponent 0,206 zijn zoals ze algemeen in handboeken over scheidingsprocessen worden geciteerd.
Het paginanummer wordt in de secundaire literatuur verschillend geciteerd (87 of 321) en is niet nagegaan. Over het
oorspronkelijke fitgebied is geen bron gevonden (19 november 2026); dat de vergelijking hier geldt, is een aanname van het model.

**Waarom een correlatie.** De exponent volgt nergens uit; het is een fitparameter, net als 54,4
en 117,2 bij Gilliland (8.10). Kirkbride staat erom bekend dat hij bij scherpe scheidingen de
voeding een paar trappen naast de optimale plek kan leggen. Hij is hier gebruikt omdat hij de
gebruikelijke aanvulling op Fenske-Underwood-Gilliland is, niet omdat hij nauwkeurig is.

**Invoer.** Dezelfde sleutels als 11.2 (C16:0 / C18:2), de voeding van K-02 en de producten uit de
specificatie; B/D is **molair**. N is dezelfde N als in `drukprofiel()`, zodat de twee secties
samen precies het bed vullen dat module 8 uitrekent. De getallen staan niet in dit bestand maar
komen uit `installatie.js` (`N_R`, `N_S`); de testsuite rekent ze na.

**Plausibiliteitstoets.** De pseudo-binaire McCabe-Thiele van module 5 legt de voedingstrap
onafhankelijk hiervan; de testsuite eist dat de twee binnen enkele trappen van elkaar blijven. Een
groot verschil zou betekenen dat een van beide buiten zijn gebied werkt.

**Niet van toepassing op K-01.** Pitch komt niet in het destillaat, dus x_HK,D = 0 en de formule
bestaat niet. Zie 17.6.

## 12. Energiebalans (module 6)

### 12.1 Wetten, en één nieuwe soort gegeven

Module 6 voegt **geen stofwaarden voor de vetzuren** toe: de verdampingswarmte komt uit
hoofdstuk 3, de smeltpunten staan in 12.4. Wat er wél bij komt is een nieuw soort gegeven — het
**temperatuurniveau van een verwarmingsmedium**, dat geen stofeigenschap is maar een
procesgegeven.

| Wet | Vorm | Gebruik |
|---|---|---|
| Behoud van energie | F·h_F + Q_reb = D·h_D + B·h_B + Q_cond | duty's, kwantitatief |
| Warmteoverdracht | Q = U·A·ΔT_log | **kwalitatief** — zie 12.2 |

### 12.2 Waarom warmteoverdracht hier alleen kwalitatief wordt gebruikt

Er is **geen warmteoverdrachtscoëfficiënt U en geen oppervlak A vastgelegd**, en er is geen bron
om ze aan te ontlenen. De module rekent dus géén duty uit een temperatuurverschil en géén
wandtemperatuur.

Waar de wet wél voor dient is de onontkoombare gevolgtrekking: zonder temperatuurverschil geen
warmtestroom, dus moet het medium altijd heter zijn dan het proces, met een werkbare marge. Die
marge `ΔT_min` staat op **25 K** en is een **ontwerpkeuze, geen natuurconstante**: bij een kleinere
marge heb je meer oppervlak nodig, bij een grotere een heter medium — en dat kost
productkwaliteit aan de wand (8.13). Hij staat als parameter in de code zodat zichtbaar is wat
hij met het plafond doet.

**Verwarmingsmedia.** Generieke, afgeronde procesgegevens (`VERWARMINGSMEDIA` in `energy.js`).
De twee stoomwaarden komen uit de stoomtabel van NIST (onder de tabel); de olietemperatuur is sinds v1.1.0 als
ontwerpkeuze gemarkeerd (zie onder de tabel).

| Medium | Aanvoer | Herkomst |
|---|---|---|
| Thermische olie | 280 °C | ontwerpkeuze binnen een grens; rond praktijkgetal voor een oliecircuit in de oleochemie |
| Verzadigde stoom 40 bar | 250 °C | de verzadigingstemperatuur bij 40 bar absoluut, 250,4 °C (eigen interpolatie in de tabel), afgerond |
| Verzadigde stoom 10 bar | 180 °C | de verzadigingstemperatuur bij 10 bar absoluut, 179,9 °C (eigen interpolatie in de tabel), afgerond |

**De stoomtabel.** NIST Chemistry WebBook, SRD 69, "Thermophysical Properties of Fluid Systems", water, verzadiging naar druk, met de
toestandsvergelijking van W. Wagner en A. Pruß, "The IAPWS Formulation 1995 for the Thermodynamic Properties of Ordinary Water Substance
for General and Scientific Use", *J. Phys. Chem. Ref. Data* 31 (2) (2002) 387–535, doi 10.1063/1.1461829. De tabel is ingezien op
19 november 2026: 179,50 °C bij 9,9131 bar en 180,12 °C bij 10,056 bar; 249,92 °C bij 39,711 bar en 250,55 °C bij 40,130 bar.
"Bar" is hier absolute druk; bij overdruk (11 en 41 bar absoluut) liggen beide 2 tot 4 K hoger.

Thermische olie is het **standaardmedium** voor deze toepassing, en dat is geen willekeurige
keuze: zie de tabel in 8.12. Het voordeel is hoge temperatuur bij lage druk. De beperking is de
maximale **filmtemperatuur** van de olie zelf — boven een bepaalde wandtemperatuur kraakt de olie
— zodat het aanvoerniveau niet vrij op te schroeven is.

**De 280 °C is een ontwerpkeuze, geen stofwaarde**. Tot dan stond zij als open bron gemarkeerd, en dat beloofde een
literatuurbron die er voor een keuze niet is. De redenering voor de waarde: de aanvoer moet genoeg
boven de bodemtemperatuur liggen om warmte over te dragen, en niet hoger dan nodig, want de
buiswand volgt de olie. Wat wél een bron heeft, is de **grens**: de temperatuur waarboven de olie
zelf ontleedt. Die hoort bij een vloeistof, niet bij "thermische olie". Er is geen algemene norm
met een getal; de leverancier geeft de grens op.

Een blad is gelezen (8 oktober 2026), als voorbeeld van hoe zo een grens eruitziet:

| Type vloeistof | Aanbevolen bulktemperatuur | Hoogste bij langdurig gebruik | Hoogste filmtemperatuur |
|---|---|---|---|
| synthetisch, dibenzyltolueen | 325 °C | 350 °C (met duidelijk kortere levensduur) | 380 °C |

Bron: een openbaar gegevensblad van een leverancier van warmteolie, voor een synthetische vloeistof op basis van
dibenzyltolueen (gevonden op 11 oktober 2026; zonder firmanaam, zoals bij elke leveranciersbron in deze lijst). Geldigheid: deze ene vloeistof, in een gesloten systeem met geforceerde circulatie in
de vloeistoffase; de waarden zijn aanbevelingen van de leverancier op grond van zijn eigen
stabiliteitsproeven, geen genormeerde grens. **Het model kiest geen product.** De tabel laat
alleen zien dat 280 °C ruim onder zulke grenzen ligt (45 K onder deze bulktemperatuur, 100 K onder
deze filmtemperatuur); een minerale olie heeft lagere grenzen, en daarvoor is geen blad gelezen.

### 12.3 Wat er niet in de balans zit

De volledige balans over de kolom luidt:

```
Q_reboiler + H_F = Q_condensor + H_D + H_B
```

**Welke post ontbreekt.** De drie stromen zijn alle drie verzadigde vloeistof, maar bij
*verschillende* temperatuur: de bodem verlaat de kolom bij 232,1 °C (heter dan de voeding bij
223,2 °C), het destillaat bij 215,1 °C (kouder). Dat verschil is **voelbare warmte**. Met
F = D + B valt die term samen tot:

```
H_D + H_B − H_F = cp · [ D·(T_D − T_F) + B·(T_B − T_F) ]
                = cp · 9,00 mol·K/s
```

De factor is bekend; alleen **cp niet**. Bij een aangenomen cp van 400 tot 1000 J/(mol·K) — de
orde waarin een vloeibaar vetzuur zit — is die post **3,6 tot 9,0 kW**, ofwel 0,25 tot 0,62 %
van de condensorduty. Klein, maar niet nul. Die getallen zijn een *orde van grootte* en geen
resultaat: de cp is aangenomen, niet onderbouwd.

**Zonder die post luidt de balans Q_reboiler = Q_condensor**, en dat is wat `energy.js`
uitrekent: 1935,8 kW aan beide kanten (12.5; tot de gemeten stofdata 1448 kW). De **latente** boekhouding sluit dus exact; de
**volledige** balans sluit op die voelbare term na.

> **Wat de energiebalanstest wél en níét toetst.**
> `energiesluitingsfout()` controleert of Q_reboiler met Q_condensor klopt, gegeven de
> dampstromen en de verdampingswarmten. Dat is een zinvolle controle — zij zou de fout uit 8.15
> meteen hebben gevangen. Maar zij toetst **niet** de volledige balans: de voelbare term zit per
> constructie niet in de berekening, dus kan een test hem ook niet missen. Een sluitingsfout van
> nul betekent hier "de latente stromen kloppen onderling", niet "de kolom is energetisch
> doorgerekend".

**Wat er nodig is om hem wél volledig te sluiten:** soortelijke warmten (cp) van de vetzuren in
de vloeistoffase, met bron en geldigheidsbereik, in dit bestand. Daarmee is de voelbare term uit
te rekenen in plaats van te schatten, kan q als vrije parameter mee in plaats van vastgezet op 1,
en kan `overallEnergiebalans()` een echte sluitingsfout over alle vier de posten geven. Nieuw
openstaand punt 13.

Dat de term hier nu ontbreekt mag, omdat de voeding als kokende vloeistof binnenkomt (q = 1,
dezelfde aanname als module 5): een voeding op haar bubbelpunt hoeft niet te worden opgewarmd.

Het knelt zodra die voorwaarde niet geldt — een **onderkoelde** voeding na een stilstand, of een
voorverwarmer die het niet bijhoudt. De werkelijke duty ligt dan hoger dan wat de module
uitrekent en de kolom lijkt zonder reden meer stoom te vragen. Er staat géén geschat getal bij:
dat zou verzonnen zijn. Wel de richting en de voorwaarde. Zie `GEEN_VOELBARE_WARMTE`.

**Geen warmteverlies naar de omgeving.** Een kolom van 230 °C verliest warmte door zijn isolatie;
dat zit er niet in.

### 12.4 Smeltpunten

Nodig voor de stolgrens van de condensor (8.14). Vastgelegd als `Tm_C` per component in
`components.js`.

| Component | Smeltpunt | Herkomst |
|---|---|---|
| C12:0 | 43,8 °C | handboekwaarde |
| C14:0 | 54,4 °C | handboekwaarde |
| C16:0 | 62,9 °C | handboekwaarde |
| C18:0 | 69,3 °C | handboekwaarde |
| C18:1 | 13,4 °C | handboekwaarde (cis-vorm) |
| C18:2 | −5,0 °C | handboekwaarde |
| Zwaar residu | — | pseudo-component: geen smeltpunt, wel een verwekingstraject |

De sprong van 13,4 °C (C18:1) naar 69,3 °C (C18:0) bij vrijwel gelijke molmassa is dezelfde
eigenschap waarop de scheiding in 8.1 berust: wat met destillatie niet lukt, lukt met
kristallisatie juist wel.

Deze waarden staan als **handboekwaarde** en wachten op dezelfde verificatie als de kookpunten
van 2.2 en 2.4; zie openstaand punt 1.

### 12.5 Afgeleide waarden bij kolomcondities

Voor de kolom van 11.3 bij 10 000 kg/h en 20 mbar.

**Stand op 21 oktober 2026.** De eerste zeven rijen zijn herrekend uit `energiebalans()` in `energy.js`, met het ontwerp van
module 5 (`ontwerp()` in `stages.js`, r = 0,98 voor C16:0 en C18:2, refluxfactor 1,3). De testsuite houdt de
duty's vast op 1935,8 kW en de duty per kg voeding op 696,9 kJ.
- **De bron van het getal:** een uitkomst van het model, geen literatuurwaarde. Het is de latente balans Q = V · ΔH_vap, met V =
  D · (R + 1) en ΔH_vap als koorde over de gemeten dampspanningen (Falleiro 2012, 3.1).
- **Tot de gemeten stofdata stond hier 1448 kW**, met de verdampingswarmte uit de Antoine-afgeleide. Daarna was het 2053 kW
  (23.9), en sinds de koorde 1935,8 kW.
- **De laatste drie rijen** (het plafond, de stolgrens en het dauwpunt) zijn van voor de gemeten stofdata en niet herrekend.

| Grootheid | Waarde |
|---|---|
| Dampstroom uit de top | 23,27 mol/s = 21 428 kg/h |
| ΔH_vap top (bij 218,3 °C) | 83,2 kJ/mol |
| ΔH_vap bodem (bij 232,8 °C) | 86,6 kJ/mol |
| Dampstroom in de stripsectie | 22,34 mol/s — **4,0 % lager** (zie 8.15; tot de gemeten stofdata 13 %) |
| **Condensorduty** | **1935,8 kW** |
| **Reboilerduty** | **1935,8 kW** — gelijk, zoals een latente balans hoort te sluiten |
| Per kg voeding | 697 kJ/kg = 0,70 GJ/t |
| Plafond met thermische olie | 255 °C → max. 48,2 mbar |
| Stolgrens destillaat | 62,9 °C (C16:0, 97,1 mol%) |
| Dauwpunt destillaat bij 20 mbar | 215,5 °C — 152 K boven de stolgrens |

De dampstroom is ruim **twee keer de voedingsstroom** (21,4 tegen 10 t/h; tot de gemeten stofdata 17,9). Dat komt volledig door
de reflux uit module 5: elke procent extra zuiverheid daar wordt hier in stoom betaald.

---

## 13. Vacuümsysteem en lekdebiet (module 7)

### 13.1 Wet, en wat er niet in zit

Module 7 voegt **geen stofwaarden voor de vetzuren** toe. Wat er wel bij komt is de molmassa van
droge lucht (`M_LUCHT_KGMOL` = 0,02896 kg/mol: 28,9644 kg/kmol afgerond, de standaardwaarde op zeeniveau in R. A. Minzner (red.), *The 1976
Standard Atmosphere Above 86-km Altitude*, NASA SP-398, NASA, 1976; het rapport ingezien op 19 november 2026), nodig om een lekdebiet in kg/h
naar mol/s om te rekenen.

| Wet | Vorm | Gebruik |
|---|---|---|
| Ideale gaswet | p·V = n·R·T | molair volume, volumestroom, zuigbelasting |
| Dalton (hoofdstuk 9) | p_i / p = y_i | hoeveel product met de afgezogen lucht meegaat |

**Vacuüm is een volumeprobleem.** Herschreven naar V = R·T/p staat de druk in de noemer:

| Druk (bij 215 °C) | Molair volume |
|---|---|
| 1013,25 mbar | 0,040 m³/mol |
| 50 mbar | 0,812 m³/mol |
| 20 mbar | **2,03 m³/mol** |
| 10 mbar | 4,06 m³/mol |
| 5 mbar | 8,12 m³/mol |

Vijftig keer zoveel volume bij 20 mbar als bij atmosferisch, voor dezelfde hoeveelheid stof.

**Niet in de module:** geen pompkarakteristiek en geen ejecteurberekening — de module zegt wat er
wég moet, niet hoe een specifieke pomp dat doet. Ook geen drukval over de leidingen naar het
vacuümsysteem.

### 13.2 `LEKDEBIET_IS_EEN_SCHATTING` — het lekdebiet is invoer, geen uitkomst

Hoeveel lucht een installatie binnenlaat volgt uit **geen enkele natuurwet**: het hangt af van het
aantal flenzen, de staat van de pakkingen en hoe er is gelast. **Er bestaan wel openbare empirische correlaties**, die het lek aan
het volume van het systeem koppelen (herschreven op 20 oktober 2026; tot dan stond hier dat er geen te onderbouwen correlatie
was). De module rekent er niet mee: het lekdebiet blijft invoer. De correlaties geven de orde waaraan de drie niveaus hieronder
worden getoetst.

De drie niveaus (`LEKNIVEAUS`) zijn sinds v1.1.6 uit die correlaties afgeleid, bij de
topdruk van 20 mbar en het geschatte volume van ongeveer 231 m³ hieronder:

| Niveau | Lek | Afleiding | Omschrijving |
|---|---|---|---|
| Strak | 5 kg/h | de helft van normaal | gelast, weinig flenzen, goed onderhouden |
| Normaal | 10 kg/h | rond Ryans en Croll (10,7 kg/h); **het ontwerplek** | gebruikelijke staat van een productiekolom |
| Lekkend | 20 kg/h | net boven het HEI-maximum voor een commercieel dicht systeem (18 kg/h) | versleten pakkingen; het vacuüm gaat merkbaar achteruit |

Tot ronde 3: 2, 5 en 10 kg/h, ronde ordegetallen zonder bron. Het ontwerplek van het vacuümsysteem is het normale niveau
(`MODELAANNAMEN.lek_ontwerp_kgh` = `LEKNIVEAUS.NORMAAL`, 10 kg/h; de testsuite toetst dat het zo in deze tabel staat). De
lekscenario's zijn als veelvoud van het ontwerplek meegeschaald: de storing "vacuümlek" naar 44 kg/h (4,4 keer het ontwerplek, zoals
22 kg/h dat was bij 5), de trendoefening "sluipend lek" van het ontwerplek naar het dubbele.

Elke uitkomst van deze module schaalt **recht evenredig** met het gekozen lekdebiet — de tests
toetsen dat. De berekening is dus alleen zo goed als die aanname, en de UI hoort het lekdebiet als
zichtbare invoer te tonen.

**De twee correlaties**:
- **HEI**, *Standards for Steam Jet Vacuum Systems*, 5e druk, de grafiek van het **maximale** lek in een "commercieel dicht"
  systeem, als fit L [lb/h] = a · V^b (V in ft³), met per band van de absolute bedrijfsdruk eigen a en b. Voor 3,1 tot 20 mmHg is
  a = 0,1010 en b = 0,6637 (R. Talukder en P. K. Mandal, *PTQ* Q4 2024, pp. 52–53, vgl. (1) en tabel 4). **Verificatie:** de
  coëfficiënten zijn uit een verhaspelde tekstweergave van het artikel gelezen, niet uit een gave tabel; een onafhankelijke fit
  aan dezelfde HEI-grafiek (de open bibliotheek `fluids`, functie `vacuum_air_leakage_HEI2633`, band 3–20 mmHg: L = e^−2,340 ·
  V^0,6657) geeft binnen 3 % hetzelfde. Dit is een bovengrens, geen gemiddelde.
- **Ryans en Croll** (1981), zoals overgenomen in K. Coker, *Ludwig's Applied Process Design for Chemical and Petrochemical
  Plants*, 4e druk (Gulf Professional Publishing, 2007): W [lb/h] = 0,026 · P^0,34 · V^0,6 voor P < 10 torr, 0,032 · P^0,26 ·
  V^0,6 voor 10 tot 100 torr, en 0,106 · V^0,6 daarboven, met **P de vacuümdiepte** (atmosfeer min bedrijfsdruk, in torr) en V
  in ft³. **Verificatie:** gelezen in de implementatie van de bibliotheek `fluids` (`vacuum_air_leakage_Ryans_Croll`), die Coker
  als bron noemt; Coker zelf is niet ingezien. Dat P de vacuümdiepte is, past bij de natuurkunde: de drie stukken sluiten bij 10
  en 100 torr op elkaar aan, en bij diep vacuüm is de stroom door een lek gesmoord en hangt zij niet meer van de druk af. Voor
  een kolom onder vacuüm geldt dus W = 0,106 · V^0,6.

**Het systeemvolume** (eigen schatting, uit het model): de doorsnede van elke kolom uit de meetkunde van 20.13, de bedhoogte uit
N · HETP plus 4 m per kolom voor verdeler, druppelvanger, kop, bodem en terugvoer van de reboiler, en 15 m³ voor de condensors, de
vaten en de leidingen. Bij 20 mbar: K-01 3,94 m² × 6,7 m ≈ 26 m³, K-02 8,81 m² × 21,6 m ≈ 190 m³, samen met de rest **≈ 231 m³**
(8170 ft³). Bij 10 mbar worden de kolommen breder (het dampvolume verdubbelt, en bij een vaste F-factor groeit de doorsnede met √2; bij
K-02 iets meer, omdat de refluxverhouding stijgt) en K-02 iets hoger:
**≈ 358 m³**. De toeslagen zijn aannames; de correlaties schalen met V^0,6 tot V^0,66, dus 20 % meer volume geeft 12 tot 13 % meer
lek.

| Topdruk | Volume | HEI (maximum, commercieel dicht) | Ryans en Croll | De niveaus van het model |
|---|---|---|---|---|
| 20 mbar (15 torr) | ≈ 231 m³ | **18 kg/h** | **10,7 kg/h** | **5 / 10 / 20 kg/h** (tot ronde 3: 2 / 5 / 10) |
| 10 mbar (7,5 torr) | ≈ 358 m³ | **24 kg/h** | **13,9 kg/h** | (het model draait niet op 10 mbar; 9.3) |

**Wat dat zegt.** Tot ronde 3 lagen de niveaus aan de lage kant van de open correlaties: "lekkend" (10 kg/h) rond Ryans en Croll,
"normaal" (5 kg/h) op de helft daarvan. Sinds ronde 3 ligt "normaal" rond Ryans en Croll en "lekkend" net boven het HEI-maximum.
De correlaties geven een orde voor een systeem van deze omvang, geen meting van deze installatie; de toeslagen in het volume zijn
aannames, en 20 % meer volume geeft 12 tot 13 % meer lek.

*In bedrijf wordt het lek niet berekend maar **gemeten**:* sluit de kolom af van het vacuümsysteem
en kijk hoe snel de druk oploopt. Uit die snelheid en het bekende volume volgt het lekdebiet
rechtstreeks uit de ideale gaswet. Die proef zit niet in de module.

### 13.3 Dieper vacuüm kost pompcapaciteit — de afweging uit 8.12 rond

Bij gelijk lekdebiet schaalt het zuigvolume vrijwel met 1/p (5 kg/h lek, uitlaat 100 °C):

| Druk | Zuigvolume | Productverlies |
|---|---|---|
| 50 mbar | 107 m³/h | 0,048 kg/h |
| 20 mbar | 269 m³/h | 0,120 kg/h |
| 10 mbar | 539 m³/h | 0,240 kg/h |
| 5 mbar | 1083 m³/h | 0,483 kg/h |

Van 20 naar 10 mbar **verdubbelt** de benodigde capaciteit. Daarmee is de keten uit 8.12 rond: een
kouder verwarmingsmedium verlaagt het drukplafond, een lager drukplafond dwingt een dieper vacuüm
af, en een dieper vacuüm vraagt een grotere pomp. Vacuüm, verwarming en scheiding zijn geen drie
losse ontwerpkeuzes maar één.

### 13.4 `DAMPVOLUME_IS_EEN_BOVENGRENS` — alles is een bovengrens

Zie 8.2. De ideale gaswet telt **deeltjes**, en vetzuurdamp dimeriseert: twee moleculen die als één
deeltje reizen tellen als één mol, niet als twee. Deze module rekent met de monomeer-molmassa en
**overschat** daarmee het aantal deeltjes, en dus het volume en de vacuümbelasting.

Elke volumestroom en elke zuigbelasting hier is dus een **bovengrens** — voor een ontwerp de veilige
kant, maar geen voorspelling. Hoevéél het scheelt is niet te zeggen: daarvoor is een
associatie-evenwicht nodig, en dat is niet vastgelegd (openstaand punt 7). `DIMERISATIE_DAMPFASE`
in `components.js` kondigde precies dit aan; `DAMPVOLUME_IS_EEN_BOVENGRENS` in `vacuüm.js` is die
aangekondigde melding.

### 13.5 Afgeleide waarden

Voor de kolom van 11.3 en 12.5, bij 20 mbar. Door de testsuite herberekend.

| Grootheid | Waarde |
|---|---|
| Ruwe dampstroom uit de top | 142 194 m³/h — **onmogelijk voor een pomp** |
| Zuigbelasting (5 kg/h lek, uitlaat 100 °C) | **269 m³/h** |
| Verhouding tussen die twee | ruim 500 × |
| Productverlies via de vacuümleiding | 0,120 kg/h |
| Molair volume bij 20 mbar en 215 °C | 2,03 m³/mol |

### 13.6 De meegesleepte productdamp in de zuigbelasting: verwaarloosbaar bij HET ONTWERPPUNT

Een proef op de tests (v0.9) haalde de meegesleepte productdamp uit `zuigbelasting()`, en
geen enkele controle viel om. Dat is geen gat in de tests maar een **equivalentie, en die geldt bij
het ontwerppunt, niet in het algemeen**. De verhouding tussen lucht en productdamp is geen
eigenschap van de code maar van het bedrijfspunt, vooral van de temperatuur aan de condensoruitlaat.

Herberekend met `zuigbelasting()` (C16:0, lek 5 kg/h, 28 september 2026):

| Condensoruitlaat | Druk | Lucht : productdamp (mol) | Productdamp in de zuigbelasting |
|---|---|---|---|
| 100 °C (ontwerp) | 20 mbar (ontwerp) | ongeveer 28 800 : 1 | 0,003 % |
| 100 °C | 5 mbar | ongeveer 7 200 : 1 | 0,014 % |
| 120 °C | 20 mbar | ongeveer 1 550 : 1 | 0,065 % |
| 140 °C | 20 mbar | ongeveer 170 : 1 | 0,59 % |

Het lekdebiet doet er niet toe: de productdamp is volgens Dalton evenredig met de lucht. Wat telt is
p_sat(T_uit) / (p − p_sat): een warmere condensoruitlaat of een dieper vacuüm maakt de term groter.
Bij het ontwerppunt valt hij ruim binnen de afronding van elke toets. Bij een condensor die
achteruitgaat, loopt hij met een factor honderd op. In het oefenmodel speelt dat niet, omdat
`MODELAANNAMEN.T_condensoruit_C` vast op 100 °C staat. Wie die temperatuur ooit laat bewegen,
bijvoorbeeld bij een condensorstoring, moet A4 opnieuw beoordelen.

Twee kanttekeningen:
- De dampspanning van C16:0 bij 100 tot 140 °C is **extrapolatie**: het meetvenster loopt van 210
  tot 260 °C (zie de onzekerheid bij `MEESLEEP_TEGENOVER_STOLLEN.meesleeptabel` in `vacuum.js`).
  De factoren hierboven zijn daarom ordegroottes en geen meetwaarden.
- In de v0.9-notitie in `CHANGELOG.md` stond voor deze verhouding "ongeveer 640". Dat getal kwam
  bij het herberekenen niet terug en is hier niet overgenomen.

---

## 14. Pakking, drukval en flooding (module 8)

### 14.1 Eén wet en drie correlaties

Dit is de meest empirische module van de reeks. Module 5 had één correlatie zonder natuurwet
(Gilliland); hier zijn het er drie, en ze bepalen bijna alles.

| | Vorm | Status |
|---|---|---|
| Drukopbouw | p_bodem = p_top + Δp | **exact** — een boekhouding, geen correlatie |
| HETP | H = N · HETP | **gefit** — zie hieronder |
| Drukval | Δp = N · Δp_per_trap | **gefit** |
| F-factor | F = u·√ρ, met F_max | **definitie exact, grens gefit** |

**HETP is fundamenteler onbepaald dan Gilliland.** Een theoretische trap is een *bedacht* begrip:
een plek waar damp en vloeistof volledig in evenwicht komen. In een pakkingbed gebeurt dat nergens
en overal tegelijk — er is doorlopende uitwisseling over de hele hoogte, zonder trappen. HETP
verbindt twee wereldbeelden die niet op elkaar passen. Hij is bovendien geen eigenschap van de
pakking: hij hangt af van het stofsysteem, de belasting, de kwaliteit van de vloeistofverdeling en
de bedhoogte. Twee kolommen met dezelfde pakking kunnen een verschillende HETP hebben.

**De F-factor vraagt een scherper onderscheid.** De *definitie* F = u·√ρ is exact — een afspraak,
geen meting. De *grens* waarboven een intern gaat floodden volgt uit meten en uit leveranciersdata,
per pakkingtype en per stofsysteem verschillend. De formule is hard, het getal dat je erin stopt
niet.

### 14.2 Kengetallen per intern

Generiek en afgerond (`INTERNEN` in `packing.js`). De gestructureerde pakking, de standaard, heeft sinds 20 oktober 2026 een
bron voor beide kengetallen (hieronder). De gestorte pakking en de zeefschotels hebben ronde
waarden, een aanname van het model en geen meetdata, die er staan om de **keuze** te kunnen vergelijken. De orde van grootte komt uit D. W. Green en R. H. Perry (red.), *Perry's Chemical Engineers' Handbook*, 8e druk, McGraw-Hill, 2008, Section 14 (een kopie van de pagina's 14-58 tot ongeveer 14-99 ingezien op 19 november 2026, zonder titelpagina; de druk is afgeleid uit de paginakoppen): ongeveer 10 mbar drukval per theoretische trap voor schotels en 3 tot 4 mbar voor gestorte pakking, en voor gestorte pakking HETP = 18·D_P of 93/a_P (bij diep vacuüm mogelijk optimistisch). De HETP van 0,75 m past bij een pakking van ongeveer 42 mm (eigen rekenwerk); de drukvallen in het model liggen een factor 2 tot 2,7 onder Perry's typische waarden. Voor het rendement van zeefschotels in deze dienst, waar de HETP van 1,10 m van afhangt, is geen bron gevonden.

| Intern | Δp/trap | HETP | F_max |
|---|---|---|---|
| Gestructureerde pakking (standaard) | 0,5 mbar | 0,45 m | 2,5 |
| Gestorte pakking | 1,5 mbar | 0,75 m | 2,0 |
| Zeefschotels | 5,0 mbar | 1,10 m | 2,0 |

De drukval geldt bij **ontwerpbelasting** en met **schone** pakking. Meer belasting geeft ruwweg
kwadratisch meer drukval; vervuiling geeft nog veel meer (8.21).

**HETP 0,45 m (gestructureerde pakking): binnen de vuistregel van Kister**. H. Z.
Kister, "Rules of Thumb: Distillation", *The Chemical Engineer* 992 (februari 2024), regel vier: voor organische systemen met een
oppervlaktespanning onder 25 dyn/cm is HETP [m] = 100 / a_p + 0,1 voor gestructureerde pakking van het Y-type, met a_p de
specifieke oppervlakte in m²/m³; "generally plus or minus 25 %", bij een goede verdeling van vloeistof en damp. Voor een
plaatpakking van 250 m²/m³ geeft dat **0,50 m**, met een band van 0,375 tot 0,625 m. De 0,45 m van het model ligt binnen die band.
Dat de oppervlaktespanning van vetzuren bij 200 tot 250 °C onder 25 dyn/cm ligt, zodat de regel van toepassing is, is **uit het
hoofd vermeld, niet nagelezen** (bij kamertemperatuur ligt zij rond 30 dyn/cm, en zij daalt met de temperatuur). Gelezen op de site
van het tijdschrift (de regel en de ±25 % staan er letterlijk). Gevoeligheid: de HETP werkt in dit model alleen door in de
bedhoogte (hoogte = N · HETP), niet in een temperatuur, stroom of samenstelling.

**Drukval 0,5 mbar per trap (gestructureerde pakking): onder de floodinggrens uit een meting bij diep vacuüm**. R. Schulz, T. Rietfort, H. Jansen en Ž. Olujić, "Experimental evidence on performance of an advanced
gauze structured packing under deep vacuum distillation conditions", *Chemical Engineering Transactions* 69 (2018) 763–768, doi
10.3303/CET1869128, open toegang. Gemeten bij totale terugvloei met een para- en orthoxyleen bij 0,02 en 0,1 bara aan de top, aan
een gaaspakking van 500 m²/m³. Bij 0,02 bara valt de efficiëntie weg (flooding) bij een drukval van **minder dan 3,0 mbar/m**
(tegen 3,5 mbar/m bij 0,1 bara); onder het belastingspunt ligt de drukval dus ruim daaronder. Het artikel noemt ook dat vrije
vetzuren in de praktijk "well below 0.1 bara at the column top" gedestilleerd worden. **De afleiding:** 0,5 mbar per trap met een
HETP van 0,45 m is 0,5 / 0,45 = **1,1 mbar/m**. Dat ligt onder de 3 mbar/m waar die pakking bij 20 mbar floodt, en past bij een
kolom die op driekwart van haar floodingbelasting is ontworpen (`F_ontwerp_fractie` = 0,75, 20.13). **Wat de bron niet geeft:**
het artikel meet een gaaspakking met een andere specifieke oppervlakte dan de plaatpakking van het model; de 3 mbar/m is een
bovengrens bij flooding, geen ontwerpwaarde. **Gevoeligheid:** groot. De bodemdruk van K-02 is 20 mbar plus 39 trappen maal de
drukval per trap; bij +20 % halveert de marge van K-02 tot het plafond van het verwarmingsmedium (van 4,9 naar 2,4 K), bij
1,0 mbar per trap is zij negatief.

### 14.3 Grenzen van het werkgebied

| Grens | Waarde | Herkomst |
|---|---|---|
| Minimale bevochtiging | 0,5 m³/(m²·h) | aanname van het model, een rond getal; de orde van grootte: D. W. Green en R. H. Perry (red.), *Perry's Chemical Engineers' Handbook*, 8e druk, McGraw-Hill, 2008, Section 14: grootschalige proeven met gestructureerde pakking gaven goede efficiëntie tot 0,5 m³/(m²·h), zonder dat een ondergrens bereikt werd (een kopie van Section 14 ingezien op 19 november 2026, zonder titelpagina) |
| Minimale verdeler-turndown | 40 % van ontwerp | aanname van het model, een rond getal; de orde van grootte: Perry's 8e druk (2008), Section 14: een gatenverdeler haalt ongeveer 2 : 1 (50 % van ontwerp), met twee rijen gaten meer, en de turndown van een gepakte kolom wordt meestal door de verdeler begrensd |
| Ontwerpfractie van F_max | 70–80 % | gebruikelijke ontwerpmarge |

Voor deze kolom loopt het werkgebied daarmee van **40 tot 100 %** van de ontwerpbelasting:
eronder uitdroging (8.20), erboven flooding (8.19).

**Vloeistofdichtheid.** De bevochtigingsgraad vraagt een dichtheid; de aanroeper geeft die mee, bij de temperatuur van de
vloeistof (hoofdstuk 4). Voor het bed van K-02 is dat 733,6 kg/m³ bij 234,2 °C (het midden tussen top en bodem), een
extrapolatie van 34 K boven het meetbereik.

### 14.4 Afgeleide waarden

Voor de kolom van 11.3, met gestructureerde pakking. Door de testsuite herberekend.

| Grootheid | Waarde |
|---|---|
| Drukval over het bed | 15,7 mbar |
| Bodemdruk | 35,7 mbar |
| **Bodemtemperatuur** | **246,9 °C** (was 232,1 °C zonder drukval) |
| Marge tot het mediumplafond | 8,1 K (was 23 K) |
| Hoogte pakkingbed | 14,2 m |
| Dampdichtheid in de top | 0,126 kg/m³ |
| Diameter op F_max | 2,67 m |
| Diameter bij een ontwerp op 75 % van F_max | 3,09 m |
| Bevochtiging bij ontwerp | 2,93 m³/(m²·h) |
| Werkgebied | 40 – 100 % van ontwerp |

> **Correctie op de eerdere modules.** Modules 5, 6 en 7 rekenden met **één druk** voor de hele
> kolom: 20 mbar overal. Hun bodemtemperatuur van 232,1 °C is daarmee de waarde **zonder** drukval.
> Met een realistisch drukprofiel is die 14,8 K hoger, en de marge tot het mediumplafond van 8.12
> krimpt van 23 naar 8 K. Die eerdere waarden zijn niet fout — ze hoorden bij een expliciet
> gekozen enkele druk — maar wie de bodemtemperatuur wil gebruiken, moet de waarde uit dit
> hoofdstuk nemen.

---

## 15. Thermische afbraak (module 9)

### 15.1 Eén wet, en een getal dat ontbreekt

| | Vorm | Status |
|---|---|---|
| Arrhenius | k = A·exp(−Ea/(R·T)) | **wet** — empirisch vastgesteld, theoretisch onderbouwd |
| Arrhenius als verhouding | k₂/k₁ = exp((Ea/R)·(1/T₁ − 1/T₂)) | **wet** — A valt weg |
| Activeringsenergie Ea | 31,0 tot 127,4 kJ/mol | **band uit gemeten vetzuurreacties** (isomerisatie), niet aan de afbraak zelf gemeten — zie 15.2 |
| Pre-exponentiële factor A | — | **niet vastgelegd**, zie 15.3 |

De *vorm* van de wet staat niet ter discussie. Arrhenius (1889) stelde hem empirisch vast; de
botsingstheorie en de overgangstoestandstheorie onderbouwen hem: alleen moleculen met genoeg energie
om over de barrière Ea te komen reageren, en dat aandeel groeit exponentieel met T.

**Wat de wet aanneemt en hier niet waar is.** Arrhenius beschrijft één reactie met één
activeringsenergie. Thermische afbraak van vetzuren is dat niet: polymerisatie, dimerisatie,
decarboxylatie, isomerisatie en oxidatie lopen door elkaar, elk met een eigen Ea. Wat in de praktijk
gemeten wordt is een **schijnbare** activeringsenergie van dat hele pakket, en die kan met de
temperatuur verschuiven zodra een andere reactie de overhand neemt.

### 15.2 De literatuurtoets op de activeringsenergie — uitkomst: geen bruikbare bron

Opdracht was de vuistregel om te keren als vertrekpunt (factor 2 per 8 K rond 500 K ⇒ Ea ≈ 180
kJ/mol) en te toetsen of gerapporteerde waarden voor thermische afbraak en polymerisatie van
onverzadigde vetzuren in die orde liggen.

**Uitkomst: er is geen gepubliceerde activeringsenergie gevonden voor de reactie die hier telt** —
de thermische polymerisatie die in een vetzuurkolom de pitch en de verkleuring maakt, bij 200–280 °C
onder vacuüm. Wat wél gevonden is, staat hieronder met vermelding van wat het beschrijft.

| Systeem | Ea (kJ/mol) | Reactie | Bruikbaar? |
|---|---|---|---|
| Oliezuur, thermische cis-trans isomerisatie | 31,0 ± 0,8 | isomerisatie | nee |
| Camellia-olie, cis-trans isomerisatie | 97,2 / 112,6 / 127,4 | isomerisatie | nee |
| Trioleaat, cis-trans isomerisatie | 106 | isomerisatie | nee |
| Linoleenzuur, thermoxidatie | 74,6 ± 3,4 (93,8–116,3 met inhibitoren) | oxidatie | nee |
| Petroleumpitch, pyrolyse (TGA, isoconversioneel) | 176,5 / 176,8 / 178,8 / 189,9 | pyrolyse | nee |

Waarom geen van deze bruikbaar is:

- **Isomerisatie** verandert de vórm van het molecuul, niet zijn grootte. Het maakt geen pitch en
  geen aankoeking. De camellia-olie-waarden betreffen bovendien een triglyceride-olie, geen vrij
  vetzuur.
- **Oxidatie** vraagt zuurstof. Onder 20 mbar met stikstofsperring is die er nauwelijks, dus dit is
  niet de reactie die in de kolom domineert — al verklaart hij wel verkleuring bij luchtintrek
  (13.2).
- **Petroleumpitch-pyrolyse** ligt met 176–190 kJ/mol opvallend dicht bij de 178,4 die de vuistregel
  impliceert, en **juist daarom is het geen onderbouwing.** Petroleumpitch is een ander materiaal
  dan vetzuurpitch, pyrolyse is een andere reactie dan polymerisatie, en de TGA-metingen liggen
  honderden graden boven de 250 °C van deze kolom. Een getal dat toevallig klopt is geen bron. Wie
  het hier invult, haalt de juiste uitkomst om de verkeerde reden — en dat is in een
  trainingsmodule erger dan een eerlijk gat.

**Wat de toets wél oplevert: een richting.** Alle waarden die voor *vetzuur*reacties zijn gemeten
liggen tussen 31 en 127 kJ/mol, dus **lager** dan de 178,4 die de vuistregel impliceert — een factor
1,4 tot 5,7 lager. Ligt de afbraakreactie in dezelfde orde als de andere vetzuurreacties, dan
**overdrijft de vuistregel de temperatuurgevoeligheid.** Dat is de reden dat module 9 met een band
rekent en niet met een getal.

`EA_BAND` in `degradation.js` - **vijfde doorloop**:

| Grens | Ea | Herkomst | Gemeten? |
|---|---|---|---|
| onder | 31,0 ± 0,8 kJ/mol | thermische cis-trans-isomerisatie van oliezuur (Raman/PLS), doi 10.3390/spectroscj3020017 | ja, maar aan isomerisatie, niet aan de afbraak |
| midden | 62,8 kJ/mol | **middenwaarde voor de weergave**: het meetkundig midden van de band | **nee** — geen meetwaarde en geen bron |
| boven | 127,4 kJ/mol | cis-trans-isomerisatie in camellia-olie, sciencedirect S0023643822012282 | ja, maar aan isomerisatie, en aan een triglyceride-olie |

**Wat er veranderde, en waarom.** Tot de vijfde doorloop liep de band van een ronde 100 kJ/mol tot
de 178,4 kJ/mol van de omgekeerde vuistregel, met de bovengrens als standaard. Die bovengrens ligt
boven het hele gemeten bereik voor vetzuurreacties, en hij was afgeleid bij een temperatuurpaar uit
de vervallen stofdata (223,18 en 231,27 °C). Hij is **vervallen als anker**; de vuistregel blijft
als leerstof bestaan (`EA_VUISTREGEL`, gemarkeerd als vervallen). De band is nu het gemeten bereik
zelf, met een bron per uiterste. Er is geen standaard en geen afgeleid middenpunt met schijnprecisie;
de middenwaarde staat er alleen omdat een scherm dat één lijn tekent die ergens moet leggen.

**Wat dit zegt, en wat niet.** Beide uitersten zijn gemeten aan cis-trans-isomerisatie, en die maakt
geen pitch. De band zegt dus: *als* de afbraakreactie in dezelfde orde ligt als de vetzuurreacties
die wel gemeten zijn, dan ligt het effect binnen deze band. Punt 9 van de openstaande punten blijft
daarmee open.

**Het geldigheidsbereik.** De ondergrens, 31 kJ/mol, is gemeten tussen 100 en 160 °C: A. Watanabe en Y. Numata,
"Determination of the Activation Energy of the Thermal Isomerization of Oleic Acid with Raman Spectroscopy and Partial Least Squares
Regression", *Spectroscopy Journal* 3 (2) (2025), artikel 17, doi 10.3390/spectroscj3020017 (de samenvatting ingezien op 19 november
2026). Bij 230–280 °C is zij dus een extrapolatie, 70 tot 120 K boven het meetbereik. Van de bovengrens, 127,4 kJ/mol (C. He, L. Qin,
J. Yang en Y. Zhu, *LWT* 173 (2023) 114293, doi 10.1016/j.lwt.2022.114293), is het meetbereik niet bevestigd: de samenvatting kon
niet worden ingezien, en alleen een zoekmachinefragment ervan noemt proeven tot 280 °C. Dat de band bij 230–280 °C geldt, is een
aanname van het model.

**Wat het doet met de uitkomsten** (alle uitgerekend door het model, de testsuite):

| Uitkomst | Band 100–178,4 (vervallen) | Band 31,0–127,4 |
|---|---|---|
| De drukval van K-02 (17,6 K) | factor 2,2 tot 4,2 | factor 1,3 tot 2,8 |
| Deel van de afbraak dat modules 5–7 missen | 55 tot 76 % | 22 tot 64 % |
| Reboilerwand K-02 (tot 280 °C olie) | tot 3,4 à 8,9 keer | tot 1,5 à 4,8 keer |
| Reboilerwand K-01, warme stilstand | 6 tot 25 keer | 1,7 tot 9,8 keer |
| Winst van geforceerde circulatie (K-01) | 4,8 tot 16,4 | 1,6 tot 7,4 |
| Bodemafname K-01 dichtgeknepen (dosis) | 1,74 tot 2,16 keer | 1,45 tot 1,88 keer |
| Dosis K-01 tegenover K-02 | – | 2,9 tot 4,9 keer |
| Misvatting "voeding is de bodem" (7,2 K) | "ruwweg een verdubbeling" | 1,11 tot 1,55 keer |

De uitkomsten worden kleiner, en de verhouding tussen boven- en ondergrens groter (4,1 tegen 1,8 in
Ea). Wat overeind blijft, is de **richting**: elke factor is groter dan één, de fout van een
vergeten temperatuurstijging valt altijd dezelfde kant op, en elke keuze die over de hele band
dezelfde kant op valt (geforceerde circulatie, de kolom niet warm laten staan) blijft staan. Wat
verdween, zijn uitspraken die aan de vuistregel hingen: "verdubbelt ook bij de voorzichtigste
aanname", "een orde winst", "ruwweg een verdubbeling". Die zijn in de teksten herschreven.

Geraadpleegde vindplaatsen (geen van deze levert een bruikbare Ea voor dit systeem; ze staan hier
om de toets navolgbaar te maken):

- Raman/PLS-studie naar thermische isomerisatie van oliezuur — <https://doi.org/10.3390/spectroscj3020017>
- Cis-trans-isomerisatie in camellia-olie — <https://www.sciencedirect.com/science/article/pii/S0023643822012282>
- Isomerisatiekinetiek van trioleaat — <https://www.sciencedirect.com/science/article/pii/S2405844018313628>
- Autoxidatie van onverzadigde vetzuren en hun esters — <https://www.researchgate.net/publication/251155477_Autooxidation_of_Unsaturated_Fatty_Acids_and_Their_Esters>
- Pyrolysekinetiek van petroleumpitch via TGA — <https://link.springer.com/article/10.1007/s10973-024-13963-2>

### 15.3 Wat deze module bewust niet kan

**Geen absolute afbraaksnelheid.** Daarvoor is de pre-exponentiële factor A nodig, en die is voor
dit systeem niet bekend. Er komt uit module 9 dus nergens een getal in ppm dimeer per uur of in
kleurpunten. Alles is relatief, ten opzichte van een expliciet benoemd referentiepunt.
`afbraakmaat()` geeft daarom een **dimensieloze verhouding** en `relatieveSnelheid()` een getal dat
alleen als teller of noemer van een verhouding betekenis heeft.

**Geen reactiemechanisme en geen selectiviteit.** Welk deel van de afbraak dimeer wordt, welk deel
kleur en welk deel gas, staat hier niet. Dat vraagt een reactienetwerk met kinetiek per stap, en dat
is er niet.

**Geen wandtemperatuur.** Alleen een insluiting tussen bulk en medium (8.25).

**Geen vloeistofinhoud van de bodem.** Verblijftijd vraagt die als expliciete invoer (8.26).

### 15.4 Afgeleide waarden

Alle waarden herberekend door de testsuite; ze staan hier als controlepunt en niet als invoer.
Uitgangspunt is de kolom van 14.4: 31,47 theoretische trappen, top op 20 mbar, gestructureerde
pakking, thermische olie van 280 °C.

| Grootheid | Waarde | Herkomst |
|---|---|---|
| Ea uit de vuistregel (223,18 → 231,27 °C) | 178,4 kJ/mol | omkering, 8.23 |
| Spreiding die de vuistregel over 223–280 °C vraagt | 180,3 → 223,6 kJ/mol | 8.23 |
| Verdubbelingsstap bij Ea = 178,4 kJ/mol, bij 505 K | 8,3 K | Arrhenius |
| Verdubbelingsstap bij Ea = 130 kJ/mol, bij 505 K | 11,3 K | Arrhenius |
| Verdubbelingsstap bij Ea = 100 kJ/mol, bij 505 K | 14,7 K | Arrhenius |
| **Factor door de drukval (232,13 → 246,92 °C)** | **1,97 tot 3,35** (vuistregel 3,60) | 8.24 |
| Factor aan de wand (246,9 → 280 °C), bovengrens | 3,99 tot 11,79 (vuistregel 17,57) | 8.25 |
| Factor bij foulingfactor 2 (246,9 → 256,8 °C) | 1,54 tot 2,16 | 8.27 |
| Factor bij foulingfactor 3 (246,9 → 264,4 °C) | 2,12 tot 3,81 | 8.27 |
| Foulingfactor waarbij het mediumplafond wordt overschreden | 2 | 8.27, 12.5 |
| Bulkstijging bij verstikking (246,9 → 255,0 °C) | 8,08 K | 8.27 |
| Wand**boven**grens bij verstikking, t.o.v. schone bulk | 3,99 tot 11,79 — **ongewijzigd** | 8.27 |
| Wand**onder**grens bij verstikking, t.o.v. schone bulk | 1,42 tot 1,88 — gestegen | 8.27 |
| Schade per kg product bij 50 % doorzet | 2,85 tot 3,76 | 8.26, 8.27 |
| Prijs van 20 K setpoint erbij (wandbovengrens) | factor 3,87 | 8.27 |

Merk op dat de vuistregel bij grote temperatuurstappen sterk uit de bocht vliegt: over de 33 K van
de wandband geeft hij 17,6 tegen 11,8 voor de Ea waar hij zelf op gebaseerd is. Dat is het
rekenkundige bewijs dat het een lokale benadering is (8.23).

---

## 16. Dynamiek en regelkringen (module 10)

### 16.1 Twee wetten, één correlatie, en geen stofdata

| | Vorm | Status |
|---|---|---|
| Ophoping | d*M*/d*t* = Σin − Σuit | **wet** — massabehoud, niet op nul gezet |
| Componentophoping | d(*M*·x_i)/d*t* = Σ(in·x_in) − Σ(uit·x_uit) | **wet** |
| Tijdconstante | τ = inhoud / stroom | **gevolg** van de wet, exact |
| PI-regelaar | u = u₀ + K_p·e + (K_p/T_i)·∫e d*t* | **geen wet** — een apparaat, zie hieronder |
| RK4-integratie | vierde orde | **numerieke methode**, geen fysica |

De ophoping is dezelfde wet als in hoofdstuk 10, met het verschil dat de rechterkant daar op nul
was gezet. **Stationair is een bijzonder geval, niet de wet.**

**De PI-regelaar is geen natuurwet, en anders dan bij Gilliland (11.1) of HETP (14.1) is dat geen
gebrek:** een regelaar is een apparaat dat iemand heeft bedacht en ingesteld. De natuur regelt
niets. Een andere instelling geeft ander gedrag bij precies dezelfde fysica, en daarom is de vraag
bij iets onverwachts nooit alleen "wat doet de kolom" maar ook "wat doet de regelaar met wat de
kolom doet". Scenario `LEK_GEMASKEERD_DOOR_REGELING` is precies een geval waarin het antwoord op de
tweede vraag het antwoord op de eerste verbergt.

Er zit in deze module **geen stofdata**. De componentaccumulatie wordt per componentmassa gerekend
en niet per fractie: zo is de vergelijking lineair, sluit de balans exact (gemeten
sluitingsfout 8·10⁻¹⁶) en volgen de fracties er daarna uit.

### 16.2 De drie regelkringen

| Kring | Meting | Stuurt | K_p | T_i |
|---|---|---|---|---|
| Niveau | niveau in de sump | bodemafnameklep (B) | 2,0 | 600 s |
| Druk | topdruk | capaciteit vacuümsysteem | 0,5 | 60 s |
| Temperatuur | **stripsectie** | **reboilerduty** | 40 000 | 1200 s |

De instelwaarden van de druk- en de temperatuurkring zijn **ronde waarden om gedrag te tonen**, een aanname van het model
zonder bron, geen afstemming voor een bestaande kolom; een literatuurbron komt er voor zulke waarden niet (een afstemming volgt uit
een afleiding, zoals bij de niveaukring). De rij van de niveaukring is sinds v1.1.3 vervangen door de lambda-regel (16.5). De drie kringen zijn bovendien **ongekoppeld** gerekend en dat
zijn ze in werkelijkheid niet — de duty beïnvloedt het niveau, het niveau de samenstelling, de
samenstelling de temperatuur. De module kan de *richting* en de *tijdschaal* van gedrag laten zien,
geen afstemadvies geven.

Drie keuzes die uitleg verdienen:

1. **De niveauregeling op de bodemafname** is de kring die 8.7 aankondigde toen hij vastlegde dat de
   bodemafname geen knop is. `dutystapInDeTijd()` laat de keten nu in de tijd lopen: meer duty →
   meer damp → de regelaar knijpt B dicht → pitch concentreert op → hetere bodem → snellere afbraak.
   De klep van de verwarming is de oorzaak, de bodemklep het gevolg.
2. **De temperatuurregeling stuurt op duty en niet op reflux**, omdat dat is wat een operator in
   deze dienst gebruikt.
3. **Het meetpunt zit in de stripsectie en niet in de sump.** De sumptemperatuur wordt gedomineerd
   door de pitchconcentratie en niet door de scheiding; een sumpmeting zou dus een opconcentratie
   melden waar de regelaar niets aan kan doen.

   **Dit geldt voor module 10 (`dynamics.js`), niet voor het oefenmodel** (vastgesteld op 7 oktober
   2026). Module 10 rekent een meetpunt in de stripsectie uit (`meetpunt()`, lineair geïnterpoleerd,
   een benadering). Het oefenmodel (`simulatie.js`, hoofdstuk 20) kent die temperatuur niet: zijn
   temperatuurkring regelt op de **bodemtemperatuur**, met een eigen afstemming (60 kW/K, niet de
   waarde uit de tabel hierboven). Twee modellen, twee meetpunten, en tot die datum stond dat nergens.

`piStap()` bevat **anti-windup**: loopt de klep tegen zijn grens, dan wordt de integraal niet verder
opgeteld. Zonder dat vult de regelaar zich op en reageert hij later veel te laat — een klassieke
bedieningsklacht en geen fysisch verschijnsel.

### 16.3 Wat deze module bewust niet kan

**Geen trap-voor-trap dynamiek**, en dus geen vertraging van de zuiverheid (8.31). **Geen
geïntegreerde drukdynamiek** (8.32). **Geen absolute afbraak** — de dosis is dimensieloos en
relatief, net als in 15.3. **Geen regelaarafstemming** (16.2). **Geen holdupvolumes met bron**: de
drie configuraties zijn ronde ontwerpwaarden ter illustratie.

### 16.4 Afgeleide waarden

Alle waarden herberekend door de testsuite. Uitgangspunt: F = 10 000 kg/h `VOEDING_GENERIEK`,
z_pitch = 2,00 massa%, topdruk 20 mbar, ρ_bodem 850 kg/m³ (een aanname van toen; nu uit hoofdstuk 4, 730,9 kg/m³ bij
237,8 °C, en daarmee 14 % minder inhoud en verblijftijd dan in deze tabel).

| Grootheid | Waarde | Herkomst |
|---|---|---|
| τ damp (druk) | 2,6 s | 8.29 |
| τ refluxvat | 4,3 min | 8.29 |
| τ sump, vallende film (de bodem van K-01), pitchsnede | 88,7 min | 8.29 |
| τ sump, ruime sump, pitchsnede | 450 min | 8.29 |
| τ sump, vallende film (de bodem van K-01), fractioneringssnede | 5,4 min | 8.33 |
| Spreiding tijdschalen, realistische configuratie | factor 880 | 8.29 |
| x_pitch stationair bij B = 400 kg/h | 50,00 massa% | 8.28 |
| x_pitch stationair bij B = 340 kg/h | 58,82 massa% | 8.28 |
| **x_pitch: verschil tussen de drie configuraties** | **0 (exact identiek)** | 8.28 |
| Afbraakdosis: spreiding over de drie configuraties | factor 30 | 8.28 |
| dT/dp bij 20 mbar (C16:0) | 1,335 K/mbar | 8.30 |
| dT/dp bij 1013,25 mbar (C16:0) | 0,046 K/mbar | 8.30 |
| Kookpuntstijging bij een lek 20 → 26 mbar | 8,0 K | 8.30 |
| Weggegeven stripping, ongecompenseerde regelaar | **8,0 K** | 8.30 |
| Weggegeven stripping, gecompenseerde regelaar | 0 K | 8.30 |
| Dosis na 24 h warme stilstand (τ = 37,5 min) | 38× de normale dosis | 8.26 |
| RK4-convergentie bij halvering van de stap | factor 16,9 tot 19,7 | 8.32 |
| Sluitingsfout massabalans in de simulatie | 8·10⁻¹⁶ | 16.1 |

De laatste twee regels zijn **controles op de methode en niet op de fysica**: ze horen erbij omdat
een simulatie die er vloeiend uitziet nog steeds verkeerd kan rekenen.

### 16.5 De niveaukringen van K-01 en K-02 in het oefenmodel: lambda-afstemming (v1.1.3 en v1.1.4)

Dit gaat over het oefenmodel (`simulatie.js`, hoofdstuk 20). Module 10 (`REGELAARS` in 16.2) is niet veranderd.

**Het proces is integrerend.** De bodem is een ophoping (16.1): d*h*/d*t* = (in − uit)/*M*_max, met
*h* het niveau als fractie en *M*_max de inhoud bij 100 % [kg]. Een blijvend verschil tussen in en
uit laat het niveau zonder eind lopen. De integratietijd *M*_max/*B* zegt hoe traag de bodem is:

| | *M*_max | *B*_ontwerp | *M*_max/*B* |
|---|---|---|---|
| K-01 | 864 kg | 400 kg/h | 130 min |
| K-02 | 1908 kg | 5231 kg/h | 22 min |

*M*_max komt uit de dichtheid van elke bodem bij haar ontwerptemperatuur (hoofdstuk 4). K_p = 2·*M*_max/λ is daarmee
5186 kg/h per eenheid niveau bij K-01 en 28 622 bij K-02. De afleiding en de metingen hieronder zijn met een eerdere, aangenomen
dichtheid van 850 kg/m³ gerekend; de vorm en de keuze van λ veranderen er niet door.

**De regel.** Met een PI-regelaar op de afvoer, uit = u₀ + K_p·e + (K_p/T_i)·∫e d*t* met
e = *h* − SP, is de gesloten kring

  s² + a·s + a/T_i = 0,   a = K_p/*M*_max   [1/s],   ζ = ½·√(a·T_i)

Lambda-afstemming kiest een sluittijd λ en legt beide polen op −1/λ:

  **K_p = 2·*M*_max/λ,   T_i = 2·λ**   (dan is a·T_i = 4 en ζ = 1)

Eenheden: *M*_max in kg en λ in s geven K_p in kg/s per eenheid niveau; de kringen in
`simulatie.js` rekenen in kg/h per eenheid niveau (`lambdaAfstemmingNiveau()` in `dynamics.js`).
Dezelfde uitkomst geeft de SIMC-regel voor een integrerend proces, K_c = 1/(k′·(τ_c + θ)) en
T_i = 4·(τ_c + θ), met k′ = 1/*M*_max, dode tijd θ = 0 en τ_c = λ/2.

**Bronnen.** S. Skogestad, "Simple analytic rules for model reduction and PID controller tuning",
*Journal of Process Control* 13 (2003) 291–309 (de SIMC-regels); I.-L. Chien en P. S. Fruehauf,
"Consider IMC tuning to improve controller performance", *Chemical Engineering Progress* 86 (10)
(1990) 33–41 (lambda- of IMC-afstemming, ook voor een integrerend proces). **Uit het hoofd vermeld,
niet opnieuw in het origineel nagelezen.** De regel zelf hangt daar niet aan: hij volgt hierboven uit
de karakteristieke vergelijking, en de testsuite legt hem langs die weg en langs een eigen lineaire
kring.

**De keuze voor K-01: λ = 20 min** (`MODELAANNAMEN.lambda_niveau_K01_s`).
Dat geeft voor K-01 K_p = 6031 kg/h per eenheid niveau en T_i = 40 min. Tot v1.1.3 was het
K_p = 3·*B* = 1200 kg/h per eenheid niveau en T_i = 30 min: ζ = 0,39, een slap gedempte kring,
omdat K-01 veel inhoud heeft voor zijn kleine afvoer.

**Wat het doet na een voedingstrip met de verwarming uit.** Er komt niets meer in de bodem. In de
lineaire kring met ζ = 1 is de afwijking −(d/*M*_max)·t·e^(−t/λ), het grootst op t = λ:

  Δh_max = d·λ/(e·*M*_max)   (`niveaudalingNaStap()`)

Op dat moment staat de afvoer op nul, en daar blijft het niveau staan. Voor K-01, met d = *B*:

| | K_p [kg/h per eenheid] | T_i | Δh in het oefenmodel |
|---|---|---|---|
| tot v1.1.3 | 1200 | 30 min | 15,8 procentpunt |
| λ = 15 min | 8041 | 30 min | 3,7 |
| **λ = 20 min** | **6031** | **40 min** | **4,9** |
| λ = 30 min | 4020 | 60 min | 7,3 |

Het oefenmodel volgt de lineaire kring binnen 0,05 procentpunt. In een echte installatie is de daling
groter: de klep versterkt vlak bij de zitting minder, en de besturing reageert pas na een scancyclus.

**Geldigheid.** Een dode tijd in meting en klep die klein is tegen λ, en een afvoer die de opdracht
volgt. Buiten het ontwerppunt verandert de werkelijke versterking, en daarmee de demping. Een
kortere λ laat het niveau na een trip minder ver zakken, maar laat de afvoer harder meebewegen met
de meetruis.

**K-02, sinds v1.1.4: λ = 8 min** (`MODELAANNAMEN.lambda_niveau_K02_s`). Dezelfde regel, met een eigen
sluittijd. De bodem van K-02 is klein tegen zijn afvoer (*M*_max/*B* = 26 min, tegen 151 min bij K-01). Na een
trip die de toevoer stopt, zakt het niveau Δh = *B*·λ/(e·*M*_max) = (λ/(*M*_max/*B*))/e: met de λ van K-01 zou
dat 28 procentpunt zijn. Met 8 minuten geeft het K_p = 33698 kg/h per eenheid niveau (6,44·*B*) en T_i = 16 min,
en ζ = 1. T_i blijft boven de integratietijd van de kring van het refluxvat, dus de grootste rekenstap van het
oefenmodel verandert niet (`maximaleTijdstap()`).

Na een stop van de toevoer, in het oefenmodel met de voeding, E-01, E-03 en de reflux uit:

| | K_p | T_i | Δh lineair | Δh oefenmodel |
|---|---|---|---|---|
| tot v1.1.4 (3·*B*, 30 min) | 15694 kg/h | 30 min | 24,0 pp | 24,0 pp |
| λ = 6 min | 44930 | 12 min | 8,6 | |
| **λ = 8 min** | **33698** | **16 min** | **11,4** | **11,5** |
| λ = 10 min | 26958 | 20 min | 14,3 | |
| λ = 12 min | 22465 | 24 min | 17,1 | |

Gekozen is 8 minuten: zo blijft het niveau na zo'n stop, ook vanaf een laag setpoint, ruim boven het punt waar de
bodempomp van K-02 tegen een te laag niveau beschermd moet worden. Een kortere λ zakt minder ver, maar laat de afvoer
harder meebewegen met de meetruis, voor weinig winst. Het oefenmodel volgt de lineaire kring binnen 0,2 procentpunt.

---

## 17. De installatie: twee kolommen in serie

### 17.1 Waarom twee en niet één

Dit hoofdstuk is er gekomen nadat module 10 een tegenspraak blootlegde (8.33). Module 4 rekende aan
een bodemstroom van een paar procent, sterk opgeconcentreerd in pitch; module 5 aan een bodemstroom
van ruim de helft, C18-rijk en vrijwel pitchvrij. Stationair viel dat niet op — elke module was
intern consistent. Bij een **tijdconstante** wel, want die deelt door de stroom.

De oplossing is geen rekenkeuze maar een **fysieke**: een kolom heeft één bodemstroom. Je kunt niet
tegelijk 400 kg/h pitch en 5231 kg/h C18-rijk product uit dezelfde bodem trekken.

```
  ruwe vetzuren            lichte componenten              C16-rijk destillaat
  10 000 kg/h              naar vacuümsysteem                   4369 kg/h
        |                         ^                                 ^
        |                         |                                 |
        v                      [E-02]                            [E-04]
   +---------+   9600 kg/h    +---------+
   |  K-01   |--------------->|  K-02   |
   | 6 trap. |  pitchvrij     | 31 trap.|
   |  2,7 m  |                | 14,1 m  |
   +---------+                +---------+
     |    ^                     |    ^
     v  [E-01]                  v  [E-03]
   pitch                   C18-rijk product
   400 kg/h                    5231 kg/h
```

Apparaatnummering (`APPARATEN` in `installatie.js`), generiek, zonder verband met een bestaande installatie:

| K-01 | | K-02 | |
|---|---|---|---|
| K-01 | pitchverwijderingskolom | K-02 | fractioneerkolom C16/C18 |
| E-01 | reboiler | E-03 | reboiler |
| E-02 | condensor | E-04 | condensor |
| V-01 | destillaatvat (tot v1.1.0 "refluxvat" genoemd; K-01 heeft geen reflux, zie 17.6) | V-02 | refluxvat |
| P-01 | bodempomp (pitch) | P-02 | bodempomp (C18-rijk product) |

Het **vacuümsysteem** is gedeeld. De **vloeistofverdeler** en het **pakkingbed** hebben geen eigen
nummer (`DEELAPPARATEN`); ze zitten in de kolom, en dat een storingsscenario er toch naar verwijst
is geen reden om een nummer te verzinnen.

### 17.2 Wat het weghalen van pitch met K-02 deed

De voeding van K-02 is nu het **pitchvrije** destillaat van K-01, en module 5 rekende oorspronkelijk
mét 2 massa% pitch. Dat moest dus opnieuw — en de uitkomst is dat het nauwelijks uitmaakt:

| | Met pitch (oud) | Pitchvrij (K-02) | Verschil |
|---|---|---|---|
| N_min | 15,77 | 15,74 | −0,03 |
| R_min | 2,341 | 2,324 | −0,017 |
| N | 31,47 | 31,42 | −0,05 |
| T_bodem (met drukval) | 246,9 °C | 246,5 °C | **−0,4 K** |

**De getallen van modules 6 tot 9 staan dus,** met een correctie van minder dan een halve graad. De
reden is dat pitch maar 2 massa% was én niet-vluchtig: hij deed nauwelijks mee aan de C16/C18-
scheiding. Dat is zelf een resultaat — het laat zien dat een niet-vluchtige component de
*scheiding* van de vluchtige componenten bijna niet stoort, terwijl hij de *bodemtemperatuur* en de
*vervuiling* wél domineert.

### 17.3 K-01 is een warmteprobleem, K-02 een scheidingsprobleem

**K-01 heeft zes theoretische trappen, en dat is geen bezuiniging maar fysica.** Pitch heeft
dampspanning nul, dus is α van elke vluchtige component ten opzichte van pitch oneindig. In Fenske
staat α in de noemer — N_min = ln(…)/ln(α) — dus gaat N_min naar nul. *Scheiden van iets dat niet
verdampt kost bijna geen trappen.* Wat het wél kost is temperatuur en vacuüm: de vluchtige
componenten moeten er echt af koken, bij een druk waarbij ze niet afbreken.

**K-02 is de spiegel.** Daar verdampen de componenten allebei en ligt α rond 1,64 voor het
sleutelpaar (en veel dichter bij 1 voor de C18-componenten onderling). Α in de noemer betekent dan
véél trappen.

### 17.4 Afgeleide waarden

Alle waarden herberekend door de testsuite, bij F = 10 000 kg/h `VOEDING_GENERIEK`, topdruk 20
mbar, thermische olie 280 °C (plafond 255 °C), gestructureerde pakking.

| Grootheid | K-01 | K-02 |
|---|---|---|
| Voeding | 10 000 kg/h | 9600 kg/h |
| Bodemstroom | 400 kg/h (4,0 %) | 5231 kg/h (54,5 %) |
| Destillaat | 9600 kg/h | 4369 kg/h |
| Bodem is | residu (pitch) | product (C18-rijk) |
| Pitch in de bodem | 50,0 massa% | 0 |
| Theoretische trappen | 6 | 31,4 |
| Bedhoogte | 2,7 m | 14,1 m |
| Drukval | 3,0 mbar | 15,7 mbar |
| Bodemdruk | 23,0 mbar | 35,7 mbar |
| Bodemtemperatuur | 237,1 °C | 246,5 °C |
| Marge tot het plafond | 17,9 K | 8,5 K |
| T_bodem bij fouling ×2 | 240,4 °C (veilig) | **256,4 °C (boven plafond)** |
| T_bodem bij fouling ×4 | 246,2 °C (nog veilig) | 270,1 °C |
| τ sump (0,25 m³) | 31,9 min | 2,4 min |
| τ refluxvat | 8,0 min | 4,4 min |
| Traagste element | **de bodem** | **het refluxvat** |
| Afbraakdosis t.o.v. de andere kolom | **6,1–8,5×** | 1× |

Sluitingsfout van de balans over de hele installatie: **1,5·10⁻¹³**.

Eén waarde verdient aparte vermelding omdat zij bij gelijke druk de andere kant op wijst: **bij 20
mbar kookt de bodem van K-01 juist héter** (233,4 °C) dan die van K-02 (231,8 °C), door de
pitchverdunning. Dat K-02 in bedrijf toch 9,4 K heter draait komt volledig door de **drukval** van
zijn 31 trappen. De ketensluiting van 8.21 in zijn scherpste vorm: niet de samenstelling maar het
aantal trappen maakt K-02 tot de hete kolom.

### 17.5 Openstaand werk dat hieruit volgt

Alle vier de storingsscenario's in `src/content/storingen.js` horen bij **K-02** — terwijl de
afbraak juist in K-01 zit (8.35). Module 11 heeft dus nog **geen enkel scenario voor K-01**, en de
tests leggen dat als openstaand vast. De voor de hand liggende kandidaten:

- pitchslip van K-01 naar K-02, met een effect dat pas weken later in de drukval van K-02 zichtbaar
  wordt (8.36) — een scenario dat de cursist buiten het apparaat laat kijken waar het symptoom zit;
- de warm stilstaande K-01, waar de verblijftijd naar oneindig loopt (8.26) en waar geen alarm op
  staat;
- de bodemafname van K-01 dichtknijpen om terugwinning te verbeteren, met opconcentratie én
  verlengde verblijftijd als dubbel gevolg (10.2, 8.26).

### 17.6 De voedingsplaats van K-01 is een ontwerpkeuze

**Keuze:** de voeding komt **boven** het pakkingbed binnen; alle zes theoretische trappen zijn
strippend (`N_boven_voeding: 0` in `KOLOMMEN['K-01'].ontwerpkeuzes`).

**Waarom geen correlatie.** Kirkbride (11.4) vraagt de zware sleutel in het destillaat, en die is
bij K-01 nul: pitch heeft geen dampspanning. Het model rekent K-01 bovendien als flash met een
vaste bodemafname (module 4), niet trap voor trap, dus er is ook geen profiel waaruit de
voedingstrap zou volgen. Wat overblijft is een keuze, en die hoort als keuze te boek te staan.

**Motivatie.** Een rectificerende sectie houdt zware componenten met trappen uit de damp. Pitch
hoeft niet met trappen uit de damp gehouden te worden: het verdampt niet. Wat er wel omhoog kan
gaan zijn druppels, en die vangt een druppelvanger, geen theoretische trap. Het werk van K-01 zit
onder de voeding: de vloeistof die naar de bodem zakt bevat nog veel vetzuur, en dat moet eruit
gekookt worden voordat er maar 4 procent onderuit gaat. Dat is strippen.

**Wat de tekening daarmee doet.** Het strippende bed staat in het schema op de bodem van K-01, met
de voeding er direct boven; de ruimte daarboven is dampruimte. De tekening leest de indeling uit
`installatie.js`, en de testsuite legt tekening en model naast elkaar.

**Geen reflux naar K-01 (besloten in de tweede doorloop).** Het schema tekende een refluxstroom van
V-01 naar de top van K-01, en de uitleg van V-01 noemde reflux. Die is weggehaald. Een kolom
waarvan alle trappen strippend zijn, heeft geen rectificerende sectie, dus er valt niets terug te
refluxen. Het model kende hem ook niet ("geen scherpe reflux", `installatie.js`). Het destillaat
van K-01 gaat nu in zijn geheel naar K-02, en de testsuite houdt vast dat er geen
vloeistof op de top van K-01 terugloopt zolang `N_boven_voeding` nul is.

**Wat er in de praktijk wél bovenin zit — voor v1.2, niet gebouwd.** Een pitchkolom heeft
bovenin doorgaans een **waslaag of quench**: gecondenseerd destillaat dat over een korte laag
wordt teruggegeven, de opstijgende damp koelt en meegesleepte druppels terugslaat. Dat is geen
rectificatie in de zin van theoretische trappen. Pitch hoeft niet met trappen uit de damp te worden
gehouden, want het verdampt niet. Het is een **druppelvanger met vloeistof**.

Het doet ertoe, omdat het de **meesleepfractie** bepaalt: hoeveel pitch als druppel over de top
gaat. Die fractie staat nu als vast getal in het oefenmodel (`injecties.js`, meesleep 0,02). Het
**pitchdoorslag-scenario** (`PITCHSLIP_NAAR_K02`) hangt er direct aan. Een waslaag die droogvalt
of te weinig vloeistof krijgt, is in de praktijk een van de oorzaken van pitch in het destillaat.

Wat het vraagt: een waslaag als apparaatonderdeel in het schema, een washoeveelheid als instelling,
en een meesleepfractie die daar afhangt van washoeveelheid en dampbelasting in plaats van een vast
getal te zijn. Dat is nieuwe functionaliteit en staat daarom op de lijst voor een
volgende versie, niet in deze. Een openbare bron voor het verband tussen washoeveelheid en
meesleepfractie is gezocht en niet gevonden (19 november 2026): de enige vondst was een bijdrage van een leverancier over wassecties in
raffinaderijen, en een leveranciersbron is hier niet bruikbaar (de regels bovenaan dit bestand); het ging bovendien om ruwe olie, niet om
vetzuren.

### 17.7 De voeding: 10 000 kg/h, een ontwerpkeuze

De voeding van K-01 is **10 000 kg/h** gesplitste vetzuren (`bedrijfspuntK01()` en `installatie()` in `installatie.js`,
`beginToestand()` in `simulatie.js`). Dat is **240 t/d**: een ontwerpkeuze voor de schaal van de installatie, geen gegeven van een
bestaande installatie. **De orde**: een openbare leveranciersopgave in een vakblad
(2018) noemt complete vetzuurinstallaties met een voedingscapaciteit "up to 600 tpd". 240 t/d ligt daarbinnen, als een
middelgrote tot grote installatie.

**Wat het getal doet en niet doet.** Het model is in temperatuur en samenstelling intensief: een andere voeding verandert geen
kooktemperatuur, geen scheiding en geen zuiverheid, alleen de stromen, de duty en, via de kolomdiameter (20.13), de doorsnede en de
bodeminhoud. Bij 20 % meer voeding: dezelfde bodemtemperaturen, 169 en 360 kW meer duty in K-01 en K-02, en 20 % meer bodeminhoud.

---

## 18. Storingen als leertaken (module 11)

### 18.1 Geen nieuwe fysica

Dit hoofdstuk legt **geen stofdata en geen wetten** vast. De zeven scenario's in
`src/content/storingen.js` gebruiken uitsluitend de wetten, correlaties en getallen uit de modules
1 tot 10; wat hier bij komt is de **didactische ordening**. De tests rekenen elk getal in elk
scenario opnieuw uit de fysicamodules, zodat een scenario niet stilletjes uit de pas kan gaan lopen
met de module waarop het steunt.

### 18.2 De taakklassen

Geordend volgens 4C/ID: oplopende complexiteit, afnemende ondersteuning.
De ordening gaat **niet** over apparaat of module maar over wat de taak van de cursist vraagt.

**Bron van het model:** J.J.G. van Merriënboer en P.A. Kirschner, *Ten Steps to Complex Learning*, 3e druk,
Routledge, 2018. Geen natuurwet maar een ontwerpmodel voor lesmateriaal. De begrippenlijst zegt dat bij leertaak, taakklasse,
steun en deeltaakoefening: "Geen natuurwet", zonder wetvenster.

| # | Taakklasse | Ondersteuning | Scenario's | Kolom | Vorm |
|---|---|---|---|---|---|
| 1 | Eén apparaat, één oorzaak | hoog | `BODEMAFNAME_DICHTGEKNEPEN` | K-01 | uitgewerkt voorbeeld, met een nadoe-vraag |
| 2 | Eén waarneming, meer oorzaken | midden | `OPLOPENDE_TOPDRUK`, `UITGEDROOGD_PAKKINGBED`, `VERSTIKKING_DOOR_AANKOEKING` | K-02 | aanvullingsopdracht |
| 3 | Het signaal ontbreekt | laag | `LEK_GEMASKEERD_DOOR_REGELING`, `WARME_STILSTAND_K01` | K-02, K-01 | aanvullingsopdracht (de lek), gewone opdracht (de warme stilstand) |
| 4 | Oorzaak en gevolg gescheiden | geen | `PITCHSLIP_NAAR_K02` | K-01 → K-02 | gewone opdracht |

**De vorm per taak** (besluit van de gebruiker, 19 november 2026; `VORMEN` in `content/storingen.js`). Naar de vormen van taakondersteuning
in Ten Steps: een uitgewerkt voorbeeld geeft de taak met de hele oplossing, een
aanvullingsopdracht een deel ervan, een gewone opdracht alleen het probleem. De hulp neemt hier af over de hele reeks: taak 1 een
uitgewerkt voorbeeld met daarna een nadoe-vraag (de bodemafname naar 350 kg/h), taak 2 tot en met 5 aanvullingsopdrachten (de
verdachten met hun mechanisme staan er; het onderscheidende kenmerk achter "Toon aanwijzing", het onderscheid, de valkuil, wat je
doet en het antwoord achter "Toon de uitwerking"), taak 6 en 7 gewone opdrachten (alleen wat je ziet en de vraag; de namen van
de verdachten achter de aanwijzing). In Ten Steps neemt de steun af binnen elke taakklasse; met zeven taken in vier klassen is dat
grotendeels niet te maken, en de lek (taak 5, de eerste van klasse 3) is daarom een aanvullingsopdracht. Het eigen antwoord van de
cursist wordt niet bewaard en niet beoordeeld; de terugblik en de route zeggen in woorden welke hulp hij gebruikte. De verdeling over
de taken is een ontwerpkeuze van deze module; het boek is daarvoor niet opnieuw nagelezen.

**Waarom deze volgorde.** Klasse 4 kan niet eerder, want pitchslip vraagt dat de cursist weet
waarom K-01 vóór K-02 staat (8.36) en wat pitch in een lang pakkingbed doet. Klasse 3 kan niet vóór
2, want je merkt pas dat een signaal *ontbreekt* als je weet welke signalen er horen te zijn.
Klasse 1 staat vooraan omdat daar het rekenwerk expliciet is: x = F·z/B en τ = M/B, twee sommen die
in elke latere taak terugkomen.

**Wat de volgorde níet is:** een ordening naar ernst. De warme stilstand uit klasse 3 kost in een
etmaal meer product dan de dichtgeknepen bodemafname uit klasse 1 in een maand. De ordening gaat
over de moeilijkheid van het *denkwerk*.

**Over de verdeling per kolom:** vier scenario's bij K-02 en drie bij K-01, terwijl de meeste
schade in K-01 ontstaat (8.35). Dat is geen weeffout maar leerstof: K-02 heeft meer scenario's
omdat daar meer **te zien** is — een grotere drukval, een langer bed, een krappere marge. *Het
apparaat met de beste meetsignalen is niet het apparaat met de grootste problemen.*

### 18.3 Pitchslip: de leertaak waar de getallen het werk doen

Het zwaarste scenario, en het enige waarin oorzaak en gevolg zowel van **plaats** als van
**tijdschaal** verschillen: meesleuren in K-01 is een verschijnsel van uren, de aankoeking in K-02
bouwt zich op over weken.

De didactische kern is één contrast:

| 1 % slip van de pitchstroom | |
|---|---|
| In kg/h | 2,0 kg/h |
| **In het destillaat van K-01** | **208 ppm** — oogt verwaarloosbaar |
| **Naar het bed van K-02** | **1,44 ton per maand** — is het niet |

Dezelfde meting, twee eenheden, tegengestelde conclusies. De regel die eruit volgt: **beoordeel
deze analyse in kilo per maand, niet in ppm.**

Daar komt een harde redenering bij die de meting ondubbelzinnig maakt: pitch heeft dampspanning
nul, dus volgens het evenwicht hoort er **nul** pitch in het destillaat van K-01 te zitten. Elke
meetbare waarde bewijst dus meesleuren — er is geen andere weg omhoog.

**De negatieve bevinding hoort er ook bij.** De drukval over K-01 is met 3,0 mbar te klein om als
vroegsignaal te dienen: een verslechtering van dertig procent blijft onder de mbar en verdwijnt in
de ruis van een drukmeting op een vacuümkolom. K-02 heeft met 15,7 mbar wél resolutie — maar daar
is het het gevolg en niet de oorzaak. *De kolom met het goede meetsignaal is niet de kolom waar het
probleem begint.* Wat in K-01 wél vroeg waarschuwt zijn de **belasting** en het **sumpniveau**,
omdat meesleuren een belastingverschijnsel is.

### 18.4 Afgeleide waarden

Alle waarden herberekend door de testsuite.

| Scenario | Grootheid | Waarde |
|---|---|---|
| Pitchslip | pitchstroom in de voeding | 200 kg/h |
| | 1 % slip | 2,0 kg/h |
| | idem, in het K-01-destillaat | 208 ppm |
| | idem, per maand naar K-02 | 1,44 ton |
| | drukval K-01 / K-02 | 3,0 / 15,7 mbar |
| Warme stilstand | verblijftijd K-01 in bedrijf | 31,9 min |
| | dosis na 24 h stilstand | **45×** de normale dosis |
| | verandering in afbraak*snelheid* | **geen** (temperatuur ongewijzigd) |
| Dichtgeknepen bodemafname | pitch bij B = 400 kg/h | 50,0 massa% |
| | pitch bij B = 300 kg/h | 66,7 massa% |
| | bodemtemperatuur 400 → 300 kg/h | 237,1 → **243,2 °C** (+6,1 K) |
| | snelheidsfactor | 1,32–1,64 |
| | verblijftijd 400 → 300 kg/h | 31,9 → 42,6 min (×1,33) |
| | **dosis** | **1,76–2,18×** |
| | harde ondergrens bodemstroom | 200 kg/h (2,00 massa%) |

De laatste regel van de tabel verdient aandacht in de leertaak: van 400 naar 300 kg/h is **zes
graden** — ruim binnen elke alarmgrens — en toch een **verdubbeling** van de afbraak, omdat de
verblijftijd er bovenop komt. Dat is de rekensom die taakklasse 1 moet achterlaten.

---

## 19. Het eindproduct (module 12)

### 19.1 Geen fysica, wel drie harde eisen

Dit hoofdstuk legt geen stofdata vast. Module 12 is de schermkant, en wat er te bewaken valt zijn
drie eisen van het project, hieronder. Alle drie zijn ze machinaal getoetst.

**Fysica en UI strikt gescheiden**, in beide richtingen:

| Richting | Controle |
|---|---|
| `src/physics/` → DOM | geen `document`, geen `window` |
| `src/content/` → DOM | idem |
| `src/physics/` → `src/ui/` | geen import |
| `src/ui/` → eigen wetten | geen `WET_`/`CORRELATIE_` gedefinieerd, geen stofdata |

De laatste regel is de anti-duplicatietest: de UI mag ophalen en presenteren, niets herhalen.

**Bij elke berekening de wet erachter.** Voor elke wet die via een apparaat op het
scherm komt, wordt gecontroleerd dat naam, formule, grootheden met SI- én schermeenheid, en een
uitleg in gewone taal bestaan — én dat `src/ui/wet.js` die velden werkelijk gebruikt. Bij een
correlatie is `waaromGeenWet` verplicht.

**Huisstijl en toegankelijkheid:**

| Eis | Hoe hij wordt bewaakt |
|---|---|
| Kleuren als CSS-variabelen op `:root` | patroontest op de CSS |
| Licht en donker thema | `[data-thema='donker']` plus `prefers-color-scheme` |
| Elke gebruikte variabele bestaat | gedefinieerde tegen gebruikte `var()` |
| Elke kleurwaarde is geldig | hex-patroon per waarde |
| Geen tekst onder 14 px | elke `font-size` in rem en px nagerekend |
| Betekenis niet alleen via kleur | randdikte, merkteken, lijnsoort, woord in elk merkje |
| Toetsenbordbediening | `role`, `tabindex`, Enter/spatie, `aria-pressed`, `:focus-visible` |
| Geen externe lettertypen | alleen `system-ui`, geen `@font-face` |

De kleurwaardentest is er gekomen nadat er drie ongeldige hexwaarden in het donkere thema bleken
te staan. Een browser negeert zo'n regel **stil**: de variabele valt terug op zijn vorige waarde en
niemand merkt het, behalve dat de kleuren niet kloppen.

### 19.2 Waarom de bundel een modulesysteem heeft

De voor de hand liggende bundelaanpak — alle bestanden achter elkaar plakken en de import- en
exportregels weghalen — zou hier **stil verkeerde uitkomsten** geven. De fysicamodules hebben elk
hun eigen private hulpfuncties, en `eisGetal` komt negen keer voor, `eisPositief` zes keer,
`bisectieStijgend` en `eisSamenstelling` twee keer. In een gedeelde scope overschrijven die elkaar,
en de laatste definitie wint — met controles die bij de verkeerde module horen.

`build/bundel.js` geeft daarom elke module een eigen functiescope, met een register van dertig
regels eromheen (`__module` / `__vraag`). Dat is precies genoeg om de modulegrenzen te bewaren die
de broncode al heeft. Het script weigert exportvormen die het niet kent, in plaats van ze over te
slaan.

### 19.3 De ene toegestane URL

`http://www.w3.org/2000/svg` staat in het eindproduct en dat is goed. Het is geen adres maar een
**XML-naamruimte**: de identificatie die `document.createElementNS()` nodig heeft om een SVG-element
te maken in plaats van een HTML-element met dezelfde naam. Geen browser haalt hem op.

Hij staat als enige op een allowlist in de test, en een tweede controle eist dat hij **alleen** op
regels met `createElementNS` voorkomt. Zo is het een bewuste uitzondering met een reden, en geen
gat waar later een CDN doorheen glipt.

### 19.4 De bundel wordt uitgevoerd, niet alleen gelezen

Naast de tekstuele controles wordt het script uit `dist/vetzuurkolom.html` **gedraaid** tegen een
minimale nep-DOM (ongeveer honderd regels). Het moet zonder fout uitvoeren, meer
dan vijftig elementen opbouwen, en beide kolommen plus de cijfers uit de fysicamodules in de tekst
hebben.

Dat vangt wat een tekstcontrole nooit vangt: een weggevallen export, een gebotste naam, een
verkeerde volgorde in het register. Een bundel die stil verkeerd is samengevoegd levert een
prachtig HTML-bestand op dat in de controlekamer een leeg scherm geeft.

### 19.5 Afgeleide waarden

| Grootheid | Waarde |
|---|---|
| Omvang `dist/vetzuurkolom.html` | **793,1 kB** |
| Omvang van de bron (js + css) | 806 kB |
| Grootste bronbestand | `src/content/wetten.js`, 126 kB |
| waarvan uitlegtekst | 69 kB |
| Elementen die de bundel opbouwt | 146 |
| Externe verwijzingen | 0 (op de XML-naamruimte na) |
| Externe bibliotheken | 0 |

Er wordt **niet geminificeerd**. Dat zou het bestand ruwweg halveren, maar ook onleesbaar maken, en
een offline bestand van 0,8 MB laadt direct van schijf. De keuze is dus leesbaarheid en
controleerbaarheid boven omvang; komt daar ooit een reden voor terug, dan is minificatie een stap
in `build/bundel.js` en verandert er niets aan de bron.

### 19.6 Het bestand wordt bij elke versie opnieuw gebouwd — en waarom

`vetzuurkolom.html` is geen los bewerkt bestand maar wordt bij elke versie opnieuw uit de bron gebouwd, en een toets eist
dat het precies gelijk is aan een verse build. Reden: het bestand is *wat er wordt uitgedeeld aan cursisten*. Een bundel die
niet bij de bron hoort, deelt stilzwijgend oude getallen uit; de bodemtemperatuur van K-02 is in de loop van de bouw
verschoven van 232,1 naar 246,9 naar 246,5 °C, en de afbraakfactoren die daaraan hangen zijn meeverschoven.

---

## 20. Het oefenmodel (simulatie, stap 1: K-01)

### 20.1 Wat hier wel en niet uit een bron komt

Dit hoofdstuk legt geen stofdata vast. Alle stofwaarden die het oefenmodel gebruikt komen uit de
hoofdstukken 2 tot en met 17; `simulatie.js` rekent er alleen mee in de tijd. Wat hier wordt
verantwoord zijn de **modelaannamen**: de getallen die bepalen hoe snel het model reageert.

Dat onderscheid is de kern van dit hoofdstuk en het is machinaal getoetst:

| | Waar komt het vandaan | Hangt af van een aanname? |
|---|---|---|
| **Waar het model op uitkomt** | massabalans, evenwicht, energiebalans (module 3–7) | nee |
| **Hoe snel het daar komt** | holdup, tijdconstanten, regelaarinstellingen | ja, volledig |

Dit is `HOLDUP_IS_GEEN_BALANS` uit module 10, nu als bouwregel. Een cursist die de sump groter
maakt ziet een tragere kolom en **dezelfde** bodemsamenstelling. Als een conclusie in de module
ooit van een van de onderstaande getallen zou gaan afhangen, is dat een fout in de module en niet
een onnauwkeurigheid in de aanname.

### 20.2 De aannamen met een getal

Alle drie staan bij elkaar in `MODELAANNAMEN` in `src/physics/simulatie.js` (sinds de ronde van de dichtheid is de vierde, de
dichtheid in de bodem, een lijn door metingen: hoofdstuk 4). Eén draagt in de code nog een open bronmerk: `tau_profiel`, een
ontwerpkeuze waarvan de afleiding nog gemaakt moet worden (20.3). De overcapaciteit van de reboiler niet (v1.1.0): zij is een ontwerpmarge,
geen stofwaarde, en krijgt nooit een literatuurbron. Een TODO beloofde dat wel. Zij staat nu als
ontwerpkeuze gemarkeerd, met haar reden: zonder marge is het ontwerppunt precies het maximum en
heeft de operator geen ruimte. De zuigreserve heeft sinds v1.1.6 een bron.
Ze staan daar bij elkaar zodat zichtbaar is hoeveel er wordt aangenomen: verspreid door de code zou
niemand dat meer zien.

| Aanname | Waarde | Herkomst | Waar hij op doorwerkt |
|---|---|---|---|
| `overcapaciteit_reboiler` | 1,25 | ontwerpkeuze: gebruikelijke ontwerpmarge op een warmtewisselaar | hoeveel duty de operator boven ontwerp kan vragen |
| `tau_profiel_ontwerp_min` | 25 min | ontwerpkeuze; eerste-ordevertraging τ = V·ρ/L met V de vloeistofinhoud van het bed. Voor K-02 komt 25 min overeen met een vloeistoffractie van ongeveer 6 % (3–5 % geeft 12–20 min); voor K-01 volgt uit de maten 2–3 min, dus daar reageert het model trager dan een echte kolom. Open voor een volgende versie. | hoe ver de **gemeten** samenstellingen op de trend achterlopen (20.3) |
| `zuigreserve` | 1,5 | de capaciteit van het vacuümsysteem met de klep helemaal open: 150 % van ontwerp, J. Lines, "Operating vacuum distillation ejector systems", *PTQ* Q4 2016, aanbeveling 10 | hoe ver de drukregeling een lek nog kan opvangen |

**Wat de bron zegt over de zuigreserve.** Lines, *PTQ* Q4 2016, aanbeveling 10: de tweede en derde ejectortrap krijgen 150 %
capaciteit, uitgevoerd als drie elementen van 50 %, zodat het systeem tot 150 % van de ontworpen hoeveelheid inerte gassen
aankan; de eerste trap bijvoorbeeld als drie treinen van 40 %.
- **Geldigheid.** Het artikel gaat over vacuümkolommen in raffinaderijen, niet over vetzuurdestillatie. De aanbeveling gaat
  over de inerte gassen, en leklucht is een inert gas: zij condenseert niet in de condensor en moet volledig door het
  vacuümsysteem worden afgevoerd. Daarom geldt de 150 % hier voor de capaciteit tegen het lek. Het model kent één
  zuigcapaciteit en geen afzonderlijke trappen.
- Tot ronde 3: 1,6, zonder bron. Met de klep helemaal open en een lek van L keer het ontwerplek staat de top op
  20 mbar · L / 1,5; de klep staat in het ontwerppunt op 1 − ln 1,5 / ln 50 = 89,6 % (was 88,0 %).

**De bodeminhoud van 0,25 m³ is weg** (v1.1.6). Zij stond hier als `V_sump_m3`, maar was sinds 20.13 uit het procesmodel
verdwenen en leefde alleen nog als de vallende film van module 10 (`CONFIGURATIES` in `dynamics.js`). Die vallende film is nu de
bodem van K-01 uit 20.13: V = A · h = 3,94 m² · 0,15 m = 0,591 m³. `dynamics.js` heeft er geen eigen getal meer voor; de bodem gaat
als argument mee (`configuratie('VALLENDE_FILM', installatie().K01.sump)`). De les van module 10 en haar twee rekenvoorbeelden
rekenen ermee: bij 340 kg/h is τ = 0,591 · 850 / 340 h = 88,7 min (was 37,5).

`lek_ontwerp_kgh` en `T_condensoruit_C` zijn **geen nieuwe aannamen**: het eerste is
`LEKNIVEAUS.NORMAAL` uit module 7 (10 kg/h, 13.2), het tweede de condensoruitlaat van 100 °C uit het
rekenvoorbeeld bij `WET_IDEALE_GASWET`. Ze zijn hier overgenomen in plaats van opnieuw bedacht.

### 20.3 `CORRELATIE_PROFIELVERTRAGING` — geen wet, en met opzet

    dx/dt = (x_stationair - x) / tau,   met tau = tau_ontwerp * (L_ontwerp / L)

**Wat zij doet** (herschreven in v1.1.6; tot dan stond hier dat zij het kolomprofiel vertraagt). Sinds v1.1.0 rekent
het **proces** met de samenstelling die module 5 voor de huidige instelling geeft, zonder vertraging: de temperaturen en de balans
volgen direct (QUASI_STATIONAIRE_SCHEIDING, 8.31). Deze correlatie vertraagt alleen wat de cursist als **gemeten** samenstelling
ziet: de pitchfractie in de bodem van K-01 (`x_pitch_gemeten`) en de lichte sleutel in het C18-product van K-02
(`x_LK_in_B_gemeten`). Beide staan in een trend en in de melding van de kolom. Daarvoor lopen zij met een eerste-orde-vertraging
naar de werkelijke waarde, in plaats van er meteen te zijn. Het veld `tau_profiel_min` in de meting, de regel "Profielvertraging op
dit moment" in de melding en de toestand `dosis` (∫ dt / τ, door niets gelezen) zijn in ronde 3 weggehaald; het gedrag is
hetzelfde gebleven.

**Waarom er geen wet onder zit.** De nauwkeurige weg zou trap voor trap rekenen zijn. Module 8
stelde vast dat theoretische trappen in een pakkingbed niet bestaan (`CORRELATIE_HETP`), dus zou
dat schijnnauwkeurigheid opleveren. Het alternatief — de scheiding ogenblikkelijk laten veranderen —
is erger: dan springt de zuiverheid op het scherm zodra de cursist aan een knop draait, en dat
leert precies het omgekeerde van wat module 11 wil overbrengen.

**De voorwaarde die haar bruikbaar houdt: zij beweegt mee met de belasting.** De tijdconstante is
ruwweg holdup gedeeld door vloeistofstroom, dus bij halve doorzet duurt het twee keer zo lang. Een
**vaste** vertraging zou de cursist leren dat een teruggenomen kolom sneller reageert, en dat is
aantoonbaar onjuist — terwijl terugnemen precies is wat hij gaat doen zodra er iets misgaat.

**Geldigheidsbereik.** Ongeveer 40 tot 110 procent van de ontwerpbelasting. Onder de minimale
bevochtiging (module 8) verliest zij haar betekenis: dan is er geen doorlopend profiel meer om te
vertragen, maar een bed met droge banen. Boven het floodingpunt evenmin.

**Wat zij wel en niet geeft.** Wel een plausibele **tijdschaal**. Niet een voorspelde
**responsvorm**: een echte kolom reageert niet zuiver eerste-orde, er zit dode tijd in, en elke
component loopt anders in. De 25 minuten bij ontwerpbelasting is een aanname en geen meetwaarde;
er is geen responsiemeting aan deze kolom. **Af te leiden** met de tijdconstante van een kolomprofiel uit S. Skogestad,
"Dynamics and control of distillation columns: a tutorial introduction", *Trans IChemE* 75 A (1997), vgl. (56), met de holdup van
het bed als aanname; een eerste schatting voor K-02 geeft ongeveer 2 uur in plaats van 25 minuten (open voor een volgende versie).

Dit is dezelfde status als Gilliland (14.3) en HETP (13.2): een gemarkeerde correlatie met een
geldigheidsbereik, geen natuurwet.

### 20.4 De topdruk is een uitkomst, geen invoer

In de modules 4 tot en met 9 was de topdruk een gegeven: je vulde 20 mbar in en daar rekende alles
mee. In het oefenmodel kan dat niet meer, want dan zou een luchtlek geen gevolgen hebben.

De druk volgt daarom uit een evenwicht, en dat evenwicht is zuiver de ideale gaswet
(`WET_IDEALE_GASWET`, hoofdstuk 9):

- het lek levert een **molstroom** aan;
- het vacuümsysteem zuigt een **volumestroom** weg;
- de druk stelt zich in op de waarde waarbij die twee elkaar opheffen.

De zuigcapaciteit is **genormeerd op het ontwerppunt**, precies zoals de warmteoverdracht van de
reboiler in module 6: bij het ontwerplek (5 kg/h) en een volle klepstand hoort per definitie de
ontwerpdruk. Zo komt er geen verzonnen apparaatgetal bij.

Het gevolg is dat het lek niet als scenario hoeft te worden ingebouwd — het zit in de balans. Bij
een verdubbeld lek en een vaste klepstand verdubbelt de druk, en dat is geen modelkeuze maar de
ideale gaswet.

### 20.5 `ZUIGCAPACITEIT_IS_CONSTANT_AANGENOMEN`

De aanname zit niet in het evenwicht maar in de **capaciteit**. In dit model levert het
vacuümsysteem bij elke druk dezelfde kubieke meters per seconde, alleen geschaald met de klepstand.

Een echte stoomejector doet dat niet. Hij heeft een ontwerpzuigdruk, zijn capaciteit loopt
daarnaast terug, en onder een zekere tegendruk **slaat hij af** — dan stort het vacuüm in plaats
van geleidelijk weg te lopen. Een vloeistofringpomp kent op zijn beurt een grens bij de
dampspanning van zijn werkvloeistof.

Wat het model dus goed geeft is de **richting en de orde**: meer lek is meer druk, meer capaciteit
is minder druk, en een drukregeling heeft daarin een eindige marge. Wat het **niet** geeft is het
instortpunt van een ejector. Bij een groot lek toont de simulatie een mildere versie van wat er in
bedrijf gebeurt, en dat mag niet als geruststelling worden gelezen.

Deze notitie is aan module 7 gekoppeld: zodra het vacuümsysteem zelf leerstof wordt —
ejectortrappen, tussencondensors, het afslaan — hoort de capaciteitscurve erbij en vervalt deze
vereenvoudiging.

### 20.6 Auto of hand: het onderscheid is zelf leerstof

Alle drie de regelkringen van K-01 — druk, niveau, temperatuur — hebben een stand. In **auto**
stelt de cursist een setpoint in en zoekt de regelaar de klepstand erbij; in **hand** zet hij de
klep rechtstreeks en regelt er niets.

Dat is geen schermdetail. Een kring die per ongeluk in hand blijft staan is een klassieke fout, en
bij een storing verschilt het beeld sterker dan het lijkt. Bij een verdubbeld luchtlek, met verder
alles gelijk:

| Drukkring | Topdruk na 30 min | Bodemtemperatuur | Wat de operator ziet |
|---|---|---|---|
| **auto** | 25,0 mbar | 243,3 °C | de klepstand loopt naar zijn grens; de druk wijkt maar deels af |
| **hand** | 39,9 mbar | 248,4 °C | de druk verdubbelt zichtbaar, de duty loopt tegen het mediumplafond |

Hetzelfde lek, twee verschillende beelden, en het verschil is een schakelaar. Dat valt met een
tekst niet over te brengen; dat moet je zien gebeuren. De regelkring die het beeld het sterkst
kleurt is de temperatuurkring —
zie `ONGECOMPENSEERDE_TEMPERATUUR_MASKEERT_LEK` in hoofdstuk 16.

### 20.7 Wat de tests hiervan bewaken

| Eis | Toets |
|---|---|
| De eindtoestand hangt niet van `tau_profiel_ontwerp_min` af | dezelfde stationaire toestand bij een halve en een dubbele waarde, ander pad |
| De vertraging beweegt mee met de belasting | halve belasting geeft een dubbele tijdconstante |
| De holdup verandert de samenstelling niet | dezelfde stationaire pitchfractie bij een andere `V_sump_m3` |
| De verdampingswarmte hoort bij de **damp** | molmassa en ΔH<sub>vap</sub> uit de dampsamenstelling, niet uit de bodemvloeistof |
| De druk volgt de ideale gaswet | dubbel lek bij vaste klepstand geeft dubbele druk |
| De massabalans sluit in de tijd | in- min uitstroom gelijk aan de ophoping |

---

### 20.8 Stap 2: K-02 en de koppeling tussen de twee kolommen

**De koppeling zelf is één regel.** Wat K-01 bovenlangs kwijtraakt is de voeding van K-02:

    F(K-02) = F(installatie) - B(K-01)

Daar zit geen aanname in; het is de massabalans van module 4. Wat er wél bij komt is een gevolg dat
niet in een stationaire berekening zichtbaar is: tussen een oorzaak in K-01 en het gevolg in K-02
staat een hele kolom, met zijn eigen tijdconstanten.

**Eén vacuümsysteem voor beide kolommen.** Het processchema tekent het vacuümsysteem als gedeeld, en
het model volgt dat: er is één topdruk, één klepstand en één drukregeling. Een lek treft dus beide
kolommen tegelijk, terwijl een operator meestal naar één van de twee kijkt.

**K-02 heeft vier regelkringen waar K-01 er twee heeft**, en dat is geen willekeur maar een gevolg
van de massabalans: bij K-02 is de bodem een **product** en geen residu, dus hangt er ook aan de
topkant een productstroom die geregeld moet worden.

| Kring | Stuurt | In auto | In hand |
|---|---|---|---|
| Bodemniveau | bodemafname B | setpoint op het niveau | klepstand als kg/h |
| Refluxvatniveau | destillaatafname D | setpoint op het vatniveau | klepstand als kg/h |
| Reflux | refluxstroom L | **verhouding** R, dus L volgt D | vaste stroom |
| Temperatuur | reboilerduty Q | setpoint op de bodemtemperatuur | duty als kW |

De refluxkring is didactisch de interessantste. In **auto** is het een verhoudingsregeling: die
*rekent* en zoekt niet, dus er is geen regelafwijking en geen integraal. In **hand** staat er een
vaste stroom. Zakt de doorzet dan weg, dan blijft de reflux staan en **loopt de verhouding op**: bij
halve doorzet van 3,00 naar 6,00. De kolom scheidt dan scherper dan bedoeld en vraagt meer duty dan
nodig — en bij K-02, die maar 8,5 K onder het mediumplafond draait, is dat geen onschuldig detail.

### 20.9 De scheiding andersom gelezen: `scheidingUitReflux()`

> **Vervangen in v1.1.0.** Wat hieronder staat, beschrijft de functie zoals zij tot v1.0.1 was: zij
> las alleen de reflux en gaf beide sleutels dezelfde terugwinning. Sinds v1.1.0 leest zij ook de
> verdeling tussen top en bodem. De redenering "ontwerper tegenover operator" geldt nog; de formule
> en de zoektocht staan in **20.18**.

Module 5 rekent zoals een **ontwerper** rekent: eis een zuiverheid, hoor hoeveel trappen je nodig
hebt. Een **operator** staat er andersom voor. Het bed ligt vast; wat hij verandert is de reflux. Zijn
vraag is: *welke zuiverheid haal ik met dit bed bij deze reflux?*

Er is geen gesloten formule voor die omkering, dus wordt de terugwinning gezocht waarbij Gilliland
precies het aantal trappen oplevert dat de kolom heeft. De zoektocht is veilig omdat een scherpere
eis altijd meer trappen vraagt: er is precies één antwoord, en een test controleert die monotonie.

**De ijking.** Bij de ontwerpreflux van 3,021 komt er precies de ontwerpterugwinning van 98,00 %
uit. Dat is geen toeval maar de omkering die klopt — en een test die meteen scheef gaat staan als er
iets aan verandert.

| Reflux R | Terugwinning | Lichte sleutel in het C18-rijke product |
|---|---|---|
| 2,4 | 94,6 % | 4,81 % |
| 2,7 | 96,8 % | 2,89 % |
| **3,021 (ontwerp)** | **98,00 %** | **1,82 %** |
| 4,0 | 99,3 % | 0,69 % |

Onder ongeveer R = 0,96 is er niets meer te scheiden: dat is het minimum van Underwood bij de
laagste terugwinning waar nog van scheiding te spreken valt. Het model meldt dat dan als **grens**
en niet als uitkomst.

**Wat deze omkering erft.** Gilliland is een correlatie en geen natuurwet (`CORRELATIE_GILLILAND`,
`GILLILAND_IS_GEEN_WET`). De omkering erft dat volledig: zij geeft de **richting** en de **orde** van
wat reflux met een scheiding doet, niet een voorspelde zuiverheid op een tiende procent. Fenske en
Underwood eronder zijn wel wetten, en de tests controleren dat het resultaat beide statussen
meldt.

### 20.10 De pitchslip: waarom hij pas weken later opvalt

Pitch komt K-02 niet in door te verdampen — Raoult verbiedt dat en dat blijft zo. Hij komt erin door
**meesleep**: druppels bodemvloeistof die met de damp mee naar boven gaan. Dat is een mechanisch
verschijnsel en staat daarom als aparte instelling, met de waarde nul als normaal bedrijf.

Het scenario laat iets zien dat een stationaire berekening niet kan tonen. Bij 2 % meesleep:

| | Na 1 uur | Na 1 dag | Na 30 dagen |
|---|---|---|---|
| Pitch in het C18-rijke product | 0,076 % | 0,076 % | 0,076 % |
| Pitch in totaal in K-02 gebracht | 4 kg | 96 kg | **2880 kg** |

De **concentratie** is binnen een uur stationair en blijft onder elke analysegrens hangen, want
alles wat erin komt gaat er met het product weer uit — het is geen ophoping. Wat níét weggaat is wat
er onderweg achterblijft op de pakking en in de reboiler, en dat loopt op met de ingebrachte
**massa**.

Daarmee staat de kern van het scenario in een getal: **oorzaak en gevolg zijn gescheiden in tijd**.
De slip is er meteen, de schade pas na weken. Een operator die K-02 ziet vervuilen gaat in de
verkeerde kolom zoeken, en de meting die hem had kunnen waarschuwen — de pitchconcentratie in het
product — wijst nergens naar.

### 20.11 Twee kolommen, twee dynamische verhalen

Het oefenmodel hoort de conclusie van module 10 te **reproduceren** en niet tegen te spreken. Dat is
als test vastgelegd, en het heeft zich meteen bewezen.

| | Bodem | Refluxvat | Traagste |
|---|---|---|---|
| K-01 | 31,9 min | 8,0 min | **bodem** |
| K-02 | 2,4 min | 4,4 min | **refluxvat** |

Dezelfde verdamper, tegengesteld gedrag — omdat K-01 vier procent van zijn voeding onderuit trekt en
K-02 meer dan de helft. Wie van de ene kolom naar de andere loopt, moet zijn geduld omzetten.

**Een aanname die is teruggedraaid.** Een eerste versie gaf K-02 een bodem van 1,5 m³, met als
redenering dat zijn bodemstroom veel groter is. Dat was verkeerd om geredeneerd — een grotere stroom
vraagt geen groter vat — en het gevolg was een bodemtijdconstante van bijna een kwartier, waarmee het
oefenmodel `TWEE_KOLOMMEN_TWEE_VERHALEN` tegensprak. K-02 heeft nu dezelfde kleine sump als K-01,
en dat is bovendien inhoudelijk het juiste antwoord: module 10 koos voor beide kolommen een
vallende-filmverdamper juist om de verblijftijd kort te houden, en bij K-02 met 8,5 K marge geldt
dat argument nog sterker.

De fout is gevonden doordat de melding van het model naast die van module 10 werd gelegd. Dat is
hier tot test gemaakt: de twee getallen moeten binnen vijf procent overeenkomen.

### 20.12 Drie controles voor stap 3, en wat zij opleverden

#### A. De refluxknop werkt NIET door naar de drukval — aandachtspunt

**Bevinding: de koppeling bestaat niet.** `drukprofiel()` in `packing.js` rekent
`dp = N · dp_per_trap · foulingfactor` en kent de dampbelasting niet. Het oefenmodel roept die
functie aan, dus blijft de drukval van K-02 op **15,71 mbar staan, wat de reflux ook doet**:

| R | Dampstroom | f = V/V_ontwerp | dp **nu** | dp bij dp ∝ f² | p_bodem nu | p_bodem bij f² | T_bodem nu | T_bodem bij f² |
|---|---|---|---|---|---|---|---|---|
| 2,0 | 13 202 kg/h | 0,752 | 15,71 | 8,87 | 35,7 | 28,9 | 244,7 | 239,1 |
| **3,021** | 17 567 | 1,000 | 15,71 | 15,71 | 35,7 | 35,7 | 246,5 | 246,5 |
| 3,5 | 19 650 | 1,119 | 15,71 | 19,66 | 35,7 | 39,7 | 246,7 | 249,5 |
| 4,0 | 21 820 | 1,242 | 15,71 | 24,24 | 35,7 | 44,2 | 246,7 | 252,5 |

De bodemtemperatuur *beweegt* nu wel met R (244,7 → 246,7 °C), maar dat is volledig het
**samenstellingseffect**: scherper scheiden zet meer C18 in de bodem en die kookt hoger. Van de
drukval komt er niets.

Het effect dat ontbreekt is niet klein. Over dit refluxbereik zou de drukval met een factor 2,7
lopen en de bodemtemperatuur **13,4 K** in plaats van 2,0 K, met het mediumplafond op 255 °C in
zicht. Dat is dezelfde orde als de 14,8 K die in module 8 de hele afbraakketen in gang zette.

Module 8 *weet* dit en zegt het ook: het geldigheidsbereik van `CORRELATIE_DRUKVAL` luidt "geldt bij
ONTWERPbelasting en met SCHONE pakking. Meer belasting geeft meer drukval (ruwweg met het kwadraat
van de dampsnelheid)". `werkgebied()` in dezelfde module rekent die kwadratische regel al uit, mét
F-factor en floodingcontrole — maar staat als losse analysefunctie naast `drukprofiel()`, en het
oefenmodel gebruikt hem niet.

**Wat er nodig is om het wel te koppelen** (nog niet gebouwd):

| Nodig | Bestaat al? |
|---|---|
| Doorsnede A van elk bed | **nee** — nergens in `src/physics/`; alleen `diameterUitFfactor()` om er een af te leiden |
| Ontwerp-F-factor als fractie van flooding | **nee** — de tests hanteren 75 %, maar dat staat niet in de fysica |
| Volumestroom van de damp uit V en de ideale gaswet | ja (module 7) |
| Drukval als dp_ontwerp · f² | ja, in `werkgebied()` |
| F-factor en floodinggrens | ja (`fFactor()`, `F_max` per intern) |

De twee ontbrekende getallen zijn **apparaatgegevens zonder bron**, precies de categorie waarvoor
de regels bovenaan dit bestand eisen dat het er expliciet bij staat. Een koppeling bouwen zonder die twee betekent
een verzonnen diameter, en daar hangt dan de hele bodemtemperatuur aan.

Twee bijkomende punten voor wie dit later bouwt:

1. **Een hogere bodemdruk verlaagt de F-factor.** F = u·√ρ met u ∝ 1/p en ρ ∝ p, dus F ∝ 1/√p. De
   kwadratische opslag op de drukval en de floodingcontrole werken dus tegen elkaar in; een lineaire
   schaling van F met de belasting is een bovengrens.
2. **De duty loopt eerder vast dan de pakking.** Bij R = 4,0 raakt de reboiler van K-02 al het
   mediumplafond (`isDutyBegrensd`), bij f = 1,24. Flooding zou bij 75 % ontwerpbelasting pas bij
   f = 1,33 komen, dus rond R = 4,4. **Verstikking komt vóór flooding** — en dat is meteen het
   antwoord op de vraag bij welke R de floodinggrens wordt geraakt: bij deze kolom niet, want de
   duty is er eerder op.

#### B. Waar de meegesleepte pitch in K-02 blijft

De componentbalans over K-02, met de pitch die met de voeding meekomt:

| | Erin | Naar het C18-product | In de sump | Sluitfout |
|---|---|---|---|---|
| Na 1 uur | 4,0 kg | 3,8 kg | 0,16 kg | 4·10⁻¹⁴ % |
| Na 7 dagen | 672 kg | 672 kg | 0,16 kg | 3·10⁻¹⁴ % |
| Na 30 dagen | 2788 kg | 2788 kg | 0,16 kg | 1·10⁻¹⁴ % |

**De sump houdt de pitch niet vast.** Hij staat er met een vaste voorraad van 0,16 kg en groeit niet;
meer dan 99,99 % gaat er met het **product** weer uit. Dat is waarom de concentratie binnen een uur
stationair is (20.10) en waarom er van ophoping geen sprake is.

De balans sluit tot 10⁻¹⁴ %, ruim binnen de eis van 0,01 %, en staat als test.
Zij is meer dan een formaliteit: zou er pitch uit de balans verdwijnen, dan zou dat betekenen dat er
pitch verdampt — en dat is precies wat `DAMPEIGENSCHAP_UIT_DAMPSAMENSTELLING` verbiedt.

Wat **niet** in deze balans zit is wat er onderweg achterblijft op de pakking en in de reboiler. Dat
is de werkelijke schade, en het model rekent hem niet: het houdt alleen de ingebrachte massa bij.

#### C. De verblijftijd in de bodem van K-01 is NIET verwaarloosbaar — aandachtspunt

**Eerst wat niet kan.** Een *fractie* afbraak is met deze module niet te berekenen. Daarvoor is de
pre-exponentiële factor A nodig, en die is voor dit systeem niet met een bron te onderbouwen (15.2).
`afbraakmaat()` geeft daarom een dimensieloze **verhouding** en geen hoeveelheid. Elke absolute
fractie die hier zou staan, zou verzonnen zijn.

Wat wel kan is de dosis van de sump afzetten tegen die van de reboilerfilm. De bodem van K-01 staat
**75,3 minuten op 237,1 °C**; de film transiteert in seconden op een wand die naar de olie van 280 °C
loopt. Afbraak is snelheid **maal** tijd, dus die twee moeten tegen elkaar op:

| Filmverblijftijd | Filmtemperatuur | Dosis t.o.v. de sump (Ea-band) |
|---|---|---|
| 10 s | 280 °C (bovengrens) | 0,01 – 0,06 |
| 30 s | 280 °C | 0,04 – 0,17 |
| 60 s | 280 °C | 0,08 – 0,35 |
| 30 s | 259 °C (halverwege) | 0,02 – 0,04 |

(Stand bij het schrijven van deze paragraaf. Met de Ea-band en de bodemtemperatuur van nu komt de rij van 30 s bij 280 °C op
0,01 – 0,07; zie 20.13.)

**In geen enkel geval haalt de film de sump in.** De temperatuurfactor van 6 tot 26 wordt volledig
opgegeten door een tijdfactor van rond 1/150. De bodemsump is dus de plek waar de meeste afbraak
van het **product** gebeurt, niet de reboilerwand.

Dat spreekt `AFBRAAK_BEGINT_AAN_DE_WAND` niet tegen: die notitie doet een uitspraak over de
**snelheid** per seconde, en die blijft staan. Wat hier bij komt is de **dosis**, en dat is precies
het onderscheid dat `AFBRAAK_IS_SNELHEID_MAAL_TIJD` maakt. De wand blijft het punt waar het metaal
coket; de sump is het punt waar het product afbreekt. Twee verschillende vragen, twee verschillende
antwoorden.

**De filmverblijftijd is een aanname.** Het project legt hem nergens vast, dus is hij hier over een
bereik van 10 tot 60 s gevarieerd in plaats van op één waarde gezet. De conclusie hangt niet van de
keuze binnen dat bereik af — bij 60 s en de hoogste Ea komt de film op 0,35 en blijft hij ruim
eronder.

**En tussen de twee kolommen** reproduceert het oefenmodel de eerdere bevinding: K-02 is 9,4 K heter
(factor 1,5 tot 2,2) maar staat zeven keer korter (factor 0,142), samen een dosis van **0,22 tot
0,31** ten opzichte van K-01. De afbraak zit in de **koudste** kolom, en de reden is de verblijftijd.

**Aandachtspunt, inmiddels opgepakt.** De 31,9 minuten volgde uit `V_sump_m3 = 0,25`, een los getal
zonder bron. Dat is vervangen door een afleiding uit de meetkunde van de kolom; zie 20.13. De
verblijftijd komt daarmee op 75,3 minuten en de getallen hierboven zijn al op die basis berekend.

### 20.13 De bodeminhoud komt uit de meetkunde, niet uit een verblijftijd

**Met de dichtheid uit hoofdstuk 4**: de inhoud volgt uit V · ρ met de dichtheid van elke bodem bij haar ontwerptemperatuur: K-01 432,2 kg en 64,8 min, K-02 954,1 kg en 10,9 min. De volumes en de meetkunde hieronder blijven; de massa's en tijden zijn met een eerdere, aangenomen dichtheid van 850 kg/m³ gerekend.

Tot deze ronde stond `V_sump_m3 = 0,25` als los getal in de modelaannamen, voor **beide** kolommen.
Dat is vervangen door een afleiding, en dat leverde meer op dan een nettere verantwoording.

#### De regel die niet werkt

De voor de hand liggende regel is *inhoud = bodemstroom × ontwerpverblijftijd*. Die valt hier
meteen door de mand:

| τ_ontwerp | V K-01 | V K-02 |
|---|---|---|
| 30 min | 0,235 m³ | **3,08 m³** |

K-02 zou een bodem van drie kuub krijgen omdat zijn bodemstroom dertien keer groter is. Dertig
minuten heet C18-product in een vat is geen kolombodem maar een surgevat — precies wat deze
installatie moet vermijden. De regel verheft bovendien een aanname tot ontwerpregel: die 30 minuten
kwam nergens vandaan behalve uit de bestaande 31,9 minuten van K-01.

#### De regel die wel werkt

Een kolombodem **heeft de diameter van de kolom**. Die diameter volgt uit de **dampbelasting** en
niet uit de bodemafname. Wat een ontwerper werkelijk kiest is de **vloeistofdiepte**:

    V_sump = (pi/4) * D_kolom^2 * h

De diameter komt uit module 8, uit de F-factor aan de **top** van de kolom — daar is de druk het
laagst, het dampvolume het grootst, en slaat flooding het eerst toe.

**De verblijftijd is daarmee geen invoer meer maar een uitkomst:** τ = V·ρ/B.

| Kolom | D | A | h | V_sump | Bodemstroom | **τ** |
|---|---|---|---|---|---|---|
| K-01 | 2,24 m | 3,94 m² | 0,15 m | 0,591 m³ | 400 kg/h | **75,4 min** |
| K-02 | 3,35 m | 8,81 m² | 0,15 m | 1,32 m³ | 5231 kg/h | **12,9 min** |

Stand van 21 oktober 2026, uit `installatie()` (bijgewerkt bij v1.1.6). Bij het invoeren van
deze afleiding gaf het model voor K-02 3,06 m, 7,33 m², 1,100 m³ en 10,7 min. De diameter volgt de dampbelasting aan de top
(nu 46,7 m³/s bij K-02 tegen 20,5 m³/s bij K-01), en die van K-02 is sindsdien veranderd. Module 10 (8.33) en de schatting
van het systeemvolume (13.2) rekenen met dezelfde waarden.

#### De twee ontwerpkeuzes

Beide staan in `ONTWERPKEUZES_GEOMETRIE` in `installatie.js`, en beide zijn **keuzes** en geen
literatuurwaarden.

**`h_vloeistof_sump_m = 0,15 m`.** Genoeg voor een niveauregelbereik en voor de aanzuiging van de
pomp, en verder zo weinig mogelijk: elke centimeter is extra tijd op temperatuur, en dat is bij deze
producten het duurste wat er is. De gebruikelijke bandbreedte voor een kolombodem is 0,3 tot 0,6 m;
hier is bewust de **onderkant** opgezocht, omdat de hele opzet van deze installatie (modules 9 en
10) om verblijftijd draait.

**`F_ontwerp_fractie = 0,75`.** Een kolom wordt niet *op* zijn floodingpunt ontworpen maar met
marge. Driekwart is gebruikelijk; met F_max = 2,5 voor gestructureerde pakking is de ontwerp-F dus
1,875.

#### Wat er niet van afhangt — en dat is de kern

De vloeistofdiepte bepaalt **hoe traag** beide kolommen zijn, maar niet hun **verhouding**: zij
schaalt beide bodems met dezelfde factor. Een halve of een drievoudige diepte geeft exact dezelfde
uitkomst voor elke vergelijking tussen de twee kolommen, en de tests controleren dat.

Daarmee hangt de dosisverhouding **volledig aan meetkunde**:

    tijdfactor = (A_K02 / A_K01) * (B_K01 / B_K02) = 2,24 * 0,0765 = 0,171

(Bij het invoeren van de afleiding: 1,86 · 0,0765 = 0,142.)

#### Wat dit met de drie doses deed

| | Voor (V = 0,25 m³) | Na (meetkunde, stand 21 oktober 2026) |
|---|---|---|
| Verblijftijd K-01 | 31,9 min | **75,4 min** (×2,4) |
| Verblijftijd K-02 | 2,4 min | **12,9 min** (×5,4) |
| Reboilerfilm (30 s, 280 °C) t.o.v. K-01-sump | 0,10 – 0,41 | **0,01 – 0,07** |
| K-02 t.o.v. K-01 | 0,12 – 0,16 | **0,20 – 0,34** |

De laatste twee rijen rekenen met de Ea-band en de bodemtemperaturen van nu (237,8 °C bij K-01, 250,1 °C bij K-02;
`verhoudingBand_C` in `degradation.js`, `afbraakPerKolom` in `installatie.js`):
- **De film:** (0,5 min / 75,4 min) · 1,74 tot 9,84, de versnelling bij 280 °C tegen 237,8 °C.
- **K-02 tegen K-01:** (12,9 / 75,4) · 1,19 tot 2,02.

Bij het invoeren van de afleiding stond hier 0,04 – 0,17 en 0,22 – 0,31.

**Alle drie de conclusies blijven staan**, en twee ervan zijn sterker geworden:

1. De sump is de plek waar het product afbreekt, niet de reboilerwand. De marge is groter geworden.
2. De afbraak zit in de **koudste** kolom. En nu om de goede reden: niet omdat K-01 een groot vat
   heeft gekregen — K-02 heeft juist ruim het dubbele volume — maar omdat een kolom van die
   diameter met vier procent bodemafname niet anders kan dan een lange verblijftijd hebben. Dat is
   meetkunde, geen ontwerpfout, en het is ook precies waarom de reboiler van K-01 een
   vallende-filmverdamper is: je kunt de bodem niet klein maken, dus houd je de **film** kort.
3. De verblijftijd van K-01 is niet verwaarloosbaar — en met 75 minuten nog minder dan eerst.

#### Wat wel kantelde: `TWEE_KOLOMMEN_TWEE_VERHALEN`

De oude formulering luidde: bij K-01 is de bodem traag, bij K-02 juist het **refluxvat** (4,4 min
tegen 2,4 min voor de bodem). Die rustte op de stilzwijgende aanname dat beide kolommen dezelfde
bodem**inhoud** hebben. Met de meetkundige afleiding komt de bodem van K-02 op 12,9 minuten, en
daarmee is hij trager dan zijn eigen refluxvat (5,0 minuten, 26.29).

**In beide kolommen is de bodem dus het trage element.** Wat verschilt is hoevéél trager: 75 tegen
13 minuten, een factor 5,9 — en dat terwijl K-02 de **bredere** kolom is met ruim het dubbele
volume. Het verschil zit volledig in de noemer van τ = inhoud / stroom.

De les is niet veranderd maar scherper geworden, en zij staat nu op meetkunde in plaats van op een
aangenomen vatinhoud. De oude formulering staat als `vervallenAanname` in de notitie, zodat wie haar
ergens tegenkomt kan zien waarom zij niet meer klopt.

#### Wat de tests bewaken

| Eis | Toets |
|---|---|
| `V_sump` volgt uit D | A = (π/4)·D², V = A·h, τ = V·ρ/B — alle drie nagerekend |
| De twee kunnen niet uiteen lopen | een dubbele doorsnede geeft een dubbele inhoud én verblijftijd |
| De diameter komt uit de **damp**belasting | K-02 is breder omdat hij meer damp verwerkt |
| De vloeistofdiepte raakt geen conclusie | halve en drievoudige diepte geven dezelfde verhouding |
| Het ontwerp zit op 75 % van flooding | F_ontwerp / F_max = 0,75 |

### 20.14 Stap 3: het bedieningspaneel en het levende schema

Geen nieuwe fysica. Wat hier bij komt is de weg van het model naar het scherm, en één
vereenvoudiging die daarbij zichtbaar werd.

#### Wat een regelkring op het paneel laat zien

Vier dingen, in de volgorde waarin ze op elk echt paneel staan: **PV** (wat de kring ziet), **SP**
(wat hij wil), **OUT** (wat hij doet) en de **stand**. Alle zeven kringen van het model staan erop —
één gedeelde drukregeling, twee bij K-01, vier bij K-02 — en er staat er geen bij die het model niet
kent. Een test vergelijkt beide kanten, zodat een kring die er in `simulatie.js` bij komt niet
stilzwijgend van het paneel kan wegblijven.

De stand bepaalt wat de cursist mag verzetten: in **auto** het setpoint, in **hand** de klep. Wat
niet mag wordt uitgeschakeld **met een toelichting** erbij en niet alleen met een grijstint.

Bij het overgaan naar hand neemt de klep de stand over die de regelaar op dat moment had —
**stootvrij overschakelen**. Een klep die bij het omschakelen springt is in bedrijf een bekende en
gevaarlijke fout, en een oefenmodel dat dat wel doet leert een verkeerde gewoonte aan.

#### Waarom er een tijdknop op zit

De installatie heeft tijdconstanten van seconden tot uren. Op ware snelheid gebeurt er op het scherm
niets: de bodem van K-01 heeft een tijdconstante van 75 minuten. De vier versnellingen zijn daarom
geen willekeurige reeks maar de spreiding van de tijdconstanten zelf:

| | Waarvoor |
|---|---|
| 1× | druk en stromen: seconden |
| 10× | niveaus: minuten |
| 60× | temperaturen: tientallen minuten |
| 600× | samenstellingen: uren |

De versnelling staat **zichtbaar in beeld** met de gesimuleerde tijd erbij. Anders leert de cursist
een verkeerd gevoel voor tempo, en dat is nu juist wat deze module wil bijbrengen.

#### Betekenis nooit alleen via kleur

De huisstijl eist het, en bij een levend scherm is de verleiding het grootst. Daarom:

| Wat | Hoe het te zien is |
|---|---|
| Stand van een kring | het **woord** AUTO of HAND, op het paneel én in het schema onder het apparaat |
| Grenswaarde geraakt | een **driehoek met een uitroepteken** op het symbool, plus de reden in de alarmstrook |
| Vloeistofspiegel | een **vlak** dat meebeweegt, niet een kleurverandering |
| Stroming | **bewegende streepjes**, en bij stilstand staat de animatie stil in plaats van door te lopen |
| Kring in hand | een dikkere linkerrand **naast** het woord op de knop |

De waarschuwingsdriehoek staat binnen de omtrek van zijn eigen symbool. Dat is geen stijlkeuze: een
teken dat ernaast zou staan, zou een label kunnen raken, en de botsingstest eist dat elk label vrij
ligt.

#### Grenswaarden komen in beeld met de reden

Zeven soorten, elk met de reden erbij in plaats van alleen de constatering:

- het vacuümsysteem aan zijn maximum — de druk loopt weg, de kring kan niets meer bijregelen;
- de reboiler begrensd door het medium — **verstikking, geen regelfout** (module 9);
- de bodem binnen 5 K van het mediumplafond, of erboven;
- een bodem- of refluxvatniveau buiten 10–90 %;
- een bodem die is **overgelopen** (zie hieronder);
- pitch die in K-02 terecht is gekomen;
- een reflux buiten wat het bed kan gebruiken.

#### `NIVEAU_LOOPT_DOOR_BOVEN_HONDERD`

Het oefenmodel maakte iets zichtbaar dat in de stationaire modules niet kon opvallen: zet de
bodemafname te ver dicht, en het gemelde niveau loopt door tot 267 procent. De ophoping kent geen
deksel.

**Er is bewust niet geklemd.** Klemmen op honderd procent zou de massabalans stil kapotmaken: er zou
massa verdwijnen zonder dat er een stroom tegenover staat, en juist die balans is wat dit model
betrouwbaar maakt. Liever een getal dat zichtbaar buiten het bereik valt dan een balans die stiekem
niet meer sluit.

Wat er in werkelijkheid gebeurt — de vloeistof stijgt de kolom in, zet het onderste deel van het bed
onder, de drukval schiet omhoog en de scheiding stort in — is een eigen verschijnsel en zit niet in
dit model. Het scherm zegt dat nu ook met zoveel woorden in plaats van een getal te tonen alsof het
nog iets betekent. Zodra module 11 een overlopende bodem als scenario uitwerkt, hoort daar een model
bij dat de vloeistof de kolom in laat lopen.

### 20.15 Stap 4: trends en de deeltaakoefening trendlezen

#### Drie vensters, want er is er geen die alles laat zien

De vensters volgen de spreiding van de tijdconstanten, net als de vier versnellingen:

| Venster | Duur | Monsterinterval | Waarvoor |
|---|---|---|---|
| Minuten | 30 min | 10 s | druk, stromen, een slingerende regelaar |
| Uren | 12 u | 2 min | niveaus, temperaturen, een sprongantwoord |
| Dagen | 14 d | 1 u | samenstellingen, vervuiling, een sluipende afwijking |

Fijn dichtbij en grof ver weg, zoals een historian het doet. Elke grafiek heeft **één eenheid**,
want twee eenheden op één as laat de kijker hellingen vergelijken die niets met elkaar te maken
hebben. Elke reeks krijgt een eigen **lijnsoort** naast zijn kleur, en zijn naam en laatste waarde
staan **aan het eind van de lijn** in plaats van in een legenda met kleurvlakjes.

Er is één grafiek met een andere rol: **de opgetelde pitch die K-02 is binnengekomen**, in kg. Dat
is de enige grootheid in het beeld die optelt in plaats van zich in te stellen, en juist daarom de
enige waarop een sluipende vervuiling zichtbaar wordt. Zonder die lijn zou oefening 4 niet kunnen
bestaan: alle andere lijnen zijn bij een pitchdoorslag vlak, op elk venster.

#### De vier oefeningen

Opbouw van vorm naar venster. De eerste drie oefenen het herkennen van een **vorm**; de vierde
oefent dat de vorm afhangt van het **venster**, en dat is waarom hij als laatste staat.

| # | Oefening | Wat het beeld laat zien |
|---|---|---|
| 1 | Sprong op het setpoint | T van K-01: 237,1 → 240,1 °C, in ongeveer een uur |
| 2 | Slingerend niveau | niveau 49,3–51,4 % met vijf richtingswisselingen in een half uur |
| 3 | Sluipend lek | p 20,0 → 25,0 mbar, T van K-01 → 245,3 °C en van K-02 → 249,8 °C |
| 4 | Pitchdoorslag | concentratie vlak op **beide** vensters; alleen de opgetelde massa loopt door |

**De beelden worden gedraaid en niet getekend.** Het zou eenvoudiger zijn vier plaatjes met de hand
te maken, maar dan kan er een curve op het scherm komen die het model niet zou opleveren, en leert
de cursist een vorm herkennen die in de installatie niet voorkomt. Elk scenario gebruikt alleen
instellingen die het model al kent: een setpoint, een afstemming, een lekdebiet, een meesleepfractie.

#### Wat stap 4 aan het licht bracht

Drie defecten, alle drie ontstaan doordat trends met **grotere tijdstappen** en **langzame
verstoringen** werken waar de eerdere tests met kleine stappen en sprongen werkten.

**1. De drukkring had een lusversterking van bijna drie.** De druk reageert met ongeveer 19 mbar per
eenheid klepstand; met K<sub>p</sub> = 0,15 is de lusversterking 2,85, en een teruggekoppelde lus
boven één slingert onvoorwaardelijk — hoe klein de tijdstap ook is.

Dat dit niet eerder opviel, is leerzaam: de tests belastten de kring met **sprongen**, en een sprong
duwt de klep meteen tegen zijn grens, waar de anti-windup de slingering tegenhoudt. Pas een langzame
**helling** legde het bloot. K<sub>p</sub> staat nu op 0,02, dus een lusversterking van 0,38.

**2. De drukkring is te snel voor een grote tijdstap.** Haar integratietijd is 60 s. Bij een tijdstap
van minuten zet een PI-regelaar per stap meer dan een hele integratietijd door. Boven Ti/4 wordt de
kring daarom **algebraïsch** opgelost in plaats van geïntegreerd — dezelfde gedachte als
`STIJF_STELSEL_DRUK_ALGEBRAISCH`, nu voor de regelaar. Bij tijdstappen van 10, 50 en 300 s geeft het
model nu dezelfde uitkomst tot op twee decimalen.

**3. De grootste toelaatbare tijdstap was een vast getal van 300 s, en dat was te ruim.** Bij 300 s
slingerden de niveaukringen van K-02 tussen een lege bodem en 161 procent, met een periode van zes
uur. Die grens komt nu uit de installatie zelf (`maximaleTijdstap()`): vijf stappen per tijdconstante
van het snelste vat, zes per integratietijd van de snelste kring. Het bindende element is het
**refluxvat van K-02** met 4,4 minuten, dus **52 s**.

Belangrijk detail: de grens staat op de **ontwerp**waarden en niet op de ogenblikkelijke toestand.
Een eerste versie gebruikte de huidige inhoud en weigerde middenin een simulatie verder te rekenen
zodra een vat bijna leegliep. Een leeglopend vat is een bevinding die gemeld hoort te worden, geen
reden om het rekenen te staken.

**En een gewone fout:** de pitchmassa werd begrensd op de **onbegrensde** sumpmassa, zodat zij
negatief kon worden zodra de sump onder nul dook — het model meldde toen een pitchfractie van min
vijfentwintigduizend procent. Nu wordt op de begrensde massa geklemd.

#### Twee aantekeningen, zonder wijziging

**A. De 75 minuten in de bodem van K-01 volgen uit een VLAKKE bodem op kolomdiameter.** Dat is de
meetkundige uitkomst van 20.13: 3,94 m² doorsnede, 0,15 m vloeistof, 400 kg/h afname. Het is geen
natuurconstante maar het gevolg van één vormkeuze.

In de praktijk is dat getal precies waar een ontwerper op reageert. Een **versmalde bodem**, een
**boot** of een apart **opvangpotje** onder de kolom verkleint de doorsnede op de plek waar de
vloeistof staat, zonder dat de kolom zelf smaller wordt. Daarmee wordt de verblijftijd een
ontwerpparameter in plaats van een gevolg, en kan zij een factor drie tot vijf omlaag.

Dit is bewust **niet** verwerkt. Het zou een tweede geometrie-aanname toevoegen zonder bron, en de
conclusies van 20.13 hangen er niet van af — die berusten op de VERHOUDING tussen de twee kolommen,
en die verandert alleen als je K-01 wél en K-02 níét versmalt. Openstaand punt.

**B. `NIVEAU_LOOPT_DOOR_BOVEN_HONDERD` moet in stap 5 uitkomen op flooding en meesleep, niet op een
overlopende bodem.** De notitie zegt nu dat de vloeistof bij een te hoog niveau de kolom in stijgt en
het onderste deel van het bed onder zet. Dat is de juiste richting, maar het eindpunt is niet "de
bodem loopt over": het is **flooding** van onderaf, met als gevolg dat er vloeistof — en dus pitch —
met de damp **meegesleept** wordt naar de top.

Daarmee sluit die keten aan op de pitchdoorslag van 20.10: dezelfde meesleepfractie, maar dan
veroorzaakt door een te hoog niveau in plaats van door een toevallige verstoring. Stap 5 hoort die
twee aan elkaar te knopen in plaats van een overlopende bodem apart te modelleren. Alleen de link is
hier vastgelegd; er is niets gebouwd.

### 20.16 Stap 5: storingsinjectie, en de integriteitsregel

#### De regel: een storing zet geen symptoom

Een storing verandert **precies één fysische parameter**. Alles wat de cursist daarna ziet — een
oplopende druk, een wegzakkende doorzet, een verkleurend product — volgt daar via de bestaande
fysica uit. Zodra een symptoom apart wordt ingetypt is het geen model meer maar een filmpje, en dan
klopt de oorzaak-gevolgketen (lek of overbelasting, hogere druk, hogere bodemtemperatuur, snellere afbraak) niet meer.

Om die regel **toetsbaar** te maken is een injectie geen functie maar een **beschrijving**: een pad,
een eindwaarde en een tijd. Een functie zou alles kunnen doen; een pad is na te lopen.

| Storing | Parameter | Naar | Over | Versnelling |
|---|---|---|---|---|
| Vacuümlek | `instelling.lek_kgh` | 22 kg/h | 2 u | 60× |
| Reboilerfouling K-01 | `instelling.fouling_E01` | 0,55 | 6 u | 600× |
| Condensoruitval E-02 | `instelling.condensor_E02` | 0,40 | 5 min | 10× |
| Voedingstrip | `instelling.F_kgh` | 0 | direct | 60× |
| Overbelasting | `instelling.F_kgh` | 14 000 kg/h | 30 min | 60× |
| Afname dichtgeknepen | `K01.kringen.niveau.klep_kgh` | 120 kg/h | 10 min | 60× |
| Pitchdoorslag | `instelling.meesleep_K01_fractie` | 0,02 | direct | 600× |

**Drie tests bewaken de regel.** Ten eerste: elk pad wijst naar een instelling of een klepstand, en
het blad van het pad is nooit een meetwaarde, een vast apparaatgegeven of een geïntegreerde
toestand. Ten tweede — en dat is de scherpste — de hele toestand wordt vóór en ná het toepassen
**blad voor blad** vergeleken: verandert er ook maar één ander pad, dan valt de test om. Ten derde
de andere kant: voor elke storing moet er ook werkelijk een symptoom uit vólgen, want een injectie
die niets teweegbrengt is net zo fout als een die een symptoom intypt.

#### Vier nieuwe parameters, want drie storingen hadden nog geen aangrijpingspunt

**Reboilerfouling** vermenigvuldigt de beschikbare duty: een aangekoekte wand draagt bij hetzelfde
temperatuurverschil minder warmte over, dus U·A zakt. Het kenmerk van verstikking is dan niet de
duty zelf maar dat de reboiler **begrensd** raakt — de regelaar vraagt meer dan er doorheen komt.

**Condensoruitval** stuurt de niet-gecondenseerde damp naar het vacuümsysteem. Dat is de reden dat
dit de snelste storing van de zeven is: de dampstroom is duizenden malen de leklucht, dus het
vacuüm stort in minuten in.

Daarmee kwam het model wel buiten zijn bereik. Boven ongeveer 100 mbar kookt de voeding boven 270 °C
— ver boven wat enig verwarmingsmedium hier levert — en bij nog hogere drukken draaien de
sleutelcomponenten zelfs om, waar `stages.js` terecht op afslaat. Er is daarom een **drukplafond**
van 100 mbar: het model klemt daar én meldt dat het buiten zijn bereik is. Doorrekenen zou getallen
opleveren die niets voorstellen.

**De voeding heeft een bodem** van 2 % van ontwerp. Een trip zet de klep dicht, maar nul kan het
model niet: zonder instroom is er geen samenstelling meer om een balans over te maken. Er wordt met
een paar procent doorgerekend, en onder de minimale bevochtiging (module 8) gelden de correlaties
van de pakking sowieso niet meer.

#### Punt 5 afgemaakt: `NIVEAU_LOOPT_DOOR_BOVEN_HONDERD`

De aangekondigde link uit 20.15 is uitgewerkt en de notitie staat nu als **opgelost**.

Een bodem die volloopt **loopt niet over** — hij zet het onderste deel van het pakkingbed onder. Daar
begint de keten:

    niveau > 100 %  ->  vloeistof in het onderste deel van het bed
                    ->  minder ruimte voor de damp: MEER DRUKVAL
                    ->  hogere bodemdruk, dus een hetere bodem (module 8)
                    ->  hogere dampsnelheid: de damp rukt DRUPPELS mee
                    ->  MEESLEEP naar de top - en bij K-01 is dat pitch

Gemeten in het model, met de klep van 400 naar 120 kg/h:

| Na | Niveau | Drukval K-01 | Bodem K-01 | Meesleep | Pitch in K-02 |
|---|---|---|---|---|---|
| 2 u | 106 % | 3,17 mbar | 237,3 °C | 0,14 % | 0 kg |
| 5 u | 185 % | 5,56 mbar | 240,2 °C | 2,14 % | 2 kg |
| 10 u | 304 % | 9,00 mbar | 244,5 °C | 5,00 % | 14 kg |

**Dat is het leerpunt:** een operator die de bodemafname knijpt om de pitch dikker te maken, stuurt
hem daarmee de andere kolom in. Dezelfde meesleepfractie als bij de pitchdoorslag, maar nu als
**gevolg** van een klepstand in plaats van als een toevallige verstoring — en de ingestelde meesleep
staat daarbij gewoon op nul.

De drie getallen in `opstuwing()` zijn **geen** natuurwetten en geen gepubliceerde correlaties: hoeveel
bed er per eenheid overmaat onder komt, hoeveel de drukval daarvan oploopt, en hoeveel er dan wordt
meegesleept. Alle drie zijn aannamen van het model in `MODELAANNAMEN`. De drukvalfactor heeft de orde van grootte van een gevlood
bed: bij 20 mbar is de drukval bij flooding ongeveer 2,7 keer die in het ontwerppunt (Schulz e.a., 2018, in 14.2; eigen rekenwerk), al is
het mechanisme anders, want hier komt het bed van onderaf onder vloeistof. De 0,5 is af te leiden uit de meetkunde van bodem en bed en is
nog niet afgeleid. Voor de 5 % meesleep bij flooding is geen openbare bron gevonden. Wat het model geeft is de
**keten** en de richting, niet bij welk niveau precies welke meesleepfractie hoort.

Wat er nog **niet** in zit: de floodinggrens zelf, de F-factor tegen F_max uit module 8. De opstuwing
werkt op het niveau en niet op de dampsnelheid. Zodra de kolomdoorsnede aan de drukval wordt
gekoppeld (20.12.A) horen die twee aan elkaar geknoopt te worden — het is hetzelfde verschijnsel van
twee kanten bekeken.

#### Blind oefenen en de nabespreking

De cursist krijgt bij het starten alleen een **nummer**, geen etiket. Een test controleert dat geen
enkele storingsnaam op het scherm staat voordat er is nabesproken — in de controlekamer hangt er ook
geen bordje.

Hij grijpt in met het paneel uit stap 3, en **het model laat hem begaan**. Een verkeerde ingreep
wordt niet geblokkeerd maar maakt het erger langs een navolgbare weg; een oefenmodel dat een
verkeerde ingreep tegenhoudt leert niet waarom hij verkeerd was.

De nabespreking beantwoordt vier vragen:

1. **Wat het werkelijk was**, met de vermelding welke ene parameter er is verzet.
2. **Het eerste betrouwbare signaal** — welke meting als eerste buiten haar band kwam. Dat wordt uit
   het **verloop** bepaald en niet uit een lijstje: het antwoord hoort uit de meting te komen.
3. **De wet die de keten verklaart**, met formule en eenheden (harde eis 1 uit hoofdstuk 2).
4. **Wat de ingreep van de cursist deed**, uit een logboek van alles wat hij heeft versteld, naast
   wat er volgens het scenario had moeten gebeuren.

De **tijdschaal** is bewust aan de cursist gelaten. Elke storing heeft een passende versnelling in de
tabel staan, maar het scherm kiest die niet voor hem: kiezen op welk tijdvenster je kijkt is
onderdeel van de vaardigheid, en dat is precies wat oefening 4 van stap 4 inoefent.

---

### 20.17 Voedingspomp P-03: een schakelaar, geen pompkromme (v1.1.0)

**Geen stofwaarde en geen apparaatgegeven: een ontwerpkeuze, en een bewuste leegte.** Sinds v1.1.0
heeft de installatie een voedingspomp, P-03, met een afsluiter erachter. De aanleiding: een beveiliging op een te hoog
bodemniveau heeft iets nodig om de voeding mee te stoppen, en tot dan toe was de voeding een getal
zonder apparaat.

**Wat het model ervan weet.** Een ding: of de voeding vrij is (`instelling.voeding_vrij`). Staat
de pomp uit of is de afsluiter dicht, dan gaat er niets de kolom in; anders de doorzet `F_kgh`,
met haar verstoring.

    F = F_kgh · (1 + verstoring)   als de voeding vrij is
    F = 0                          als zij dat niet is

De wet erachter is behoud van massa: wat er niet in komt, hoopt niet op, en wat er nog verdampt en
wordt afgevoerd, komt uit de inhoud van de bodem.

**Wat het model er NIET van weet, en waarom er niets is ingevuld.** Geen pompkromme, geen
drukopbouw, geen minimumdebiet, geen NPSH, geen klepkarakteristiek van de afsluiter. Voor geen van
die grootheden is er een gegeven, en zij zijn voor wat de module wil laten zien niet nodig: de
doorzet is een instelling van de operator, geen uitkomst van een pomp. Een verzonnen kromme zou
meer beloven dan het model weet. De uitleg bij het apparaat zegt dat met zoveel woorden.

**Geldigheid.** De schakelaar is ogenblikkelijk: er is geen uitlooptijd van de pomp en geen
sluittijd van de afsluiter. Wie de eerste seconden na een stop wil bestuderen, heeft aan dit model
niets.

**Gevolg dat het model wel laat zien**: zonder voeding kookt de bodem van K-01 bij de
ontwerpduty in ruim een minuut van 50 naar 30 procent, en valt daarna het destillaat weg, en
daarmee de voeding van K-02. De massabalans blijft daarbij sluiten.

### 20.18 De componentbalans van K-02: reflux én verdeling (v1.1.0)

**Geen stofwaarde en geen nieuw apparaatgegeven: een reparatie van het model**, met twee wetten en
één correlatie die er al waren (hoofdstuk 11).

**Wat er mis was.** Tot v1.1.0 volgde de samenstelling van de twee producten van K-02 alleen uit de
reflux: `scheidingUitReflux()` zocht de terugwinning, voor beide sleutels even groot, waarbij
Gilliland het aantal trappen van het bed opleverde. De stromen volgden intussen de niveauregelaars.
In het ontwerppunt paste dat op elkaar. Daarbuiten niet: bij een andere duty trok de top meer of
minder weg, de samenstelling bleef staan, en er ging tot 23,5 procent meer palmitinezuur de kolom uit
dan erin kwam. De temperatuurkring van K-02 had daardoor geen grip: meer duty maakte de bodem niet
warmer.

**Wat er nu staat.** Twee knoppen, twee vragen.

    xi_i / (1 - xi_i) = c · alfa_i ^ N_min          Fenske, voor elke component
    som over i van w_i · xi_i = D / (D + B)         behoud van massa: de sluiting
    x_D,i = z_i · xi_i / som(z · xi)                het topproduct
    x_B,i = z_i · (1 - xi_i) / som(z · (1 - xi))    het bodemproduct

| Grootheid | Betekenis | Eenheid |
|---|---|---|
| xi_i | het deel van component i dat naar de top gaat | - |
| alfa_i | relatieve vluchtigheid ten opzichte van de zware sleutel, meetkundig gemiddeld over top en bodem | - |
| N_min | minimaal aantal theoretische trappen: hoe scherp het bed bij deze reflux scheidt | - |
| w_i, z_i | massa- en molfractie van component i in de voeding | - |
| D, B | destillaat- en bodemstroom van de vorige tijdstap | kg/h |
| c | volgt uit de sluiting; het is xi/(1-xi) van de zware sleutel | - |

- **De reflux bepaalt hoe scherp het bed scheidt.** N_min wordt gezocht zo dat Gilliland, met de
  R_min van Underwood bij de gevonden producten, precies het aantal trappen van het bed geeft.
- **De verdeling bepaalt waar de snede ligt.** Wat over de top vertrekt, kan niet ook onderuit.
  Daarmee sluit de balans per component, `F·z_i = D·x_D,i + B·x_B,i`, in elk stationair punt.

De wetten zijn Fenske, Underwood en behoud van massa; Gilliland is een correlatie en blijft dat
(11.1, 8.10). De **scherpte** erft de onzekerheid van Gilliland volledig. De **balans** erft haar
niet: die is opgelegd en sluit altijd.

**Geldigheid.** Ideaal mengsel, constante molaire overloop, verzadigde vloeistofvoeding (q = 1),
constante relatieve vluchtigheid over de kolom: dezelfde aannamen als in hoofdstuk 11. De
verdeling moet tussen 2 en 98 procent liggen; daarbuiten is een van de twee producten vrijwel weg,
houdt het model de rand aan en meldt het dat. Staan beide pompen stil, dan geldt de verdeling van
het ontwerp, ook met een melding.

**Wat het NIET kan: `SAMENSTELLING_REAGEERT_DIRECT`.** De som is stationair. Het model weet van de
bodem en het refluxvat de massa en niet de samenstelling. In een overgang reageert de samenstelling
daarom direct op de stromen, en de bodemtemperatuur met haar. Twee gevolgen:
- de temperatuurkring van K-02 lijkt sneller te regelen dan in een echte kolom, waar eerst de
  vloeistof op de pakking, in de bodem en in het vat van samenstelling moet veranderen. Dat staat
  op het scherm, bij die kring;
- zolang de bodem of het vat vult of leegloopt, sluit de balans per component over de kolom niet
  exact. In elk stationair punt sluit zij wel.

**De rekeninstellingen** (`ZOEKTOCHT_SCHEIDING` in `simulatie.js`, `RONDES_FENSKE_VERDELING` in
`stages.js`). Geen stofwaarden en geen aannamen over de installatie; zij zeggen hoe nauwkeurig en
hoe lang er gezocht wordt.

| Instelling | Waarde | Reden |
|---|---|---|
| tolerantie op N_min | 1e-6 | het verschil met een strengere tolerantie is 1,6e-10 in een molfractie |
| rondes voor de relatieve vluchtigheid | 2 | alfa hangt aan de temperaturen van de producten, en die aan alfa; na twee rondes verandert de uitkomst niet meer merkbaar |
| plafond op het aantal halveringen | 40 | bij deze tolerantie genoeg voor een bed tot een miljoen trappen; voor K-02 zijn het er 26 |
| ondergrens van N_min | 1 | minder dan één evenwichtstrap is geen scheiding |
| grenzen van de verdeling | 0,02 en 0,98 | daarbuiten zegt een samenstelling van het bijna verdwenen product niets |
| halveringen in de Fenske-verdeling | 80 | brengt elk startinterval terug tot onder de rekennauwkeurigheid |

**Twee afgeleiden voor het scherm**. Geen nieuwe
getallen: allebei volgen zij uit het model.
- *De melding van Underwood.* Voor het bed dat er staat is er geen harde ondergrens op de reflux:
  minder reflux scheidt geleidelijk slechter. Voor een GEVRAAGDE scheiding is die grens er wel, en
  dat is wat Underwood zegt. Het model meldt daarom een reflux onder de R_min van de
  ontwerpscheiding (2,94, uit `installatie.js`): daaronder haalt geen bed de ontwerpzuiverheid.
- *Waar de lichte sleutel op is* (`bodemZonderLichteSleutel()`). Bij vaste refluxverhouding wordt
  de verdeling gezocht waarbij de lichte sleutel in de bodem op 0,05 mol-procent uitkomt
  (`GRENS_LICHTE_SLEUTEL_OP`: een afspraak om een punt aan te wijzen, geen stofwaarde). De bodem
  kookt daar bij 250,6 °C en het topproduct bevat 4 procent C18, tegen 0,4 bij ontwerp. Daarboven
  wordt de bodem alleen nog warmer doordat er C18 over de top gaat. De uitleg bij E-03 gebruikt
  deze getallen. Geldigheid: een stationair punt, met de onzekerheid van Gilliland in de scherpte.

**Het ontwerppunt verschuift een fractie.** De niet-sleutels gingen tot v1.1.0 volledig naar een
kant (`productenUitSpecificatie()`, de klassieke aanname). Nu verdelen ook zij zich volgens
Fenske: er komt een spoor oliezuur in de top. Het verschil met v1.0.1 is hooguit 0,024 procentpunt
in een molfractie en 0,004 K in de bodemtemperatuur. Het ontwerppunt in `installatie.js` zelf is
niet aangeraakt; de tolerantie is in de testsuite verantwoord.

### 20.19 Zoeken met een startwaarde (v1.1.1)

**Geen stofwaarde, geen apparaatgegeven en geen wet: rekenwerk.** Twee grootheden in het oefenmodel
zijn niet uit te schrijven en worden gezocht: de scherpte die het bed van K-02 bij een reflux haalt
(`scheidingUitReflux()`, 20.18) en de dampfractie die bij een bodemstroom van K-01 hoort
(`dampfractieUitBodemfractie()`, module 4). Tot v1.1.1 halveerden beide elke tijdstap het hele
bereik: 26 en 40 rondes. Samen was dat negen tiende van de rekentijd van een stap.

**Wat er nu staat** (`src/physics/zoek.js`, `zoekNulpunt()`). In een lopende simulatie ligt het
antwoord van deze stap vlak naast dat van de vorige. De zoektocht krijgt dat antwoord mee als
startwaarde:

1. een peiling op de startwaarde zegt aan welke kant het nulpunt ligt;
2. dan steeds wijder die kant op, tot het nulpunt is ingesloten (de vensters);
3. binnen het venster schat de koorde (regula falsi) het nulpunt, en twee peilingen vlak ernaast
   sluiten het in;
4. is het nulpunt na het wijdste venster niet ingesloten, dan wordt er gehalveerd over wat er van
   het bereik over is: **de terugval**.

Zonder startwaarde wordt er alleen gehalveerd, in dezelfde rondes als voor v1.1.1. Dat is wat de
losse berekeningen doen: het ontwerppunt, de kengetallen, de rekenvoorbeelden. Hun uitkomsten zijn
bit voor bit dezelfde gebleven.

**Wat er niet verandert: de tolerantie.** Een zoektocht eindigt altijd tussen een punt onder het
nulpunt en een punt erboven die niet verder uit elkaar liggen dan de tolerantie van voorheen: een
miljoenste trap voor de scherpte (`ZOEKTOCHT_SCHEIDING.tolerantie_N_min`), 1e-12 voor de
dampfractie. Twee zoektochten met een andere voorgeschiedenis vinden dus hetzelfde antwoord, op die
tolerantie na. Bit voor bit hetzelfde is het niet: waar binnen de tolerantie de zoektocht eindigt,
hangt af van waar zij begon. In de bodemtemperatuur is dat een honderdmiljoenste kelvin.

**De balans per component van K-02 hangt hier niet aan.** Zij wordt na de zoektocht opgelegd, met de
exacte verdeling tussen top en bodem (20.18), en sluit dus even goed waar de zoektocht ook eindigt.

**De instellingen**, geen van alle een stofwaarde: zij zeggen alleen hoe er gezocht wordt.

| Instelling | Waarde | Waarom |
|---|---|---|
| `ZOEKEN_MET_START.vensters` | 0,9; 100; 10 000 maal de tolerantie | Het eerste net geen tolerantie: staat het proces stil, dan is de zoektocht na twee peilingen klaar. De volgende voor een overgang. |
| `ZOEKEN_MET_START.halve_stap` | 0,45 maal de tolerantie | Twee peilingen aan weerskanten van de schatting liggen dan 0,9 tolerantie uiteen. |
| `ZOEKEN_MET_START.rondes_koorde` | 6 | Daarna wordt de rest gehalveerd; de zoektocht eindigt dus altijd. |
| `VENSTERS_PSI` (`balance.js`) | 1e3; 1e6; 1e9 maal de tolerantie | De tolerantie op de dampfractie is 1e-12; zij schuift per stap veel meer dan dat. |

**De peilingen bij de startwaarde tellen niet mee voor het plafond op de rondes.** Anders kon een
zoektocht met startwaarde eerder stoppen dan een zonder, en lag het antwoord niet meer binnen
dezelfde tolerantie.

### 20.20 De druk en de dampstroom samen opgelost (v1.1.2)

**Geen stofwaarde, geen apparaatgegeven en geen nieuwe wet: de reparatie van een rekenfout.** De wet
is de ideale gaswet van hoofdstuk 7, via `evenwichtsdruk_mbar()`:

    p = evenwichtsdruk( S, lek, (1 - f_E02) · V_K01(p) + (1 - f_E04) · V_K02(p) )

met S de zuigcapaciteit [m³/s], f de condensorfactor [-] en V de dampstroom die de reboiler bij de
druk p levert [mol/s]. Tot v1.1.2 stond rechts de dampstroom van de vorige tijdstap. De aanname
daarachter - de dampstroom is veel trager dan de druk - houdt geen stand zodra de reboiler tegen het
mediumplafond loopt: de druk sprong dan elke stap tussen 12 en 100 mbar.

**Wat er nu staat** (`algebraischGekoppeld()` in `simulatie.js`). Gezocht wordt de druk waarbij links
en rechts gelijk zijn. Rechts daalt met p (een warmere bodem, minder temperatuurverschil met het
medium, minder damp) en links stijgt: er is een snijpunt. De zoektocht is `zoekNulpunt()` van 20.19
en begint bij de druk van de vorige stap. Vraagt de damp op de grens van het model
(`MODELAANNAMEN.p_max_model_mbar`) nog meer druk, dan wordt er geklemd en gemeld, als voorheen.

| Instelling | Waarde | Waarom |
|---|---|---|
| `ZOEKTOCHT_DRUK.tolerantie_mbar` | 1e-6 mbar | ver onder wat een scherm toont |
| `ZOEKTOCHT_DRUK.max_rondes` | 60 | het halveren van 100 mbar tot de tolerantie vraagt er 27 |
| `ZOEKTOCHT_DRUK.vensters` | 0,9; 1e3; 1e6; 1e7 maal de tolerantie | tot tien mbar van de druk van de vorige stap; in het scenario verandert de druk per stap minder |

**Wat dit niet is.** De druk blijft een evenwicht en geen toestand, en de condensor blijft een
factor. Dat staat op de lijst voor een volgende versie.

## 21. Damp tegen vloeistof: één foutpatroon, drie keer

### 21.1 Wat er drie keer misging

Geen stofdata in dit hoofdstuk, maar een **foutpatroon** — en dat hoort hier omdat het drie keer
door alle andere controles heen is gekomen.

| Waar | Wat er gebeurde | Gevonden |
|---|---|---|
| 8.38, volumevergroting | molmassa van de damp uit de **bodemvloeistof** | door een schatting |
| Oefenmodel, ontwerpduty | ΔH<sub>vap</sub> uit de **vloeistof** | door een test die de duty narekende |
| 6, `energiebalans()` | ΔH<sub>vap</sub> van de bodem uit `x_B` | door de doorlichting die dit hoofdstuk beschrijft |

Steeds hetzelfde: een eigenschap van de **damp** afgeleid uit de samenstelling van de **vloeistof**
waar die damp uit opstijgt.

### 21.2 Waarom het patroon zo lastig te zien is

De verleiding zit in een correcte gedachte. Pitch verdampt niet, dus heeft hij geen
verdampingswarmte, dus telt hij in de sommatie als nul mee. Dat klopt allemaal.

    dHvap = som( fractie_i * dHvap_i )      met dHvap_pitch = 0

**De som klopt. De normering niet.** Pitch staat namelijk wél in de noemer van de molfracties. Bij
een bodem die voor een kwart uit pitch bestaat, tellen de vluchtige fracties op tot 0,75 in plaats
van 1, en zakt elke molgewogen dampeigenschap met een kwart mee.

Er komt geen foutmelding uit, geen oneindigheid, geen negatief getal — alleen een te laag getal dat
er volstrekt redelijk uitziet. Dat is waarom drie lagen controle het niet pakten: de eenheden
kloppen, de massabalans sluit, de monotonie klopt, en het getal ligt in de goede orde.

De natuurwet erachter is eenvoudig genoeg om te onthouden. **Raoult en Dalton**: een niet-vluchtige
component heeft geen dampspanning, dus geen partiaaldruk, dus `y = 0`. Een eigenschap van de damp
rekent met **y**, een eigenschap van de vloeistof met **x**.

### 21.3 Wat de doorlichting opleverde

Alle plaatsen in `src/physics/` waar een samenstelling naar een eigenschap wordt omgezet, zijn
nagelopen:

| Plek | Samenstelling | Oordeel |
|---|---|---|
| `energiebalans()` ΔH<sub>vap</sub> bodem | `x_B` | **fout** — hersteld naar de dampsamenstelling |
| `energiebalans()` ΔH<sub>vap</sub> top | `x_D` | goed — totale condensor, zie 21.4 |
| `energiebalans()` `V_kgh` | `x_D` | goed — idem |
| `installatie.js` `M_B`, `M_F` | `x_B`, `z` | goed — vloeistofmolmassa voor een massastroom |
| `balance.js` `M_destillaat` | `y_D` | goed |
| `packing.js` bevochtiging | vloeistof | goed — het gaat daar om de vloeistof |
| `simulatie.js` ΔH<sub>vap</sub> en M<sub>damp</sub> | `y` | goed — al hersteld in stap 1 |

**Het effect van de correctie in module 6.** De verdampingswarmte van de bodem gaat van 85,5 naar
86,5 kJ/mol en de stripdampstroom van 16,93 naar 16,74 mol/s. De **duty's veranderen niet**: bij
een zuiver latente balans geldt Q<sub>reboiler</sub> = V<sub>strip</sub> · ΔH<sub>vap</sub>(bodem) =
V<sub>top</sub> · ΔH<sub>vap</sub>(top) = Q<sub>condensor</sub>, dus valt de verschuiving tegen
elkaar weg. Het verschil tussen de twee dampstromen gaat van 13 naar 14 procent.

Dat het bij het ontwerppunt van module 5 maar één procent scheelt, is geen geruststelling maar de
verklaring: dat bedrijfspunt heeft 1,3 mol% pitch in de bodem. Bij **K-01**, met een kwart pitch,
gaat dezelfde som van 80,2 naar 61,2 kJ/mol — **31 procent te laag**, en een te lage duty is niet
de veilige kant, want de hele afbraakketen van module 9 hangt aan de dampstroom die eruit volgt.

### 21.4 De ene echte uitzondering: de totale condensor

Er is één plek waar een vloeistofsamenstelling wél de dampsamenstelling ís, en juist die plek zaait
de verwarring. Bij een **totale condensor** condenseert alles wat de kolom uit gaat, dus is de damp
die de condensor in gaat per definitie hetzelfde mengsel als het destillaat:

    x_D = y_top       (totale condensor, geen partiële)

Dat is geen slordigheid maar een gevolg van het apparaat, en het staat op elke plek waar het wordt
gebruikt uitgeschreven. Bij een **partiële** condensor zou het niet meer opgaan. `x_D` bevat in dit
ontwerp dan ook exact nul pitch, wat de test apart controleert.

### 21.5 Hoe het een vierde keer wordt voorkomen

De notitie `DAMPEIGENSCHAP_UIT_DAMPSAMENSTELLING` in `energy.js` legt het patroon vast. Daaromheen
staan twee lagen, want een test alleen was hier niet genoeg gebleken.

**1. De functie weigert het zelf.** `verdampingswarmteMengsel()` heet zijn argument nu `y` en
controleert dat er geen niet-vluchtige component in staat. Staat er wel een in, dan volgt een
foutmelding die zegt wat er mis is, hoe het wel moet, en naar welke notitie te kijken. De drempel is
1·10<sup>-12</sup>: fysisch hoort het exact nul te zijn, en de kleinste pitchfractie die in dit
project in een vloeistof voorkomt is van de orde 10<sup>-3</sup>, dus er zitten negen ordes tussen
afrondingsruis en een werkelijk doorgegeven vloeistofsamenstelling.

**2. De broncode wordt doorzocht**. Een bewaking in één functie pakt alleen wat daar
langskomt; een nieuwe molgewogen som elders zou er weer buiten vallen, en dat is precies hoe het
drie keer is gegaan. De test leest daarom `src/physics/` als tekst en controleert:

| Wat | Regel |
|---|---|
| elke aanroep van `verdampingswarmteMengsel` | krijgt een `y`-naam, of een uitzondering mét het woord "totale condensor" erboven |
| elke molmassagewogen som `const M_… = …reduce(…M_kgmol…)` | naam en samenstelling zeggen hetzelfde over damp of vloeistof |
| elke `gemiddeldeMolmassa_kgmol` met "damp" in de naam | komt uit een `y` |
| het aantal uitzonderingen | ten hoogste twee |

De test faalt dus ook als iemand later een nieuwe aanroep toevoegt met een `x`-samenstelling, zonder
dat daar een pitchrijke stroom voor nodig is.

**3. En de natuurwet zelf wordt getoetst.** Vier keer zoveel pitch in de bodemvloeistof mag de
dampsamenstelling níét veranderen — dat is Raoult en Dalton — terwijl elke som over de vloeistof er
wel mee meezakt. Dat is de scherpste formulering van het verschil, en zij staat als test.

---

## 22. Gevoeligheidsanalyse van de negen stofwaarden zonder bron

Voordat er bronnen worden gezocht: welke van de negen openstaande stofwaarden doet er eigenlijk
toe? Elke waarde is gevarieerd en de hele ontwerpketen is opnieuw gedraaid, op aangepaste kopieën
van de componentenlijst — er is niets in de bron gewijzigd.

### 22.1 De band hoort op p_sat, niet op de coëfficiënten

Een eerste versie van deze analyse verschoof de Antoine-constante A met tien procent. Dat is
**natuurkundig betekenisloos**: A, B en C komen uit één gezamenlijke fit en zijn geen onafhankelijke
grootheden. Wie er één aan draait, beschrijft geen onzekerheid maar een andere stof.

De band gaat daarom op de **uitkomst**: een multiplicatieve factor op p_sat, over het hele
temperatuurbereik. Numeriek is dat log₁₀(factor) bij A, maar de betekenis is een andere: de vórm van
de kromme blijft staan zoals zij is gefit, alleen het niveau schuift.

**De breedte van de band is een AANNAME en geen bron:**

| | Band op p_sat | Motivatie |
|---|---|---|
| Binnen het geldigheidsbereik van de fit | **±5 %** | de orde waarin goede dampspanningsfits onderling verschillen |
| Buiten het bereik | **×2 en ÷2** | extrapolatie van een Antoine-fit loopt exponentieel uit de pas; een factor twee is een ruwe maar niet overdreven marge |

Beide getallen zijn keuzes, geen literatuurwaarden. Zij zijn zo gekozen dat zij eerder te ruim dan
te krap zijn: een analyse die de onzekerheid onderschat is erger dan een die haar overdrijft.

### 22.2 Wie ligt er buiten zijn fit?

Het bedrijfsbereik van deze installatie loopt van **215,1 °C** (top van K-02) tot **237,1 °C**
(bodem van K-01).

| Component | Fit geldig | Binnen? | Hoe ver erbuiten |
|---|---|---|---|
| **C12:0** | 225,0 – 298,9 °C | **nee** | 9,9 K te koud |
| **C14:0** | 250,5 – 326,2 °C | **nee** | **35,4 K te koud** |
| C16:0 | 153,7 – 353,8 °C | ja | — |
| C18:0 | 173,8 – 369,9 °C | ja | — |
| C18:1 | 176,6 – 359,9 °C | ja | — |
| **C18:2** | 216,9 – 246,9 °C | **nee** | 1,8 K te koud |

Drie van de zes worden bij het bedrijfspunt geëxtrapoleerd. Bij C18:2 gaat het maar om 1,8 K, en
alleen aan de top van K-02; bij de bodem van K-01 zit hij er ruim binnen. Zijn werkelijke
onzekerheid komt dan ook niet van die 1,8 K maar van iets anders: **B en C zijn van C18:1 geleend**,
en daardoor is α(C18:2/C18:1) nu een constante die dat in werkelijkheid niet is.

### 22.3 Wat er met α gebeurt — en dat is niet wat verwacht werd

De verwachting was dat verhoudingen zoals α standhouden waar absolute waarden uiteenlopen, omdat een
gemeenschappelijke fout in een verhouding wegvalt. **Die verwachting klopt maar half, en het deel dat
niet klopt is het belangrijkste.**

**Bij vaste temperatuur valt een gemeenschappelijke factor exact weg.** α = p_sat(C16:0)/p_sat(C18:2)
bij 232 °C is 1,5448, en blijft 1,5448 als beide met 1,05 of met 2 worden vermenigvuldigd. Zover
klopt het.

**Maar α is niet gemeten bij een vaste temperatuur.** Zij wordt berekend op het bubbelpunt, en dat
punt schuift mee zodra de dampspanningen schuiven. In de volle keten:

| Alle componenten × | T_bodem K-01 | **α** | N | Bed | Marge tot 255 °C | Q_reboiler |
|---|---|---|---|---|---|---|
| 2,0 | 219,6 °C | **1,866** | 25,7 | 11,5 m | +27,7 K | 1202 kW |
| 1,2 | 232,4 °C | 1,693 | 29,7 | 13,4 m | +13,5 K | 1375 kW |
| **1,0** | **237,1 °C** | **1,638** | **31,5** | **14,2 m** | **+8,1 K** | **1448 kW** |
| 0,833 | 242,0 °C | 1,586 | 33,4 | 15,0 m | +2,5 K | 1529 kW |
| 0,5 | 256,3 °C | 1,455 | 40,3 | 18,1 m | **−14,5 K** | 1808 kW |

Een gemeenschappelijke factor 2 verschuift de bodemtemperatuur met **17,5 K** en α met **14 %** — via
de temperatuur, niet via de verhouding. En bij ÷2 is de marge tot het mediumplafond **negatief**: die
kolom is dan niet te bedrijven.

**En bij een TEGENGESTELDE afwijking slaat α er recht doorheen.** Dat is de realistische situatie,
want twee onafhankelijk gefitte correlaties wijken onafhankelijk af — en het is ook precies wat een
activiteitscoëfficiënt doet.

| Afwijking op het sleutelpaar | α | N | Bed | Marge | Q |
|---|---|---|---|---|---|
| ±5 % tegengesteld, C16:0 hoger | 1,813 | 26,6 | 12,0 m | 9,7 K | 1286 kW |
| ±5 % tegengesteld, C18:2 hoger | 1,480 | 38,8 | 17,5 m | 5,7 K | 1698 kW |
| ±10 %, C18:2 hoger | 1,344 | 50,4 | 22,7 m | 2,3 K | 2111 kW |
| ±20 %, C18:2 hoger | 1,121 | 124,0 | 55,8 m | **−14,0 K** | 4901 kW |

**Vijf procent de andere kant op is al genoeg** om het bed van 12,0 naar 17,5 m te laten lopen.

### 22.4 Het breekpunt: waar houdt het model op te bestaan?

De voorgeschreven band voor C18:2 is ×2 en ÷2. Toegepast levert dat geen getal op maar een
**foutmelding**:

| C18:2 × | Uitkomst |
|---|---|
| 1,0 | α 1,638 · N 31,5 · bed 14,2 m · marge 8,1 K |
| 1,1 | α 1,492 · N 38,2 · bed 17,2 m · marge 6,3 K |
| 1,3 | α 1,266 · N 62,2 · bed 28,0 m · marge 0,1 K |
| 1,5 | α 1,101 · N 147,2 · bed **66,2 m** · marge **−16,1 K** |
| **1,8** | **stages.js weigert: C16:0 is zwaarder dan C18:2, de sleutels staan omgekeerd** |
| 0,9 | α 1,817 · N 26,4 · bed 11,9 m · marge 9,5 K |
| **0,8** | **stages.js weigert: Underwood vindt componenten met α te dicht bij 1** |

Het model **schuift** dus niet onder die band, het **houdt op**. Dat is zelf de uitkomst: de
geldigheid van module 5 hangt aan een dampspanningsverhouding die maar een factor 1,5 respectievelijk
0,8 verdraagt, en de zwakst onderbouwde component van de set staat aan die knop.

### 22.5 Wat wél standhoudt

De **afbraakdosissen** houden over de hele band stand, en dat is geen toeval:

| Alle componenten × | T_K01 | T_K02 | Dosis K-02/K-01 | Film/sump |
|---|---|---|---|---|
| 2,0 | 219,6 | 227,3 | 0,21 – 0,28 | 0,10 – 0,77 |
| 1,0 | 237,1 | 246,9 | 0,22 – 0,31 | 0,04 – 0,17 |
| 0,6 | 251,1 | 263,2 | 0,24 – 0,36 | 0,02 – 0,06 |

"De afbraak zit in de koudste kolom" en "de reboilerfilm haalt de sump niet in" blijven in elke
variant staan.

**Waarom deze verhoudingen wél robuust zijn en α niet.** Beide zijn verhoudingen, maar van iets
anders. De dosisverhouding vergelijkt twee plekken in **dezelfde** installatie, met temperaturen die
uit **dezelfde** rekenketen komen: schuift de een, dan schuift de ander mee, en het verschil van
9,8 K blijft vrijwel staan. α vergelijkt twee **onafhankelijk gefitte correlaties**, en die wijken
onafhankelijk af.

Een verhouding is robuust als beide kanten uit dezelfde meetketen komen. Zij is juist extra kwetsbaar
als de twee kanten uit verschillende bronnen komen — dan telt de onzekerheid dubbel in plaats van
weg te vallen.

### 22.6 De overige vijf, kort

| Waarde | Uitslag | Toelichting |
|---|---|---|
| **Activeringsenergie** (#9) | ±17 % bij 9,4 K · ±27 % bij 14,8 K · **±92 % bij 42,9 K** bij Ea ±10 % | de dosisVERHOUDINGEN blijven kloppen, de absolute factoren niet |
| **Voedingssamenstelling** (#13) | pitch in de bodem **±15 %** bij ±10 % op de pitchfractie | T_bodem slechts ±0,6 % |
| **Vloeistofdichtheid** (#6) | verblijftijd en dosis ±10 % | elke verhouding onveranderd: de dichtheid schaalt beide kanten weg |
| **Associatiegraad** (#7) | dampvolume en zuigbelasting −10 % | raakt alleen de vacuümbelasting, die al als bovengrens is gemarkeerd |
| **Soortelijke warmte cp** (#12) | **0 %** | wordt niet gebruikt; zou 0,21 % duty per graad zijn |

### 22.7 De ranglijst, herzien

#### A — gevoelig én vindbaar in open literatuur

| Prioriteit | Sleutel | Waarde | Waarom, met het getal erbij |
|---|---|---|---|
| **1** | `A1_C18_2_DAMPSPANNING` | **C18:2, tweede dampspanningspunt** (#3/#10) | Het model heeft hier een **geldigheidsgrens**, geen gevoeligheid: bij ×1,8 en ×0,8 weigert `stages.js`, en al bij ×1,5 is het bed 66 m en de marge −16 K. Aan die knop staat de component waarvan B en C geleend zijn. Dampspanningsdata voor linolzuur zijn een gangbaar onderwerp in fysisch-chemische naslagwerken. |
| **2** | `A2_ACTIVITEITSCOEFFICIENT` | **Activiteitscoëfficiënten** (#8) | **Opgewaardeerd.** Een γ werkt als een TEGENGESTELDE factor op p_sat, en dat is de gevoelige richting. Vijf procent elk de andere kant op geeft al N 26,6 tot 38,8 en het bed 12,0 tot 17,5 m. De eerdere schatting van "±3,5 % op N" behandelde γ als gemeenschappelijk en onderschatte het effect met een factor vijf. UNIFAC is bovendien een open methode: hier is geen meting nodig. |
| **3** | `A3_NIVEAU_ANTOINE_SET` | **Het niveau van de hele Antoine-set** (openstaand punt 1) | **Nieuw in deze lijst.** Een systematische fout in de hele set — de "handboekwaarden" die nog niet tegen een fysiek handboek zijn geverifieerd — verschuift T_bodem met 17,5 K per factor 2 en de marge van +27,7 naar −14,5 K. Dit is geen stofwaarde maar een verificatie, en juist daarom goedkoop. |
| **4** | `A4_VOEDINGSSAMENSTELLING` | **Voedingssamenstelling** (#13) | ±15 % op de pitchconcentratie in de bodem, en daarmee op de hele opconcentratieketen van K-01. |
| **5** | `A5_VLOEISTOFDICHTHEID` | **Vloeistofdichtheid** (#6) | Alleen een schaalfactor op de verblijftijd, maar die 75 minuten is een gequoteerd getal. Van de vijf het goedkoopst te sluiten. |

#### B — gevoelig maar niet met bron te onderbouwen

**Activeringsenergie thermische afbraak** (#9). Module 9 heeft gezocht en niets bruikbaars gevonden;
wat er is, gaat over andere reacties of ander materiaal. En hij werkt het hardst door waar het ertoe
doet: ±92 % op de wandfactor van 42,9 K, precies het getal waarmee de keuze voor geforceerde
circulatie wordt onderbouwd.

De verhoudingen tussen kolommen blijven wél kloppen, dus de didactische kernuitspraken staan. Wat
niet staat is elk absoluut getal over hoeveel sneller iets afbreekt.

**Wat deze categorie vraagt** is meer dan een regel in dit bestand: de band hoort zichtbaar te zijn
op het scherm waar zij doorwerkt — bij de afbraakgetallen en bij de reboilerkeuze. Dat is nog te
bouwen. Wat het punt sluit is geen literatuurzoektocht maar een houdproef.

#### C — ongevoelig. Schatting mag blijven, met deze gevoeligheid als verantwoording

| Waarde | Uitslag bij de VOLLE band (×2 / ÷2) | Toelichting |
|---|---|---|
| **C14:0** (#2) | α +0,5 %, N −0,9 %, Q −1,0 % | **Het leerzaamste geval van de hele analyse.** C14:0 ligt 35,4 K buiten zijn fit — de verste extrapolatie van de set — en een factor twee verandert vrijwel niets. Hij is 1 massaprocent van de voeding en gaat er hoe dan ook bovenlangs uit. **Extrapolatieafstand en belang hebben niets met elkaar te maken.** |
| **C12:0** (#2) | idem | zelfde verhaal, 9,9 K buiten de fit |
| **C18:0** (#5) | < 1 % | ligt binnen zijn fit en is geen sleutelcomponent |
| **Associatiegraad** (#7) | −10 % op het dampvolume | module 7 meldt zijn getal al als bovengrens |
| **cp** (#12) | 0 % | wordt niet gebruikt; de balans is zuiver latent |

### 22.8 NAGEKOMEN: wat de meting met deze analyse deed

Deze analyse is gemaakt vóór het zoeken naar bronnen, om te bepalen wáár dat zoeken moest
beginnen. Dat zoeken slaagde (hoofdstuk 23), en daarmee is een deel van deze analyse van een
aanname in een meting veranderd. Dat hoort erbij te staan, want het toetst de methode zelf.

**De aangenomen bandbreedtes, naast de gemeten spreiding.** De band was ±5 % binnen het
geldigheidsbereik en een factor 2 erbuiten. Nu er twee onafhankelijke bronnen naast elkaar liggen,
is de werkelijke spreiding bij het bedrijfspunt te meten:

| | aangenomen band | gemeten verschil tussen twee bronnen |
|---|---|---|
| C14:0 (buiten de oude fit) | factor 2 | **0,2 K**, oftewel enkele tienden procenten |
| C16:0 (binnen de oude fit) | ±5 % | **1,2 K**, ongeveer 5 % |
| C18:0 (binnen de oude fit) | ±5 % | **11,8 K**, ongeveer 38 % |

**De aanname was dus twee keer fout, en in tegengestelde richting.** Voor de geëxtrapoleerde
componenten was de factor 2 wild overdreven: de twee bronnen liggen er binnen een halve graad.
Voor C18:0 was ±5 % juist veel te optimistisch: daar staat een onopgeloste onenigheid van bijna
twaalf graden. Extrapolatieafstand bleek opnieuw geen goede voorspeller van onzekerheid — zie de
conclusie bij C14:0 hieronder, die hierdoor alleen maar sterker wordt.

**De ranglijst is grotendeels afgewerkt.** Van groep A zijn de punten over de dampspanning van
C12:0 tot en met C18:1 gesloten met een primaire meting. Wat openstaat is beschreven in 23.6 tot
23.8 en in de openstaande punten hieronder: C18:2 (bron aangewezen, niet bereikbaar), de
activiteitscoëfficiënten, de voedingssamenstelling en de vloeistofdichtheid.

**Eén voorspelling van deze analyse kwam niet uit.** 22.5 verwachtte dat de verhoudingen (α, de
dosisverhouding tussen de kolommen) stand zouden houden en de absolute temperaturen niet. Bij de
echte datawissel gebeurde het omgekeerde: de temperaturen bewogen 0,3 tot 1,4 procent en α negen
procent. De redenering achter die verwachting klopte — een gemeenschappelijke factor valt weg uit
een verhouding — maar zij gold alleen voor een band die ALLE componenten tegelijk verschuift. Een
echte databron verschuift ze verschillend, en dan werkt het andersom: het bubbelpunt middelt uit
en de verhouding niet. Zie 23.9.

### 22.9 Wat deze analyse niet is

Dit zijn lokale gevoeligheden rond één bedrijfspunt, met één of twee waarden tegelijk. Zij zeggen
niets over drie of meer tegelijk, en de bandbreedtes zelf (±5 % en ×2) zijn aannamen en geen
gemeten spreidingen.

Wat zij wél laten zien is waar de **randen van het model** liggen, en dat is een ander soort
uitkomst dan een percentage: bij C18:2 loopt module 5 niet scheef maar vast, en dat is bruikbare
informatie voor wie beslist waar het zoekwerk begint.

---

## 23. De dampspanningsdata vervangen: van handboek naar meting

Op 24 september 2026 zijn de Antoine-sets van vijf van de zes vluchtige componenten vervangen.
Dit hoofdstuk legt vast waar de nieuwe data vandaan komt, hoe zij is getoetst, wat er níet mee is
opgelost, en wat de wissel met de uitkomsten van de module heeft gedaan.

### 23.1 Waarom de oude data moest wijken

De oude sets kwamen uit twee bronnen. C12:0 en C14:0 waren tweepunts-fits op gepubliceerde
kookpunten bij 133 en 1013 mbar; C16:0, C18:0 en C18:1 kwamen uit de NIST WebBook, terug te voeren
op Stull 1947, met geldigheidsvensters van bijna 200 K.

Beide soorten hebben hetzelfde probleem: hun zwaartepunt ligt bij ATMOSFERISCHE druk, en deze kolom
draait op 20 mbar. Voor C12:0 en C14:0 was dat regelrechte extrapolatie — C14:0 werd gebruikt bij
488 tot 510 K terwijl zijn fit pas bij 524 K begon, oftewel 35 K eronder. Voor de drie brede sets
was het geen extrapolatie op papier, maar wel de slechtste hoek van hun bereik: één Antoine-fit kan
over 200 K niet aan beide uiteinden kloppen.

### 23.2 De nieuwe bron, en de bronketen eerlijk opgeschreven

**Primaire meting.** Falleiro, R. M. M.; Silva, L. Y. A.; Meirelles, A. J. A.; Krahenbuhl, M. A.
*Vapor pressure data for fatty acids obtained using an adaptation of the DSC technique.*
Thermochimica Acta **547** (2012) 6–12. doi 10.1016/j.tca.2012.07.034.

Differentiële scanningcalorimetrie, aangepast met een wolfraamcarbide-bolletje op het gaatje van de
crucible, waardoor een opwarmsnelheid van 25 K/min mogelijk werd. Monsters van 3 tot 5 mg.
**Gemeten bereik 1333 tot 9333 Pa** — het bedrijfsgebied van deze kolom (13 tot 30 mbar) ligt
daarin.

**Waar de getallen vandaan komen.** Niet uit het artikel zelf, maar uit het **ThermoML-archief van
NIST TRC**, dat de gemeten punten van deze publicatie als gecureerde dataset bewaart: zeven
(T, p)-paren per component, met onzekerheid. Die punten staan nu als `validatiepunten` in
`components.js` en worden door de testsuite tegen de constanten gehouden.

**Controle op de digitalisering.** De gepubliceerde Antoine-constanten reproduceren de
gearchiveerde punten binnen 0,22 tot 0,47 K gemiddeld. Bovendien is er een eigen kleinste-kwadraten
fit op dezelfde punten gedaan; voor C12:0 en C16:0 komt die tot op drie decimalen uit bij de
gepubliceerde constanten. Dat is een sluitende controle op zowel de overname als de vorm
(log₁₀(p/Pa) = A − B/(T+C), T in K).

**Secundaire controlebron.** Wilson, J. A.; Chickos, J. S. *Vapor Pressures and Vaporization,
Sublimation, and Fusion Enthalpies of Some Fatty Acids.* J. Chem. Eng. Data **58** (2013) 322–333.
doi 10.1021/je300902c, eveneens via ThermoML. Andere methode (correlatie-gaschromatografie), andere
groep. **De dampspanningen zijn daar AFGELEID uit retentietijden en niet rechtstreeks gemeten** —
het ThermoML-veld noemt de methode letterlijk "Calculation" — en de onzekerheidsband is breed. Deze
bron dient daarom als controle, nooit als fitdata.

### 23.3 De verdampingswarmte, en bij welke temperatuur

Wilson en Chickos geven verdampingswarmten bij **298,15 K**, oftewel kamertemperatuur. Onze eigen
`dHvap_Jmol` staat bij 513,15 K. Die twee zomaar naast elkaar leggen levert een verschil van
tientallen procenten op dat niets met de stofdata te maken heeft en alles met de temperatuur.

**De correctiemethode, vastgelegd als AANNAME en niet als bron.** Clausius-Clapeyron wordt toegepast
op de eigen dampspanningsreeks van dezelfde publicatie, die van 298 tot 600 K loopt. De verhouding
dHvap(502 K) / dHvap(298,15 K) valt voor de drie verzadigde zuren opvallend constant uit:

| Component | dHvap bij 298,15 K | dHvap(502 K) uit dezelfde reeks | verhouding |
|---|---|---|---|
| C14:0 | 111,2 ± 17,7 kJ/mol | 78,2 | 0,703 |
| C16:0 | 121,6 ± 16,2 kJ/mol | 85,0 | 0,699 |
| C18:0 | 135,9 ± 13,8 kJ/mol | 93,7 | 0,690 |

**Watson is bewust NIET gebruikt.** Die correlatie is de gebruikelijke manier om een
verdampingswarmte naar een andere temperatuur te brengen, maar zij vraagt een kritische temperatuur
per component, en daarvoor heeft dit project geen bron. Een ongefundeerde T_c invoeren om een
correctie te kunnen maken verplaatst de onzekerheid in plaats van haar te verkleinen.

Met die correctie klopt onze eigen waarde voor alle vier de componenten binnen de onzekerheid van de
bron: C14:0 −2,2 %, C16:0 −4,4 %, C18:0 −7,9 %, C18:2 −10,0 % tegen bronsbanden van 10 tot 16 %.
De testsuite rekent dit na, noemt in elke regel de temperatuur, en heeft een negatief
geval: zónder de correctie hoort de toets voor elke component te FALEN.

**Een punt dat NIET gesloten is.** De bron geeft voor C18:2 (linolzuur) exact dezelfde waarde én
dezelfde onzekerheid als voor C18:0: 135,9 ± 13,8 kJ/mol. Dat kán kloppen, maar het is ook precies
het patroon dat bij een fout in de gegevensoverdracht hoort. Een zoekresultaat noemde bovendien
134,1 ± 10,3 kJ/mol voor linolzuur, wat hier niet mee overeenkomt. Twee onafhankelijke lezingen van
het ThermoML-bestand gaven beide 135,9 ± 13,8, maar zonder het artikel zelf is dit niet te
beslechten. **De waarde wordt daarom alleen als kruiscontrole gebruikt en nergens om mee te
rekenen.** Voor C18:2 is bovendien de schaalfactor 0,697 een AANNAME: het gemiddelde van de drie
verzadigde zuren, want een eigen dampspanningsreeks ontbreekt.

### 23.4 Wat de tweede bron bevestigt, en wat niet

| Component | overeenstemming met Falleiro | oordeel |
|---|---|---|
| C14:0 | binnen **0,21 K** op alle zeven punten | bevestigd |
| C16:0 | binnen **1,19 K** | bevestigd |
| C18:0 | stelselmatig een factor 0,62 lager, oftewel **11,4 tot 11,8 K** | **onopgelost** |
| C12:0 | geen data — Wilson en Chickos beginnen bij C14 | **niet te toetsen** |
| C18:1 | geen data — de bron heeft de TRANS-isomeer (elaïdinezuur), een andere stof | **niet te toetsen** |

Voor C14:0 en C16:0 is dit zo sterk als een controle worden kan: twee methoden, twee groepen, twee
publicaties, en ze liggen binnen een graad. Die twee punten staan als onafhankelijke
`validatiepunten` in `components.js`.

Voor **C18:0** is er een echte onenigheid van bijna 12 K en die is hier niet te beslechten. De
Chickos-punten zijn daarom NIET als validatiepunt opgenomen — een tolerantie van 12 K zou een
toets zijn die niets toetst. De onenigheid staat in `opmerkingen` bij de component en er is een
test die bewaakt dat zij genoteerd blijft. Praktisch weegt zij licht: C18:0 is 5 massaprocent van de
voeding en de gevoeligheidsanalyse geeft er minder dan 1 procent uitslag op.

Voor **C12:0** is er geen tweede bron. Hij is als enige van de vijf onbevestigd overgenomen. Hij zit
niet in de generieke voeding en de gevoeligheidsanalyse geeft hem minder dan 0,2 procent uitslag,
dus dat is aanvaardbaar — maar het hoort er te staan.

### 23.5 Het betwiste handboekpunt bij 15 mmHg

C16:0 had al een validatiepunt op **215,0 °C bij 15 mmHg**, en 15 mmHg is precies 20,0 mbar: ons
bedrijfspunt. Dat getal steunde de OUDE set (215,8 °C) en spreekt de nieuwe tegen (219,0 °C).

Dat is uitgezocht in plaats van weggemoffeld. Bij 20 mbar geven de vier beschikbare bronnen:

| Bron | T_kook C16:0 bij 20 mbar |
|---|---|
| handboekwaarde (primaire meting niet teruggevonden) | 215,0 °C |
| oude Antoine-set (Stull 1947) | 215,8 °C |
| **Falleiro 2012 (DSC-meting)** | **219,0 °C** |
| **Wilson en Chickos 2013 (correlatie-GC)** | **220,1 °C** |

De twee moderne metingen liggen 1,1 K uit elkaar en samen 4,5 K boven het handboekgetal. Dat de
oude set met het handboek overeenstemt is geen onafhankelijke bevestiging: hij was er mede op
gevalideerd.

Het handboekpunt is blijven staan, met een verruimde tolerantie van 5 K en de onenigheid expliciet
in de `opmerkingen`. Zo blijft zij zichtbaar in plaats van verdwijnen. Hetzelfde geldt voor C18:0 bij
15 mmHg, waar het verschil 6,1 K is.

### 23.6 Wat er NIET is opgelost: C18:2

**C18:2 is als enige van de zes niet verbeterd.** Falleiro 2012 dekt hem niet.

De handboekwaarden die het zoeken opleverde — 229 °C bij 16 mmHg (CRC Handbook, 97e ed.), 230 °C bij
16 mmHg (Merck Index, 11e ed.) en 230 °C bij 21 mbar (GESTIS) — zijn **geen drie bronnen maar drie
afrondingen van hetzelfde punt**, en dat punt zat al als ankerpunt in de module (229,55 °C bij
21,00 mbar, NIST WebBook naar Weast en Grasselli 1989). Via `units.js` is 16 mmHg gelijk aan
21,33 mbar. Voor alle drie geldt: **secundaire bron, primaire meting niet teruggevonden.** Ze
bevestigen A en zeggen niets over B en C.

B en C blijven dus geleend. Omdat de C18:1-set waarvan zij geleend waren is vervangen, is de lening
opnieuw gemaakt vanaf de gemeten C18:1-set en is A opnieuw op het eigen ankerpunt gefit
(A = 13,03775). Een lening laten staan van een set die niet meer bestaat is geen keuze maar een
vergissing. Het blijft een AANNAME: alleen A rust op een eigen meetpunt.

**Wat dit punt wél zou sluiten, is aangewezen maar niet bereikt.** Barbeiro, L. B.; Matricarde
Falleiro, R. M.; Meirelles, A. J. A. *Vapour pressure and VLE data of fatty compounds.* Journal of
Chemical Thermodynamics **159** (2021) 106469, doi 10.1016/j.jct.2021.106469, tabel 5. Daar is
linolzuur gemeten bij **2,67 tot 26,66 kPa** — precies ons gebied — met een maximale
standaardafwijking van 0,86 K. Gesloten toegang, geen repositorykopie (OpenAlex: `oa_status:
closed`), geen ThermoML-depot. **De waarden zijn niet opgehaald en worden niet verzonnen.** Met
instellingstoegang is dit het goedkoopste openstaande punt van het hele project.

### 23.7 Aandachtspunt: de Ea-band hangt aan een flashberekening

Dit kwam boven water doordat de stofdata veranderde, en het is een ontwerpprobleem en geen
rekenfout.

De bovengrens van de activeringsenergieband (`degradation.js`) wordt afgeleid door de vuistregel
"factor 2 per 8 K" om te keren bij het temperatuurPAAR uit de misvatting in module 3. Dat paar is
de voedingstemperatuur en de bodemtemperatuur uit een Rayleigh-flash, en dus hangt het aan de
voedingssamenstelling en aan de dampspanningsdata.

Toen die data van handboekwaarden naar een meting ging, zakte het verschil van 8,09 naar 7,20 K en
liep de geïmpliceerde Ea van 178,4 naar **201,2 kJ/mol**. Een vuistregel die "factor 2 per 8 K"
zegt hoort niet mee te bewegen met een stofdatawissel. Dat hij dat hier doet betekent dat de band
op de verkeerde grootheid is verankerd.

De propagatie is getrouw uitgevoerd — 201,2 kJ/mol staat nu in de module — maar de methode is NIET
veranderd, want dat is een inhoudelijke keuze. Twee alternatieven liggen voor de hand: de vuistregel
omkeren bij zijn eigen 8 K (dat geeft 181,4 kJ/mol bij de nieuwe voedingstemperatuur), of de band
loskoppelen van de flash en hem op het temperatuurGEBIED van de kolom baseren, zoals
`nietInternConsistent` nu al doet (180 tot 224 kJ/mol). **Beslissing openstaand.**

### 23.8 Aandachtspunt: de condensoruitlaat ligt ver onder het gemeten venster

De gemeten sets zijn scherp waar de kolom draait en zwak daarbuiten — en dat laatste raakt één
plek concreet. De meesleeptabel van module 7 rekent de dampspanning van C16:0 uit bij de
condensoruitlaat, 80 tot 140 °C. Het gemeten venster loopt van 210 tot 260 °C, dus dat is 70 tot
130 K ERONDER.

Bij 80 °C geven de drie beschikbare bronnen: de gemeten set 0,0012 Pa, de vervallen set van Stull
1,22 Pa, en Wilson en Chickos — de enige van de drie die daar werkelijk data heeft — 0,13 Pa. Een
**factor duizend** tussen de uitersten. Bij 140 °C is de spreiding geslonken tot een factor vijf.

De tabel is herrekend met de gemeten set en draagt nu een expliciete onzekerheidsnotitie. Wat zij
betrouwbaar laat zien is de VORM: meesleep loopt exponentieel met de uitlaattemperatuur, over 60 K
een factor tienduizend. Dat staat bij elke van de drie bronnen overeind. Het absolute getal in kg/h
niet. **Wat dit sluit is een dampspanningsmeting van C16:0 tussen 350 en 420 K.**

Eenzelfde voorbehoud geldt de verdampingswarmte ver buiten het venster. De nieuwe set geeft voor
C16:0 een daling van 97,0 kJ/mol bij 198 °C naar 59,0 bij 354 °C: bijna 40 procent. Watson zou daar
ongeveer 7 procent geven. Het NIVEAU bij het bedrijfspunt is door twee bronnen bevestigd (88,2 tegen
88,2 kJ/mol bij 487 K), de HELLING ver daarbuiten niet. Een fit over een venster van 50 K legt de
kromme vast maar niet haar kromming daarbuiten.

### 23.9 Wat de wissel met de uitkomsten deed

| Grootheid | oude data | gemeten data | |
|---|---|---|---|
| T_bodem K-01 | 237,1 °C | 237,8 °C | +0,3 % |
| T_bodem K-02 (zonder drukval) | 232,1 °C | 232,8 °C | +0,3 % |
| T_bodem K-02 (met drukprofiel) | 246,9 °C | 250,4 °C | +1,4 % |
| α van het sleutelpaar | 1,638 | 1,476 | **−9,9 %** |
| N_min | 15,8 | 20,0 | **+27 %** |
| R_min | 2,341 | 2,950 | **+26 %** |
| N (Gilliland) | 31,5 | 39,0 | **+24 %** |
| bedhoogte | 14,2 m | 17,6 m | **+24 %** |
| Q_reboiler | 1448 kW | 2053 kW | **+42 %** |
| **marge K-02 tot het mediumplafond** | **8,1 K** | **4,6 K** | |

**De verwachting vooraf was dat de verhoudingen stand zouden houden en de absolute temperaturen
niet. Het tegendeel bleek waar.** De temperaturen bewegen nauwelijks, de verhoudingen fors.

De reden is navolgbaar. Een kooktemperatuur is een bubbelpunt van het hele mengsel: alle componenten
schuiven dezelfde kant op en dat middelt weg. α is hier géén verhouding binnen één rekenketen maar
tussen TWEE — C16:0 uit de nieuwe meting, C18:2 nog uit de oude geleende set — en dan valt de
gemeenschappelijke factor niet weg. Dat is precies het patroon dat dit project eerder al vastlegde:
een verhouding is robuust binnen één bron en breekbaar tussen twee.

De duty verdient aparte vermelding, want daar zat een echte fout. De verdampingswarmte van C16:0 bij
487 K stond op 74,5 kJ/mol. Falleiro geeft 90,2 en Wilson en Chickos 88,2 — twee onafhankelijke
bronnen tegen één. De oude waarde was ongeveer 17 procent te laag en de reboilerduty dus ook.

### 23.10 Conclusies die de wissel overleefden

Deze zijn het vermelden waard, want zij zijn nu op een heel ander getallenstel beproefd:

- **C18:1 en C18:0 zijn niet te destilleren uit elkaar.** N_min ging van 74–118 naar 49–52
  schotels. Allebei volstrekt onhaalbaar; het antwoord op "kan dit met destillatie?" verandert niet.
- **De afbraak zit in de koudste kolom.** K-02 is 12,3 K heter dan K-01, en toch zit de dosis in
  K-01 omdat de verblijftijd daar zes keer langer is. Dat is meetkunde, geen stofdata.
- **De film haalt de sump niet in.**
- **Pitch verdampt niet en maakt de bodem warmer.**

En twee conclusies die juist STERKER werden:

- **De C18:0/C18:1-anomalie is weg.** De oude sets maakten stearinezuur beneden 202 °C vluchtiger
  dan oliezuur, wat chemisch onmogelijk is. Dat was een fit-artefact; over 70 tot 350 °C is C18:1 nu
  overal de vluchtigste. Die oude kruising staat als `alfaOmslagpuntVervallen` bewaard, mét een test
  die bewijst dat zij er bij de vervallen sets wél was.
- **De temperatuurafhankelijkheid van α is niet langer tegenstrijdig.** Met de oude data bewogen de
  twee sleutelparen tegengesteld en viel er geen leerregel uit te halen. Nu dalen beide licht met de
  temperatuur, zoals het hoort. De verhouding tussen de twee paren ging van 9,7→5,6 (een factor twee
  over 17 K) naar 4,18→4,33 (drie procent).

### 23.11 De prijs van deze wissel, eerlijk genoteerd

De vervanging raakte **243 vastgelegde getallen** in `src/content/`, `src/physics/` en
de testsuite. Elk daarvan is met de hand nagerekend en bijgewerkt; de testsuite vond ze allemaal,
want elk getal in de module wordt door een test tegen de fysica gehouden.

Dat is tegelijk het bewijs dat die opzet werkt en de waarschuwing dat zij duur is. Een stofwaarde
veranderen in dit project is geen kwestie van één regel. Wie dat nog eens doet, moet erop rekenen
dat de helft van de lesteksten mee moet.

Drie tests bleken bij die gelegenheid FRAGIEL in plaats van fout, en zijn steviger gemaakt:

- de tijdstap van de dagsimulatie en van twee oefeningen stond ingetypt op 50 s terwijl het model
  er nog maar 44 toeliet; die wordt nu bij het model opgevraagd;
- het werkgebied van de kolom werd getoetst op de MINIMALE diameter (F = F_max), waar volle
  belasting per definitie op de floodinggrens zit; dat is nu de ontwerpdiameter;
- de refluxsweep van K-02 gebruikte een ingetypte ontwerpreflux van 3,021 die het ontwerppunt niet
  meer was.

---

## 24. Groep A: het oordeel per waarde

De eis voor v1.0 was dat over **elke** waarde uit groep A een uitspraak in
dit bestand staat. Niet dat elke waarde is opgelost — dat ligt niet in onze hand — maar dat elke
waarde is **beoordeeld**: een bron met geldigheidsbereik, of de vaststelling dat er geen te vinden
was, met de gevoeligheid erbij.

Dit hoofdstuk is de plek waar dat staat, en de testsuite leest het door. Die test valt
om zodra een sleutel uit de groep-A-tabel van 22.7 hier ontbreekt, een oordeel buiten de vaste lijst
draagt, of geen bron- en gevoeligheidsregel heeft.

**De toegestane oordelen, en wat ze betekenen:**

| Oordeel | Betekenis |
|---|---|
| `GESLOTEN` | Bron met geldigheidsbereik gevonden, opgehaald en overgenomen. |
| `DEELS_GESLOTEN` | Een deel is met een bron gesloten, een deel niet. Beide delen staan benoemd. |
| `BRON_AANGEWEZEN` | De bron is aangewezen en de vindplaats geverifieerd, maar de waarden zijn niet opgehaald. |
| `GEEN_BRON` | Er is gezocht en er is niets bruikbaars gevonden. |
| `GEEN_BRON_MOGELIJK` | Er KAN geen bron zijn, omdat de grootheid op een aanname rust. |

Elk oordeel hieronder draagt drie regels: **Bron** (wat er is, of niet), **Gevoeligheid** (wat het
met het model doet) en **Wat het zou sluiten** (de eerstvolgende concrete stap).

---

#### `A1_C18_2_DAMPSPANNING` — OORDEEL: `BRON_AANGEWEZEN`

**Bron:** Barbeiro, L. B.; Matricarde Falleiro, R. M.; Meirelles, A. J. A. *Vapour pressure and VLE
data of fatty compounds.* J. Chem. Thermodyn. **159** (2021) 106469, doi 10.1016/j.jct.2021.106469,
tabel 5. Linolzuur gemeten bij **2,67 tot 26,66 kPa** — precies ons bedrijfsgebied — met een
maximale standaardafwijking van 0,86 K. Gesloten toegang, geen repositorykopie (OpenAlex:
`oa_status: closed`), geen ThermoML-depot. De waarden zijn **niet opgehaald en niet verzonnen**. De
drie handboekwaarden bij 16 mmHg (CRC, Merck, GESTIS) zijn geen drie bronnen maar drie afrondingen
van het ankerpunt dat er al in zit; secundaire bron, primaire meting niet teruggevonden. Zie 23.6.

**Gevoeligheid:** de grootste van de hele set, en het is geen gevoeligheid maar een
**geldigheidsgrens**. Bij ×1,8 en ×0,8 op p_sat weigert `stages.js` te rekenen; al bij ×1,5 is het
bed 66 m en de marge −16 K. C18:2 is bovendien de zware sleutel, dus N_min, R_min, de bedhoogte en
de duty hangen er allemaal aan. Hij is als enige van de zes vluchtige componenten niet op een eigen
meting gebaseerd.

**Wat het zou sluiten:** één middag met instellingstoegang tot het artikel. Dit is het goedkoopste
openstaande punt van het project en tegelijk het zwaarstwegende.

---

#### `A2_ACTIVITEITSCOEFFICIENT` — OORDEEL: `DEELS_GESLOTEN`

**Bron:** het paar moet eerst benoemd worden, want er zijn er twee en ze zijn ongelijk.

*Vetzuur tegenover vetzuur:* **gemeten**. Matricarde Falleiro, R. M.; Meirelles, A. J. A.;
Krahenbuhl, M. A. *Experimental determination of the (vapor + liquid) equilibrium data of binary
mixtures of fatty acids by differential scanning calorimetry.* J. Chem. Thermodyn. **42** (2010)
70–77, doi 10.1016/j.jct.2009.07.008, via het ThermoML-archief van NIST TRC. Drie binaire systemen
bij 6666 Pa, elk met vloeistof- én dampsamenstelling, dus γ is er rechtstreeks uit te rekenen met
γ_i = y_i·P / (x_i·p_sat_i(T)) en de dampspanningen uit dezelfde publicatiereeks:

| Paar | γ wijkt af van 1 |
|---|---|
| C14:0 / C16:0 | **3,1 %** — ideaal binnen de meetspreiding |
| C16:0 / C18:0 | **10,1 %** |
| C14:0 / C18:0 | **23,3 %**, tot 50 % bij oneindige verdunning |

De aanname dat vetzuren onderling ideaal mengen klopt dus voor naburige ketens en **niet** als de
ketens vier koolstoffen schelen. Dat is getoetst en geen vermoeden: de punten zijn gesplitst naar
wel of niet geëxtrapoleerde dampspanning, en binnen bereik (24,6 %) en buiten bereik (22,7 %) wijken
even veel af. Het is dus geen rekenartefact.

Alle afwijkingen zijn **negatief**. Dat is wat je verwacht als carbonzuren in de dampfase
dimeriseren: met een ideale damp aangenomen slikt γ die associatie op. **Openstaand punt 8
(activiteitscoëfficiënt) en punt 7 (associatiegraad) zijn daarmee niet onafhankelijk** — uit deze
data zijn ze niet te scheiden.

*Vetzuur tegenover de pitch-pseudocomponent:* **`GEEN_BRON_MOGELIJK`**, en dat is het deel dat er
voor deze kolom toe doet. Die pseudocomponent is zelf een aanname — een gemiddeld triglyceride dat
staat voor alles wat niet verdampt — dus een γ ervoor is een aanname op een aanname. Er is geen
meting denkbaar van het evenwicht tussen een echte stof en een verzonnen stof. Wat wel kan is een
UNIFAC-schatting met een tripalmitine als gemachtigde, en dan is de uitkomst een schatting van een
aanname; dat hoort zo te worden opgeschreven en niet als stofwaarde.

**Gevoeligheid:** groot, en in de gevaarlijke richting. Een γ werkt als een **tegengestelde** factor
op p_sat van de twee sleutels. Vijf procent elk de andere kant op geeft al N tussen 26,6 en 38,8 en
het bed tussen 12,0 en 17,5 m. De eerdere schatting van "±3,5 % op N" behandelde γ als
gemeenschappelijk en onderschatte het effect met een factor vijf.

**Wat het zou sluiten:** voor het vetzuur/vetzuur-deel niets meer — dat is gemeten. Voor het
vetzuur/pitch-deel kan alleen een UNIFAC-schatting, expliciet gelabeld als schatting van een
aanname, of een houdproef met echte bodemmonsters.

---

#### `A3_NIVEAU_ANTOINE_SET` — OORDEEL: `DEELS_GESLOTEN`

**Bron:** voor vijf van de zes vluchtige componenten **gesloten** met een directe meting in het
bedrijfsgebied: Falleiro et al. 2012, Thermochim. Acta **547**, 6–12, gemeten bij 1333–9333 Pa, via
het ThermoML-archief van NIST TRC. Bevestigd door een tweede, onafhankelijke bron en methode (Wilson
en Chickos 2013, correlatie-GC) binnen 0,21 K voor C14:0 en 1,19 K voor C16:0. Zie hoofdstuk 23.

Niet gesloten zijn drie dingen, en ze staan alle drie benoemd:
- **C18:0** — de twee bronnen verschillen 11,4 tot 11,8 K en dat is niet te beslechten (23.4).
- **C12:0** — als enige onbevestigd overgenomen; Wilson en Chickos beginnen bij C14 (23.4).
- **De handboekwaarden bij 15 mmHg** voor C16:0 en C18:0 spreken de meting met 4,0 en 6,1 K tegen.
  Ze zijn blijven staan met een verruimde tolerantie zodat de onenigheid zichtbaar blijft (23.5).

**Gevoeligheid:** een systematische fout in de hele set verschuift T_bodem met 17,5 K per factor 2
en de marge van +27,7 naar −14,5 K. Dat is precies waarom dit punt van "verificatie" naar "gesloten"
moest. De resterende onzekerheid is kleiner dan die band: de twee bronnen liggen voor de
sleutelcomponent binnen 1,2 K.

**Wat het zou sluiten:** een derde bron voor C18:0 om de 11,8 K te beslechten, en een
dampspanningsmeting van C12:0 in het bedrijfsgebied. Beide raken geen dragende uitspraak — C18:0
geeft minder dan 1 procent uitslag en C12:0 zit niet in de voeding.

---

#### `A4_VOEDINGSSAMENSTELLING` — OORDEEL: `BRON_AANGEWEZEN`

**Bron:** Japir, A. A.-W.; Salimon, J.; Derawi, D.; Bahadi, M.; Al-Shuja'a, S.; Yusop, M. R. *A
highly efficient separation and physicochemical characteristics of saturated fatty acids from crude
palm oil fatty acids mixture using methanol crystallisation method.* OCL **25** (2018) A203,
doi 10.1051/ocl/2018003. Vindplaats en licentie **geverifieerd** via Crossref en OpenAlex: diamond
open access, CC-BY, gepubliceerde versie. Het artikel beschrijft een palmvetzuurmengsel na splitsing
— precies onze voeding.

**De tabel zelf is niet opgehaald, en dat is iets anders dan onvindbaar.** Het artikel bestaat, is
open access onder CC-BY, en staat gewoon op de site van de uitgever. Wat niet lukt is het
GEAUTOMATISEERD ophalen: zowel de HTML als de PDF geven HTTP 403, en de PDF ook met een
browser-achtige user agent. Dat is botbeveiliging en geen toegangsbeperking. **Met de hand,
in een browser, is de tabel in een minuut te raadplegen.** Wat er nog nodig is, is dus geen
zoekwerk en geen abonnement maar een handeling. Een zoekmachinesamenvatting noemde 0,2 % C12:0, 1,0 % C14:0, 42,3 % C16:0,
4,3 % C18:0, 42,1 % C18:1, 9,9 % C18:2 en 0,2 % C18:3, wat binnen enkele procentpunten overeenkomt
met onze generieke voeding (1 / 44 / 5 / 38 / 10 op pitchvrije basis). **Dat is geruststellend maar
geen verificatie**, en wordt daarom niet overgenomen. De voedingssamenstelling blijft staan als
generiek en afgerond, zoals 9.2 zegt.

**Gevoeligheid:** ±10 % op de pitchfractie geeft ±15 % op de pitchconcentratie in de bodem en
daarmee op de hele opconcentratieketen van K-01; op T_bodem is het ±0,6 %. De vetzuurverhoudingen
onderling bepalen welk paar de sleutels zijn, en die keuze (C16:0 / C18:2) is robuust gebleken bij
de datawissel.

**Wat het zou sluiten:** het artikel in een browser openen en tabel 1 overnemen, en daarbij noteren
of de percentages massaprocent of GC-oppervlakteprocent zijn — dat verschil is voor een
vetzuurspectrum niet verwaarloosbaar. Eén handeling, geen abonnement nodig.

**Aanvulling, 20 oktober 2026:** de vetzuren hebben nu een gelezen bron, Codex CXS 210-1999, tabel 1, palmolie (9.2): elke
modelwaarde ligt binnen haar band. Het oordeel hierboven is niet veranderd; de tabel van Japir e.a. is nog steeds niet
overgenomen. De pitch is een ontwerpkeuze met een band (9.2).

---

#### `A5_VLOEISTOFDICHTHEID` — OORDEEL: `BRON_AANGEWEZEN`

**Bron:** twee aangewezen, geen van beide opgehaald, en allebei met een geldigheidsbereik dat ons
bedrijfspunt **niet** dekt.
- Halvorsen, J. D.; Mammel, W. C.; Clements, L. D. *Density estimation for fatty acids and vegetable
  oils based on their fatty acid composition.* JAOCS **70** (1993) 875–880, doi 10.1007/BF02545346.
  Gemodificeerde Rackett-vergelijking met een Z_RA per zuur, gemiddelde afwijking 0,20 %. De
  Z_RA-waarden zelf zijn niet opgehaald.
- Noureddini e.a., *Densities of Vegetable Oils and Fatty Acids* (University of Nebraska, digitale
  bibliotheek). Correlatieconstanten met een expliciet bereik van **24 °C of het smeltpunt tot
  110 °C**. Geautomatiseerd ophalen wordt geblokkeerd door een Cloudflare-controle; het stuk
  staat wel vrij toegankelijk in de digitale bibliotheek van de universiteit en is **met de
  hand gewoon te openen**. Ook hier geldt dus: niet onvindbaar, wel niet opgehaald.

Dat tweede bereik is het punt: de bodem van deze kolom staat op **230 tot 250 °C**, oftewel 120 tot
140 K boven het gemeten venster. **Ook als de tabel wordt opgehaald, sluit zij dit punt niet** — zij
verplaatst de extrapolatie alleen van een rond getal naar een correlatie. Het model rekent met
een ordegetal van toen. Het model rekent nu met een lijn door metingen tot 200 °C, daarboven geëxtrapoleerd (hoofdstuk 4).

**Gevoeligheid:** klein en van één soort. De dichtheid is een zuivere **schaalfactor op de
verblijftijd** en daarmee op de afbraakdosis: ±10 % erin is ±10 % eruit. Elke VERHOUDING is er
ongevoelig voor, want zij schaalt teller en noemer gelijk — de uitspraken "de afbraak zit in de
koudste kolom" en "de film haalt de sump niet in" staan er los van. Wat er wél aan hangt is het
gequoteerde getal van 75 minuten verblijftijd in de sump van K-01.

**Wat het zou sluiten:** een dichtheidsmeting van vloeibaar vetzuur tussen 200 en 260 °C. Die is in
de open literatuur niet gevonden; het gangbare meetbereik stopt rond 110 °C omdat vetzuren daarboven
beginnen af te breken — dezelfde reden waarom deze kolom onder vacuüm draait.

---

## 25. Meetruis en verstoringen: een ontwerpkeuze, geen meting

Staat in `src/physics/ruis.js`. Vierde doorloop.

**Waarom.** Een draaiende fabriek geeft nooit een kaarsrechte lijn. Zonder ruis kan de
deeltaakoefening trendlezen niet werken: signaal van ruis onderscheiden ís de vaardigheid.

**Wat dit hoofdstuk NIET is.** Geen van de getallen hieronder komt uit een bron in de zin van de
stofdata: het zijn ONTWERPKEUZES voor een oefenmodel, gekozen bij een meetprincipe dat hier
expliciet wordt aangenomen. Een leveranciersspecificatie noemt de *nauwkeurigheid* van een meter;
de *ruis* die op een trend te zien is, is daaraan verwant maar niet hetzelfde. Wie een meetprincipe
anders aanneemt, kiest een andere grootte - en hoort dat hier te veranderen, niet in de code.

**Twee soorten, strikt gescheiden.**

- **Meetruis** zit op de METING en niet in het proces. Hij gaat niet door het model; hij komt er
  pas bij op het scherm (trend en schema), via één leesfunctie, zodat schema en trend hetzelfde
  getal tonen. Vorm: normaal verdeeld, afgekapt op drie standaardafwijkingen, per seconde opnieuw
  getrokken uit een vaste seed. Een meting die op vijf sigma naast zit, is in een echte installatie
  een defecte opnemer en geen ruis.
- **Verstoringen** zitten in het PROCES en gaan wél door het model: het voedingsdebiet en het
  pitchgehalte van de voeding schommelen langzaam. Zo blijft de afbraakdosis een uitkomst van de
  fysica en geen toevalswandeling.

### 25.1 Meetruis per meting

| Meting | Meetprincipe (aangenomen) | Ruis (1σ) | Waarom |
|---|---|---|---|
| Topdruk, bodemdruk K-01 en K-02 | absolutedrukopnemer met capacitief membraan | 0,1 mbar | Een halve procent op ongeveer 20 mbar: de onrust van een vacuümopnemer in bedrijf. |
| Bodemtemperatuur K-01 en K-02 | Pt100 in een dompelbuis | 0,2 K | De dompelbuis dempt de snelle schommelingen van de kokende vloeistof. |
| Bodemniveau K-01 en K-02 | drukverschil over de bodem | 1 procentpunt | Een kokende bodem onder vacuüm borrelt en schuimt; de dichtheid boven de meetpunten wisselt. |
| Niveau refluxvat V-02 | drukverschil over het vat | 0,5 procentpunt | Het refluxvat kookt niet. |
| Voeding, destillaat K-01, C16- en C18-product | meetflens (drukverschil over een meetopening) | 1 % van de meetwaarde | Een meetflens in een hete, grote vloeistofstroom. De halve procent van een coriolismeter zou hier te optimistisch zijn. |
| Pitch uit K-01 | coriolismeter | 0,5 % van de meetwaarde | Pitch is heet, stroperig en de stroom is klein: te weinig drukverschil en een te lage Reynolds voor een meetflens. Een coriolismeter is daar de gebruikelijke keuze. |

**Zonder meetruis** blijven: berekende grootheden (reboilerduty, dampstroom, refluxverhouding, het
mediumplafond), de analyses (pitch in de bodem, lichte sleutel in het C18-product - die hebben hun
eigen vertraging al) en de opgetelde pitch in K-02. Ook de grenswaarden en de herkenning van het
eerste signaal bij een storingsscenario lezen de modelwaarde, niet de gemeten waarde: anders zou
een alarm op ruis gaan knipperen. Dat is een vereenvoudiging; een echt alarmsysteem heeft daar een
filter of een hysterese voor.

### 25.2 Verstoringen op de voeding

| Verstoring | Grootte (1σ) | Tijdschaal | Waarom |
|---|---|---|---|
| Voedingsdebiet | 0,5 % | 23, 71 en 187 minuten | De voeding staat op debietregeling vanuit een buffertank; wat de regeling doorlaat, schommelt rond het setpoint. |
| Pitchgehalte van de voeding | 0,05 massaprocentpunt op 2,00 | 97, 283 en 661 minuten | De vetsplitsing levert niet elk uur hetzelfde; de buffertank dempt het. |

Elke verstoring is een som van drie sinussen met perioden die geen veelvoud van elkaar zijn, met
fasen uit de seed. Dat is begrensd (nooit meer dan anderhalve procent op het debiet) en
herhaalbaar. **Alleen het pitchgehalte** van de samenstelling kan bewegen: het oefenmodel volgt van
de voeding alleen de pitch dynamisch (de pitchbalans van de bodem van K-01). Het evenwicht rekent
met de vaste generieke voeding. Een schommeling in de C16/C18-verhouding zit er dus niet in.

### 25.3 De massabalans op meetwaarden

Op het schema staat het balansverschil als eigen grootheid:

    verschil = voeding − (pitch + C16 + C18 + verlies) − ophoping in de vaten   [kg/h]

**Onafhankelijk (vijfde doorloop).** De eerste versie van deze balans liet de ophoping weg en
rekende met het destillaat van K-01 - dat zelf als voeding min bodemstroom werd berekend. Hij kon
de massafout van K-01 daardoor niet zien: hij hergebruikte de grootheid die de fout bevatte. Nu komt
de ophoping uit de verandering van de inhoud van de bodems, het refluxvat en de leiding naar K-02
over de tijdstap (`massabalansStap()` in simulatie.js), en de stromen uit de metingen. Zonder
meetruis sluit hij exact; bij een kunstmatig massalek valt hij om (de testsuite). In
een echte installatie komt de ophoping uit de niveaumeters, gefilterd over minuten; hier staat zij
zonder meetruis, want de afgeleide van een ruisend niveau over tien seconden is onbruikbaar.
"Verlies" is damp die in een haperende condensor niet condenseert en naar het vacuüm gaat.

Een balans op meetwaarden sluit nooit precies. De vier metingen zijn onafhankelijk, dus tellen
hun varianties op. Bij ontwerp:

    σ = wortel(100² + 2² + 43,7² + 52,3²) ≈ 121 kg/h

De **ruisband** is vier keer die standaardafwijking, ongeveer ± 480 kg/h: een verschil uit ruis
alleen valt er ongeveer een keer op de zestienduizend monsters buiten. De testsuite
eist dat het balansverschil in twaalf uur rust bij élk monster binnen de band blijft.

**Wat dat betekent voor de operator.** Eén monster vangt alleen een verlies dat groter is dan de
band - bij deze installatie een procent of vijf van de voeding. Een kleiner verlies zie je niet
per monster maar wel in het gemiddelde: een uur aan monsters middelt de ruis tot ongeveer een
tiende. Daarom is een verschil dat LANGZAAM OPLOOPT het signaal - een lek, een driftende meting, of
een niveau dat wegloopt - en een verschil dat heen en weer springt niet.

---

## 26. Het bedieningsmodel (model 2)

Hoofdstuk 26 hoort bij het bedieningsmodel, model 2. Hier staan de paragrafen waar model 2 op het scherm naar verwijst, onder
hun eigen nummer, en de twee stukken die model 1 eruit gebruikt: de inhoud van refluxvat V-02 met de afstemming van zijn
niveaukring (26.29) en de meetkunde waarmee de vulling van een liggend vat getekend wordt (26.34). Een nummer dat ontbreekt
(zoals 26.3, 26.10, 26.15 en 26.24), hoort bij het bouwen van het model en staat alleen in de volledige bronnenlijst.

Elke regel van model 2 heeft een van de zes soorten van model 1: natuurwet, definitie, afgeleide methode, correlatie,
regelalgoritme of modelaanname. Een regelalgoritme is hier meestal een regel van deze installatie: een beveiliging, een grens
of wat er na een trip gebeurt. Daar hoort geen publicatie bij; elders kan het anders zijn ingesteld.

### 26.1 De klepvergelijking (`WET_KLEPVERGELIJKING`)

**Vorm:** Q = Kv · f(h) · √(Δp / (ρ/ρ₀)), met Q in m³/h, Δp in bar, Kv de doorlaatcoëfficiënt van de
volle klep (water in m³/h bij 1 bar drukval) en ρ₀ de dichtheid van water.

**Herkomst: vorm uit IEC 60534-2-1, *Industrial-process control valves - Part 2-1: Flow capacity - Sizing equations for fluid flow under installed conditions*, 2e uitgave, 2011 (de uitgave nagezocht in de webwinkel van IEC), primaire tekst niet ingezien, clausulenummers niet
geverifieerd.** De vorm is bevestigd door meerdere secundaire bronnen (klepdimensioneringsgidsen en
rekenmodules die naar IEC 60534-2-1 verwijzen). De voorbeeld-pdf van de norm was niet leesbaar; de
constante N₁ en het vergelijkingsnummer zijn daarom niet nagezocht. Voor deze
trainingsmodule telt de vorm: wortel uit de drukval, omgekeerd met de wortel van de dichtheid.

**ρ₀ = 1000 kg/m³**: water bij 15 °C als referentie van Kv, afgerond (werkelijk 999,1 kg/m³).

**Geldigheid:** Newtonse vloeistof, turbulent, niet verstikt, geen flashing of cavitatie. **Niet
gecontroleerd**: verstikte stroming en cavitatie in de klep.

### 26.2 De inherente karakteristiek (`WET_KLEPKARAKTERISTIEK`)

**Vorm:** lineair f = h; gelijkprocentueel f = R^(h−1), met h de relatieve slag en R het
regelbereik. **Herkomst: de twee vormen uit IEC 60534-2-4, *Industrial-process control valves - Part 2-4: Flow capacity - Inherent flow characteristics and rangeability*, 2e uitgave, 2009 (de uitgave nagezocht in de webwinkel van IEC), primaire tekst niet ingezien.** De
theoretische gelijkprocentuele vorm komt bij h = 0 op 1/R uit; een echte klep sluit, dus in de code
geeft h = 0 een doorlaat van nul.

**Vlak bij de zitting: recht naar nul.** Een echte klep wijkt vlak bij de zitting af van de
ideale kromme; zij loopt daar niet op een vijftigste uit, maar gaat dicht. De bodemafnamekleppen
van K-01 en K-02 zijn daarom gelijkprocentueel vanaf een zittingslag h_z en lopen daaronder recht
dicht:

    f = R^((h − h_z)/(1 − h_z) − 1)    voor h ≥ h_z
    f = h / (h_z · R)                   voor h < h_z

De kromme boven h_z is gelijkprocentueel (elke gelijke stap geeft hetzelfde percentage erbij) en
loopt van f = 1/R bij h_z tot f = 1 helemaal open. **Het regelbereik blijft zo R = Q_max/Q_min:**
de verhouding tussen de grootste en de kleinste doorlaat waarop de kromme geldt, bij een vaste
drukval (26.14). Dat is de reden voor deze vorm en niet voor f = R^(h−1) met een rechte lijn eronder:
dan zou de kromme pas boven 1/R beginnen en het regelbereik kleiner zijn dan R. Onder h_z loopt de
doorlaat doorlopend naar nul, met een helling die ongeveer vijf keer zo steil is als die van de
kromme net boven h_z: regelen gaat daar grof. Zonder zittingslag (h_z = 0) is de vorm f = R^(h−1),
met een sprong bij de zitting; de vacuümklep (26.19) gebruikt die.

**h_z = 0,05 (5 procent slag) is een ontwerpaanname zonder bron** (`SLAG_ZITTING` in `klep.js`,
een aanname van het model). Dat een echte klep vlak bij de zitting van de kromme afwijkt, is algemeen; waar dat
begint, hangt van de klep en zijn aandrijving af, en een gegevensblad is er niet. Vijf procent is
gekozen als "een kleine klepstand". **Gevoeligheid:** de klep is gedimensioneerd op het ontwerppunt,
half open, dus de Kv volgt mee: met h_z = 0,05 wordt hij ongeveer 11 procent groter dan zonder
(3,40 tegen 3,07 m³/h bij K-01), en daarmee schuiven de kleinste regelbare stroom en het regelbereik
in de installatie (26.14) en de afstemming (Kp 0,958 tegen 1,005). Het ontwerppunt zelf verandert
niet.

**R = 50 is een ontwerpaanname**, een typische waarde voor een gelijkprocentuele klep, geen
gegeven van een bestaande klep.

### 26.5 De laag-laagbeveiliging (LSLL) en haar meting (`WET_EIGEN_METING`, regelalgoritme)

**De LSLL heeft een eigen meting**, `K01.LSLL.PV`, van een tweede
transmitter op dezelfde bodem: dezelfde sigma als de meting van de regelkring, maar een eigen
ruistrekking (zaadwaarde `seed|LSLL`). De beveiliging leest alleen die; wijzen de twee metingen
iets anders aan, dan volgt zij de hare. Een regelalgoritme van deze installatie, geen bron: de reden is
**onafhankelijkheid, niet redundantie** - een storing in de meting van de kring mag niet tegelijk de
regeling en haar beveiliging wegnemen.

Valt de meting van de regelkring uit, dan trept de beveiliging niet. Valt haar eigen meting uit,
dan trept zij wel.

**De LSLL grijpt aan op P-01** (`WET_LSLL_OP_POMP`, regelalgoritme):
een trip schakelt de pomp af en stuurt de regelklep niet dicht. Een ontwerpkeuze; zij berust op
natuurkunde en algemene praktijk, niet op gegevens van een bestaande installatie. De redenering:
het probleem bij een laag niveau zit
aan de zuigzijde (NPSH, 26.11); de klep staat in de pers en kan daar niets aan veranderen, en een
dichte persklep laat de pomp zonder doorstroming draaien, wat bij een vloeistof op kookpunt damp in
het pomphuis geeft. Een start wordt geweigerd zolang de trip staat, en na een reset start de pomp
niet vanzelf: een pomp die zichzelf herstart na een beveiligingsingreep zou leren dat een trip vanzelf
overgaat. Een trip zet de regelkring naar HAND, met de klep op de stand van het moment van trippen
(26.9). De reset staat op de faceplate van P-01: je reset een beveiliging waar zij aangrijpt, en je
diagnosticeert waar zij meet.

**Een meting buiten haar bereik: de richting telt** (`WET_LSLL_BUITEN_BEREIK`, regelalgoritme). Een
transmitter die buiten zijn bereik raakt, blijft op de grens staan en meldt `BUITEN_BEREIK`. Voor de
laag-laagbeveiliging:
- **slecht of nooit geschreven:** geen informatie, dus trippen;
- **buiten bereik aan de bovenkant:** zeker niet laag, dus niet trippen;
- **buiten bereik aan de onderkant:** zeker wel laag, dus trippen.

Een verzadigde transmitter zegt "het niveau is minstens de bovenkant van mijn bereik"; voor een
laag-laagbeveiliging is dat het meest eenduidige bericht dat er is. *De reden:* een beveiliging die
trept op elke meting die niet goed of onzeker is, trept ook op een volle bodem. De beveiliging die
te weinig vloeistof moet voorkomen, zet dan de pomp uit terwijl de bodem volloopt; dat maakt het
erger in plaats van veiliger. In de code:
`niveauVoorLSLL()` geeft de grens waarop de transmitter staat, en de gewone vergelijking met de
tripgrens doet de rest; dat klopt zolang de tripgrens binnen het meetbereik ligt.

**Geldigheid, en wat de regel niet ziet.** Een keuze van deze installatie; elders kan een meting
buiten bereik altijd als storing tellen. Een transmitter die kapot is en bovenin blijft hangen,
ziet er voor deze regel net zo uit als een volle bodem. Met twee onafhankelijke metingen is dat te
onderscheiden: lopen zij uiteen, dan is er een stuk.
**De regelkring behandelt dezelfde meting wel als onbruikbaar en valt naar HAND, en dat is geen
inconsequentie** (regelalgoritme). Een beveiliging stelt een
ja-nee-vraag: is het niveau te laag? Op die vraag geeft een eenzijdige grens ("minstens de bovenkant
van het bereik") een eenduidig antwoord. Een regelaar heeft een getal nodig om een afwijking mee
uit te rekenen, en een eenzijdige grens is geen getal. Dezelfde meting is dus bruikbaar voor de
beveiliging en onbruikbaar voor de regeling. Het staat in de uitleg bij beide regels
(`WET_LSLL_BUITEN_BEREIK` en `WET_TERUGVAL`).

### 26.6 Stootvrij omschakelen (`regelaar.js`)

Geen natuurwet en geen nieuwe correlatie: de omkering van de PI-regelaar uit model 1
(`CORRELATIE_REGELAAR`, `piStap` in `dynamics.js`), die zelf niet opnieuw wordt geschreven.

**Het setpoint volgt in HAND de meting, tot de operator er zelf een zet** (`WET_SP_VOLGEN`,
regelalgoritme). De regelmodule zet het setpoint in HAND elke scan gelijk aan de meting, zodat de
kring bij het terugschakelen naar AUTO zonder afwijking begint. Daarbij:
- zet de operator in HAND zelf een setpoint, dan houdt het volgen op en blijft zijn setpoint staan.
  Zo kan hij het alvast goedzetten voor het terugschakelen;
- komt de kring opnieuw in HAND (door de operator, of doordat zij terugvalt), dan volgt het
  setpoint weer de meting;
- de regellaag meldt de toestand in een tag (`K01.LIC.SP.STAND`: VOLGT of VAST), en de faceplate
  zegt haar in tekst naast het setpoint, met in HAND erbij wat zij betekent voor het terugschakelen.

De klep springt in geen van beide gevallen (het stootvrije omschakelen hierboven); met een gezet
setpoint gaat de kring na het terugschakelen wel naar dat setpoint regelen. Een keuze van deze
installatie: elders kan het setpoint in HAND altijd volgen, of nooit.

### 26.8 De besturingslaag: LSLL-grenzen en het gedrag vlak bij dicht

**De grenzen van de LSLL (`GRENZEN_LSLL`): trip onder 10 procent, reset pas boven 20 procent.**
Ontwerpkeuzes, geen bron en geen gegevens van een bestaande installatie. De tripgrens ligt ver
genoeg boven leeg dat de klep dicht is voordat de pomp zuigt. De resetgrens ligt er bewust een
stuk boven: zo kan een reset niet worden gegeven terwijl het niveau nog op de tripgrens hangt en de
beveiliging bij de eerste golf opnieuw valt. Waar de grenzen in een echte installatie liggen, hangt
af van de pomp (NPSH, de vloeistofkolom boven de zuigmond) en de bodemgeometrie.

**De afstemming van de regelmodule komt uit model 1** (`afstemmingUitModel1()`): dezelfde Ti
en dezelfde lusversterking, omgerekend met de versterking van de klep in de installatie
(`geinstalleerdeVersterking_kgs`, 26.13): met de pompkromme en de leiding beweegt de drukval over
de klep mee, en dat is wat de regelaar ziet. Geen nieuwe getallen.

**Vlak bij dicht loopt de klep onder de zittingslag recht dicht** (26.2). Zonder zittingslag gaf de
gelijkprocentuele karakteristiek f = R^(h−1) bij h = 0 de waarde nul, maar net daarboven al 1/R, bij
R = 50 dus 2 procent van de Kv; in de kring gaf dat bij een uitgang rond nul kleine stoten. Met de
zittingslag is er geen stoot: de stroom groeit vanaf nul met de uitgang.

### 26.9 Een besturingsblok in storing (`WET_BLOKSTORING`, regelalgoritme)

Geen natuurwet en geen bron: een **regelalgoritme** van deze installatie. Een andere
installatie kan het anders hebben ingeregeld, en de uitleg op het scherm zegt dat ook.

**De regel.** Een scan van de regeling heeft vaste stappen: de beveiliging
bepaalt de tripstand, het besluit over P-01 volgt daaruit (`pompOpdracht` in `beveiliging.js`),
de regelmodule rekent haar gewenste uitgang, en een aparte uitgangskeuze (`kiesUitgang` in
`regelaar.js`) bepaalt wat de klep krijgt. Die keuze staat buiten de blokken en draait altijd:
- de stand van de vorige scan als de regelmodule in storing staat;
- anders de gewenste uitgang.

Een trip raakt de klep niet: de beveiliging zet de pomp uit (26.5, 26.11). Stopt de pomp, om
welke reden ook, dan valt de regelkring naar HAND met de uitgang vast (`WET_TERUGVAL`): een kring die
zijn werk niet kan doen, valt terug. AUTO wordt alleen geweigerd als het onmogelijk is (geen
bruikbare meting), niet als het zinloos is (pomp stil). Waarom HAND, in volgorde: STORING (zonder
werkende regelmodule kan niets), METING (zonder bruikbare meting is AUTO onmogelijk), POMP (AUTO is
mogelijk maar zinloos), OPERATOR.

Een blok dat gooit of een onbruikbare uitkomst geeft, staat in storing, wordt niet meer uitgevoerd
en blijft in storing tot de operator reset. Een reset maakt het blok opnieuw aan: de regelmodule in
HAND op de vastgehouden stand, de beveiliging in trip. Of de oorzaak weg is, valt vooraf niet te
zien; gooit het blok opnieuw, dan staat het meteen weer in storing.

**Waarom zo.**
- *Vasthouden tot een reset*, omdat een blok dat zichzelf herstelt een fout verbergt die af en toe
  optreedt. Het is ook hetzelfde gedrag als de LSLL: wat de cursist daar leert, geldt hier ook.
- *Een uitgangskeuze buiten de blokken*, omdat er precies een plek mag zijn waar de uitgang naar de
  klep gekozen wordt. Gooit de beveiliging, dan is haar tripstand SLECHT, en die telt als getript:
  twijfel beschermt wat onomkeerbaar is (26.5, 26.8).
- *De laatste stand en geen vaste veilige stand* voor de regelmodule, omdat een niveaukring die
  stilvalt traag en zichtbaar wegloopt, en de LSLL het onomkeerbare deel al afdekt. Bij een kring
  zonder zo'n beveiliging kan een vaste stand de betere keuze zijn.
- *Het alarm noemt de gelatchte storing als oorzaak*, niet de gooi zelf. Zo blijft de regel
  kloppen dat een alarm verdwijnt als zijn oorzaak weg is, en is er geen dode kring zonder alarm.
- *Waarom HAND*, in tekst (`K01.LIC.HANDREDEN`): de reden die als eerste weg moet voordat AUTO
  weer kan. Eerst STORING (het blok draait niet), dan METING (AUTO wordt geweigerd), dan OPERATOR.

**Wat het vangnet niet dekt.** Alleen de twee blokken. Een fout in de tag-database, de klokken, de
veldlaag of het procesmodel zet de hele simulatie stil.

### 26.11 P-01, de terugslagklep en de minimumdebietleiding

**Een ontwerpkeuze.** Zij berust op natuurkunde en algemene praktijk, niet op gegevens van een
bestaande installatie.

**Twee natuurwetten** liggen eronder, zonder getallen:
- `WET_NPSH`: bij een vloeistof op haar kookpunt vallen de druk boven de vloeistof en haar eigen
  dampspanning tegen elkaar weg; wat overblijft is de vloeistofhoogte boven de pomp min de weerstand
  van de zuigleiding. Daarom weegt het bodemniveau hier zwaarder dan op een vat onder atmosferische
  druk. De pomphoogte is een ontwerpkeuze, de zuigleiding en de NPSHr zijn aannames, en het model
  rekent de marge uit (26.27).
- `WET_DOODDRUKKEN`: een pomp zonder doorstroming voert zijn asvermogen af in de vloeistof die hij
  rondslaat; bij kookpunt verdampt die in het pomphuis. Daarom is een dichte persklep met een
  draaiende pomp niet neutraal maar schadelijk.

**De minimumdebietleiding** (`WET_MINIMUMDEBIET`, modelaanname): een vaste restrictie van de pers
terug naar de kolombodem, altijd open. Een pomp heeft een eigen minimale doorstroming nodig; dat is
een eigenschap van de pomp, geen procesgrootheid. Omdat het model geen apart pompgegeven kent, nemen
we daarvoor **20 procent van de ontwerpstroom** (`MINIMUMDEBIET_DEEL`). De Kv van de restrictie volgt
daaruit langs de klepvergelijking, bij de drukopbouw van de pomp in het ontwerppunt: de restrictie
loopt van de pers terug naar de bodem waaruit de pomp zuigt, dus over haar staat de hele drukopbouw
(26.13). Een vaste
restrictie en geen geregelde klep: er is niets dat kan falen, en de les is zichtbaar. Wat erdoor
stroomt, komt terug in de bodem: voor de massabalans van de bodem maakt het niets uit.

**De terugslagklep zit na de aftakking van de minimumdebietleiding en vóór de regelklep.** Dat is
niet vanzelfsprekend. Zat zij vóór de aftakking, dan kon bij een stilstaande pomp de opslag, die op
atmosferische druk staat, via de regelklep en de altijd open minimumdebietleiding terugstromen naar
de bodem onder vacuüm. Met deze volgorde houdt de terugslagklep dat tegen, en staat het pomphuis bij
stilstand via de minimumdebietleiding alleen in verbinding met de bodem zelf. In het model: draait
P-01 niet, dan is de stroom door de regelklep nul.

**P-01 heeft een toestand**, draait of staat stil (`K01.P01.STAND`), en volgt een opdracht van de
besturing (`K01.P01.OPDRACHT`). Zonder goede opdracht houdt hij zijn stand. Bij het opstarten draait
hij: het ontwerppunt van model 1. Zijn pompkromme staat in 26.13.

### 26.12 De scancyclus van de beveiliging (`WET_SCAN_BEVEILIGING`, regelalgoritme)

**Een regelalgoritme, geen natuurwet.** De beveiliging (de LSLL en het
besluit over P-01) scant op haar eigen klok, los van de regelkring. Bij gelijktijdigheid is de
volgorde vast: eerst de rekenstap van het proces, dan de beveiliging, dan de regelkring. Het besluit
over de pomp valt in de scan van de beveiliging; de regelkring gaat bij haar eerstvolgende scan naar
HAND als de pomp sinds haar vorige scan is gestopt, ook als hij inmiddels weer draait.

**Standaard even snel als de regelkring: 1 s.** De instelling staat in de ingeklapte groep van het
simulatorvlak, met de vermelding dat het geen bedieningsknop is. Bereik 0,1 tot 10 s, net als de
scancyclus van de besturing.

**Waarom niet standaard sneller.** De realistische ordening is een beveiliging die sneller scant dan
de regelkring. Maar die wordt pas zichtbaar als de rekenstap van het procesmodel mee omlaag gaat: de
meting verandert niet vaker dan het proces rekent, dus een scan van 0,1 s op een rekenstap van 1 s
ziet niets wat een scan van 1 s niet ook ziet. Een kleinere rekenstap kost rekenwerk dat bij hoge
simulatiesnelheid (60x, 600x) niet meer rondkomt. Daarom is de standaard gelijk, en kan wie het
verschijnsel wil zien beide omlaag zetten.

**Sneller dan de rekenstap: toegestaan en zichtbaar.** Staat de scancyclus van de beveiliging onder
de rekenstap, dan zegt het vlak ernaast: "sneller dan de rekenstap - ziet niets extra's". Zonder die
melding ziet iemand geen verschil en denkt hij dat de instelling kapot is. Dezelfde regel als
`WET_TERUGVAL`: het onmogelijke weiger je, het zinloze laat je toe en maak je zichtbaar.

**Geldigheid.** Een keuze van deze installatie. In het veld kan een beveiliging een eigen, nog
snellere besturing hebben, of in hardware zitten; dan scant zij niet in cycli van deze orde.

### 26.13 De pompkromme van P-01, de leidingweerstand en het werkpunt

**Geen gegevens van een bestaande pomp of leiding.** Alles in deze paragraaf is een ontwerpkeuze op
natuurkunde en algemene praktijk. Er is
geen gegevensblad en geen leveranciersdata gebruikt, en er is niets verzonnen dat daarop lijkt.

**Het net.** P-01 zuigt uit de bodem van K-01. Achter de pomp splitst de stroom: door de
terugslagklep, de persleiding en de regelklep LV naar de opslag, en door de vaste restrictie terug
naar de bodem (26.11). Beide takken beginnen op de pers.

#### Het werkpunt (`WET_WERKPUNT`, afgeleide methode)

De stroom stelt zich in waar de pomp precies de druk opbouwt die het systeem bij die stroom vraagt:

    Δp_pomp(Q_klep + Q_min) = Δp_stat + Δp_leiding(Q_klep) + Δp_klep(Q_klep) = Δp_restrictie(Q_min)

Dat is behoud van massa (door de pomp gaat wat er door de twee takken samen gaat) en van energie
(de drukopbouw wordt in elke tak precies verbruikt). **Herkomst: op boekniveau: F. M. White en H. Xue, *Fluid Mechanics*, 9e druk, McGraw-Hill, 2021, en R. Darby en R. P. Chhabra, *Chemical Engineering Fluid Mechanics*, 3e druk, CRC Press, 2017. Uitgave en jaar zijn nagegaan in de catalogusgegevens van de uitgever en van bibliotheken (WorldCat, de British Library); de boeken zelf zijn niet ingezien, en een hoofdstuk of pagina wordt daarom niet genoemd.** Het geldt voor elke pomp in elk leidingnet, en is daarom als afgeleide methode
gemarkeerd. `werkpuntP01()` lost het op door halveren: bij een hogere drukopbouw nemen beide takken
meer af en levert de pomp minder, dus er is precies één snijpunt.

**Geldigheid.** Stationair: de stroom staat direct op het nieuwe werkpunt, zonder de traagheid van
de vloeistof in de leiding. Hoogteverschillen en de weerstand van de zuigleiding zijn in het werkpunt niet
meegenomen; het model kent geen meetkunde (26.10). De hoogte boven de pomp en de weerstand van de zuigleiding
tellen alleen in de NPSH (26.27), aan de perszijde kent het model geen hoogte. **Niet gecontroleerd:** cavitatie of flashing in
de klep, en de NPSH-marge van de pomp.

#### De leidingweerstand (`WET_LEIDINGWEERSTAND`, afgeleide methode)

**Vorm:** Δp_leiding = c · Q². De vorm van Darcy-Weisbach (Δp evenredig met ρv²/2) bij een vaste
wrijvingsfactor. **Herkomst: de vorm van Darcy en Weisbach, op boekniveau: F. M. White en H. Xue, *Fluid Mechanics*, 9e druk, McGraw-Hill, 2021, en R. Darby en R. P. Chhabra, *Chemical Engineering Fluid Mechanics*, 3e druk, CRC Press, 2017. Uitgave en jaar zijn nagegaan in de catalogusgegevens van de uitgever en van bibliotheken (WorldCat, de British Library); de boeken zelf zijn niet ingezien, en een hoofdstuk of pagina wordt daarom niet genoemd.** Geldig voor turbulente
stroming; in werkelijkheid hangt de wrijvingsfactor zwak van de stroom af, en bij een heel kleine
stroom is de stroming laminair en de drukval evenredig met de stroom. Dat onderscheid maakt het
model niet.

**De waarde van c is een ontwerpkeuze:** de leiding, met de terugslagklep erin, kost **1 bar bij
de grootste bodemstroom** (zie hieronder). Het model kent geen lengte en geen diameter. Bij de
ontwerpstroom is dat een kwart daarvan, 0,25 bar. Waarom een bar en geen halve: de pomp moet
tussen het ontwerppunt en zijn capaciteit druk verliezen, en hoe minder de leiding daar opneemt,
hoe steiler zijn kromme moet zijn. Met een halve bar kwam de nullastdruk de helft boven de druk in
het maximumpunt te liggen; met een bar is dat een achtste.

**Het statische drukverschil is een ontwerpkeuze: 1 bar, afgerond.** De opslag staat op
atmosferische druk, de bodem van K-01 onder diep vacuüm. Hoogteverschillen aan de perszijde zijn niet bekend
en niet meegenomen. Het getal beweegt niet mee met de kolomdruk: een vacuümlek van tientallen millibar
verschuift het werkpunt verwaarloosbaar, en dat is niet gemodelleerd.

#### De pompkromme (`WET_POMPKROMME`, modelaanname)

**Vorm:** Δp_pomp = Δp₀ − k · Q². Een parabool: de gebruikelijke eenvoudige benadering van de
kromme van een centrifugaalpomp op vast toerental. **Een eigenschap van het apparaat, geen wet, en
hier een generieke keuze op algemene praktijk; geen bron.** Welke kromme een pomp werkelijk heeft,
staat op zijn gegevensblad.

De twee getallen volgen uit de twee punten die de pomp moet halen (berekend, niet gekozen):

1. **het ontwerppunt:** de ontwerpbodemstroom van model 1 met de regelklep half open, plus het
   minimumdebiet;
2. **het maximumpunt:** de capaciteit, twee keer de ontwerpbodemstroom met de regelklep helemaal
   open, plus het minimumdebiet bij die druk.

Dat geeft een nullastdruk van ongeveer 2,33 bar, en in het maximumpunt 2,07 bar: de nullastdruk
ligt ongeveer 13 procent boven de druk in het maximumpunt. Voor een centrifugaalpomp is een
verhouding van die orde gewoon; dat is een vuistregel uit algemene praktijk, niet nagezocht in een
bron.

**Geldigheidsbereik:** van geen stroom tot 1,2 keer de stroom in het maximumpunt. Daarbuiten is de
parabool een extrapolatie; `werkpuntP01()` meldt dat in `binnenBereik`. In het ontwerp van deze
installatie komt de pomp daar niet: met de klep helemaal open staat hij precies in het maximumpunt.

**De capaciteit: 800 kg/h, twee keer de ontwerpbodemstroom** (`ONTWERP_P01.B_max_verhouding`). Een
eigenschap van het apparaat, met een eigen reden:
- het ontwerppunt ligt dan op de helft van de capaciteit, waar een pomp hoort te draaien, en niet
  ver links op de kromme tegen het minimumdebietgebied aan;
- het minimumdebiet van 80 kg/h (26.11) is dan een tiende van de capaciteit;
- anderhalf tot twee keer de normale stroom is voor een bodempomp gebruikelijk. Algemene praktijk;
  geen bron.

Door de pomp zelf gaat in het maximumpunt de bodemstroom plus het minimumdebiet, samen ongeveer
877 kg/h.

**Wat de capaciteit NIET is: de factor vier uit model 1.** De niveauregelaar van model 1 begrenst
zijn uitgang op vier keer de ontwerpbodemstroom (`niveauregeling` in `simulatie.js`). Dat is een
regelbereik, geen pompgegeven: het staat nergens onderbouwd, model 1 kent geen pomp, en in HAND
geldt de grens niet eens. Een aanname over de regeling is geen eigenschap van het apparaat.

**Het gevolg, en dat is bedoeld:** de twee modellen reageren verschillend op een grote storing.
Model 1 vraagt bij tien procent meer voeding stationair ongeveer 1400 kg/h bodemstroom, en krijgt
die, want daar is de afvoer geen apparaat. In model 2 loopt de klep helemaal open, levert P-01 zijn
800 kg/h, en loopt de bodem vol: de opstuwingsketen van model 1 begint (20.16). Een voedingsstap
van twee procent (600 kg/h) vangt de pomp op; bij ongeveer vier procent zit hij aan zijn
capaciteit.

#### Wat eruit komt

Met P-01 in bedrijf, per klepstand (`werkpuntP01()`):

| Klepstand | Drukopbouw pomp [bar] | Drukval klep [bar] | Bodemstroom [kg/h] | Minimumdebiet [kg/h] |
|---|---|---|---|---|
| 0 % | 2,33 | 1,33 | 0 | 81,4 |
| 25 % | 2,31 | 1,27 | 161 | 81,0 |
| 50 % | 2,25 | 1,00 | 400 | 80,0 |
| 75 % | 2,13 | 0,38 | 691 | 77,8 |
| 100 % | 2,07 | 0,07 | 800 | 76,6 |

- **De drukval over de klep is een uitkomst.** Helemaal open staat er nog minder dan een tiende
  bar over; de rest nemen de leiding en de pomp. Bij een vaste bar zou de klep helemaal open
  ongeveer 8 keer de ontwerpstroom geven; in de installatie 2 keer.
- **De stroom verdeelt zich over de restrictie en de regelklep.** Over de restrictie staat de hele
  drukopbouw: bij een dichte regelklep gaat er iets meer doorheen dan in het ontwerppunt, bij een
  open iets minder. Het verschil is klein, omdat deze pompkromme vlak is.
- **Een zwakkere pomp geeft bij dezelfde klepstand minder stroom**, en de regelaar zet
  de klep dan verder open.
- **De regelaar ziet de klep in de installatie** (`geinstalleerdeVersterking_kgs()`). In het
  ontwerppunt is de versterking ongeveer 76 procent van die van de klep bij een vaste drukval; de
  afstemming uit model 1 wordt daarmee omgerekend (26.8). Verder open loopt het verschil op: de
  versterking is het grootst bij een klepstand van ongeveer 55 procent en neemt daarboven af,
  terwijl de klep op zichzelf tot het eind scherper wordt.

### 26.14 Het regelbereik van de klep: op het gegevensblad en in de installatie

**Het regelbereik van de klep zelf** (`WET_REGELBEREIK`, definitie). Een eigenschap van het
apparaat: R is de grootste gedeeld door de kleinste regelbare doorlaat, bij een vaste drukval. Voor
deze klep **50 op 1**, een typische waarde voor een gelijkprocentuele klep en geen gegeven van een
bestaande klep (26.2). De kleinste regelbare doorlaat is dus een vijftigste, 2 procent, van de volle
Kv. **Die 2 procent is doorlaat: geen klepstand en geen stroom.**

**Het regelbereik is het geldigheidsbereik van de karakteristiek**, zoals het temperatuurbereik dat
is van een Antoine-correlatie: voor een apparaatkarakteristiek geldt hetzelfde als voor stofdata.
`regelbereik()` geeft het; `binnenRegelbereik()` zegt of een klepstand
erbinnen valt. Onder de kleinste regelbare doorlaat geldt de gelijkprocentuele kromme niet; daar
loopt de klep in dit model recht dicht, onder de zittingslag (26.2). Het model extrapoleert de
formule dus niet onder haar bereik; het meldt dat de klep eronder staat (zie hieronder).

**Het regelbereik in de installatie** (`WET_REGELBEREIK_INSTALLATIE`, afgeleide methode). Uit de
klepvergelijking, twee keer toegepast:

    Q_max / Q_min = R · √(Δp_open / Δp_dicht)

De doorlaten verhouden zich als R; de drukvallen volgen uit het werkpunt (26.13), en zijn niet
gelijk. Met de klep op zijn kleinste regelbare doorlaat staat er 1,31 bar over, helemaal open nog
0,07 bar. Dat geeft (`regelbereikInInstallatie()`):

- kleinste regelbare stroom: **71,8 kg/h**; bij een vaste bar zou dat 62,7 kg/h zijn;
- grootste stroom: **800 kg/h**; bij een vaste bar zou dat 3135 kg/h zijn;
- het regelbereik in de installatie: **ongeveer 11 op 1**, tegen 50 op 1 voor de klep zelf.

**Waar het verschil vandaan komt:** vrijwel helemaal van de bovenkant. Staat de klep helemaal open,
dan nemen de leiding en de pomp bijna alle druk en blijft er voor de klep weinig over; de grootste
stroom valt daardoor bijna vier keer lager uit dan de klep alleen zou geven. Aan de onderkant gebeurt
het omgekeerde, maar zwak: bij een bijna dichte klep staat er iets meer dan een bar over, en is de
kleinste stroom een zevende hoger. Dat dit volgt uit de drukverdeling en niet uit de klep, maakt het
een afgeleide methode; het geldt voor elke klep in elk leidingnet. De getallen horen bij de pomp en de
leiding zoals zij ontworpen zijn: een zwakkere pomp schuift het hele bereik omlaag, bij vrijwel
dezelfde verhouding; meer leidingweerstand maakt de grootste stroom kleiner en het bereik nauwer.

**Zichtbaar maken, niet weigeren.** De faceplate van de
regelkring toont het regelbereik in de installatie. Staat de klep onder de zittingslag terwijl de
pomp draait, dicht of vlak bij de zitting, dan zegt het scherm dat hij onder zijn regelbereik
staat, met de kleinste regelbare stroom erbij: daaronder volgt de klep zijn kromme niet meer en
gaat regelen grof. Het veld schrijft de toestand in een tag (`K01.LV.BEREIK.STAND`). De grens in
klepstand is de zittingslag, en die is zonder bron (26.2).

**Bekende vereenvoudiging: bij de zitting loopt het model recht dicht.** Een echte klep verliest zijn
regeling vlak bij de zitting geleidelijk: er is lekkage langs de zitting, de klep beweegt daar
schoksgewijs (stick-slip), en er is een minimaal regelbaar debiet dat van de klep en de aandrijving
afhangt. In het model loopt de klep onder de zittingslag recht naar nul, zonder lekkage en zonder
stick-slip. Een echt zittingmodel vraagt gegevens van een klep die het model niet heeft; de
zittingslag is een waarde zonder bron (26.2).

### 26.16 Reboiler E-01 en de temperatuurkring van K-01 (`reboiler.js`)

**Er komt geen getal bij.** Alles wat de temperatuurkring nodig heeft, legt model 1 al vast, en
model 2 leest het daar:

| Wat | Waarde | Waar het staat | Stand van de bron |
|---|---|---|---|
| Verwarmingsmedium | thermische olie, aanvoer 280 °C | `energy.js`, hoofdstuk 11 en 16.2 | ontwerpkeuze, zie hieronder |
| Overcapaciteit van de reboiler | 1,25 | `simulatie.js`, `MODELAANNAMEN` | ontwerpmarge, zie hieronder |
| Ontwerpduty van E-01 | 841,6 kW | uitkomst van de energiebalans van model 1 | berekend |
| Versterking van de regelaar | 60 kW/K | `simulatie.js`, binnen `temperatuurregeling()` | afstemming, geen stofwaarde |
| Integratietijd | 1200 s | `simulatie.js`, `MODELAANNAMEN` | afstemming, geen stofwaarde |

- *De overcapaciteit (1,25)* is een ontwerpmarge, geen stofwaarde. Zij krijgt nooit een
  literatuurbron. Zij staat in het register van model 2 als modelaanname `WET_OVERCAPACITEIT`,
  met haar reden: zonder marge is het ontwerppunt precies het maximum en heeft de operator geen
  ruimte; met marge blijft fouling een tijd onzichtbaar.
- *De 280 °C* is een ontwerpkeuze, onder de temperatuur waarboven de olie zelf ontleedt. De
  redenering voor de waarde: de aanvoer moet genoeg boven de bodemtemperatuur liggen om warmte over
  te dragen (hier 42 K boven de bodem van K-01), en niet hoger dan nodig, want de buiswand volgt de
  olie. **De grens hoort bij een vloeistof, niet bij "thermische olie".** Er is geen algemene norm met
  een getal, en dat is zelf het vermelden waard: het blad hieronder zegt met zoveel woorden dat er
  wel normen zijn om de thermische stabiliteit te METEN, maar geen vastgestelde norm om er een
  aanbevolen bulktemperatuur uit af te leiden. DIN 4754 vraagt volgens hetzelfde blad van de
  leverancier dat hij de toelaatbare aanvoertemperatuur opgeeft waarbij de vloeistof minstens een
  jaar bruikbaar blijft.

  Een blad is gelezen (8 oktober 2026), als voorbeeld van hoe zo een grens eruitziet:

  | Type vloeistof | Aanbevolen bulktemperatuur | Hoogste bij langdurig gebruik | Hoogste filmtemperatuur |
  |---|---|---|---|
  | synthetisch, dibenzyltolueen | 325 °C | 350 °C (met duidelijk kortere levensduur) | 380 °C |

  Bron: een openbaar gegevensblad van een leverancier van warmteolie, voor een synthetische vloeistof op basis van
  dibenzyltolueen (zonder firmanaam, zoals bij elke leveranciersbron in deze lijst). Geldigheid: deze ene vloeistof, in een
  gesloten systeem met geforceerde circulatie in
  de vloeistoffase; de waarden zijn aanbevelingen van de leverancier op grond van zijn eigen
  stabiliteitsproeven, geen genormeerde grens. **Het model kiest geen product.** De tabel laat
  alleen zien dat 280 °C ruim onder zulke grenzen ligt (45 K onder deze bulktemperatuur, 100 K onder
  deze filmtemperatuur); een minerale olie heeft lagere grenzen, en daarvoor is geen blad gelezen.

  *Twee grenzen, en waar zij spelen.* De bulktemperatuur gaat over de olie als geheel. De
  filmtemperatuur gaat over de olie aan de wand waar zij wordt OPGEWARMD: de wand van de
  oliestoker, niet die van de reboiler (daar geeft de olie warmte af en is haar film kouder dan de
  bulk). Aan de vetzuurzijde is het omgekeerd: daar is de buiswand van de reboiler de hete wand, en
  die wandtemperatuur drijft de afbraak (hoofdstuk 9). In beide gevallen ontstaat de schade aan de
  wand waar een vloeistof wordt opgewarmd, niet in de bulk. **Dit model rekent geen van die twee
  wandtemperaturen uit** (12.2); daarom is droogvallen zo ernstig, en daarom is er geen trip op
  temperatuur (26.17).

**Stoom of olie.** Hoofdstuk 8.7 spreekt van "stoom op de reboiler". Model 1 legt het medium vast
als thermische olie, omdat stoom van 40 bar de bodem van K-01 niet aan de kook krijgt (hoofdstuk 11).
Model 2 volgt model 1: de klep zit in de thermische olie, en het scherm noemt geen stoom.

**De regelklep: `WET_REBOILERKLEP`, modelaanname.**

    Q = h · Q_max

De klep bepaalt welk deel van de beschikbare warmteoverdracht wordt benut. **Lineair, en de reden
is deze en geen andere:** in dit model is de duty recht evenredig met de klepstand, dus geeft een
lineaire klep over de hele slag dezelfde kringversterking. Het is GEEN algemene regel dat
temperatuurkleppen lineair zijn. In een echte warmtewisselaar vlakt de duty af bij een groot
oliedebiet, en juist daarom kiest men daar vaak een gelijkprocentuele klep, om die afvlakking te
compenseren. Dit model kent geen oliedebiet en dus geen afvlakking. In het ontwerppunt staat de
klep op 1 / 1,25 = 80 %.

**Het mediumplafond: `WET_MEDIUMPLAFOND`, natuurwet.** Zonder temperatuurverschil geen warmtestroom.
Wat de reboiler hoogstens kan leveren, rekent `simulatie.js` uit, en model 2 leest het daar:

    Q_max = Q_ontwerp · 1,25 · (T_olie − T_bodem) / (T_olie − T_bodem,ontwerp) · fouling

*Geldigheid.* Dat een warmtestroom een temperatuurverschil vraagt, geldt overal. De vorm hierboven
is een normering op het ontwerppunt: er is geen warmteoverdrachtscoëfficiënt, geen oppervlak, geen
oliedebiet en geen retourtemperatuur (hoofdstuk 12.2). **De fouling is een factor die het model
KRIJGT.** Het rekent niet uit waarom een aangekoekte wand minder warmte doorlaat; het kan dat
alleen vertellen. De tekst op het scherm zegt dat ook.

**De afstemming is gemeten, niet overgetypt.** De 60 kW/K staat in model 1 als getal binnen een
functie. `afstemmingTIC()` meet haar aan die regelaar (een afwijking van een kelvin, een
verwaarloosbare tijdstap) en deelt haar door de versterking van de klep, Q_max in het ontwerppunt:
0,0570 klepstand per kelvin. Rond het ontwerppunt is dat dezelfde regelaar. Verandert model 1 zijn
afstemming, dan volgt model 2 vanzelf.

**Wat dit anders maakt dan model 1, en dat is een gevolg en geen keuze.** Model 1 stuurt de DUTY;
in HAND staat de duty vast. Model 2 stuurt een KLEPSTAND; in HAND staat de klep vast, en dan
beweegt de duty mee met wat de reboiler kan leveren. Koelt de bodem af, dan groeit het
temperatuurverschil en levert dezelfde klepstand meer warmte: een zelfregeling van 0,8 · 1052 kW /
42,2 K = 20 kW/K, bovenop de regelaar. Gevolgen:

- met de temperatuurkring in AUTO is de uitschieter na een voedingsstap in model 2 kleiner dan in
  model 1, in de verhouding 60 / (60 + 20);
- met de olieklep vast in HAND loopt de bodem bij tien procent meer voeding NIET meer vol. De duty
  stijgt van 842 naar ongeveer 908 kW, de bodem koelt ruim drie graden af, en de niveaukring vangt
  de rest op. Bij een vastgehouden duty, zoals in model 1, loopt de afvoer vast op de capaciteit
  van P-01 en loopt de bodem vol (26.13). Een vastgehouden duty is een fictie van model 1; in het
  echt loopt de duty mee met het temperatuurverschil. De les is zelfregeling;
- de zelfregeling werkt alleen zolang er marge is. Met de olieklep tegen het plafond (reboiler
  vervuild tot 0,7, de temperatuur wordt niet meer gehaald) vangt de installatie dezelfde stap
  niet op: P-01 loopt vast op zijn capaciteit en de bodem loopt vol. De opstuwingsketen is er dus
  nog; zij begint nu waar de marge op is.

**Meetpunt: de bodemtemperatuur, en dat is een vereenvoudiging.** Het oefenmodel kent van K-01
alleen de bodemtemperatuur (`T_K01` in `MEETRUIS`) en regelt daarop; een meetpunt in de stripsectie
heeft alleen module 10 (16.2). De faceplate zegt het in tekst: in het echt zit het meetpunt hoger,
omdat de bodemtemperatuur vooral de pitchconcentratie volgt en niet de scheiding.

**Geen drukcompensatie, en dus geen uitspraak over de samenstelling.** Een temperatuur in een
vacuümkolom is zonder drukcompensatie geen maat voor de samenstelling: het kookpunt schuift mee met
de druk (hoofdstuk 8, dT/dp bij 20 mbar). De faceplate zegt dat; geen tekst van deze kring beweert
dat de temperatuur de samenstelling aangeeft.

**De beveiliging op de verwarming** staat in 26.17.

**Een setpoint buiten zijn grenzen wordt geweigerd, niet afgeknepen.** De faceplate zegt dat de
invoer niet is overgenomen, met de grenzen erbij; de
regelmodule weigert ook zelf. Ontwerpkeuze: een invoer die stil wordt aangepast, is voor de
operator niet te onderscheiden van een storing. Komt een setpoint ooit van een cascade, dan hoort
het begrensd te worden, zichtbaar; dat is niet gebouwd.

**Het setpoint** loopt van de onderkant van het meetbereik van de temperatuurmeting (0 °C, 26.15)
tot SP_max = T_alarm − 3σ = 249,3 °C (26.33).

**De schaal van de trend** (200 tot 280 °C) is een keuze van het beeld, geen meetbereik: van ruim
onder het ontwerppunt tot de aanvoertemperatuur van de olie, waar de bodem niet boven kan komen.

### 26.17 De beveiliging op de verwarming van E-01 (`WET_LSL_VERWARMING`, regelalgoritme)

Een ontwerpkeuze op natuurkunde en algemene praktijk; geen gegevens van een bestaande installatie.

**Waarom het niveau.** E-01 is een vallende-filmreboiler (8.26, 8.34 en `apparaten.js`). De
vloeistof loopt als een film langs de verwarmde buiswand; valt de film weg, dan staat de wand droog
op olietemperatuur. Een laag bodemniveau is daarom de trip.

**De opzet: het patroon van P-01, opnieuw gebruikt.** Hetzelfde beveiligingsblok (`maakLSLL`), met
andere grenzen:

| | Trip | Reset | Grijpt aan op |
|---|---|---|---|
| Verwarming E-01 | 30 % | 40 % | de olieklep: dicht |
| Laag-laag P-01 (26.8) | 10 % | 20 % | de pomp: uit |

- de trip blijft staan tot een eigen reset; die werkt pas boven de resetgrens;
- zolang de trip staat, staat de temperatuurkring in HAND met uitgang nul, en worden AUTO en openen
  geweigerd (dezelfde regel als de niveaukring bij een pompstop, met uitgang nul erbij);
- na de reset blijft de klep dicht tot de operator hem opent: er start niets vanzelf;
- de trip grijpt aan op de klep zelf (de vrijgave `K01.E01.VRIJGAVE`, door het veld gelezen), en
  wacht niet op de volgende scan van de regelkring.

**Waarom 30 %, en dat is de procesreden.** Er moet een ruime band zitten tussen "verwarming eruit"
en "pomp eruit". Het eerste is herstelbaar, het tweede niet, en de operator moet daartussen tijd
hebben om in te grijpen. De trip van de verwarming ligt bovendien boven de RESETgrens van de pomp.
Dezelfde hysterese als de LSLL (10 procentpunt). De grenzen zijn ontwerpkeuzes. `regeling.js`
weigert een instelling waarin de volgorde niet klopt (`volgordeTripsKlopt()`).
*Gemeten, niet als onderbouwing:* zonder voeding en met de afvoerklep open zit er in dit model
12,8 minuten tussen de twee trips.

**Dezelfde opnemer als de LSLL.** De beveiliging is los van de regelkring; dat is de scheiding die
ertoe doet, en twee beveiligingsfuncties op een beveiligingsopnemer is in de praktijk normaal.
**Wat het kost:** valt die ene opnemer uit, dan zijn beide beveiligingen hun meting kwijt, en
trippen beide. Of een opnemer voor twee beveiligingen mag, bepaalt in een echte installatie de
veiligheidsstudie. Er is geen derde transmitter verzonnen.

**Wat niet gemodelleerd is.** In een echte vallende-filmreboiler voedt een eigen circulatiepomp de
film, niet de productpomp. Uitval daarvan droogt de buizen sneller dan een dalend niveau. Het model
kent die pomp niet; de trip grijpt alleen aan op niveau, en de uitleg op het scherm zegt dat het
niveau niet het enige is waardoor een reboiler kan droogvallen. P-01 voedt de film NIET: dat zou
een ongebruikelijke opstelling zijn waarin meer product aftrekken ook meer bevochtiging betekent.
Het procesbeeld toont de leidingen van E-01 daarom met hun richting, maar zonder aandrijving.

**Het gevolg voor het niveau.** Gaat de verwarming eruit, dan verdampt er niets meer en loopt het
bodemniveau weer op: de beveiliging helpt het niveau. De keerzijde staat ook op het scherm: bij
doorlopende voeding (10 t/h in een bodem van ongeveer 213 kg) loopt de bodem binnen tien minuten
vol.

**Geen trip op hoge temperatuur.** Het echte gevaar is de temperatuur van de buiswand, en die rekent
het model niet uit (12.2); een trip zou doen alsof. Wel een alarm: 26.18.

**Het procesbeeld** toont de richting van de vallende film met pijlen: boven in, onder uit, terug
naar de kolom. Wat de vloeistof naar boven brengt, staat er niet; het beeld zegt in tekst dat de
circulatie niet gemodelleerd is.

**De reboiler heeft op het beeld vier aansluitingen**: het vetzuur erin en eruit, de olie erin en
eruit. De olieretour is getekend, zonder getal.
Dat het oliedebiet en de retourtemperatuur niet gemodelleerd zijn, is een reden om er geen getal
bij te zetten, geen reden om de leiding weg te laten; zonder retour ging de olie op het beeld
nergens heen, terwijl de uitleg zegt dat zij in de oliestoker wordt opgewarmd. Aan welk uiteinde de
olie in- en uitgaat, weet het model niet: beide olieleidingen zitten daarom aan dezelfde kant, en
het beeld zegt dat het geen opstelling vastlegt.

**In het vat staan de vier grenzen:** de reset van de verwarming (40 %), haar trip (30 %), en de
reset (20 %) en de trip (10 %) van de laag-laagbeveiliging. Zo ziet de cursist wanneer hij de
verwarming weer vrij mag geven.

**Wat hierdoor verandert.** Met doorlopende voeding en de afvoerklep open in HAND loopt de bodem
niet meer leeg tot de laag-laaggrens: op 30 % gaat de verwarming eruit en loopt de bodem weer vol.
De laag-laagbeveiliging van P-01 wordt in dit model alleen nog geraakt als ook de voeding weg is.

### 26.18 Het alarm op hoge bodemtemperatuur (`WET_ALARM_HOGE_TEMPERATUUR`, regelalgoritme)

**Een alarm op hoge temperatuur hoort waar het PRODUCT gaat lijden, niet waar het apparaat ophoudt.**

**Waarom niet het plafond van 255 °C.** Die 255 °C is een ontwerpgetal van model 1: de
olietemperatuur min 25 K minimale drijvende kracht (hoofdstuk 11). Het is geen grens die de
temperatuur niet kan passeren, maar een alarm daar zou te laat komen of helemaal niet: bij een
voedingstrip met de temperatuurkring in AUTO komt de bodem tot 251,3 °C en dus nooit bij het
plafond.

**De waarde: 249,9 °C, uitgerekend en niet ingetypt.** De temperatuur waar de afbraaksnelheid het
dubbele is van die in het ontwerppunt (237,8 °C), volgens Arrhenius (hoofdstuk 9, `degradation.js`):

    exp( (Ea/R) · (1/T_ontwerp − 1/T_alarm) ) = 2

`alarmgrensBodemtemperatuur_K()` leest de ontwerptemperatuur uit het procesmodel en de
activeringsenergie uit `EA_BAND`, en gebruikt `verdubbelingsstap_K()`.

**Welke activeringsenergie: de bovengrens van de band (127,4 kJ/mol).** De activeringsenergie van
de afbraak is niet gemeten; module 9 geeft een band. De verdubbelingstemperatuur schuift daarmee:

| Activeringsenergie | Verdubbeling bij |
|---|---|
| bovengrens, 127,4 kJ/mol | **249,9 °C** (het alarm) |
| weergavemidden, 62,8 kJ/mol | 262,9 °C |
| ondergrens, 31,0 kJ/mol | 291,5 °C, boven de olietemperatuur |

De bovengrens is de goede kant, om twee redenen. Hij geeft het vroegste alarm. En hij is de kant
waar overschrijden het hardst aankomt: een hoge activeringsenergie laat de snelheid steil oplopen.

**De vuistregel als controle, niet als bron.** De praktijkregel van een verdubbeling per 8 K geeft
245,8 °C, 4 K lager. Dat is geruststellend: twee verschillende wegen landen dicht bij elkaar. De
vuistregel is geen bron voor de waarde (9.x, `EA_VUISTREGEL`).

**De factor twee is een keuze.** Dat afbraak met de temperatuur versnelt, is natuurkunde; bij welke
factor je gewaarschuwd wilt worden, niet.

**Een alarm, geen trip.** Het grijpt nergens op in, staat zolang de oorzaak er is, en verdwijnt
daarna vanzelf, met de dode band van 26.33. Een meting boven haar bereik
telt als hoog; een meting zonder waarde geeft dit alarm niet, dan meldt de regelkring de slechte
meting.

**Gaat het ook werkelijk af?** Nagegaan in het model, omdat een alarm dat nooit kan afgaan voor de
reden waarvoor het bestaat, het probleem alleen verplaatst. De voeding valt weg, de verwarming
blijft zoals zij staat:

| Temperatuurkring | Alarm | Olieklep dicht | Trip op laag niveau | Hoogste bodemtemperatuur |
|---|---|---|---|---|
| in HAND | na 80 s | na 90 s (door de trip) | na 90 s | 264,9 °C |
| in AUTO | na 110 s | na 140 s (door de regelaar) | na 360 s | 251,3 °C |

- **Wat stopt de temperatuur?** In HAND: de beveiliging op laag niveau. In AUTO: de regelaar zelf,
  die de klep dichtstuurt ruim voor de trip. In beide gevallen loopt de bodem niet meer op tot de
  olietemperatuur, zoals zonder beveiliging (280 °C).
- **Wat het alarm waard is:** in AUTO komt het minuten voor de trip. In HAND maar tien seconden; de
  uitleg op het scherm zegt dat, en zegt dat het alarm vooral vertelt wat het product doormaakt.
- *Rand:* de getallen gelden bij een rekenstap van 10 s. Bij 1 s komt het alarm in HAND na 73 s, de
  trip na 85 s, en is de hoogste temperatuur 255,7 °C: de volgorde blijft, de hoogte van de piek
  hangt van de rekenstap af. Na het afschakelen koelt de bodem in het model niet af.

### 26.19 De drukkring: de regelklep in de zuigleiding (`vacuumklep.js`)

**Wat model 1 vastlegt, en model 2 daar leest.** De druk is een uitkomst: zij stelt zich in waar het
vacuümsysteem evenveel wegzuigt als er aan lek en niet-gecondenseerde damp bijkomt (hoofdstuk 20.4).
De regelaar stuurt de zuigcapaciteit als deel van de ontwerpcapaciteit, tot de zuigreserve van 1,5
(20.2). Bij een vaste belasting is druk maal capaciteit constant. De druk heeft in het model geen
eigen dynamiek: zij volgt de capaciteit zonder vertraging (`STIJF_STELSEL_DRUK_ALGEBRAISCH`).

**De actuator: een regelklep in de zuigleiding (PV).** De reden: een regeling met een gasinlaat
(ballast, valse lucht) vraagt niet-condenseerbaren en hun effect op de condensor, en die rekent het
model niet uit. Wat je niet kunt rekenen, bouw je niet. **In veel echte vetzuurfabrieken wordt het
vacuüm juist met een gasinlaat geregeld, omdat stoomejecteurs zich niet laten knijpen** (algemene
praktijkkennis, niet nagezocht in een bron); de uitleg op het scherm zegt dat.

**De klep is gelijkprocentueel: `WET_VACUUMKLEP`, modelaanname.**

    S = S_max · R^(h − 1),   R = 50,   S_max = 1,5   (de zuigreserve, 20.2)

Het principe is dat de klepkarakteristiek de procesversterking compenseert. Hier is p · S constant,
dus |dp/dS| = p/S: de versterking loopt met het kwadraat van de druk (factor 25 over 12 tot 60
mbar). Met een gelijkprocentuele klep is |dp/dh| = p · ln R: een factor is eruit, er blijft factor 5.
Bij de reboiler was de procesversterking constant en werd de klep lineair (26.16): hetzelfde
principe, een andere uitkomst. Het is geen regel dat drukkleppen gelijkprocentueel zijn. R = 50 is
het regelbereik van de bodemafnameklep (26.3): dezelfde generieke klep. In het ontwerppunt staat de
klep op 1 − ln 1,5 / ln 50 = 89,6 %.

**De afstemming: dezelfde eis als model 1, op een andere plaats gesteld.** Model 1 stelde zijn
drukregelaar af op een lusversterking van 0,02 per mbar maal 20 mbar = 0,4, in zijn ene
bedrijfspunt. Model 2 heeft een operator die het setpoint verzet; de eis geldt daarom in het
ongunstigste punt van het bereik, de bovenkant van het meetbereik (60 mbar):

    Kp = 0,4 / (p_max · ln R)

Dat is geen ander antwoord dan model 1 geeft, maar een andere eis. De 0,4 wordt aan model 1
nagerekend (`lusversterkingModel1()`), de integratietijd (60 s) is die van model 1. Er komt geen
getal bij behalve R.

**Waarom: de kring van model 1 slingert buiten zijn bedrijfspunt.**
Met een setpoint van 40 of 58 mbar springt de druk in model 1 elke stap tussen 13,3 en 100 mbar;
bij een lek van 0,3 keer het ontwerplek (3 kg/h, setpoint 20 mbar) tussen 4,0 en 100 mbar. Een kleinere
rekenstap helpt niet: met 10 s en met 1 s is de band dezelfde (model 1 zonder ruis, dertig minuten
inlopen, dan de band over tien minuten). De oorzaak is de oplopende versterking, samen met het ontbreken van drukdynamiek.
**Die slingering wordt in model 2 niet als les getoond:** voor een groot deel is zij een artefact
van een druk die in nul seconden reageert, en dat is geen procesgedrag.

**De prijs: de kring is traag.** Van 20 naar 40 mbar duurt ongeveer twaalf minuten; bij een klein
lek (0,3 keer het ontwerplek, 3 kg/h) is de druk na veertig minuten nog 0,3 mbar van het
setpoint. **Traag, niet blijvend:**
het verschil halveert ruim per uur en is na drie uur kleiner dan een honderdste mbar. De
integrerende actie doet haar werk. Model 1 noemt dit "de
snelste kring van de kolom"; dat is zij in model 2 niet.

**Het setpoint** heeft als bovengrens 23,50 mbar (26.36); erbuiten wordt het geweigerd met een
melding (26.16).

**Een topdruk boven het meetbereik: `WET_DRUK_BOVEN_BEREIK`, regelalgoritme.** Verzadigt de
topmeting, dan valt de drukregeling naar HAND (26.15): een
grens is geen getal, en dat blijft staan. Maar een grens aan een kant beantwoordt wel een vraag aan
een kant. Boven het bereik is de druk zeker te hoog, dus meer zuigen is zonder twijfel de goede kant
op: **de zuigklep gaat helemaal open**, en het scherm zegt beide dingen, dat de regelaar het niet
meer doet en waarom de klep toch bewogen is. Dezelfde redenering als de richtinggevoelige
laag-laagbeveiliging (26.5), nu aan de regelkant.

- Zolang de meting boven haar bereik staat, blijft de klep open; een andere stand en AUTO worden
  geweigerd en gemeld.
- Komt de druk terug binnen het bereik, dan blijft de kring in HAND met de klep open; de operator
  zet hem zelf terug in AUTO.
- Zonder waarde, of onder het bereik, zegt de meting niets over de richting: dan blijft de klep
  staan.
- *Niet gedekt:* een opnemer die kapot is en bovenin blijft hangen, ziet er net zo uit als een te
  hoge druk; de klep gaat dan ook open. Dezelfde rand als bij de LSLL.
- *Wat het scheelt:* bij een plotseling lek van vijf keer het ontwerplek (50 kg/h) staat de top met
  de klep open op 20 · 5 / 1,5 = 66,7 mbar; op de ontwerpstand (89,6 %) is het 100 mbar, de grens
  van het model.

**Is het meetbereik van 60 mbar te smal?** Nagezocht.
Model 1 legt geen meetbereik vast: de bereiken zijn een keuze van model 2 (26.15). Model 1 legt wel
vast welke lekken het kent: `LEKNIVEAUS` in `vacuum.js` noemt 5, 10 en 20 kg/h, en de
oefening "sluipend lek" van model 1 loopt van 10 tot 20 kg/h. Het grootste lek dat model 1 bij naam noemt, het "lekkend
systeem" van 20 kg/h, geeft in de eerste stap 40 mbar en blijft binnen het bereik; de regelaar blijft in AUTO. De
regel uit 26.15 is dus gehaald voor de storingen die model 1 kent. Een lek boven 30 kg/h valt buiten wat model 1 beschrijft;
daarvoor geldt de regel hierboven.

**Het alarm op hoge topdruk** staat op 23,8 mbar, afgeleid op de bodem van K-02; zie 26.36.

### 26.22 De hoog-hoogbeveiliging van de bodem van K-01, en de voeding (`WET_LSHH_OP_VOEDING`, regelalgoritme)

Geen van de getallen hieronder is een gegeven van een bestaande installatie: het zijn ronde
ontwerpkeuzes, op natuurkunde en algemene praktijk.

**Wat er is.** Een hoog-alarm (LSH) en een hoog-hoogbeveiliging (LSHH) op de bodem van K-01, met
een eigen, derde niveautransmitter (`K01.LSHH.PV`), los van die van de regelkring en van de
laag-laagbeveiliging (`WET_EIGEN_METING`, 26.5). De trip stopt de voeding: voedingspomp P-03 uit
en voedingsafsluiter XV-01 dicht. Bodempomp P-01, de afvoerklep en het vacuüm blijven zoals zij
staan, zodat het niveau kan zakken door de afvoer. Elke trip sluit ook de
olieklep van E-01, en een reset vraagt behalve een niveau onder de resetgrens ook een beschikbare
bodemafvoer (26.23).

| Grootheid | Waarde | Herkomst | Waar |
|---|---|---|---|
| alarmgrens (LSH) | 80 % van de bodem | ontwerpkeuze | `GRENZEN_LSHH` in `regeling/interlock.js` |
| tripgrens (LSHH) | 90 % van de bodem | ontwerpkeuze | idem |
| resetgrens | onder 75 % van de bodem | ontwerpkeuze | idem |
| meetbereik van de derde transmitter | 0 tot 100 % | hetzelfde als de twee andere (26.15) | `veld/veld.js` |

Geldigheid: de grenzen horen bij dit model. Waar zij in een echte kolom liggen, hangt af van de
bodemmeetkunde, van waar het bed begint en van wat er na een trip nog binnenkomt; het model kent
daarvan alleen de massa in de bodem.

**Waarom 90 en niet 95 procent:** de hoog-hoog van K-02, die dezelfde voeding stopt, krijgt na de
trip nog de naloop van K-01.

**Een meting buiten haar bereik of zonder waarde** (`WET_LSHH_GRENS`). De
richting telt, zoals bij de laag-laagbeveiliging, gespiegeld: boven het bereik is het niveau zeker
hoog (trip), onder het bereik zeker niet. Een meting zonder waarde laat de beveiliging trippen:
wat zij niet kan zien, telt als niet veilig. De prijs: een kapotte opnemer stopt de voeding. Niet
gedekt: een opnemer die kapot is en onderin blijft hangen.

**Voedingspomp P-03 en voedingsafsluiter XV-01 zijn alleen aan en uit.**
Er is geen pompkromme, geen klepkarakteristiek, geen looptijd en geen minimumdebietleiding: voor
geen daarvan is er een gegeven, en het procesmodel kent van de voeding alleen een instelling en
een schakelaar (`instelling.voeding_vrij`, 20.17). Model 2 zet die
schakelaar: de voeding loopt als P-03 draait EN XV-01 open staat. Het scherm zegt dat er niet meer
gemodelleerd is. XV-01 is een klep en staat daarom niet in `installatie.js`; zijn tags hangen aan
apparaat P-03.

**P-03 start niet tegen een dichte afsluiter** (`WET_P03_AFSLUITER`). Een
start met XV-01 dicht wordt geweigerd, en sluit de operator XV-01, dan stopt P-03. De natuurwet
eronder is die van het dooddrukken (`WET_DOODDRUKKEN`, 26.11): zonder doorstroming gaat het
asvermogen in het pomphuis zitten. Het model rekent die opwarming niet uit.

**De tijd van alarm tot trip** (`WET_OPHOPING_BODEM`, afgeleide methode: behoud van massa).

    dM/dt = F - V - B                              (K-01 heeft geen reflux)
    t = (h_trip - h_alarm) * M_max / (F - V - B)

h als fractie [-], M_max in kg, de stromen in kg/s, t in s. De wet is dezelfde als
`WET_ACCUMULATIE` van model 1 (`dynamics.js`); de deling staat een keer, in `tijdconstante()`, en
`tijdTussenNiveaus_s()` in `beveiliging.js` roept haar aan. M_max is de massa in de bodem bij
honderd procent zoals het procesmodel haar heeft: 864 kg (twee keer de inhoud op het setpoint van
50 procent, 20.13, met de dichtheid van de bodem bij haar ontwerptemperatuur, hoofdstuk 4). Tussen 80 en 90 procent zit dus
86,4 kg.

| Geval | Netto instroom | Alarm tot trip |
|---|---|---|
| de afvoer loopt dicht, de rest blijft gelijk | 400 kg/h: wat P-01 in het ontwerppunt afvoert | 13 minuten |
| de verwarming valt weg, P-01 op zijn capaciteit (800 kg/h) | 9200 kg/h: de hele voeding min de afvoer | 34 seconden |

Geldigheid: een constante netto instroom, en een niveau dat recht evenredig is met de massa (een
rechte bodem, een vaste dichtheid). De twee tijden staan ook in de uitleg op het scherm.

**De balk bovenaan en de eerste trip** (`WET_EERSTE_TRIP`, regelalgoritme).
De besturing onthoudt van elke trip het tijdstip van de scan waarin hij begon, en wijst de oudste
aan; twee in dezelfde scan heten gelijktijdig. Zij houdt
die eerste vast tot er geen trip meer staat, ook als hij zelf al gereset is
(`eersteTripVastgehouden()`): na de reset van de hoog-hoog van K-01 staat de laag van K-02 die zij
meetrok er nog, en die is een gevolg, niet de oorzaak. De balk noemt de trips
eerst en dan de alarmen, in een vaste volgorde, en zegt hoeveel er nog meer zijn.

**Boven honderd procent.** Wat het model boven honderd procent doet (de onderkant van het bed onder
vloeistof, `opstuwing()`) is een modelaanname (20.2).

### 26.23 Elke hoog-hoogtrip sluit ook de verwarming, en de reset vraagt een beschikbare afvoer (`WET_LSHH_VERWARMING`, regelalgoritme)

**De olieklep van E-01 gaat dicht bij elke hoog-hoogtrip, ongeacht de afvoer**, en **een reset
werkt alleen met het niveau onder 75 % EN een beschikbare afvoer** (hieronder). Ontwerpkeuzes, op
natuurkunde en algemene praktijk, niet op gegevens van een bestaande installatie. Er hoort geen
stofwaarde en geen nieuw getal bij.

**Waarom bij elke trip: een open afvoer houdt het indikken niet tegen.** Na een hoog-hoogtrip komt
er niets meer in. Blijft de verwarming
aan, dan verdampt het vetzuur en blijft de pitch achter; het kookpunt loopt op tot de bodem zo heet
is als de thermische olie. De reboiler verdampt in het ontwerppunt ruim tien keer zoveel als P-01
op zijn capaciteit afvoert. Een volle bodem met de helft pitch erin werd daardoor ook met de
afvoerklep helemaal open 280 °C: het alarm op hoge bodemtemperatuur (26.18) na ruim twee minuten,
de laag-niveautrip pas na ruim dertien. Nagespeeld zonder de regel (de vrijgave van de olieklep na
elke scan teruggezet): 280,0 °C met de afvoer dicht en met de afvoer helemaal
open. Met de regel blijft de bodem in beide gevallen op de temperatuur van de trip.

**Het besluit over de olieklep** staat een keer, in `verwarmingOpdracht()` in `beveiliging.js`,
naast dat van de laag-niveautrip (26.17), en geeft de reden mee (`K01.E01.VRIJGAVE.REDEN`: geen,
de laag-niveautrip, of de hoog-hoog). De laag-niveautrip gaat als reden voor. De functie leest de
afvoer niet. Zolang de trip staat, staat de temperatuurkring in HAND met uitgang nul en worden AUTO
en openen geweigerd, zoals bij de laag-niveautrip. Na de reset is de olieklep vrij maar blijft hij
dicht tot de operator hem opent: er start niets vanzelf.

**Wanneer de afvoer beschikbaar is** (`afvoerBeschikbaar()`). Dit telt voor de reset en voor wat het
scherm zegt, niet voor het sluiten van de olieklep.

| Keuze | Waarde | Herkomst |
|---|---|---|
| de afvoer is beschikbaar | als P-01 draait en de niveaukring in AUTO staat of de afvoerklep open staat | ontwerpkeuze |
| de afvoer is niet beschikbaar | als P-01 stilstaat, of de niveaukring in HAND staat met de afvoerklep op nul | idem |
| waaraan gelezen | de pomp en de klep aan de terugmelding van het veld (`K01.P01.STAND`, `K01.LV.POS`), niet aan de opdracht; de mode zoals de niveaukring haar meldt (`K01.LIC.MODE`) | ontwerpkeuze: een klep die vastzit, staat niet waar de regelaar hem vraagt |
| een terugmelding zonder waarde | telt als stil, als niet in AUTO, als dicht | dezelfde regel als in 26.5 en 26.22: wat niet te zien is, telt als niet veilig |
| een klep op een kier | telt als open | de regel vraagt een ja of een nee; er is geen drempel, dus ook geen getal dat een bron vraagt |

De reden voor AUTO: met de niveaukring in AUTO stuurt zij de afvoerklep na een trip zelf naar nul
zodra het niveau ver genoeg gezakt is, en opent zij hem weer zodra het stijgt. De afvoer is dan niet
dicht maar in rust. In HAND valt de regel samen met het werkpunt van P-01 (26.13): beschikbaar is
precies waar pomp en klep samen iets afvoeren. De besturing
meldt de stand als tag (`K01.LV.AFVOER`: beschikbaar of niet).

**De reset: onder 75 % EN een beschikbare afvoer** (`WET_LSHH_GRENS`). De
voorwaarde staat in het blok zelf (`maakLSHH` in `regeling/interlock.js`); de regellaag geeft
het blok elke scan de stand van de afvoer mee. De gedachte: wie de voeding weer vrijgeeft op een
bodem die niets kwijt kan, laat hem opnieuw vollopen. Een reset die geweigerd wordt, is niet
onthouden.

**Wat het in de simulatie doet** (de echte veldlaag en regeling, zonder ruis, de
olieklep op de ontwerpstand).
- *Afvoer dicht* (de niveaukring in HAND op nul): de hoog-hoog trept na ruim een uur op 90 procent;
  een rekenstap later is de olieklep dicht. Acht uur later staat de bodem nog op de temperatuur
  van de trip, ruim veertig graden onder de olie, met de helft pitch, op 90 procent. Het niveau
  zakt niet en de reset wordt geweigerd tot de afvoer beschikbaar is.
- *Afvoer open*: het niveau zakt alleen nog door de afvoer; in het model stopt de verdamping meteen.
- *Met de kring in HAND en de klep open* loopt de bodem leeg tot de laag-laagbeveiliging P-01
  stopt: drie trips uit een oorzaak (26.22, de balk).

**Wat deze keuzes kosten.** Het niveau zakt na een trip veel trager dan met de verwarming aan.

**De niveaukring na de trip** (`WET_LIC_NA_TRIP`, regelalgoritme). De kring
blijft na een hoog-hoogtrip in AUTO. Haar afstemming is die van het procesmodel, de lambda-afstemming
van 16.5 (K_p = 6031 kg/h per eenheid niveau, T_i = 40 min), omgerekend naar een klepstand
(`afstemmingUitModel1()`). Na een hoog-hoogtrip zakt het niveau bij het setpoint van 50 procent
7,1 procentpunt (16.5, 26.28). Geldigheid: geen gegevens van een bestaande installatie; de getallen
horen bij dit model.

Geldigheid: de 280 °C is de grens van het model (de aanvoertemperatuur van de thermische olie,
een ontwerpkeuze, 12.2), geen gemeten kookpunt van pitch; en de afbraak die bij die temperatuur
hoort, toont model 2 niet. Niet gemodelleerd: een looptijd van de olieklep.

**Modelgrens: de verdamping stopt in het model meteen.** In het procesmodel volgt de dampstroom
rechtstreeks uit de duty van die stap, en de
bodemtemperatuur is het kookpunt bij de bodemdruk: de bodem en E-01 hebben geen warmte-inhoud.
Sluit de olieklep, dan is de duty in de volgende rekenstap nul en het destillaat ook (van 9600 kg/h
naar nul in een stap, bij een onveranderde bodemtemperatuur). In
werkelijkheid zit er nog warmte in de vloeistof, in het staal en in de olie in de reboiler
(Q = m x cp x dT), en zolang de bodem heter is dan het kookpunt bij de kolomdruk verdampt er nog
wat na; hoeveel, kan dit model niet zeggen, want het kent geen van die massa's en geen
soortelijke warmte van de bodem. De schermteksten zeggen daarom "in dit model stopt de verdamping
meteen; in werkelijkheid verdampt er nog even wat na".

### 26.25 De beveiligingen van de bodem van K-02 (ontwerpregels; `WET_BALANS_BODEM_K02`, afgeleide methode)

**Geen gegevens van een bestaande installatie.** Elke grens is een ontwerpkeuze; de afleiding staat
erbij.

**Dezelfde drie beveiligingen als bij K-01, elk op een eigen meting** (`WET_EIGEN_METING`, 26.5): de
laag-laag en de laag delen een opnemer (`K02.LSLL.PV`), de hoog-hoog heeft een derde
(`K02.LSHH.PV`), los van de meting van de regelkring (`K02.LT.PV`). Elk een eigen ruiskanaal op de
ruis van de niveaumeting van K-02 (`n_K02`). Dezelfde blokken (`maakLSLL`, `maakLSHH`), dezelfde
regels voor een meting buiten haar bereik of zonder waarde (26.5, 26.22), en dezelfde reset met de
hand die alleen werkt als de oorzaak weg is.

| Beveiliging | Grens | Reset | Grijpt aan op |
|---|---|---|---|
| laag-laag (LSLL) | onder 10 % | boven 20 % | P-02 uit |
| laag (LSL) | onder 30 % | boven 40 % | E-03 uit |
| hoog-alarm (LSH) | boven 80 % | - | niets: een alarm |
| hoog-hoog (LSHH) | op of boven 90 % | onder 75 %, met een beschikbare afvoer | voeding van K-01 (P-03 uit, XV-01 dicht), olieklep E-01 dicht, E-03 uit en de reflux uit V-02 gestopt |

**De grenzen zijn die van K-01** (26.8, 26.17, 26.22), als eigen objecten in `interlock.js`
(`GRENZEN_LSLL_K02`, `GRENZEN_LSL_E03`, `GRENZEN_LSHH_K02`): een andere keuze voor de een mag de
ander niet stil meenemen. Eigenlijk volgen de lage grenzen uit de
verblijftijd van de bodem en de NPSH van P-02, en de bodem van K-02 is snel: de interne stromen zijn
bijna drie keer de voeding. Dat is open, zie 26.24.

**Waarom de laag de verwarming van E-03 uitzet.** Zonder voeding kookt de bodem van K-02
over de top leeg: V − L = D, ook met een dichte afvoer. Zonder deze beveiliging gebeurde dat na een
voedingstrip in ongeveer 13 minuten. De beveiliging sluit de olieklep van E-03 en zet de
temperatuurkring van K-02 in HAND op nul; na de reset blijft zij daar, er start niets vanzelf (26.33).

**Waarom de hoog-hoog op K-01 aangrijpt.** De toevoer naar K-02 is het destillaat van K-01,
en dat is wat K-01 verdampt (ṁ_damp ≈ Q / ΔH_verdamping). Er is geen buffer tussen de twee kolommen
(26.22). De voeding van K-01 stoppen alleen stopt de toevoer naar K-02 niet meteen: K-01
kookt door op wat er in zijn bodem staat. Daarom sluit de trip ook de olieklep van E-01
(`verwarmingOpdracht`, reden `LSHH_K02`). K-01 moet daarna opnieuw worden opgestart.

**Waarom ook E-03 uitgaat.** Zonder verdamping gaat er na de trip niets meer over de top, en komt er
geen C18 meer in het C16-product. E-03 komt pas terug na de reset van de hoog-hoog, met dezelfde
voorwaarden als bij K-01: het niveau onder 75 procent en een beschikbare afvoer. De vrijgave
(`K02.E03.VRIJGAVE`) houdt E-03 tegen (`verwarmingOpdracht`, reden `LSHH_K02`); na de reset start er
niets vanzelf (26.33).

**Waarom ook de reflux stopt.** Met E-03 uit
verdampt er niets meer, maar refluxvat V-02 geeft zijn inhoud nog af: de refluxkring volgt het
destillaat in een vaste verhouding. Die inhoud liep terug de bodem in, en met een dichte afvoer stond de bodem
daardoor tot 114 procent, tot in het bed: een trip die tot een onbeveiligde toestand leidde. Daarom stopt de
trip ook de reflux, als tripactie: de vrijgave `K02.REFLUX.VRIJGAVE` (`refluxOpdracht()` in `beveiliging.js`).
Zolang de trip staat, staat de debietregelaar van de reflux in HAND met de uitgang op nul en sluit het veld
FV; na de reset gaat zij terug naar haar mode van voor de trip (26.32). Het beeld "K-02 top" zegt bij de
reflux dat zij door de trip gestopt is.

**Wat de bodem dan doet: behoud van massa** (`WET_BALANS_BODEM_K02`, afgeleide methode). dM/dt = L_in − B − V,
met V = Q / ΔH_vap (module 6, de energiebalans van de reboiler; module 10, `WET_ACCUMULATIE`). L_in is wat
er van boven in de bodem loopt: de voeding van K-02 en de reflux door het bed. Over een tijd opgeteld is het
dezelfde wet: ∫(in − uit)d*t* = Δ*V*, met in = L_in en uit = B + V; de bodem heeft een vaste dichtheid, dus
Δ*V* = Δ*M*/ρ. Na de trip zijn de voeding nul (er zit geen buffer tussen K-01 en K-02) en de reflux gestopt,
dus L_in = 0, en Q = 0, dus V = 0: Δ*V* = −∫B d*t*, de bodem verliest alleen nog wat de afvoer doorlaat. De
berekening staat een keer, in `afgeleidenK02()` en `dutyK02()` (`simulatie.js`).

De bodem van K-02 komt na de trip nooit boven 100 procent, ook niet met een dichte afvoer. Zonder de
refluxstop haalt het model dat niet.

**Wat het model doet** (gemeten met `dt` = 1 s, ruis uit, na tien minuten rust; de TIC van K-02 in HAND met de
olieklep op 80 %, de FIC in CAS, zoals bij de start):
- **Afvoer van K-02 dicht in HAND:** het hoog-alarm na 5,8 minuten, de hoog-hoogtrip na
  8,4 minuten. E-03 en de reflux gaan bij de trip uit: er komt niets meer bij en er
  gaat niets uit, en het niveau blijft een uur lang op 90,2 procent staan, waar de trip kwam.
- **Hoog-hoogtrip van K-02 met een beschikbare afvoer, kring in AUTO** (de eigen meting van de beveiliging
  valt uit): vanaf 50 procent zakt het niveau tot 33,1 procent, vanaf de ondergrens van 40
  procent tot 23,1 procent: boven de resetgrens van de laag-laag van P-02 (20 procent). De daling komt van de
  kring (16.5), niet van de verdamping.
- **Hoog-hoogtrip met de afvoer dicht in HAND, daarna de kring in AUTO:**
  de trip zet de kring niet op HAND, dat doet alleen een stilgevallen P-02. In AUTO blijft zij in AUTO. Het
  setpoint volgde in HAND de meting en staat op zijn bovengrens van 70 procent; de klep gaat open tot 9,2 procent,
  en de meting van LSHH zakt in ongeveer 21 minuten onder de resetgrens van 75 procent. Daarna werkt de reset.
- **Voedingstrip, kring van K-02 in AUTO:** de laag van K-01 komt na 1,5 minuten, die van K-02 na 5,6
  minuten. In AUTO zakt het niveau van K-02 tot 29,9 procent op de ongefilterde meting van de beveiligingen,
  net onder de laag-niveaugrens (de gefilterde meting van de kring tot 30,9 procent).
- **Voeding weg en afvoerklep van K-02 open:** de laag na 2,4 minuten, de laag-laag na 10,5 minuten; P-02
  gaat uit.

**De niveaukring na een trip** (`WET_LIC_NA_TRIP_K02`): de lambda-afstemming met λ = 8 min (16.5):
Kp = 2,06 klepstand per eenheid niveau, dus
de P-actie alleen vraagt voor een dichte klep e = Δu/Kp, ongeveer 24 procent; de I-actie haalt
daar binnen ongeveer een sluittijd een groot deel af. De uitleg zegt wat het niveau na een trip doet, en dat
de operator de kring zelf in HAND kan zetten om het ergens anders te houden.

**De grens van het model bij de massabalans.** Het bed van K-02 houdt in het model geen
vloeistof vast: `afgeleidenK02()` in `simulatie.js` rekent dM/dt = F + L − V − B, en wat bovenin het bed komt,
staat in dezelfde stap in de bodem. Na de trip is L_in dus meteen nul. In werkelijkheid hangt er vloeistof in de
pakking die nog een tijd nadruppelt; hoeveel, hangt af van de pakking en de belasting, en daar is hier geen bron
voor. Het waarom-paneel van `WET_BALANS_BODEM_K02` noemt de grens.

**De grens van het model bij de bodemtemperatuur.** Staat de verdeling tussen top en bodem
op haar rand (`productverdeling()` in `simulatie.js`, `isVerdelingBegrensd`), dan zegt het beeld
"K-02 bodem" dat bij de temperatuurmeting, en toont het de temperatuur afgerond op een hele graad. Het
veld schrijft die stand in `K02.VERDELING.STAND`. De samenstelling springt in dit model direct.

### 26.26 De grenzen van het setpoint van de niveaukringen (`WET_SP_GRENZEN_NIVEAU`, regelalgoritme)

Geen melding bij een setpoint vlak boven een tripgrens, maar grenzen op het setpoint, zoals een DCS
die kent, op de niveaukringen van K-01 en K-02. De ondergrens zo hoog dat de doorschot van de
regelaar niet onder de laag-niveaubeveiliging komt. Een ontwerpkeuze met de berekening erbij; geen
gegeven van een bestaande installatie.

| Kring | SP_min | SP_max | Waar |
|---|---|---|---|
| K-01 | 45 % | 65 % | `SP_GRENZEN_NIVEAU` in `regeling/regeling.js` |
| K-02 | 40 % | 70 % | idem |

**De berekening** (`doorschotSetpointstap()` in `physics/regelaar.js`). De bodem is een integrerend
proces: dh/dt = (in - uit) / M_max. Met de PI-regelaar op de afvoer, direct werkend, en rond het
ontwerppunt lineair:

    dh/dt = -a·(e + ∫e dt / Ti),   e = h - SP,   a = Kp / M_max,   zeta = ½·√(a·Ti)

Kp is de afstemming van model 1 in kg/h per eenheid niveau, M_max de massa van de volle
bodem. De doorschot d van een setpointstap, als deel van de stap, hangt dan alleen af van a·Ti; de
functie rekent haar uit door de kring met een kleine stap door te rekenen. Omdat de P-actie op de
afwijking werkt, schiet de kring ook bij een demping boven 0,7 nog door.

De grootste stap is van de ene grens naar de andere. De eis: SP_min - d·(SP_max - SP_min) boven de
laag-niveaubeveiliging (30 %), en SP_max + d·(SP_max - SP_min) onder het hoog-alarm (80 %).

Beide kringen hebben de lambda-afstemming (16.5): a·Ti = 4,00, zeta = 1,00 en
d = 14 % voor allebei. Voor K-02 (Kp 33 698 kg/h per eenheid, Ti 16 min) geeft de stap van 70 naar 40 dan
40 - 4,1 = 35,9 %, en omhoog 74,1 %; de grenzen zijn 40 tot 70 procent. De uitleg op het scherm noemt
de getallen. De ondergrens van K-02 heeft ook een tweede reden, zoals die van K-01: na een hoog-hoogtrip
zakt het niveau vanaf 40 procent tot 23,1 procent, boven de resetgrens van de laag-laag (26.25).

**Wat de regelaar doet** (`regeling/pid.js`, ook voor het meetbereik van de
temperatuur- en de drukkring): een setpoint buiten de grenzen wordt geweigerd, niet afgeknepen, en de
faceplate zegt waarom. In HAND volgt het setpoint de meting tot de grens: staat het niveau eronder,
dan staat het setpoint op de grens, en regelt de kring na AUTO daarheen. De faceplate toont de grenzen
altijd, uit de tags `K01.LIC.SP.MIN`, `K01.LIC.SP.MAX` en die van K-02.

Geldigheid: de doorschot is die van een stap vanaf een niveau dat op het setpoint staat, zonder
storing tegelijk. Een voedingstrip of een trip van de andere kolom laat het niveau verder zakken dan
een setpointstap; daar zijn de beveiligingen voor.

### 26.27 De statische hoogte boven P-01 en P-02: h_statisch = 4 m (ontwerpkeuze)

h_statisch = 4 m voor P-01 en P-02. Een ontwerpkeuze, geen gegeven van een bestaande installatie.
h_statisch is de hoogte van
het vloeistofoppervlak boven de hartlijn van de pomp, **gemeten bij het laagste niveau waarop de pomp
moet blijven draaien**: de laag-laagbeveiliging. Bij een hoger niveau is er meer.

**De wet** (`WET_NPSH`, afgeleide methode, zie ook 26.11 en 26.24). De netto positieve zuighoogte die de
installatie biedt:

    NPSH_a = (p_oppervlak − p_damp) / (ρ · g) + h_statisch − h_verlies        [m]

met p in Pa, ρ in kg/m³, g = 9,81 m/s², hoogtes in m. In een kokende vacuümbodem staat de vloeistof op
haar kookpunt bij de druk erboven: p_oppervlak = p_damp. De eerste term valt weg, hoe diep het vacuüm
ook is, en er blijft

    NPSH_a = h_statisch − h_verlies.

De eis: NPSH_a > NPSH_r + marge, dus h_statisch > NPSH_r + marge + h_verlies.

**Bronnen.** Hoofdbron is het Hydraulic Institute:
- de vergelijking: *Pump Fundamentals – Pump Principles* (edl.pumps.org/pump-fundamentals/pump-principles);
  de vorm staat in elk handboek over pompen;
- de marge: ANSI/HI 9.6.1, *Rotodynamic Pumps Guideline for NPSH Margin* (pumps.org/standards/ansi-hi-9-6-1).

Daarnaast Pumps & Systems, voor de zuig-specifieke snelheid waaruit de ondergrens van NPSH_r volgt.

**De drie getallen, en wat er voor elk wel en niet onderbouwd is:**

| Grootheid | Gebruikt | Onderbouwing | Status |
|---|---|---|---|
| NPSH_r | 2,0 m | Ondergrens uit de zuig-specifieke snelheid: Nss = n·√Q / NPSH_r^0,75 (Amerikaanse eenheden: tpm, gpm, ft), met Nss = 8500, de bovengrens die het Hydraulic Institute voor de meeste toepassingen aanbeveelt. Bij n = 2900 tpm en Q = 10 m³/h (44 gpm) volgt NPSH_r = (2900·√44 / 8500)^(4/3) = 3,0 ft = 0,9 m. Bron van de formule en de grens: Pumps & Systems, "The Basics of Suction Specific Speed" (pumpsandsystems.com/basics-suction-specific-speed) en "Suction Calculators" (J. Evans). Verdubbeld tot 2 m omdat de pompen ver van hun beste werkpunt draaien (bij het ontwerpdebiet 400 kg/h draait P-01 op een fractie van haar capaciteit), en NPSH_r daar stijgt. | **Aanname.** De ondergrens heeft een bron; de factor 2 niet. Er is geen openbare pompkromme van een pomp van deze grootte gevonden. |
| marge | 1,0 m | ANSI/HI 9.6.1, *Rotodynamic Pumps Guideline for NPSH Margin* (Hydraulic Institute), uitgave 2012, tabel voor procespompen in de petroleum- en koolwaterstofindustrie: een verhouding van 1,1, of 1,0 m, "select the greater". Bij NPSH_r = 2 m is 1,1 × 2 = 2,2 m, dus 0,2 m marge; 1,0 m is groter. | **Bron: ANSI/HI 9.6.1-2012.** De norm zelf is betaald en niet ingezien; de waarde uit de tabel van 2012 is gelezen in de achtergrond hieronder. De uitgaven van 2017 en 2024 rekenen de marge op NPSH_R in plaats van op NPSH3; of 1,0 m daar ongewijzigd staat, is niet nagegaan. |
| h_verlies | 0,3 m | Eigen schatting met Darcy-Weisbach (h = f · L/D · v²/2g plus de weerstand van een zeef). Voor P-02: DN80, 8 m zuigleiding en een zeef, ongeveer 0,16 m. Voor P-01 hangt het af van de viscositeit van de pitch bij 240 °C, en daarvoor is geen bron gevonden; 0,3 m dekt P-02 ruim en P-01 alleen bij een lage viscositeit. | **Aanname, geen bron.** |

**Achtergrond, geen bron:** amarineblog.com, "NPSH – How much is enough?" (2017). Het artikel neemt de
tabel van ANSI/HI 9.6.1-2012 over; daar is de marge van 1,0 m gelezen. Het telt niet als bron: de bron is
de norm.

**De som:** 2,0 + 1,0 + 0,3 = 3,3 m, naar boven afgerond op 4 m. De 0,7 m daarboven dekt een vervuilde
zeef en gas dat in de zuigleiding vrijkomt.

**Ter vergelijking** (geen bron voor het getal, wel voor de orde van grootte): een voorbeeld uit het
leidingontwerp gebruikt minimaal 1,8 m (pipingengineer.org); een raffinaderijcasus noemt ongeveer 7 m
bij veel grotere pompen (forumbericht, anekdotisch).

**Geldigheid.** Centrifugaalpompen bij n ≈ 2900 tpm en een debiet in de orde van 10 m³/h, enkelzijdig
aangezogen, met een vloeistof op haar kookpunt. **Niet voor P-01 als die een verdringerpomp is**: een
centrifugaalpomp voor 0,5 tot 1 m³/h viskeuze pitch is ongebruikelijk, en een verdringerpomp heeft
een andere NPSH-eis (NPIP, in druk in plaats van hoogte).

**Waar het getal staat:**
- in `src/physics/pomp.js`: `ZUIGZIJDE_P01` en `ZUIGZIJDE_P02`, elk een eigen object, en `zuighoogte()`,
  die NPSH_a = 4 − 0,3 = 3,7 m uitrekent tegen NPSH_r + marge = 3,0 m;
- op K-01 bodem en K-02 bodem bij de maatlijn: "h_statisch 4,0 m". De maatlijn loopt van de
  laag-laaggrens tot de hartlijn van de pomp, want daar geldt het getal;
- in de uitleg van `WET_NPSH`, in het waarom-paneel van P-01 en P-02. De NPSH-regel zelf staat niet op
  het procesbeeld, maar alleen daar.

### 26.28 Het filter op de niveaumeting van K-01, K-02 en V-02 (`WET_METINGFILTER`, regelalgoritme)

Bij de lambda-afstemming (16.5) gaf de sterkere afstemming (K_p vijf keer zo groot) de meetruis van de
niveaumeting vijf keer zo hard door naar
de klep: met de ruis van model 2 (σ ongeveer 1 procentpunt per scan) een σ van 4,7 procentpunt op de
klepuitgang en 58 kg/h op de afvoer, tegen 0,9 procentpunt en 12 kg/h met de oude afstemming.

**De regel.** Een eerste-ordefilter op de meting van de regelaar, en alleen daar:

  τ_f · d*y*_f/d*t* = *y* − *y*_f,   per scan exact: *y*_f(k) = *y*_f(k−1) + (1 − e^(−Δt/τ_f))·(*y*(k) − *y*_f(k−1))

met τ_f = 20 s (`FILTERTIJD_LIC_K01_S`, `filterStap()` in `regelaar.js`). De tag, de faceplate en de
trend tonen de meting zelf; de beveiligingen lezen hun eigen meting, zonder filter. In HAND volgt het
setpoint de gefilterde meting, zodat het terugschakelen naar AUTO stootvrij blijft.

**Waarom 20 s.** Een eerste-ordefilter vertraagt met ongeveer τ_f. Tegen de sluittijd van de kring
(λ = 20 min) is dat een zestigste: de kring zelf verandert nauwelijks, de snelle onrust wel. Een
filtertijd die niet klein is tegen λ, vertraagt de kring en kan haar laten slingeren.

**Herkomst.** Een ontwerpkeuze, geen meting en geen bestaande installatie. Het filter zelf is een
standaardelement van een regelaar of een transmitter (demping); de vorm is de eerste-ordevergelijking
hierboven. De ruis van model 2 is zelf een aanname van het model (`ruis.js`).

**Gemeten in model 2.**

| | zonder filter | met filter (20 s) |
|---|---|---|
| σ van de meting, meetruis aan, een uur | 1,0 procentpunt | 1,0 procentpunt (de tag is de meting zelf) |
| σ van wat de regelaar gebruikt | 1,0 procentpunt | 0,2 procentpunt |
| σ van de klepuitgang | 4,7 procentpunt | 0,8 procentpunt |
| daling na een hoog-hoogtrip bij SP 50, λ = 20 min | 7,1 procentpunt | 7,1 procentpunt (verschil < 0,5) |
| LV op 100 % bij tien procent meer voeding | 840 s | 890 s |
| laagste niveau na het terugkomen van LV (SP 50 %) | 48,36 % | 48,36 % |

De daling na een trip voor λ = 15, 20 en 30 min, met het filter: 5,4, 7,1 en 10,6 procentpunt.

**K-02.** Hetzelfde filter, met dezelfde vergelijking (`filterStap()`), maar **in de transmitter** van
LT K-02 (`FILTERTIJD_LT_K02_S`, de demping in `transmitter.js`): de tag `K02.LT.PV` is de gedempte
meting, en die lezen de regelaar, de faceplate, de trend, het beeld "K-02 bodem" en het overzicht. De
regelaar van K-02 filtert niet nog een keer. De beveiligingen van K-02 (LSHH, LSL en LSLL) hebben hun
eigen opnemers (`K02.LSLL.PV`, `K02.LSHH.PV`, `WET_EIGEN_METING`), zonder demping. Bij K-01 tonen de
faceplate en de trend de meting zelf; bij K-02 de gedempte. Dat verschil is een ontwerpkeuze, en de
uitleg op het scherm zegt het.

Na een sprong Δ*y* volgt de gedempte meting *y*_f = *y*_0 + Δ*y*·(1 − e^(−*t*/τ_f)): na τ_f 63 procent, na 3·τ_f
95 procent. Dat is de staprespons van een eerste-ordesysteem; zie bijvoorbeeld D. E. Seborg, T. F. Edgar en D. A.
Mellichamp, *Process Dynamics and Control* (Wiley), het hoofdstuk over eerste-ordesystemen, en daar ook het
exponentiële filter op een meting. **Uit het hoofd vermeld, niet opnieuw in het origineel nagelezen.**

**Gemeten in model 2** (λ = 8 min): de klepruis van LIC K-02 (σ, meetruis aan,
een half uur) 2,0 procentpunt zonder demping, 0,3 met; na de hoog-hoogtrip van K-02 zakt het niveau vanaf SP 50
tot 33,1 % (zonder demping 33,5), vanaf SP 40 tot 23,1 % (23,5). Springt het niveau in een stap over een grens,
dan trippen de hoog-hoog, de laag en de laag-laag binnen twee scans, terwijl de gedempte meting de grens nog niet
heeft bereikt.

**V-02.** Hetzelfde filter als bij K-02, in de transmitter van LT V-02 (`FILTERTIJD_LT_V02_S` = 20 s): de tag
`K02.V02.LT.PV` is de gedempte meting, en die lezen de regelaar, de faceplate, de trend, het beeld "K-02 top" en het overzicht.
De hoog-hoog (met haar hoog-alarm) en de laag-laag van V-02 hebben hun eigen opnemers (`K02.V02.LSHH.PV`, `K02.V02.LSLL.PV`),
zonder demping. De sluittijd van de vatkring is 4 min (26.29), twaalf keer de filtertijd.

**Waarom.** Zonder filter gaf LIC V-02 de meetruis van het vatniveau door aan klep LV, dus aan het destillaat, en via CAS aan het
setpoint van de FIC (26.32). **Gemeten in model 2** (meetruis aan, zaad 7 en 11, een seconde per stap; zonder en met het filter):

| | zonder filter | met filter (20 s) |
|---|---|---|
| σ van klep LV, 1200 tot 3600 s | 0,77 en 0,75 procentpunt | 0,12 en 0,14 procentpunt |
| σ van het echte destillaat, 1200 tot 3600 s | 109 en 107 kg/h | 17 en 19 kg/h |
| σ van het setpoint van de FIC, 1200 tot 3600 s | 66 en 73 kg/h | 48 en 64 kg/h |
| σ van de verandering van dat setpoint per seconde | 22 kg/h | 8 kg/h |
| σ van het setpoint, 120 tot 600 s (zaad 7) | 96 kg/h | 92 kg/h |
| hoogste vatniveau na de stap 40 → 55 %, reflux vast / in CAS | 62,0 / 58,9 % | 62,3 / 60,0 % |
| alarm tot trip, LV dicht | 11,3 min | 11,3 min |

Het filter haalt de snelle onrust uit het destillaat; het setpoint van de FIC schommelt per seconde nog maar een derde zo veel.
Zijn sigma over een half uur zakt minder, omdat wat overblijft langzaam is: het gemiddelde van het destillaat verschuift per
blok van ruim zes minuten een tiental kg/h, maal R ≈ 3,8. Die langzame beweging volgt het setpoint, zoals de verhouding hoort te doen;
een filter van 20 s haalt haar er niet uit. Springt het vatniveau in een stap over een grens, dan komen het hoog-alarm, de
hoog-hoog en de laag-laag van V-02 binnen twee scans, terwijl de gedempte meting de grens nog niet heeft bereikt.

### 26.29 De inhoud van refluxvat V-02 en de afstemming van de vatkring (ontwerpkeuzes)

De inhoud van V-02 is een ontwerpkeuze met een afleiding, inhoud = verblijftijd × debiet, met een gangbare verblijftijd uit
openbare bronnen; geen gegevens van een bestaande installatie.

**De wet**: behoud van massa over het vat,

    dM/dt = V_cond − L − D        [kg/s]

met M de inhoud van V-02 [kg], V_cond het condensaat uit E-04 (in het ontwerppunt de hele damp van K-02), L de reflux en
D het destillaat [kg/s].

**De inhoud.** De verblijftijd is wat er in het vat staat, gedeeld door wat erdoorheen gaat:

    τ = M(50 %) / (L + D)        →        M(50 %) = τ · (L + D)

| Grootheid | Waarde | Herkomst |
|---|---|---|
| L + D in het ontwerppunt | 21 041 kg/h (L 16 673, D 4 369, R = 3,817) | model 1, `installatie.js` |
| τ bij half vol | 5 min (`VERBLIJFTIJD_REFLUXVAT_K02_S` = 300 s) | vuistregel, zie hieronder |
| M(50 %) | 5/60 · 21 041 = **1753 kg** | afgeleid |
| M_max (vol) | **3507 kg** | 2 · M(50 %) |
| vroeger (tot v1.1.5) | 1275 kg bij half vol, 2550 kg vol: 3,6 min | zonder bron, vervallen |

**De bron van de vijf minuten.** De "rules of thumb" voor vaten (*Vessels (Drums)*) in de samenvatting vooraan in:
- S. M. Walas, *Chemical Process Equipment: Selection and Design*, Butterworths Series in Chemical Engineering, Butterworths,
  Boston, 1988 (herdruk Butterworth-Heinemann, 1990, ISBN 0-7506-9385-1);
- J. R. Couper, W. R. Penney, J. R. Fair en S. M. Walas, *Chemical Process Equipment: Selection and Design*, derde druk,
  Butterworth-Heinemann (Elsevier), Waltham MA, 2012, ISBN 978-0-12-396959-0.

De regel: vijf minuten verblijftijd bij een half vol refluxvat; vijf tot tien minuten voor een vat dat een volgende kolom
voedt. **Gelezen in secundaire bronnen** die de lijst overnemen, niet in het boek zelf; de uitgavegegevens zijn nagezocht in
bibliotheekcatalogi en bij de uitgever. Het is een vuistregel voor het ontwerp, geen meting; een echt vat kan groter zijn.

**Geldigheid.** Een refluxvat dat reflux en destillaat levert, bij het ontwerpdebiet. Buiten het ontwerppunt verandert de
verblijftijd met L + D; de inhoud blijft dezelfde.

**De afstemming van de vatkring** (`MODELAANNAMEN.lambda_vatniveau_K02_s` = 240 s). Dezelfde lambda-regel als de
bodemkringen (16.5), met een versterking erbij: de kring stuurt het destillaat, en de reflux volgt het destillaat in een
vaste verhouding. Een eenheid meer destillaat haalt dus 1 + R eenheden uit het vat:

    K_p = 2 · M_max / (g · λ),    T_i = 2 · λ,    g = 1 + R        (`lambdaAfstemmingNiveau(M_max, λ, g)`)

Met M_max = 3507 kg, R = 3,817 en λ = 4 min: **K_p = 21 843 kg/h per eenheid niveau, T_i = 8 min**, kritisch gedempt
(ζ = 1).

**Waarom λ = 4 minuten.** Gekozen op de daling na een uitval van de verwarming van K-02: dan komt er niets meer in het vat,
en zakt het niveau met de lineaire kring

    Δh = V · λ / (e · M_max)

met V de damp die wegvalt [kg/s] en e = 2,718. Vanaf 50 % tot 35,2 %, vanaf 40 % tot 25,2 %. In model 1 nagerekend: 14,8
procentpunt gezakt tegen 14,7 voorspeld. Een kortere λ zakt minder maar geeft meer klepbeweging op de meetruis; een langere
zakt verder.

**Gevolg voor model 1.** De langste tijdstap die model 1 toestaat, volgt uit T_i van deze kring: 60 s.

### 26.30 Refluxpomp P-04, zijn twee takken en de kleppen FV en LV (`WET_WERKPUNT_TWEE_TAKKEN`, afgeleide methode; ontwerpkeuzes)

**Met de dichtheid uit hoofdstuk 4**: P-04 rekent met het condensaat bij de toptemperatuur van K-02, 745,2 kg/m³. De massastromen hieronder hangen daar niet van af: de kleppen zijn in het ontwerppunt gedimensioneerd met de dichtheid van hun eigen vloeistof. De ontwerpstroom door de pomp, 25 250 kg/h, is 33,9 m³/h (149,2 gpm).

P-04 is het dertiende apparaat. Alle getallen hieronder zijn **ontwerpkeuzes**, geen gegevens van een
bestaande pomp, leiding of klep.

**De wet** (`WET_WERKPUNT_TWEE_TAKKEN`, afgeleide methode): het werkpunt van 26.11, met een tak meer. Door de pomp gaat wat door de
drie takken samen gaat, en elke tak ziet dezelfde drukopbouw:

    Δp_pomp(Q) = Δp_stat,R + c_R·Q_R² + Δp_FV(Q_R)  =  Δp_stat,D + c_D·Q_D² + Δp_LV(Q_D)
    Q = Q_R + Q_D + Q_min

met Δp in Pa, Q in m³/s, c in Pa·s²/m⁶; Δp_klep uit de klepvergelijking (`WET_KLEPVERGELIJKING`). Opent de ene klep, dan
zakt de drukopbouw en gaat er door de andere tak iets minder: de twee kleppen merken elkaar. Bij FV op 80 % en LV op 50 %
gaat het destillaat van 4 369 naar 4 052 kg/h.

**De keuzes** (`ONTWERP_P04`, `ONTWERP_FV_K02`, `ONTWERP_LV_V02`):

| Grootheid | Waarde | Reden |
|---|---|---|
| Δp_stat refluxtak | 0 bar | de top van K-02 staat op de druk van V-02; hoogteverschillen aan de perszijde kent het model niet (zie "Welke hoogte het model kent" hieronder) |
| Δp_stat destillaattak | 1 bar | V-02 onder diep vacuüm, de opslag op atmosferische druk; afgerond |
| leidingweerstand | 1 bar bij twee keer de ontwerpstroom, per tak | zoals P-01 en P-02 |
| FV | gelijkprocentueel, R = 50, half open bij het ontwerp, 2 bar drukval (Kv 107) | zoals de andere regelkleppen |
| LV | gelijkprocentueel, R = 50, half open bij het ontwerp, 1 bar drukval (Kv 40) | idem |
| pomp | 2,25 bar bij het ontwerp, 2,33 bar bij nul debiet; capaciteit twee keer de ontwerpstroom | zoals P-01 en P-02 |
| minimumdebiet | een vijfde van L + D: 4 208 kg/h, terug naar V-02 | idem |

**Wat eruit volgt** (de massastromen; zij hangen niet af van de dichtheid):

| | reflux L | destillaat D |
|---|---|---|
| ontwerp, beide kleppen half open | 16 673 kg/h | 4 369 kg/h |
| FV helemaal open, LV half | 44 268 kg/h | 3 891 kg/h |
| LV helemaal open, FV half | 16 550 kg/h | 9 339 kg/h |
| regelbereik (`regelbereikP04`, kleinste regelbare stroom tot open) | 2 806 tot 44 268 kg/h | 770 tot 9 339 kg/h |
| versterking in de installatie bij half open | 59 666 kg/h per eenheid stand | 14 240 kg/h per eenheid stand |

Beide kleppen helemaal open valt buiten het bereik van de pompkromme; het model zegt dat (`binnenBereik`).

**De zuigzijde** (`ZUIGZIJDE_P04`). Dezelfde wet en dezelfde getallen als P-01 en P-02 in 26.27: het condensaat
in V-02 staat op zijn kookpunt, want het vat hangt aan het vacuümsysteem; dus NPSH_a = h_statisch − h_verlies. Met
h_statisch = 4 m, gemeten bij de laag-laag van V-02, en h_verlies = 0,3 m: NPSH_a = 3,7 m, tegen NPSH_r + marge = 2,1 + 1,0
= 3,1 m (ANSI/HI 9.6.1); 0,6 m over. h_verlies is dezelfde aanname als in 26.27.

**NPSH_r van P-04: 2,1 m, een ontwerpkeuze met een afleiding.** Dezelfde weg als in 26.27, met de stroom van P-04:
- de ontwerpstroom door de pomp is reflux plus destillaat plus het minimumdebiet: 21 042 + 4 208 = 25 250 kg/h, bij
  ρ = 745,2 kg/m³ (het condensaat in V-02, hoofdstuk 4) 33,9 m³/h, dat is 149,2 gpm;
- de zuig-specifieke snelheid Nss = n·√Q / NPSH_r^0,75 (Amerikaanse eenheden: tpm, gpm, ft), met Nss = 8500, de bovengrens die
  het Hydraulic Institute voor de meeste toepassingen aanbeveelt, en n = 2900 tpm (bron: Pumps & Systems, als in 26.27);
- dan NPSH_r ≥ (2900 · √149,2 / 8500)^(4/3) = 6,71 ft = **2,04 m**, de kleinste NPSH_r die een pomp met haar beste werkpunt
  bij deze stroom redelijkerwijs heeft;
- **de keuze: 2,1 m**, naar boven afgerond op een tiende. De pomp is gekozen met haar beste werkpunt bij de ontwerpstroom (een ontwerpkeuze,
  geen gegeven van een bestaande pomp); anders dan bij P-01 hoeft er daarom niet verdubbeld te worden, want P-04 draait in het
  ontwerppunt bij dat beste punt. Ter vergelijking: voor P-02 (10 m³/h) geeft dezelfde weg 0,91 m.

De eis blijft: NPSH_a = 3,7 m tegen NPSH_r + marge = 2,1 + 1,0 = 3,1 m, 0,6 m over. De verhouding NPSH_a / NPSH_r is 1,76. ANSI/HI 9.6.1 (uitgave 2012, de tabel van 26.27) vraagt een verhouding van 1,1 of 1,0 m, de grootste van de twee: 1,1 × 2,1 = 2,31 m tegen 2,1 + 1,0 = 3,1 m, dus geldt de 1,0 m, en die is gehaald. De norm zelf is niet ingezien (26.27). **Geldigheid: het ontwerppunt.** Boven het beste
werkpunt stijgt NPSH_r, en de weerstand van de zuigleiding stijgt met het kwadraat van de stroom. Met FV helemaal open gaat er
ongeveer twee keer de ontwerpstroom door P-04. **Het model toetst de NPSH alleen in het ontwerppunt**, niet bij elke stroom: een
FV die helemaal open staat, geeft in het model geen cavitatie.

**De modelgrens in het waarom-paneel van P-04** (`zuighoogteBijStroom`, `SCHAAL_ZUIGHOOGTE`, `grensStroomZuighoogte` in
`pomp.js`; alleen voor de uitleg, niet in het werkpunt of een beveiliging):

    h_verlies(Q) = h_verlies · (Q/Q_0)²          NPSH_r(Q) ≥ NPSH_r · (Q/Q_0)^(2/3)          NPSH_a(Q) = h_statisch − h_verlies(Q)

met Q_0 de ontwerpstroom door de pomp (33,9 m³/h). De eerste regel is Darcy-Weisbach bij turbulente stroming. De tweede is een
**ondergrens uit de zuig-specifieke snelheid** hierboven, S = N·√Q / NPSH_r^(3/4): bij een vaste S en een vast toerental is
NPSH_r ∝ Q^(2/3). Die relatie geldt bij het beste werkpunt van een pomp; voor een en dezelfde pomp boven haar beste punt stijgt
NPSH_r sneller, hoeveel staat op het gegevensblad. De uitkomst: de marge (NPSH_a − NPSH_r − 1 m) is op bij **ongeveer 1,3 keer** de
ontwerpstroom (1,30); met FV helemaal open (LV half) gaat er 69,9 m³/h door P-04, **2,1 keer** (2,06), en dan is h_verlies 1,3 m,
NPSH_a 2,7 m en NPSH_r **minstens 3,4 m** (3,40). Met de ondergrens is de marge dus al op; met de werkelijke kromme eerder. Ter
vergelijking: een pomp met haar beste punt bij 69,9 m³/h vraagt langs dezelfde weg als hierboven minstens 3,31 m (vanaf 2,04 m in
plaats van de gekozen 2,1 m).

**Welke hoogte het model kent.** Eén hoogte: de vloeistof boven de pomp aan de zuigzijde, 4 m bij de
laag-laag, en die telt alleen in de
NPSH, niet in het werkpunt. Aan de perszijde kent het model geen hoogte: de refluxtak heeft Δp_stat = 0, dus de reflux naar
de top van K-02 vraagt in het model alleen de weerstand van de leiding en van FV. In een echte installatie staat V-02 meestal
een stuk lager dan de top van K-02, en levert P-04 dat hoogteverschil er ook nog bij. Dat staat in de aantekening bij P-04,
in zijn waarom-paneel (`P04_AANTEKENING`).

**Modelgrens.** In het procesmodel (model 1) is P-04 geen pomp: reflux en destillaat zijn daar twee stromen. Model 2 maakt er
een pomp van en geeft de twee stromen langs het HAND-pad van de reflux- en de vatkring aan het procesmodel, zoals bij P-02.

### 26.31 De beveiligingen van refluxvat V-02 (`WET_LSHH_V02`, `WET_LSLL_V02`, regelalgoritmes)

De hoog-hoog van V-02 zet E-03 uit, de bron van het condensaat; de laag-laag zet P-04 uit en stopt
daarmee de reflux; met een reset met voorwaarden en een eerste-tripmelding.

| | trip | alarm | reset | actie | resetvoorwaarde |
|---|---|---|---|---|---|
| hoog-hoog V-02 | 90 % | 70 % | onder 65 % | E-03 uit | afvoer beschikbaar: P-04 draait en de vatkring in AUTO of LV open |
| laag-laag V-02 | 10 % | — | boven 20 % | P-04 uit (reflux en destillaat weg) | niveau boven de resetgrens; P-04 start niet vanzelf |

**Herkomst van de grenzen.** Overgenomen van de bodems (26.25), als ontwerpkeuze: geen eigen bron. De zuighoogte van P-04 is
gemeten bij de laag-laag (26.30). **Het hoog-alarm op 70 procent**, zodat er tussen
alarm en trip ruim de ingreeptijd zit (hieronder). De resetgrens volgt uit de regel `WET_LSHH_GRENS` (h_reset < h_alarm): 65
procent, vijf procentpunt onder het alarm, zoals bij de bodems.

**De tijd van het alarm tot de trip** (`WET_OPHOPING_V02`, afgeleide methode; in het waarom-paneel van LIC V-02, met de getallen):

    t = (h_trip − h_alarm) · M_max / D = (0,90 − 0,70) · 3507 kg / 4369 kg/h = 0,161 h ≈ 9,6 min

met de afvoer dicht en de reflux op een vast debiet, zodat het vat netto het destillaat krijgt (26.29). In model 2 gemeten,
met E-03 op zijn ontwerpduty: 11,3 minuten (met de olieklep van E-03 op een vaste stand 4,7 minuten, zie 26.33).
**De ingreeptijd**: een minimale aanvaardbare responstijd van meer dan vijf minuten voor een ingreep
vanuit de controlekamer (Bridges e.a., CCPS Global Congress on Process Safety, 2017; 26.29),
in de code `INGREEPTIJD_CONTROLEKAMER_S` = 300 s. 9,6 minuten is bijna twee keer zoveel.

**De grenzen van het setpoint van LIC V-02: 40 tot 55 procent** (`SP_GRENZEN_NIVEAU.V02`). De regel
`WET_SP_GRENZEN_NIVEAU` (26.26): ook de grootste stap die een operator kan zetten, blijft onder het hoog-alarm.
Daarbij twee eisen die er hier bij komen: de bovengrens ligt onder de resetgrens van de hoog-hoog (65), zodat een setpoint dat
in HAND de meting volgde (`WET_SP_VOLGEN`) het vat na een trip niet boven de reset houdt; en de stap moet ook
met de reflux vast (de FIC in AUTO) onder het alarm blijven. De doorschot van de lineaire kring: met de reflux in CAS ζ = 1,
13,5 procent van de stap; met de reflux vast ζ = 0,46, 32,5 procent. Van 40 naar 55: in de lineaire kring tot 57,0 procent (CAS) en 59,9 procent (vast).
**In model 2 gemeten**: met de reflux in CAS tot 58,9 procent, met de reflux vast tot **62,0 procent**, acht procentpunt onder het
alarm; met het filter op LT V-02 (26.28) tot 60,0 en 62,3 procent: de doorschot is in de installatie groter dan in de lineaire
kring, zoals 26.26 bij K-02 al zag. De uitleg van de setpointgrenzen noemt de getallen.

**Waarom de hoog-hoog er is.** Een vol refluxvat stuwt het condensaat terug in E-04: de condensor verliest oppervlak, de
topdruk loopt op, en vloeistof kan meegaan naar het vacuümsysteem. Wat het vat vult, is de damp van K-02, uit E-03.

**Wat er daarna gebeurt** (met de getallen uit de uitleg naast de simulatie):
- na de hoog-hoog van V-02 verdampt de bodem van K-02 niet meer, terwijl de voeding en de reflux er nog in lopen: binnen
  enkele minuten (gemeten 3,2, met de kring van V-02 na de trip in AUTO) volgt de hoog-hoog van K-02 (26.25), die de voeding van
  K-01 en de reflux stopt en E-01 uitzet. De weg eruit (besluit 2 van de gebruiker, 22 november 2026; in de uitleg en bij de TIC van
  K-02; groep 261 E): in dit model kan V-02 eerst. Met de kring van V-02 in AUTO zakt het vat onder de resetgrens van 65 procent,
  terwijl de hoog-hoog van K-02 al staat en nog niet gereset kan worden. Daarna brengt de niveaukring van K-02 de bodem onder 75
  procent (gemeten 1,7 minuut later), en kan ook die hoog-hoog gereset worden. Pas als beide gereset zijn, is E-03 vrij;
- na de laag-laag van V-02 stopt de reflux, de bodem van K-02 loopt leeg: binnen ongeveer een minuut (gemeten 1,2) volgt
  de laag op E-03. V-02 loopt daarna weer op tot ongeveer 29 procent, boven de resetgrens.

**Geldigheid.** Een keuze van deze installatie. Een andere installatie kan de hoog-hoog op de reflux of het destillaat laten
aangrijpen in plaats van op de verwarming.

### 26.32 De debietkring van de reflux: afstemming, CAS en de terugkeer na een trip (ontwerpregels)

Een debietregelaar met HAND, AUTO en CAS, setpoint = R · D. De toptemperatuur krijgt geen kring, maar
wordt als modelgrens benoemd.

**De meting.** Het refluxdebiet als aanvullende meting van model 2 (`MEETRUIS_BEDIENING.L_K02`), een meetflens, met een ruis
van een procent van de meetwaarde (relatief), zoals de voeding en de productstromen van model 1 (hoofdstuk 25). In het ontwerppunt
is dat ongeveer 170 kg/h. Bereik 1,25 × de stroom met FV open. **Tot 24 november 2026** was de ruis absoluut, 170 kg/h:
bij een stilstaande reflux zakte de meting dan de helft van de tijd onder nul, meldde de transmitter "buiten bereik" en viel de
debietregelaar steeds naar HAND met "de meting is slecht" (besluit 5 van de gebruiker, 24 november 2026). Een meetflens meet
een drukverschil dat met het kwadraat van de stroom gaat; zonder stroom is er geen drukverschil en geen onrust. De meting staat niet in `MEETRUIS` van model 1, zodat model 1 niet verandert.

**De afstemming** (`WET_AFSTEMMING_DEBIET`, `AFSTEMMING_DEBIET`). Een debiet door een klep reageert in dit model binnen een
scan: het proces is een versterking k zonder traagheid (k = 59 666 kg/h per eenheid stand bij half open, 26.30). Een
PI-regelaar met een lusversterking a = K_p · k = 0,3 en T_i = 10 s geeft een gesloten kring met

    τ_cl = T_i · (1 + a) / a ≈ 43 s.

Per scan Δt is de kring een tweede-ordevergelijking z² − (1 − a − b)·z − a = 0 met b = a·Δt/T_i; stabiel als beide wortels
binnen de eenheidscirkel liggen. De grootste wortel: 0,998 bij Δt = 0,1 s, 0,977 bij 1 s, 0,888 bij 5 s, 0,783 bij 10 s,
de langste scan die de klokken toestaan. Een sterkere afstemming (a = 0,6, T_i = 3 s) is bij 10 s instabiel.
**Herkomst:** een keuze, geen gemeten kring. Een echte debietkring heeft een klep met een looptijd en een meting met demping,
en wordt vaak met een kleinere T_i en een lagere versterking ingeregeld.

**CAS** (`WET_VERHOUDING`). In CAS is het setpoint R maal het gemeten destillaat, uitgerekend door de refluxregeling van
model 1 (niet opnieuw). Buiten CAS volgt R het setpoint (SP/D), zodat de omschakeling naar CAS stootvrij is;
R zetten kan alleen in CAS. Wordt de meting van het destillaat onbruikbaar, dan valt de kring terug naar AUTO met het
setpoint waar het stond. Grenzen van R: van R_min van Underwood voor de ontwerpscheiding (2,94) tot twee keer de
ontwerpverhouding (7,63), de capaciteit van de refluxtak.

**Het filter op het destillaat in CAS** (`WET_FILTER_CAS`, regelalgoritme). Zonder filter sprong het setpoint in CAS met de
meetruis van het destillaat mee: een procent ruis op ongeveer 4 400 kg/h,
maal R ≈ 3,8, is ruim 160 kg/h (een sigma), op de schermen 16 465 tot 17 507 kg/h binnen enkele seconden. Daarom rekent CAS met een
eerste-ordefilter op het destillaat, dezelfde regel als 26.28:

    τ_f · dD_f/dt = D − D_f;   per scan D_f(k) = D_f(k−1) + (1 − exp(−Δt/τ_f)) · (D(k) − D_f(k−1));   SP = R · D_f

met τ_f = 20 s (`FILTERTIJD_D_CAS_S`), dezelfde filtertijd als de transmitter van K-02. Van witte ruis blijft na het filter
√(α/(2−α)) over, met α = 1 − exp(−Δt/τ_f): bij een scan van een seconde 0,158, ongeveer een zesde. Ook buiten CAS volgt R het
setpoint gedeeld door D_f, zodat de omschakeling naar CAS stootvrij blijft. De meting zelf, de faceplate en de trend blijven
ongefilterd. Een echte verandering van D komt na ongeveer τ_f in het setpoint: kort tegen de sluittijd van de vatkring (λ = 4 min).
**Gemeten in model 2** (met de meetruis aan): met dit filter schommelt het setpoint 96 kg/h (sigma), zonder ongeveer 440. Dat is
meer dan een zesde, omdat het destillaat zelf schommelde (109 kg/h tegen een meetfout van 43): LIC V-02 gaf de meetruis van het
niveau door aan LV. Daarom staat er ook een filter van 20 s op de meting van LIC V-02 (26.28, met de getallen erna). Van de
meetfout op D (gemeten min echt) blijft in het setpoint ongeveer een zesde over, zoals de formule zegt.

**De terugkeer na de hoog-hoog van K-02** (`WET_REFLUX_NA_TRIP`). De reflux stopt zolang de trip staat en
loopt na de reset weer. Zolang de trip staat, staat de FIC in HAND met de uitgang op nul, en sluit het veld FV. Na de reset
gaat de FIC terug naar haar mode van voor de trip (CAS of AUTO), stootvrij vanaf dicht; de uitleg zegt het. Het alternatief,
dat de FIC in HAND blijft en de operator de reflux zelf terugzet, staat in de uitleg als wat een andere installatie kan doen.

### 26.33 Reboiler E-03 en de temperatuurkring van K-02 (`reboiler.js`)

**Dezelfde regels als E-01, per reboiler gelezen.** `reboiler.js` kent twee reboilers (`REBOILERS`: E-01 bij
K-01, E-03 bij K-02). Wat per reboiler verschilt, leest `reboilerOntwerppunt(id)` uit de meting van de eigen kolom, in een stap
van het procesmodel zonder ruis. Het procesmodel van model 1 is daarvoor niet veranderd.
- **De klep:** `WET_REBOILERKLEP`, Q = h · Q_max (26.16). Dezelfde lineaire klep, met dezelfde reden.
- **Het mediumplafond:** `WET_MEDIUMPLAFOND` (26.16). Q_max = Q_ontwerp · 1,25 · (T_olie − T_bodem) / (T_olie − T_bodem,ontwerp),
  met de olie op 280 °C en een plafond van 280 − 25 = 255 °C. Voor beide kolommen hetzelfde medium.

| | E-01 (K-01) | E-03 (K-02) |
|---|---|---|
| Ontwerpduty | 841,6 kW | 1799,8 kW |
| Q_max in het ontwerppunt (× 1,25) | 1052,0 kW | 2250,1 kW |
| Klepstand in het ontwerppunt, u0 = Q / Q_max | 0,800 | 0,800 |
| Bodemtemperatuur in het ontwerppunt | 237,8 °C | 250,1 °C |
| Marge tot het plafond (255 °C) | 17,2 K | 4,9 K |

**De afstemming van E-01: gemeten aan model 1** (`afstemmingTIC(id)`). Model 1 regelt de duty met één regelaar
voor beide kolommen: Kp = 60 kW/K (een getal binnen `temperatuurregeling()`) en Ti = 1200 s (`MODELAANNAMEN.Ti_temperatuur_s`).
Model 2 stuurt een klepstand, en de versterking van de klep is dQ/dh = Q_max van de eigen reboiler:

    Kp_klep = Kp_model1 / Q_max,ontwerp

- **E-01:** 60 / 1052,0 = 0,0570 per K, Ti = 1200 s, u0 = 0,800.

De Kp wordt gemeten, niet overgetypt: de regelaar krijgt een afwijking van 1 K en een verwaarloosbare tijdstap, en antwoordt met
Q − Q_ontwerp = Kp · (1 + dt/Ti). De TIC van K-02 heeft een eigen afstemming (hieronder).

**Het alarm op hoge bodemtemperatuur van K-02: het plafond van de olie, met een afgeleide marge** (`alarmgrensBodem('E03')`).
De regel van K-01 (26.18) toegepast op K-02:

    exp( (Ea/R) · (1/T_ontwerp − 1/T_afbraak) ) = 2,   Ea = 127,4 kJ/mol (bovengrens van de band)

- **Uitkomst:** T_ontwerp = 250,1 °C geeft T_afbraak = 262,8 °C, 7,8 K **boven** het plafond van 255 °C.
- **Een grens daarboven komt te laat:** de
  bodemtemperatuur is het kookpunt bij de bodemdruk, en die kan de druk er wel brengen. In het model, met de zuigklep vast op haar
  ontwerpstand en zonder de PAHH, staat de bodem van K-02 een uur na een lek van 20 kg/h (het lekkende systeem van module 7;
  40 mbar) op 255,5 °C, en na een lek van 30 kg/h (60 mbar) op 263,9 °C (de TIC van K-02 in HAND op 80 %, zonder ruis,
  rekenstap 1 s). Wat de olie niet kan, is de bodem
  daar houden met haar ontwerpduty: met de klep helemaal open is Q_max = Q_ontwerp · 1,25 · (280 − T_bodem) / (280 − 250,1)
  (`WET_MEDIUMPLAFOND`), en dat is kleiner dan de ontwerpduty boven 280 − 29,9 / 1,25 = 256,1 °C; op 262,8 °C nog 72 %. Een alarm
  op 262,8 °C zou dus pas komen als E-03 al lang achterloopt.
- **Daarom het plafond met een marge:**

      T_alarm = min( T_afbraak, T_plafond − k · σ ),   k = 3,   σ = 0,2 K

- **σ** is de meetruis van de bodemtemperatuur (Pt100 in een dompelbuis, `MEETRUIS.T_K02` in `ruis.js`).
- **Waarom k = 3:**
  - staat de bodem op het plafond, dan ligt de meting met 99,87 % kans boven de grens, en het alarm staat vrijwel zeker;
  - een setpoint dat zelf drie σ onder de grens blijft, haalt haar maar zelden door de ruis alleen.
- **Voor K-02: T_alarm = 255 − 0,6 = 254,4 °C, met de reden "plafond van de olie".**
- **Voor K-01 verandert er niets:** 249,9 °C ligt onder 254,4 °C, dus blijft de grens die uit Arrhenius (reden "afbraak").
- **Waarom het verschil tussen de kolommen een les is:** bij K-01 komt de afbraak eerder dan het plafond, bij K-02 het plafond
  eerder dan de verdubbeling van de afbraak. Dat volgt uit de kleine marge van K-02, 4,9 K tot het plafond.
- **De bovengrens van het setpoint** ligt dezelfde marge eronder: 254,4 − 0,6 = 253,8 °C.

**Geldigheid en wat open blijft.**
- De marge rust op de meetruis van het model, niet op een gemeten transmitter. De factor 3 is een keuze, met de reden hierboven.
- `WET_ALARM_HOGE_TEMPERATUUR` en de andere regels van `reboiler.js` staan in het register onder E-01. De regel voor het plafond
  van de olie als alarmgrens, `WET_ALARM_PLAFOND_OLIE`, staat onder E-03.

**De bediening van E-03 in model 2.**
- **De olieklep TV K-02** (`veld.js`): een klepactuator zoals die van E-01. De duty is Q = h · Q_max (`WET_REBOILERKLEP`), met
  Q_max uit de vorige stap van het procesmodel; `proces.js` geeft haar aan het procesmodel langs het HAND-pad van de
  temperatuurkring van K-02. `simulatie.js` is niet veranderd. Houdt een beveiliging E-03 tegen, dan zet het veld de klep dicht.
- **De TIC van K-02** (`regeling.js`): dezelfde regelkring als de TIC van K-01, met de afstemming hieronder en de begintoestand
  van model 1: HAND, de klep op 80 %. De grenzen van het setpoint: de onderkant van het meetbereik,
  zoals bij K-01, en als bovengrens `bovengrensSetpointBodem_K('E03')` = 254,4 − 0,6 = 253,8 °C.
- **Na een trip** staat de TIC in HAND op nul, en na de reset blijft zij daar (een reset is geen herstart); zij keert
  niet terug naar haar mode van voor de trip, anders dan de debietregelaar van de reflux (`WET_REFLUX_NA_TRIP`).
- **Zelfregeling.** Met de klep op een vaste stand volgt de duty van E-03 het temperatuurverschil met de olie, zoals bij E-01.
  Waar de bodemtemperatuur van K-02 in het model meteen springt (de verdeling op haar rand), springt de duty mee: stopt de afvoer
  van V-02, dan zakt de bodem in een stap van 250,1 naar 242,4 °C en levert E-03 op 80 % klep 2261 kW in plaats van 1800. Met de
  reflux op een vast debiet (de FIC in AUTO) vult V-02 dan twee keer zo snel: tussen het hoog-alarm en de hoog-hoogtrip van V-02 zit
  4,7 minuut in plaats van 11,3. Dat is minder dan de ingreeptijd van 26.31. Staat de FIC in CAS, zoals bij de start, dan volgt de
  reflux het destillaat en valt zij met de afvoer weg: dan tript eerst de laag op E-03, na 2,4 minuten, en komt V-02 niet boven
  69,8 %. (De afvoer van V-02 dicht na tien minuten rust, de olieklep vast op 80 %, zonder ruis, rekenstap 1 s.)
- **De alarmmarge van V-02.** De marge tussen het hoog-alarm en de hoog-hoog van V-02 is getoetst zoals 26.31 haar afleidt:
  met E-03 op zijn ontwerpduty (de olieklep op Q_ontwerp / Q_max). Dan zit er 11,3 minuut tussen, en de
  uitleg zegt "ongeveer tien minuten". Met de olieklep op een vaste stand en de FIC in AUTO geeft het model 4,7 minuut: de bodemtemperatuur
  van K-02 zakt in een stap 8 K (de verdeling op haar rand; de samenstelling reageert direct), en E-03 levert dan meer (Q = U·A·ΔT). De
  uitleg van `WET_LSHH_V02` zegt dat, en dat een echte kolom geleidelijk zakt.
- **De TAH van K-02 bij een dichte bodemafvoer.** Dezelfde modelgrens: zonder bodemafvoer staat de bodemtemperatuur in een stap op
  255,75 °C, en het alarm komt na drie seconden. De uitleg van `WET_ALARM_PLAFOND_OLIE` zegt het.
- **De tekst van het alarm:** "bij het plafond van de olie: meer duty maakt de bodem niet heter; de bodemtemperatuur volgt de druk
  (Clausius-Clapeyron). Er grijpt niets vanzelf in."

**De afstemming van de TIC van K-02: een staptest en de lambda-regel** (`STAPTEST_E03`, `LAMBDA_TIC_K02_S`,
`afstemmingTICLambda()` in `reboiler.js`; regel `WET_AFSTEMMING_TIC_K02`). `simulatie.js` en de 60 kW/K van model 1 blijven
ongemoeid; E-01 houdt de afstemming van model 1.

*De staptest*, in model 2: de TIC in HAND, zonder ruis, rekenstap 1 s, tien minuten rust, dan de olieklep van 80 % naar een
andere stand; het antwoord een half uur gevolgd, de eindwaarde het gemiddelde over de laatste tien minuten.

| stap | ΔT eind | K [K per klepslag] | K [K/kW] | θ (2 %) | t63 | τ = t63 − θ | piek |
|---|---|---|---|---|---|---|---|
| +0,05 | +0,454 K | 9,08 | 0,0040 | 10 s | 50 s | 40 s | +0,604 K |
| −0,05 | −0,665 K | 13,30 | 0,0059 | 11 s | 57 s | 46 s | −1,003 K |
| +0,02 | +0,234 K | 11,72 | 0,0052 | 10 s | 56 s | 46 s | +0,329 K |
| −0,02 | −0,261 K | 13,05 | 0,0058 | 11 s | 57 s | 46 s | −0,400 K |
| +0,10 | +0,717 K | 7,17 | 0,0032 | 9 s | 51 s | 42 s | +1,047 K |

K per kW is K per klepslag gedeeld door Q_max in het ontwerppunt, 2250 kW. De versterking hangt af van de richting en de grootte
van de stap; vastgelegd zijn **K = 13,5 K per klepslag** (de grootste: dan is de kring nergens te scherp; vastgelegd bij een staptest die
−0,05 op 13,46 gaf, het model van nu geeft 13,30, ruim 1 % minder; de oorzaak is niet uitgezocht, de afstemming blijft), **θ = 10 s** en
**τ = 45 s**. De bodem schiet door tot anderhalf keer haar eindverandering en zakt in tien minuten terug: de samenstelling volgt
de reflux en het destillaat, en die volgen de vatkring van V-02. Een eerste orde is dus een benadering.

*De regel* (lambda- of IMC-afstemming voor een eerste orde met dode tijd):

    **Kp = τ / (K · (λ + θ)),   Ti = min(τ ; 4 · (λ + θ))**

Eenheden: K in K per eenheid klepstand, tijden in s, Kp in klepstand per K. Bronnen: D. E. Rivera, M. Morari en S. Skogestad,
"Internal model control. 4. PID controller design", *Ind. Eng. Chem. Process Des. Dev.* 25 (1986) 252–265; S. Skogestad, "Simple
analytic rules for model reduction and PID controller tuning", *Journal of Process Control* 13 (2003) 291–309 (SIMC, met
τ_c = λ). **Uit het hoofd vermeld, niet opnieuw in het origineel nagelezen**, zoals in 16.5.

*De keuze λ = τ = 45 s* (de gesloten kring even snel als het proces). Vergeleken in model 2, setpointstap van 0,6 K in AUTO,
zonder ruis, rekenstap 1 s, en de klepruis met de meetruis aan (seed 7, rekenstap 1 s: de standaardafwijking van de klepstand over
30 minuten, na tien minuten HAND en tien minuten AUTO):

| afstemming | Kp [per K] | Ti | 90 % van −0,6 K | 90 % van +0,6 K | doorschot | klepruis (σ) |
|---|---|---|---|---|---|---|
| die van model 1 | 0,0267 | 1200 s | niet in 30 min | niet in 30 min | - | 0,63 procentpunt |
| λ = θ = 10 s (SIMC, strak) | 0,167 | 45 s | 40 s | 58 s | 54 % / 25 % | 3,53 procentpunt |
| **λ = τ = 45 s** | **0,0606** | **45 s** | **72 s** | **119 s** | **25 % / 3 %** | **1,29 procentpunt** |
| λ = 3τ = 135 s | 0,0230 | 45 s | 161 s | 798 s | 0 % | 0,58 procentpunt |

Met λ = θ komt de meetruis van 0,2 K versterkt op de klep; met λ = 3τ volgt de regelaar een stap omhoog pas na dertien minuten.

**Wat de afstemming verandert, met de TIC in AUTO** (de kring begint in HAND, zoals model 1, en gaat na tien minuten in AUTO;
de trip tien minuten later; de FIC in CAS, zonder ruis, rekenstap 1 s; "in plaats van" is met de afstemming van model 1):
- *een voedingstrip:* de TIC knijpt E-03 af, de laag van K-02 komt na 1095 s in plaats van 370 s. Na 877 s staat de bodem op
  de rand van de verdeling en springt de bodemtemperatuur heen en weer over het alarm: de TAH van K-02 gaat tot de trip telkens
  aan en uit. Zonder voeding komt het niveau daarna niet meer boven de resetgrens van de laag;
- *de afvoer van K-02 dicht:* de hoog-hoog van K-02 na 151 s in plaats van 360 s (de TIC knijpt E-03 af als de bodem op de rand
  springt, er verdampt minder).

**De dode band op het alarm op hoge bodemtemperatuur** (`WET_DOODBAND_ALARM`, regelalgoritme; `DOODBAND_ALARM_TEMPERATUUR`,
`doodbandAlarmTemperatuur_K()` en `hogeBodemtemperatuur(pv, T_alarm, { doodband_K, stond })` in `reboiler.js`), bij de TAH van
K-01 en van K-02. Het alarm gaat aan als T_bodem > T_alarm, en pas uit als T_bodem ≤ T_alarm − d, met

  **d = max( k · σ ; min( f · (T_max − T_min) ; T_alarm − SP_max ) ),   f = 0,01,   k = 3**

Eenheden: temperaturen en d in K, f en k zonder eenheid, (T_min, T_max) het meetbereik van de temperatuurmeting.

- **De startwaarde** f · (T_max − T_min) is de aanbevolen dode band uit de richtlijnen voor alarmbeheer. Die geven een aanbevolen
  dode band per soort meting, als percentage van het bereik: debiet 5 %, druk 2 %, **temperatuur 1 %** (niveau: 5 % bij de eerste
  vermelding, 2 % bij de tweede). Vermeld door:
  - E. Naghoosi, I. Izadi en T. Chen, "A study on the relation between alarm deadbands and optimal alarm limits", *Proceedings of
    the 2011 American Control Conference*, San Francisco, 2011, tabel 1 ("Standards recommendations for alarm deadbands"), met als
    bronnen EEMUA Publication 191 (2007) en ISA S18.02 (2009); "In guidelines a deadband is defined as a percentage of the
    operating range";
  - C.-A. Musa, "Why should I use an alarm deadband?", blog van een adviesbureau voor functionele veiligheid, 5 november 2020:
    "EEMUA 191 recommended deadband settings", als "percentage of the range".

  **De normen zelf (EEMUA 191, ISA-18.2 / IEC 62682) zijn niet ingezien**; zij zijn niet vrij toegankelijk. De waarde rust op deze
  twee vermeldingen, die voor temperatuur overeenkomen. Geldigheid: een startwaarde; de richtlijnen zeggen dat de dode band bij het
  doorlichten van de alarmen (*rationalisatie*) per alarm op het gedrag van het signaal wordt afgestemd. Als "bereik" geldt hier het
  meetbereik van de transmitter (26.15): 0 tot 400 °C, dus een startwaarde van 4 K.
- **De begrenzing** T_alarm − SP_max: de grens waaronder het alarm uitgaat, zakt nooit onder het hoogste setpoint van de regelaar.
  Een alarm dat in toegestaan bedrijf niet meer uit kan, is niet goed.
- **De ondergrens** k · σ (`ONDERGRENS_DOODBAND_SIGMA`): kleiner laat de meetruis alleen het alarm aan en uit gaan.

**De bovengrens van het setpoint van K-01** volgt dezelfde regel als die van K-02: **SP_max = T_alarm − k · σ**,
k = `MARGE_PLAFOND_SIGMA` = 3, via `bovengrensSetpointBodem_K('E01')`. Een setpoint boven het eigen alarm en boven het plafond van
de olie (255 °C, Q = U·A·ΔT) is niet haalbaar; de TIC zou de olieklep helemaal opendraaien, en de TAH blijft staan. Eén regel voor
beide kolommen. De som: SP_max = 249,9 − 3 · 0,2 = **249,3 °C** (precies 249,317 °C, uit de grens van Arrhenius, 249,917 °C). De
ondergrens blijft de onderkant van het meetbereik, 0 °C.

**De sommen**, met σ = 0,2 K en het meetbereik 0 tot 400 °C (startwaarde 4 K):

| alarm | T_alarm | SP_max | T_alarm − SP_max | min(4 ; …) | d = max(0,6 ; …) | uit onder |
|---|---|---|---|---|---|---|
| TAH K-02 | 254,4 °C | 253,8 °C | 0,6 K | 0,6 K | **0,6 K** | **253,8 °C** (= SP_max) |
| TAH K-01 | 249,9 °C | 249,3 °C | 0,6 K | 0,6 K | **0,6 K** | **249,3 °C** (= SP_max) |

Bij beide kolommen vallen begrenzing en ondergrens samen, omdat SP_max zelf drie keer de meetruis onder het alarm ligt. De
regeling schrijft de grens waaronder het alarm uitgaat als tag (`K01.TAH.UITGRENS`, `K02.TAH.UITGRENS`); de faceplate toont haar
in de rij TAH. `doodbandAlarmTemperatuur_K(bereik, T_alarm, SP_max, σ)` en `doodbandAlarmBodem_K(id, bereik, SP_max)` in
`reboiler.js`; de regeling geeft de bovengrens van het setpoint van haar eigen kring mee.

**Wat de dode band doet, gemeten in model 2:**
- *Voedingstrip met de TIC van K-02 in AUTO* (tien minuten HAND, tien minuten AUTO, dan de trip), zonder ruis, rekenstap 1 s: in het
  uur na de trip gaat de TAH **89 keer** aan, met de startwaarde van 4 K, met de dode band van 0,6 K en zonder dode band (geteld
  als de keren dat de meting de grens van onderen passeert); 79 keer daarvan voordat de laag van K-02 tript. Tot die trip springt de
  bodemtemperatuur tussen 250,14 °C en de rand van de verdeling, 255,75 °C: 5,6 K, meer dan de dode band. Met de meetruis aan
  (seed 7, rekenstap 0,1 s), in hetzelfde uur: 119 keer zonder dode band en met 0,6 K, 109 keer met 4 K. De sprong is de grens van het
  model (de samenstelling reageert direct), niet de ruis. Een dode band die haar opvangt, moet groter zijn dan 5,6 K; bij K-02 ligt
  de grens waaronder het alarm uitgaat dan onder de ontwerptemperatuur, en gaat het alarm in bedrijf niet meer uit. Uit de bron
  komt zo een waarde niet.
- *Een matig lek met de TIC van K-01 in AUTO* (drie keer het ontwerplek, 30 kg/h, na tien minuten AUTO; zonder ruis, rekenstap
  10 s): de bodem van K-01 zakt in twintig minuten tot 249,7 °C, 0,2 K onder de grens. Zonder dode band viel de
  TAH van K-01 daar weg; met de dode band blijft hij staan. De uitleg van het drukalarm zegt het.

**Op het scherm:** een setpoint boven 249,3 °C wordt geweigerd; de faceplate noemt de grenzen met een decimaal ("tussen 0,0 en
249,3 °C"), zoals bij K-02. De regel `WET_ALARM_HOGE_TEMPERATUUR` heeft de formule SP_max = T_alarm − 3·σ en een zin over het
hoogste setpoint in de uitleg. In HAND volgt het setpoint de meting tot 249,3 °C en niet verder (`WET_SP_VOLGEN`).

### 26.34 De vulling van een liggend vat op het schema: inhoud en hoogte (meetkunde)

- **Wat het model rekent.** Het niveau van destillaatvat V-01 en refluxvat V-02 is in het procesmodel een inhoud: de massa in het
  vat gedeeld door de massa van het volle vat. Een echte niveaumeting meet hoogte. Dit verschil blijft in het model; alleen de
  tekening rekent om.
- **Waarom de tekening omrekent.** Het processchema tekent V-01 en V-02 als liggende vaten met ronde kopse kanten. In een liggende
  cilinder lopen inhoud en hoogte niet gelijk op; een vulling die de inhoud als hoogte tekent, staat bij lage inhoud te laag en bij
  hoge te hoog. De tekening toont daarom de hoogte die bij de inhoud hoort.
- **De wet: de oppervlakte van een cirkelsegment** (meetkunde; zij geldt voor elke cilinder). Een cirkel met straal R, gevuld tot
  hoogte h vanaf de onderkant:

      A(h) = R²·arccos((R − h)/R) − (R − h)·√(2Rh − h²)

  **Afleiding.** De koorde op hoogte h snijdt de cirkel onder de middelpuntshoek 2θ, met cos θ = (R − h)/R. De sector heeft de
  oppervlakte R²·θ; de driehoek tussen het middelpunt en de koorde heeft de oppervlakte (R − h)·√(R² − (R − h)²) = (R − h)·√(2Rh − h²).
  Onder het midden (h < R) is het segment de sector min die driehoek; boven het midden is R − h negatief, en telt de driehoek er vanzelf
  bij. Controle: A(0) = 0, A(R) = πR²/2, A(2R) = πR².
- **De inhoudsfractie** van het cilindrische deel is A(h)/(πR²), onafhankelijk van de straal en de lengte. Het model keert dat om door
  halveren (de inhoud stijgt strikt met de hoogte), op 1e-12 van de diameter.

  | inhoud | 10 % | 25 % | 50 % | 75 % | 90 % |
  |---|---|---|---|---|---|
  | hoogte (fractie van de diameter) | 15,7 % | 29,8 % | 50,0 % | 70,2 % | 84,4 % |

- **Geldigheid en aanname.** Een liggende cilinder met een vaste doorsnede; de kopse kanten tellen niet mee (daar loopt de inhoud iets
  anders met de hoogte). Het model kent de afmetingen van de vaten niet, alleen hun inhoud; de omrekening gebruikt daarom alleen de
  fractie, en de tekening de diameter van het getekende vat.
- **Op het scherm:** de vulling van V-01 en V-02 op het processchema blijft binnen de omtrek van het vat, ook in de ronde kopse kanten,
  tot de hoogte uit deze omrekening. De bodems van K-01 en K-02 staan rechtop: daar is de inhoud evenredig met de hoogte, en rekent de
  tekening niet om.

---

### 26.36 De grenzen van de topdruk en de hoog-hoogbeveiliging op de topdruk (`WET_ALARM_HOGE_DRUK`, `WET_PAHH`, regelalgoritmes)

- **De regel.** Een PAHH op de topdruk die de olieklep van E-01 en die van E-03 sluit,
  met een afgeleide grens. Bij een hogere druk koken de bodems heter (Clausius-Clapeyron), en boven het plafond van de olie gaan
  zij doorstoken; de afbraak volgt Arrhenius. De voeding blijft lopen; de bestaande niveautrips nemen het over. Na de reset gaan
  E-01 en E-03 niet vanzelf aan (26.33). Alle drie de grenzen komen uit de bodem van K-02, de kritieke kolom.
- **Waarom K-02.** In het ontwerppunt kookt de bodem van K-02 op 250,1 °C, 4,9 K onder het plafond van de olie (255 °C); die van
  K-01 op 237,8 °C, 17 K eronder. Een grens op K-01 komt voor K-02 te laat.
- **De som.** Natuurwet: Raoult en Dalton (de bubbeldruk, `vle.js`), met de drukopbouw over het bed (module 8):
  p_top(T) = p_bubbel(x_B, T) − Δp_bed. x_B is de bodem van K-02 zoals hij in het ontwerppunt is samengesteld (`installatie.js`,
  `bedrijfspuntK02`), Δp_bed = 19,51 mbar (de drukval over het bed van K-02, N × de drukval per trap; 14.2).

  | Grens | Regel | Temperatuur van de bodem | Bodemdruk | Topdruk |
  |---|---|---|---|---|
  | het ontwerppunt | — | 250,08 °C | 39,51 mbar | 20,00 mbar |
  | hoogste setpoint PIC | SP_max = p_alarm − 3σ, σ = 0,1 mbar (`MEETRUIS.p_top`, `ruis.js`; de factor 3 is `MARGE_PLAFOND_SIGMA`, 26.33) | — | — | **23,50 mbar** |
  | alarm PAH | de bodem halverwege: (250,08 + 255) / 2 = 252,54 °C | 252,54 °C | 43,31 mbar | **23,80 mbar** |
  | trip PAHH | de bodem op het plafond, ten hoogste de bovenkant van het meetbereik (60 mbar) | 255 °C | 47,43 mbar | **27,93 mbar** |
  | reset PAHH | onder de alarmgrens, met een goede meting; daarna de operator | — | — | onder 23,80 mbar |

  Langs twee andere wegen nagerekend: met bisectie op het bubbelpunt in plaats van de bubbeldruk, en in het
  procesmodel zelf (bij de tripgrens kookt de bodem van K-02 daar op 255,0 °C, bij de alarmgrens op 252,5 °C).
  *Geldigheid:* dezelfde dampspanningsdata als het procesmodel (hoofdstuk 9). De bodemdruk loopt hier van 39,5 tot 47,4 mbar en de
  temperatuur van 250,1 tot 255 °C; het geldigheidsbereik van die data is in hoofdstuk 9 beoordeeld, en is voor deze grenzen niet
  opnieuw beoordeeld. De grens geldt voor de bodem in het ontwerppunt: wordt de bodem zwaarder, dan kookt hij bij dezelfde druk heter.
  De drukval over het bed is in het model vast; zij volgt de belasting niet.
- **De trip.** Een eigen opnemer op de topdruk (`VAC.PAHH.PV`, dezelfde ruis en hetzelfde bereik als PT, een eigen ruiskanaal;
  `WET_EIGEN_METING`, 26.5). Trip vanaf de grens, of bij een meting zonder informatie (bij twijfel beschermen, zoals de andere
  beveiligingen); boven het meetbereik is zeker hoog. De trip sluit beide oliekleppen en zet beide temperatuurregelaars in HAND op
  nul (`verwarmingOpdracht` en `verwarmingE03Opdracht`, `beveiliging.js`). Na de reset blijven ze daar.
- **De grens van het model, op het scherm.** De zuigcapaciteit is vast (`MODELAANNAMEN.zuigreserve`; geen pomp- of
  ejecteurkromme), er is geen ejecteur die instort bij overbelasting, en de druk heeft geen eigen dynamiek: zij is in elke stap het
  evenwicht tussen zuigcapaciteit en belasting (`simulatie.js`, `evenwichtsdruk_mbar`). Het gevolg voor de PAHH: een plots lek vanaf
  ongeveer 1,4 keer het ontwerplek (14 kg/h), en een uitgevallen condensor, brengen de druk in een stap van 20 mbar tot boven
  27,9 mbar, zonder dat het alarm eerst komt. Ook het "lekkend systeem" van module 7 (20 kg/h, 13.2) geeft in model 2 de trip.
  Alleen een sluipend lek loopt langzaam op, en geeft het alarm voorwaarschuwing. Tot 13,5 kg/h vangt de drukregeling een plots lek
  op: het alarm komt en gaat weer, terwijl het lek er nog is.
- **De herstart van E-03 na de PAHH** (in de uitleg van de reset van de PAHH en bij de TIC van K-02). De getallen, uit het model:
  40 procent is de resetgrens van de laag op E-03 (`GRENZEN_LSL_E03`);
  ongeveer 760 kg is 40 procent van de bodem van K-02 vol, 0,40 × 1908 kg = 763 kg (de inhoud bij het ontwerpniveau, 954 kg bij 50 procent,
  met de dichtheid uit hoofdstuk 4, `installatie.js`); ruim 1000 kg is wat V-02 na twee uur lek (2 × het ontwerplek) moet bijvullen tot half vol, het ontwerppunt: 1753 − 711 =
  1042 kg (26.29). Natuurwet: behoud van massa; zolang er geen voeding uit K-01 komt, komt wat V-02 bijvult uit de bodem van K-02. Geldt in
  dit model, waar een reboiler zijn duty in de eerste stap levert.
- **Wat een echte installatie anders doet** (in de uitleg van de PAH en de PAHH).
  Natuurwet: de ideale gaswet over het volume van het vacuümsysteem, dp/dt = R·T/V · (ṅ_in − ṅ_uit). Met ṅ_uit = S·p/(R·T) is dat een
  eerste-ordesysteem met de tijdconstante τ = V/S. De drie getallen in de uitleg, met hun som en naast het procesmodel:

  | Getal | Som in de uitleg | Het procesmodel | Hoe het model het geeft |
  |---|---|---|---|
  | wat de drukregeling opvangt | 1,5 × 10 kg/h = **15 kg/h** (de zuigreserve maal het ontwerplek, 20.2 en 13.2) | 15,00 kg/h | het lek waarbij de zuigklep helemaal open 20 mbar houdt |
  | waar 20 kg/h eindigt, de klep helemaal open | 20 mbar × 20 / 15 = **26,7 mbar** | 26,67 mbar | een stap met de klep op de zuigreserve en een lek van 20 kg/h |
  | vanaf welk plots lek de eerste stap tript | 10 kg/h × 27,9 / 20 = **ongeveer 14 kg/h** | 13,96 kg/h | het lek waarbij een stap met de klep op de stand van het ontwerppunt 27,93 mbar haalt |

  De som rekent met een belasting evenredig met lek / druk; het model telt ook de meegesleepte damp (`belastingVolume_m3s`) en komt
  binnen 0,01 mbar en 0,04 kg/h op hetzelfde. 26,7 mbar ligt boven het alarm (23,8) en onder de trip (27,9).
  *De tijdconstante, alleen ter controle van de bewering, niet op het scherm:* met het geschatte systeemvolume van 231 m³ (13.2) en de
  zuigcapaciteit van het ontwerppunt (0,149 m³/s, `zuigcapaciteit_m3s`) is τ ≈ 1550 s, ongeveer 26 minuten (met de klep helemaal open
  ongeveer 17 minuten): veel trager dan de drukregeling, dus in een echte installatie heeft de regelaar de tijd om de klep open te
  zetten. Het verloop zelf is niet nagerekend, want het model heeft geen eigen dynamiek van de druk; of de druk onderweg toch even over
  de trip schiet, hangt af van de afstemming.
- **Wat er na de trip gebeurt** (nagegaan in het model, beide temperatuurregelaars in AUTO): de voeding loopt
  door terwijl K-01 niet meer verdampt; na 2,25 minuten volgt de hoog-hoog van K-01, die de voeding stopt. De bodem van K-02
  stijgt eerst (de reflux uit V-02 loopt door) en zakt daarna tot rond 28 procent (27,6), onder de laag op E-03 (30 procent): ook die trip volgt, ongeveer 14 minuten na de PAHH. De eerste-tripmelding blijft de PAHH, de oorzaak; de laag op E-03 is een gevolg, en haar reset kan pas vanaf 40 procent. Met de
  reboilers uit valt de damp weg: na een uitgevallen condensor zakt de druk snel, na een lek blijft zij zo hoog als het lek haar
  maakt. De bodemtemperatuur is in het model het kookpunt bij de bodemdruk, ook als er niets meer kookt (geen warmte-inhoud): bij een
  lek dat blijft, toont het scherm een bodem boven het plafond.
- **Waar het staat.** `grenzenTopdruk()`, `topdrukBijBodemK02_Pa()`, `alarmgrensTopdruk_Pa()` en `bovengrensSetpointDruk_Pa()` in
  `physics/vacuumklep.js`; `WET_PAHH` en de redenen PAHH in `physics/beveiliging.js`; het blok `maakPAHH` in
  `bediening/regeling/interlock.js`; de bedrading in `regeling.js`. Een ontwerpkeuze van deze installatie: een andere installatie kan
  zo een trip ook de voeding laten stoppen, of alleen alarmeren.

### 26.37 De motorbeveiliging van P-01 (`WET_MOTORSTORING`, regelalgoritme)

**De regel**. Spreekt de motorbeveiliging van
bodempomp P-01 aan, dan meldt het veld dat (`K01.P01.STORING`: GEEN of STORING). De besturing zet de opdracht aan de pomp dan op uit,
weigert een start zolang de melding staat, en de balk bovenaan meldt "STORING P-01", na de trips en voor de alarmen. Is de
motorbeveiliging ter plaatse hersteld, dan start de pomp niet vanzelf: de opdracht staat op uit, en pas een nieuwe start zet haar aan.

- **Waarom deze regel.** Zonder melding stond een uitgevallen pomp alleen als "stil" op het beeld; de balk bleef 39 minuten op "Geen
  alarm, geen trip", tot het hoog-alarm van K-01 (doorgerekend in het model). In een installatie geeft het uitvallen
  van een motor een melding.
- **Geen herstart vanzelf.** Een pomp die na het herstel van haar beveiliging vanzelf weer aanslaat, kan iemand verrassen die er ter
  plaatse aan werkt; daarom blijft de opdracht op uit tot een nieuwe start. Dezelfde gedachte als bij de laag-laagbeveiliging (26.5):
  geen herstart vanzelf na een reset.
- **Wat het model niet kent.** Waarom de beveiliging aanspreekt (overbelasting, een as die zwaar loopt, een fase die wegvalt), de
  stroom van de motor, en de looptijdbewaking (de besturing ziet dat een pomp niet draait terwijl zij aan moet staan). De melding is
  een toestand van het veld, gezet door de laag van de instructeur in een scenario (26.39).
- **Geldigheid.** Een keuze van deze installatie, voor P-01: alleen die pomp heeft de regel nodig in scenario 1. De andere pompen
  krijgen haar als een scenario hen nodig heeft. Geen publicatie: een regel van deze installatie.
- **Waar het staat.** `pompOpdracht({ ..., motorStoring })` en `WET_MOTORSTORING` in `physics/beveiliging.js`; de storing van de
  actuator (`STORINGEN_POMP`) in `bediening/veld/pomp.js`; de melding in `bediening/veld/veld.js`; de bedrading in
  `bediening/regeling/regeling.js`; de reden op de faceplate in `bediening/os/pompfaceplate.js`.

### 26.38 De dode band op het alarm op hoge topdruk en op de hoog-alarmen van de niveaus (`WET_DOODBAND_ALARM_HOOG`, regelalgoritme)

**De regel**, met dezelfde opbouw als de dode band op het alarm op hoge
bodemtemperatuur (26.33, ronde A5 en A6):

    **aan als x > x_alarm;  uit pas als x ≤ x_alarm − d;  d = max( 3 · σ ; min( f · bereik ; x_alarm − SP_max ) )**

Eenheden: x, x_alarm, d, σ, SP_max en het bereik in de eenheid van de meting (Pa voor de topdruk, een fractie voor een niveau); f
zonder eenheid.

- **De bron.** De richtlijnen voor alarmbeheer EEMUA Publication 191 (2007) en ISA-18.2 (2009) geven een aanbevolen dode band per
  soort meting, als percentage van het bereik, vermeld door Naghoosi, Izadi en Chen (2011, tabel 1) en door Musa (exida, 2020); de
  vermeldingen staan in 26.33, ronde A5. Druk: **2 %**, in beide vermeldingen. Niveau: **5 %** bij de eerste vermelding, **2 %** bij
  de tweede. **De normen zelf zijn niet ingezien**; zij zijn niet vrij toegankelijk.
- **De waarde van f is een aanname van het model**: `DOODBAND_ALARM_HOOG` in
  `physics/beveiliging.js`, f_druk = 0,02 en f_niveau = 0,05. Voor een niveau neemt het model de grootste van de twee vermeldingen:
  met een meetruis van 1 procentpunt in de bodems (`ruis.js`) is 2 % kleiner dan drie keer de ruis, en dan zou de ondergrens van
  3σ het alarm al bepalen.
- **De getallen.**
  - Topdruk: meetbereik 0 tot 60 mbar, f · bereik = 1,2 mbar; x_alarm − SP_max = 23,8 − 23,5 = 0,3 mbar; 3σ = 3 · 0,1 = 0,3 mbar.
    Dus **d = 0,3 mbar**: het alarm gaat aan boven 23,8 mbar en uit onder 23,5 mbar, het hoogste setpoint van de drukregelaar (26.36).
  - K-01 en K-02: f · bereik = 5 procentpunt; x_alarm − SP_max = 80 − 65 = 15 en 80 − 70 = 10 procentpunt; 3σ = 3 procentpunt. Dus
    **d = 5 procentpunt**: aan bij 80 %, uit onder 75 %.
  - V-02: 70 − 55 = 15 procentpunt; 3σ = 1,5 procentpunt; **d = 5 procentpunt**: aan bij 70 %, uit onder 65 %.
- **Waarom.** In een lekscenario (doorgerekend met meetruis, één zaad) ging het alarm op hoge topdruk zonder dode
  band honderden keren aan en uit, en het hoog-alarm van K-01 in het scenario van P-01 tientallen keren.
- **Geldigheid.** Een beginwaarde; de richtlijnen zeggen dat de dode band per alarm op het gedrag van het signaal wordt bijgesteld.
  Springt de meting verder dan de dode band, dan helpt zij niet.
- **Waar het staat.** `doodbandAlarmHoog()`, `DOODBAND_ALARM_HOOG`, de dode band in `hoogNiveau()` en `WET_DOODBAND_ALARM_HOOG` in
  `physics/beveiliging.js`; de dode band in `hogeTopdruk()` in `physics/vacuumklep.js`; de bedrading in
  `bediening/regeling/regeling.js`.

### 26.39 De scenario's en de laag van de instructeur (ontwerpkeuzes)

**Wat een scenario is**. Een dienst waarin iets
misgaat: een beginstand, een storing op een moment binnen een venster, het herstel door de buitendienst, een tijdslimiet en een
voorwaarde voor "stabiel". Een scenario is een beschrijving, geen functie (`bediening/instructeur/scenarios.js`), zoals de injecties
van de uitlegmodule.

- **De integriteitsregel.** Een storing verandert precies één parameter, nooit een symptoom: hier een tag van de instructeur
  (`INS.P01.STORING`), die de veldlaag vertaalt naar een storing in de pomp. Alles wat de cursist ziet, volgt uit de bestaande fysica
  en regeling.
- **Op modeltijd, met een zaad.** Het moment van de storing valt binnen het venster van het scenario (scenario 1: 180 tot 480 s na de
  start), gekozen door een zaad met een kleine vaste generator (FNV-1a en mulberry32, `trekMoment()`); hetzelfde zaad geeft hetzelfde
  moment. In pauze loopt de modeltijd niet, dus gebeurt er niets.
- **De buitendienst.** Een vaste lijst opdrachten, in elk scenario dezelfde. De opdracht die de storing herstelt, is na een
  vaste modeltijd klaar (scenario 1: 600 s); een andere opdracht meldt na 900 s "niets bijzonders gevonden". Beide tijden zijn
  **aannames van het model**, gekozen zodat een snelle cursist de trip voor kan zijn (het hoog-alarm komt 39 minuten na de storing) en
  een verkeerde opdracht merkbaar tijd kost. De buitendienst doet een opdracht tegelijk.
- **De beginstand.** Het ontwerppunt met de temperatuurregelaars in AUTO, meetruis aan, snelheid 10×. Het scenario
  loopt 2 s modeltijd en gaat dan in pauze, zodat elke meting een keer geschreven is als de cursist rondkijkt.
- **De afloop.** Stabiel: na de storing en haar herstel 15 minuten aaneen zonder trip, met de eisen van het scenario (scenario 1: P-01
  draait, de niveauregelaar van K-01 in AUTO, het niveau tussen 40 en 60 %, de voeding loopt). Of de tijdslimiet van 240 minuten (tot
  ronde 1b 180; besluit 2 van de gebruiker: na een trip zakt K-01 in AUTO onder de band, en de herstart van K-02 komt erbij), of
  de cursist stopt. Een trip beëindigt geen scenario: het herstel na de trip hoort bij de taak.
- **De nabespreking.** Geen score, en niets wordt bewaard. Wat goed en wat niet goed ging, volgt uit vaste regels op de tijdlijn, die
  uit de geschiedenis van de tag-database komt. Een gevolg staat er alleen als het in het model is gemeten. De wet erachter staat er
  met de getallen van het procesmodel: bij scenario 1 de ophoping in de bodem van K-01, t = (h_alarm − h_0) · M_max / B, met M_max =
  864 kg en B = 400 kg/h (het ontwerp): 39 minuten tot het hoog-alarm en 52 minuten tot de hoog-hoog. In het model kwamen ze na 38,9
  en 51,8 minuten (zonder meetruis). Sinds ronde 1b ook de som van het alarm tot de trip, de formule van de regel: 13 minuten
  (besluit 4 van de gebruiker).
- **De herstart van K-02 (ronde 1b, besluit 1 van de gebruiker).** Na de hoog-hoog van K-01 tript K-02 op laag niveau: er komt geen
  destillaat meer uit K-01. De aanwijzing en de nabespreking zeggen: wachten tot het niveau van K-02 boven de reset van de laag staat
  (40 %), dan E-03 eerst op de helft van zijn stand in het ontwerp (40 %), daarna stap voor stap hoger. Op de stand van het ontwerp
  (80 %) kookt de bodem leeg en tript K-02 binnen enkele minuten opnieuw; op de helft niet. Beide gemeten in het model (groep 270 G).
- **Scenario 2, de voeding valt weg (ronde 2).** De aanvoer uit de vetsplitsing stopt: een parameter (`INS.VOEDING.STORING`), en P-03
  blijft draaien. Het moment ligt tussen 3 en 8 min, en de aanvoer komt vanzelf terug na 25 tot 40 min (een eigen trekking van het zaad).
  Aannames van het model: de buitendienst bij P-03 meldt na 5 min dat er niets aankomt en herstelt niets; vraagt de cursist het na het
  herstel opnieuw, dan meldt zij dat de aanvoer er weer is. Zo kan een cursist die P-03 stopte, weten wanneer hij kan herstarten.
  - De wet in de nabespreking: behoud van massa (dM/dt = in - V - B) en van energie (V = Q / ΔH_v) over beide kolommen, met de getallen
    van het procesmodel. E-01 levert 842 kW en verdampt daarmee 9 600 kg/h (ΔH_v 316 kJ/kg); zonder voeding zakt de bodem van K-01 van
    50 naar 30 % in 1,0 min, die van K-02 zonder het destillaat van K-01 in 2,4 min, als alles doorloopt. In het model trager: de
    regelaars knijpen de verwarming en de afvoer terug (K-02 13 tot 16 min).
  - De wachtstand van K-02 (E-03 terug, en de reflux, het destillaat en de afvoer dicht) volgt leertaak 6 van model 1. Gemeten: alleen
    E-03 terug geeft toch de laag-trip (de reflux uit V-02 loopt naar de bodem en de niveauregelaar voert haar af tot onder de laag); met
    ook de afvoer dicht loopt de bodem vol tot de hoog-hoog.
  - Wat het model niet rekent (S10): zonder warmte blijft een bodem op haar kookpunt, en een pomp zonder aanvoer loopt niet droog.
  - Het eerste alarm, de hoge bodemtemperatuur van K-01, volgt niet uit die som maar uit de samenstelling: de wet van Raoult met de
    dampspanning van Antoine (7.1; de dampspanningen 23.2). De nabespreking geeft er de wet en de gemeten tijd bij, geen som: hoe snel
    de samenstelling verschuift, rekent het model stap voor stap (besluit 1 van de gebruiker bij de goedkeuring van ronde 2).
  - Als de aanvoer terugkomt, meldt de vetsplitsing dat zelf, een keer, in de balk en in de tijdlijn (besluit 3 van de gebruiker). Aanname
    van het model: de melding staat vijf minuten vooraan in de balk, of korter als er daarna een nieuwe trip komt; die is dan belangrijker.
    Vijf minuten is langer dan het duurt tot de hoog-hoog van K-01 als P-03 bleef draaien met E-01 uit (gemeten 3,0 tot 3,2 min).
- **Scenario 3, een luchtlek dat de regelkringen wegregelen (ronde 3).** Een luchtlek aan het vacuümsysteem: een parameter
  (`INS.VAC.LEK`), die vanaf een moment tussen 3 en 10 min in 120 min groeit tot 12 kg/h boven het ontwerplek. Geen aanwijzing
  (taakklasse 3). Aannames van het model: de buitendienst bij het vacuüm dicht het lek na 15 tot 25 min (een eigen trekking van het
  zaad); een andere opdracht meldt na 15 min dat er niets is gevonden.
  - De wet in de nabespreking: de ideale gaswet in het vacuüm (de pomp zuigt een volume, geen kilo's), met de zuigreserve van 1,5
    (20.2). De PIC houdt 20 mbar tot een lek van 1,5 × 10 = 15 kg/h; het drukalarm (23,8 mbar) komt bij 17,8 kg/h en de PAHH (27,9 mbar)
    bij 20,9 kg/h: na 50,0, 78,5 en 109,4 min. Gemeten zonder meetruis: de zuigklep op 100 % na 56,9 min (later dan de som: de I-actie
    loopt een groeiend lek achterna), het drukalarm na 78,5 en de PAHH na 109,5 min. En Clausius-Clapeyron (7.1): palmitinezuur kookt
    bij 20 mbar bij 219,0 °C, met dT/dp = 1,14 K per mbar, twintig keer zoveel als bij 1013 mbar; tot het drukalarm stijgt het kookpunt
    4,0 K.
  - De eerste trip, de hoog-hoog van K-01 (gemeten 98,4 min na het lek), volgt niet uit een som. De nabespreking geeft de gemeten tijd
    met waarom, en zegt dat zij het begin van de keten van trips is, niet de oorzaak.
  - **De afname na het dichten, een modelaanname.** Het procesmodel heeft geen volume in het vacuüm: in een stap dicht viel de druk van
    24 naar 13 mbar, en tripte de kolom na een goede ingreep. Daarom neemt het lek na het dichten af met de tijdconstante van het
    leegpompen van een volume, τ = V/S: V = 231 m³ (13.2), S de zuigcapaciteit met de klep open, τ ongeveer 17 min. Bron van de
    formule, op boekniveau: K. Jousten (red.), *Handbook of Vacuum Technology*, 2e druk, Wiley-VCH, Weinheim, 2016 (ISBN
    978-3-527-41338-6); uitgave en jaar nagegaan op de pagina van de uitgever. Het boek is niet ingezien: er wordt geen hoofdstuk of
    bladzijde uit genoemd. Gemeten: stabiel na 141 tot 146 min, laagste topdruk 18,2 tot 18,4 mbar.
  - De oorzaak van een niveautrip in "Wat er gebeurde" volgt de niveaukring vlak voor de trip: AUTO met de afvoer zoals de regelaar
    haar had, of HAND met de klepstand die de cursist koos, of die de besturing zette (`BIJ_TRIP` in `tijdlijn.js`). Zo ook in
    scenario 2.
- **Waar het staat.** `bediening/instructeur/` (de laag), `bediening/os/scenario.js` (het venster), `bediening/os/tijdlijn.js` en
  `bediening/os/nabespreking.js`; de bedrading in `bediening/main.js`.

## 27. Het P&ID van de uitlegmodule

### 27.1 De symbolen: ISO 10628-2 en ANSI/ISA-5.1, en wat het P&ID daarbuiten kiest

Het P&ID (de tab P&ID van model 1; `content/pid.js` en `ui/pid.js`) tekent de hele installatie op één blad: elk apparaat met zijn
nummer en een hoofdmaat uit het rekenmodel, de leidingen met hun stroomrichting, en de instrumenten, regelkringen, beveiligingen en
trips van de bedieningsmodule, met dezelfde tags.

- **De normen, op boekniveau.**
  - ISO 10628-2:2012, *Diagrams for the chemical and petrochemical industry — Part 2: Graphical symbols*, eerste uitgave,
    International Organization for Standardization (ISO), Genève, december 2012; bij de laatste toetsing in 2024 bevestigd. Samen met
    ISO 10628-1:2014 vervangt zij ISO 10628 uit 1997. Uitgave en jaar nagegaan op de pagina van de norm bij ISO (norm 51841) en bij
    een verkoper van normen (AFNOR).
  - ANSI/ISA-5.1-2024, *Instrumentation and Control Symbols and Identification*, International Society of Automation (ISA), 2024
    (verschenen in oktober 2024). Zij volgt ANSI/ISA-5.1-2022 en ISA-5.1-2009 op; tot de uitgave van 2024 heette zij
    *Instrumentation Symbols and Identification*. Uitgave en jaar nagegaan op de productpagina van ISA en in de ANSI Webstore.
- **Wat er is ingezien.** Alleen die catalogusgegevens. Beide normen zijn betaald en zijn niet ingezien: er wordt geen symboolnummer,
  tabel of paragraaf uit genoemd. De vorm van de symbolen volgt wat openbare samenvattingen en leerboeken als de vorm van deze normen
  tonen (een kolom met pakking, een warmtewisselaar met buizenbundel, een liggend vat, een centrifugaalpomp, een regelklep met
  membraanaandrijving; een cirkel voor een instrument in het veld, een cirkel in een vierkant met een streep voor een functie in het
  besturingssysteem op het scherm van de operator, een gestippelde lijn voor een signaal). Dat de vorm overeenkomt met de norm, is
  een aanname van dit model, geen controle aan de tekst van de norm.
- **Wat het P&ID buiten de normen om kiest.**
  - **De tags** zijn die van de bedieningsmodule (K01.LIC, VAC.PIC, K02.FIC), niet een lettercode met een lusnummer. In de cirkel
    staan boven de letters van de functie, onder de plek; samen vormen ze de tag.
  - **Een beveiliging met een eigen opnemer** (LSLL, LSHH, PAHH) staat als instrument in het veld; een beveiliging of alarm dat de
    opnemer van een andere beveiliging leest (LSL, LSH), en de alarmen TAH en PAH, als functie in het besturingssysteem.
  - **Een vergrendeling** staat als ruit met een nummer (I1 tot en met I7) bij wat zij stopt of sluit. Welke trip wat doet, staat in
    een oorzaak-gevolgtabel onder de tekening, niet als signaallijn van elke trip naar elk apparaat: een trip van K-02 stopt ook de
    voeding van K-01, en zo een lijn zou de hele tekening kruisen.
  - **Het vacuümsysteem** staat als vacuümpomp: het rekenmodel kiest geen soort.
  - **Tussen V-01 en K-02 staat geen pomp**: het rekenmodel heeft er geen. Bij de leiding staat daarom "vrij verval", en de uitleg
    boven de tekening rekent uit hoe hoog V-01 daarvoor minstens moet staan (27.2; keuring van de gebruiker, 23 november 2026).
  - **De pitch loopt van het blad af**, met "pitch naar opslag" bij het eind, zoals afgas, C16-rijk en C18-rijk. Het blad toont alleen
    deze installatie; waar een product heen gaat, staat bij de pijl. Een procesleiding begint en eindigt op een apparaat, op een
    andere leiding of aan de rand van het blad, en loopt door geen instrument (keuring van de gebruiker, 23 november 2026).
  - **Een leiding voor warmteolie of koelwater** heeft een eind op haar apparaat en een vrij eind met haar naam erbij, de aansluiting
    op het nutsnet. Het olie- en het koelwatercircuit zelf staan niet op het blad: het rekenmodel kent ze niet.
  - **LSH en LSL.** LSH is in de bedieningsmodule een hoogalarm; volgens ISA-5.1 heet dat LAH. De legenda zegt het, en de tags blijven
    die van de bedieningsmodule. LSL is daar geen alarm maar een beveiliging, die de verwarming uitzet (I3, I5), en heet dus ook
    volgens ISA-5.1 terecht LSL. De keuring vroeg om "LSH en LSL zijn hoog- en laagalarmen"; voor LSL klopt dat niet, en daarom
    noemt de legenda ze apart.
  - **De uitleg staat boven de tekening**, inklapbaar, en het P&ID krijgt bij de tab P&ID de hele breedte. Het paneel van een
    apparaat staat rechts naast het vak met de tekening of eronder, nooit eroverheen.
  - **Twee tags zijn geen instrument in het veld**: de eerste-tripmelding (ALM.EERSTETRIP, een functie van het besturingssysteem) en
    K02.VERDELING (alleen in het rekenmodel: of de verdeling tussen top en bodem van K-02 op de rand van het model staat). Zij staan
    in een notitievak op de tekening.
  - **De hoofdmaten** komen uit het rekenmodel: diameter en bedhoogte van een kolom (hoofdstuk 14), de duty van een reboiler in het
    ontwerppunt, de damp die een condensor condenseert, de inhoud van V-02 bij half vol (26.29), het ontwerpdebiet van een pomp en de
    topdruk. V-01 heeft in het rekenmodel geen inhoud; daar staat alleen de stroom.
  - **Een P&ID is geen plattegrond**: de apparaten staan niet op schaal en niet op hun plek. Alleen de verdeling van het bed van K-02
    boven en onder de voeding volgt het model.
- **Waar het staat.** De tags, vergrendelingen en teksten in `content/pid.js`, de tekening in `ui/pid.js`. Een toets legt de tags
  naast de tag-database, de koppeltabel en de beveiligingslogica van de bedieningsmodule: geen tag te veel of te weinig, en elke
  trip doet op het P&ID wat zij in de bedieningsmodule doet.

### 27.2 Van V-01 naar K-02 zonder pomp: het vrije verval

In het rekenmodel loopt het destillaat van K-01 zonder pomp van V-01 naar K-02. Dat kan alleen op vrij verval: de vloeistof gaat van
de topdruk van K-01 naar de druk op het voedingspunt van K-02, en die is hoger, want boven dat punt ligt het bovenste deel van het
bed van K-02, met zijn drukval. De uitleg boven het P&ID rekent uit hoe hoog V-01 minstens boven dat voedingspunt moet staan
(`physics/vrijverval.js`; elk getal een plaatshouder uit `physics/kengetallen.js`).

- **De wet.** De hydrostatische druk: in een stilstaande vloeistof neemt de druk toe met de diepte, Δp = ρ · g · Δh, dus
  Δh = Δp / (ρ · g), met Δp in Pa, ρ in kg/m³, g in m/s² en Δh in m. Een natuurwet. Oorspronkelijke publicatie: S. Stevin,
  *De Beghinselen des Waterwichts*, Leiden, 1586, waarin de druk van een vloeistof op een bodem alleen van de hoogte van de vloeistof
  afhangt. Niet ingezien; de gegevens uit referentielijsten. Handboek, op boekniveau: R. B. Bird, W. E. Stewart en E. N. Lightfoot,
  *Transport Phenomena*, 2e druk, Wiley, 2002.
- **De getallen, uit het model.** De druk in V-01 is de topdruk van K-01 (20 mbar; het model kent geen drukval in de damplijn en de
  condensor). De druk op het voedingspunt van K-02 is zijn topdruk plus de drukval over de trappen boven de voeding: de drukopbouw
  (14.1) over N_R van de N trappen, 20 + 19,5 · 22,5 / 39,0 = 31,2 mbar. Het drukverschil is dus 11,2 mbar, 1124 Pa. De dichtheid
  is die van het destillaat bij de toptemperatuur van K-01, 224,5 °C: 741 kg/m³ uit de lijn van hoofdstuk 4, 24 K boven het hoogste
  meetpunt (een extrapolatie, met de onzekerheid van hoofdstuk 4). Het model kent geen onderkoeling in E-02. g is de
  standaardwaarde (hoofdstuk 7). Samen: Δh = 1124 / (741 · 9,81) = 0,15 m.
- **Wat er niet in zit: de leiding.** Een stromende vloeistof verliest druk aan wrijving langs de wand, in bochten en in afsluiters,
  ongeveer met het kwadraat van de stroom. Het rekenmodel kent geen leiding met een lengte en een diameter, dus de 0,15 m is een
  ondergrens; de uitleg zegt dat in woorden. In een echte installatie staat hier meestal een pomp.
- **Waarom geen wetvenster.** De afleiding staat in de uitleg van het P&ID, met de wet, de formule, de eenheden, de waarden uit het
  model en een uitleg in gewone taal, zoals elke berekening in deze module. Een eigen wetvenster voor de hydrostatische druk, de vijfentwintigste
  wet van model 1, vraagt een aanpassing van bestaande controles en is daarom een voorstel voor later.

---

## Openstaande punten

De negen punten die om een STOFWAARDE vragen (2, 3, 5, 6, 7, 8, 9, 12 en 13) zijn in
hoofdstuk 22 op gevoeligheid doorgerekend en in drie groepen gerangschikt. Begin bij
groep A; groep C mag als schatting blijven staan.

1. De kookpunten in 2.2 en 2.4 die als "handboekwaarde" staan, verifiëren tegen een
   fysiek handboek, inclusief editie en pagina.
2. ~~Een dampspanningscorrelatie zoeken voor **C12:0 en C14:0** die het werkgebied van de
   kolom wél dekt.~~ **GESLOTEN** met Falleiro 2012, gemeten bij 1333–9333 Pa; zie
   hoofdstuk 23. C14:0 is bovendien door een tweede bron bevestigd binnen 0,21 K. Voor
   C12:0 is er geen tweede bron — hij is als enige onbevestigd overgenomen (23.4).
3. Een tweede dampspanningspunt voor **C18:2** zoeken, zodat B en C niet meer van C18:1
   hoeven te worden geleend en α(C18:2/C18:1) geen constante meer is. **OPEN, maar de bron
   is aangewezen:** Barbeiro et al. 2021, J. Chem. Thermodyn. 159, 106469, tabel 5 — gemeten
   bij 2,67–26,66 kPa. Gesloten toegang, geen repositorykopie, geen ThermoML-depot. De drie
   handboekwaarden bij 16 mmHg zijn drie afrondingen van het punt dat er al in zit. Zie 23.6.
4. ~~Het **omslagpunt bij 202,1 °C** (2.6) opnieuw beoordelen.~~ **GESLOTEN.** Met de gemeten
   sets is de kruising verdwenen: C18:1 is over 70–350 °C overal vluchtiger dan C18:0, zoals
   het hoort. Het was een fit-artefact. Zie 23.10.
5. De spreiding in het normale kookpunt van **C18:0** (358 / 361 / 383 °C) uitzoeken.
6. ~~**Vloeistofdichtheden** vastleggen (hoofdstuk 4).~~ **Gedaan:** een lijn voor het mengsel uit de metingen van Wang, Sun en
   Teja (2016), C14:0 tot 200 °C; daarboven extrapolatie (hoofdstuk 4).
7. Een **associatiegraad** voor de dimerisatie zoeken, of expliciet besluiten dat module 7
   met een bovengrens werkt (8.2).
8. **Activiteitscoëfficiënten** voor het systeem vetzuren/triglyceriden zoeken, of een
   UNIFAC-schatting maken, om te kunnen zeggen hóe groot de afwijking van Raoult in de
   pitchrijke bodem is. Nu staat alleen de richting vast (8.5).
9. De **activeringsenergie** voor thermische afbraak van vetzuren. **Getoetst in module 9 en
   nog steeds open** (15.2). Er is geen gepubliceerde Ea gevonden voor de polymerisatie die
   pitch en verkleuring maakt bij 200–280 °C onder vacuüm. Gevonden zijn alleen waarden voor
   isomerisatie en oxidatie van vetzuren (31–127 kJ/mol) en voor pyrolyse van *petroleum*pitch
   (176–190 kJ/mol); die laatste ligt dicht bij de 178,4 die de vuistregel impliceert, maar is
   een ander materiaal en een andere reactie en is daarom **bewust niet overgenomen**. Module 9
   rekent sinds de vijfde doorloop met het gemeten bereik voor vetzuurreacties, 31,0 tot 127,4
   kJ/mol (15.2); de vuistregel van 178,4 is als anker vervallen. Wat het punt sluit is een houdproef:
   monsters op temperatuur bij 200–280 °C onder vacuüm, dimeergehalte en kleur tegen de tijd.
   Daarmee vervalt ook de noodzaak om A buiten beschouwing te laten (15.3).
10. De **zware sleutel C18:2** bepaalt alle uitkomsten van module 5, en is nu als ENIGE van de
    zes vluchtige componenten niet op een meting gebaseerd (23.6). Dat maakt punt 3 het
    belangrijkste openstaande stofdatapunt van het project.
11. De **geldigheidsgrenzen van Gilliland** (8.10) verifiëren tegen de oorspronkelijke
    publicatie; ze zijn nu overgenomen zoals algemeen geciteerd.
12. **Soortelijke warmten (cp)** van de vetzuren in de vloeistoffase zoeken, met bron en
    geldigheidsbereik. Daarmee kan de energiebalans van hoofdstuk 12 volledig sluiten in
    plaats van alleen latent, en kan q een vrije parameter worden (12.3).
13. De **voedingssamenstelling** van 9.2 is generiek en afgerond. Zij is bedoeld als
    representatief voor een palmgebaseerde voeding en niet als meting; een literatuurbron
    voor een typisch spectrum na vetsplitsing zou de onderbouwing verbeteren.
14. ~~Een punt over het bouwproces van het bestand.~~ **GESLOTEN**; zie 19.6.
15. ~~**De Ea-band hangt aan een flashberekening**~~ **GESLOTEN in de vijfde doorloop**: de band
    hangt niet meer aan de vuistregel maar aan het gemeten bereik (15.2). Het oorspronkelijke punt:
    **De Ea-band hangt aan een flashberekening** en niet aan de vuistregel zelf. De bovengrens
    schoof van 178,4 naar 201,2 kJ/mol toen de stofdata veranderde, en een vuistregel die
    "factor 2 per 8 K" zegt hoort dat niet te doen. De propagatie is getrouw uitgevoerd, de
    METHODE niet veranderd: dat is een inhoudelijke keuze. Zie 23.7 voor de twee alternatieven.
16. **De dampspanning van C16:0 tussen 350 en 420 K.** De meesleeptabel van module 7 rekent bij
    de condensoruitlaat (80–140 °C), 70 tot 130 K onder het gemeten venster. Daar lopen de drie
    beschikbare bronnen een factor duizend uiteen. De VORM van de tabel klopt, het absolute
    getal in kg/h niet. Zie 23.8.
17. **De verdampingswarmte ver buiten het gemeten venster.** Het niveau bij het bedrijfspunt is
    door twee bronnen bevestigd, de helling daarbuiten niet: de nieuwe set geeft voor C16:0 bijna
    40 procent daling over 198–354 °C waar Watson ongeveer 7 procent zou geven. Een fit over
    50 K legt de kromme vast maar niet haar kromming daarbuiten. Zie 23.8.
