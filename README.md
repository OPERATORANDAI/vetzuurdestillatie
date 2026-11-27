# Vacuümdestillatie van vetzuren — de uitlegmodule

Een les over **vacuüm-vetzuurdestillatie** in de oleochemie, als één HTML-bestand dat volledig offline werkt. De installatie
bestaat uit twee kolommen in serie: **K-01** haalt de pitch uit de ruwe vetzuren, **K-02** splitst het pitchvrije destillaat
in een C16-rijke en een C18-rijke stroom.

Dit is de **uitlegmodule** (model 1): zij laat zien waarom de kolom doet wat ze doet. Een tweede module, de
**bedieningsmodule** (versie 0.16, [`vetzuurkolom-bediening.html`](vetzuurkolom-bediening.html)), laat zien
hoe je de installatie bedient: DCS-schermen met faceplates, regelaars, alarmen en beveiligingen. Begin met de uitlegmodule;
wie de wetten kent, begrijpt in de bedieningsmodule waarom een regelaar of beveiliging zo is ingesteld. In de
bedieningsmodule opent een klik bij een regel of een apparaat de uitlegmodule in een tweede tabblad; de terugbalk daar brengt
je terug, terwijl de bedieningsmodule doorloopt. Dat werkt als de twee bestanden in dezelfde map staan. In Firefox brengt de
terugbalk je niet zelf terug: ga dan naar het andere tabblad.

**Versie 1.1.20, 27 november 2026.**

- **Openen:** [`index.html`](index.html), de startpagina, of meteen [`vetzuurkolom.html`](vetzuurkolom.html) downloaden en
  dubbelklikken. Geen installatie, geen internet, geen server; alles zit in het ene bestand. Staan de twee in dezelfde map,
  dan werkt ook de knop "← Startpagina" in de module zonder internet.
- **Bedoeld voor een pc of laptop**, vanaf 1366 × 768. Op een tablet past het ook. Op een telefoon werkt het, maar het P&ID en
  de schermen van de bedieningsmodule passen niet in de breedte: de pagina schuift opzij. Getest in Chrome, Edge en Firefox (november
  2026), als bestand en via een webserver, en in Chrome in telefoon- en tabletformaat.
- **Taal:** Nederlands.

## Voor wie

Procesoperators (VaPro B-niveau) en iedereen die wil begrijpen waarom een vacuüm-vetzuurkolom doet wat hij doet, zonder
daarvoor een procestechnische opleiding te volgen. Praktisch en begrijpelijk, maar natuurkundig correct.

## Wat het is

Een interactieve les die laat zien waaróm een vetzuurkolom doet wat ze doet, met bij elke berekening de natuurwet erachter,
met storingen om te oefenen en een nabespreking.

- **Een processchema** met de hoofdstromen en de apparaten. Afsluiters en regelkringen zijn niet getekend; die staan in de
  bedieningsmodule. Klik op een apparaat (ⓘ) en je leest wat het doet, welke wet erachter zit en wat er in bedrijf misgaat.
- **Zeven leertaken**: storingen zoals je ze in de controlekamer tegenkomt, van een apparaat met een oorzaak tot oorzaak
  en gevolg in verschillende kolommen. Je leest de situatie, kijkt in het schema, kiest je ingreep en controleert je
  verwachting.
- **Oefenen**: de installatie draait in de tijd, met regelkringen en trends. Je draait zelf aan de regelaars of start een
  storing, en na afloop volgt een nabespreking.
- **Eerlijk over wat niet vaststaat.** Waarden zonder bron zijn gemarkeerd, in de module zelf en in
  [`BRONNEN.md`](BRONNEN.md) (hoofdstuk 24 geeft per waarde een oordeel en de gevoeligheid).

## Wat het niet is

- **Geen ontwerpgereedschap en geen ontwerpgegevens.** Alle waarden zijn generiek en afgerond. Reken er geen kolom mee uit:
  de correlaties zijn de gangbare leerboekvormen met hun leerboekbeperkingen, en de apparaatgegevens zijn ronde waarden,
  geen leveranciersdata.
- **Geen OTS.** Het is geen operator training simulator van een bestaande installatie. Je kunt de installatie laten draaien en
  storingen inbrengen, maar het model is gebouwd om iets uit te leggen, niet om bediening te toetsen.
- **Geen gegevens van een bestaande installatie of werkgever.** Geen bedrijfsnamen, geen tagnummers, geen procesdata uit de
  praktijk. De apparaatnummers (K-01, E-01, V-02 …) zijn generiek.
- **Geen beveiligingen.** Model 1 heeft geen beveiligingen. In de scenario's lopen niveaus en temperaturen daardoor tot
  waarden die een echte installatie niet haalt; de module markeert die en zegt in de nabespreking dat een echte installatie
  daar een beveiliging heeft. Een tripgrens is per installatie vastgelegd, in de veiligheidsstudie, en is geen vast getal;
  daarom staan de grenzen erbij als die van "het bedieningsmodel (in ontwikkeling)": de bedieningsmodule, een tweede model van
  dezelfde installatie, waar ze zijn ingesteld. Zij staat in dezelfde map.
- **Nog niet beproefd door een operator uit de doelgroep.** De bouwer heeft één blinde diagnostische doorloop gedaan. Of
  iemand die de module nooit zag de weg vindt, en of de uitleg de wet overbrengt, is nog niet getoetst.
- **Geen garantie.** Gebruik is op eigen risico; zie de licentie.

## De bedieningsmodule

Versie 0.16, 26 november 2026. Dezelfde installatie, maar nu bedien je haar zelf, op DCS-schermen volgens ISA-101.

- **Zes schermen**: het overzicht, per kolom de bodem en de top, en het vacuüm, met de navigatie per kolom en langs de stroom.
- **Alle zeven regelkringen bedienbaar**, elk met een faceplate en een trend: het bodemniveau en de bodemtemperatuur van beide
  kolommen, de topdruk, het niveau van het refluxvat en de reflux, met AUTO, HAND en waar het kan CAS.
- **De beveiligingen** met hun trip, een reset met voorwaarden en een eerste-tripmelding, en het venster Beveiliging met alle trips.
- **Bij elke regel de uitleg**: de soort (natuurwet, definitie, afgeleide methode, correlatie, regelalgoritme of modelaanname), de
  formule en de bron, en een klik naar de uitleg in de uitlegmodule.
- **Drie scenario's** van eenvoudig naar complex, elk met een nabespreking zonder score: P-01 valt uit, de voeding valt weg, en
  een luchtlek dat de regelkringen wegregelen.

**Nog niet af:** scenario 4 en 5, een alarmlijst met kwiteren en prioriteit, een scherm voor de instructeur om zelf storingen in te
brengen, en de condensors als regelkring. Ook de bedieningsmodule is nog niet beproefd door een operator uit de doelgroep.

## De natuurwetten erachter

Bij elke berekening toont de module de naam van de wet, de formule, de eenheden en een uitleg in gewone taal. Een correlatie
heet nooit een wet: bij Gilliland, HETP en de drukval per trap staat erbij waarom er geen wet onder zit.

| Wet | Waar je het in het model ziet |
|---|---|
| **Antoine en Clausius-Clapeyron** (dampspanning) | Het kookpunt hangt aan de druk: daarom stijgt de bodemtemperatuur als de topdruk stijgt, bijvoorbeeld bij een vacuümlek. |
| **Raoult en Dalton**, relatieve vluchtigheid | Damp en vloeistof verschillen van samenstelling; daarom kookt een bodem met meer pitch warmer, en scheidt vacuüm niet beter maar laat het je bij een lagere temperatuur werken. |
| **Behoud van massa** (totaal en per component), **Rachford-Rice** | Wat erin gaat, moet eruit: knijp je de bodemafname dicht, dan loopt de bodem vol en gaat er pitch naar K-02. |
| **Fenske, Underwood en McCabe-Thiele**, met de correlatie van **Gilliland** | Hoeveel trappen en hoeveel reflux een scheiding vraagt: daarom heeft K-02 een hoog bed en een reflux, en wordt de C16/C18-scheiding slechter als je de reflux terugneemt. |
| **Behoud van energie**, **warmteoverdracht** | De reboiler verdampt wat de condensor weer condenseert; zonder temperatuurverschil geen warmtestroom, dus het verwarmingsmedium is een plafond voor de bodemtemperatuur. |
| **Ideale gaswet** | Bij 20 mbar neemt damp ongeveer vijftig keer zoveel ruimte in als bij atmosferische druk; daarom kost een klein luchtlek veel zuigcapaciteit en loopt de topdruk op. |
| **Drukopbouw over het bed** (met de correlaties voor drukval, HETP en F-factor) | Bodemdruk = topdruk + drukval: meer belasting of vervuiling geeft meer drukval, dus een hetere bodem. |
| **Arrhenius** | Afbraak gaat exponentieel sneller met de temperatuur, en de schade is snelheid maal verblijftijd; daarom doet K-01, met zijn trage bodem, meer schade dan de hetere K-02. |
| **Behoud van massa in de tijd**, met PI-regelaars | Niveaus zijn ophopingen: in de trends zie je hoe snel een bodem of een vat volloopt of leegloopt, en wat een regelaar daaraan doet. |

De keten die overal terugkomt: **vacuümlek of te hoge belasting → hogere druk en meer drukval → hogere bodemtemperatuur
(Clausius-Clapeyron) → snellere afbraak (Arrhenius) → verkleuring, pitchvorming, productverlies.**

## Bronnen

Elke stofwaarde, wet en correlatie staat met bron en geldigheidsbereik in [`BRONNEN.md`](BRONNEN.md). Dat is een uitgedunde
kopie van de bronnenlijst van het project, met dezelfde hoofdstuknummers als de verwijzingen in de module. De oorspronkelijke
publicaties van de wetten staan in hoofdstuk 7.1.

## Hoe het gemaakt is

Ontworpen door Wouter de Weerd. Gebouwd met hulp van Claude Code, getest met een eigen testsuite die niet openbaar is. Wie het
maakte, de rolverdeling, hoe fouten gevangen worden en wat er nog komt: [`werkwijze.html`](werkwijze.html). De broncode staat
leesbaar in het HTML-bestand zelf; de commentaren daarin verwijzen soms naar bestanden van het project die niet openbaar
zijn, zoals de werkafspraken en de tests.

## Contact

Vragen, fouten en opmerkingen graag via de [Issues van deze repository](https://github.com/OPERATORANDAI/vetzuurdestillatie/issues)
op GitHub, of via [LinkedIn][linkedin].

[linkedin]: https://www.linkedin.com/in/wouterdeweerd

## Licentie

Copyright (c) 2026 Wouter de Weerd. Alle bestanden van deze uitgave vallen onder CC BY-SA 4.0:

- [`index.html`](index.html), de startpagina;
- [`werkwijze.html`](werkwijze.html), hoe het gemaakt is;
- [`vetzuurkolom.html`](vetzuurkolom.html), de uitlegmodule;
- [`vetzuurkolom-bediening.html`](vetzuurkolom-bediening.html), de bedieningsmodule;
- [`README.md`](README.md), deze tekst;
- [`BRONNEN.md`](BRONNEN.md), de bronnenlijst.

[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/deed.nl) (Creative Commons Naamsvermelding-GelijkDelen 4.0
Internationaal), voor de code en de tekst samen. Je mag het delen en bewerken, ook commercieel, mits je de maker noemt (Wouter de
Weerd, GitHub: OPERATORANDAI) en je bewerking onder dezelfde licentie deelt. De officiële tekst van de licentie staat ongewijzigd in
het bestand `LICENSE`.
