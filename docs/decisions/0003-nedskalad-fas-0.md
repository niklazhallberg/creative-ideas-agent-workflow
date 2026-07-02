# ADR 0003 — Nedskalad Fas 0 (och varför "bara vinnare" inte fungerar)

- **Status:** Antagen
- **Datum:** 2026-07-02
- **Berör fas:** 0
- **Kompletterar:** ADR 0002 (CD som måttstockens auktoritet).
- **Beslutsfattare:** Creative Technologist, RADON.

---

## Kontext

Fas 0 (linjalen som allt senare mäts mot) behöver bli lätt nog att faktiskt
genomföras vid sidan av ordinarie arbete. ADR 0002 bytte auktoritetsmodellen
från flera seniorer till en CD + sanity-check. Kvar var två frågor som ADR 0002
sköt fram till Fas 0-designen:

1. **Hur stort** ska batch:en vara — hur många idéer, i hur många sittningar?
2. **Vilken skala** ska CD:n betygsätta på?

Innan de svaren skrevs ned föreslogs också en tredje, radikalare förenkling: att
skippa blindbedömning helt och bara mata in "gamla idéer som lanserats och
funkat väl" som referens. Den simplifieringen bryter måttstocken. Detta ADR
dokumenterar (a) det slutgiltiga nedskalade upplägget, och (b) varför
"bara vinnare"-versionen inte kan användas — så nästa granskare inte behöver
ta samma diskussion en gång till.

---

## Beslut

Fas 0 körs i nedskalad form med följande konkreta parametrar:

### Volym
- **30–50 idéer totalt.** Börja på 30. Om CD:n själv säger "jag hittar inte
  mönstret här — jag behöver se fler" utökas till upp mot 50. Om hon säger
  "detta räcker, det är entydigt" — stanna på 30. Antalet är en fältnära
  kalibrering, inte ett magiskt tal.
- Ingen ambition om statistiskt signifikans. Målet är att fånga *om* CD:ns
  omdöme är stabilt och användbart, inte att bevisa det med p-värde.

### Blandning (tre tiers — alla tre är lastbärande)
- **~10 gamla vinnare** — idéer som lanserats från RADON och som CD:n själv
  betraktar som topp-arbete.
- **~10 bortvalda-men-mänskliga** — idéer som fanns med i tidigare briefer och
  valdes bort, eller idéer skrivna av juniorer/praktikanter som inte gick vidare.
  Poängen: mänskligt gjort, men inte "bra nog". Om arkivet är svårt att gräva i
  får vi konstruera dem — det är ett accepterat arbete, inte en genväg vi tar.
- **~10 AI-baseline** — genererat via en fryst, versionerad "svag prompt"
  (se ADR 0001 punkt 1b). Detta representerar det maskin-standard-mediokra som
  hela projektet ska överträffa.

### Skala
- **5-gradig** skala per idé: `usel / svag / okej / bra / briljant`.
- **Topp-tail-flagga:** varje idé som får `briljant` markeras dessutom med
  binär "topp av topp"-flagga. Detta är essentiellt eftersom projektets
  centrala mått är topp-10%-fångsten, inte snittet.
- **Axlar:** enkelt att börja med — hela idén betygsätts som helhet. Uppdelning
  i separata axlar (originalitet, brand-fit, körbarhet) skjuts till när Fas 3
  faktiskt behöver dem. Vi introducerar inte apparatur som inte används än.

### Blindhet
- Bedömaren ser **bara `blind`-blocket** i schemat (se `data/schema/README.md`).
  Källtyp, kund, "varför den var bra" — hålls dold under bedömningen.
- ID:n är neutrala (`idea_<8 slump>`) och avslöjar inget om ursprung.
- Ingen kod som renderar bedömningsvyn får ha access till `source`-blocket.
  Detta är en regel, inte en riktlinje.

### Två sittningar med några veckors mellanrum
- **Sittning 1:** CD bedömer hela batchen. Sanity-checkaren gör stickprov på
  ~1/3 oberoende samma vecka.
- **Sittning 2 (2–3 veckor senare):** ~5 idéer från sittning 1 läggs in på nytt
  (nytt id, ny plats i listan) tillsammans med några få nya. Om CD:n betygsätter
  dem likadant som förra gången är linjalen stabil. Vinglar hon vet vi det innan
  vi bygger ovanpå den.

### Tidsbudget
- Hård tidsbox: **2 veckor total kalendertid** (från ADR 0001).
- Realistisk CD-tid: **3–4 timmar totalt**, uppdelat på två sittningar.

---

## Alternativ vi övervägde

### Alt A — Ursprungsplanen: större batch, ranking, flera bedömare
Genomförbart i teorin, orealistiskt i praktiken. En stor betygsättningsövning
för 3–4 seniorer stoppar projektet vid resursbudgeten. Ersätts av
ADR 0002 (en CD) + ADR 0003 (nedskalad volym).

### Alt B — "Bara vinnare"-simplifieringen
Föreslaget: skippa blindbedömning helt. Mata bara in gamla lanserade idéer som
funkat väl och använd dem som referens.

Detta valdes bort — inte som en gradvis försämring, utan för att det *bryter
själva mätfunktionen*. Skälen:

1. **En linjal utan markeringar mäter ingenting.** Endast vinnare ger ingen
   kontrast mellan "bra" och "inte bra". Man får en exempelsamling, inte en
   funktion från idé → kvalitet. Fas 1 (och alla senare faser) behöver *just*
   den funktionen för att avgöra om en ny AI-genererad idé är bra eller inte —
   utan en skala kan Fas 1 bara mäta *likhet* till gamla vinnare, inte
   *kvalitet* i sig själv.
2. **Likhet till vinnare är mode collapse baklänges.** Hela projektets syfte är
   att undvika att systemet optimerar mot det statistiskt sannolika. Att mäta
   nya idéer mot "hur lika de är RADON:s tidigare vinnare" är exakt att bygga
   in det problemet — bara med RADON:s smak som mittfåra i stället för
   internets. Systemet skulle systematiskt filtrera bort just den sortens
   idéer projektet finns för att hitta.
3. **Fas 1:s huvudmått blir omätbart.** Fas 1 ska svara på: "producerar
   verbalized sampling fler distinkta konceptfamiljer i toppskiktet än naiv
   generering?" Utan en skala som kan säga *var i toppskiktet* en ny idé
   ligger, kan Fas 1 bara räkna volym-variation — vilket man kan få genom att
   producera skräp med hög varians. Metriken kollapsar.
4. **Provenance blir en dold confound.** Om referensen bara består av vinnare
   (mänskliga) och nya AI-idéer, mäter CD:n troligen "det här känns
   människoskrivet vs. maskinskrivet" — inte "det här är en bra idé". Vi mäter
   då AI-detektering, inte kreativ smak. Den mittersta tiern
   (bortvalda-men-mänskliga) är exakt det som håller mätningen ärlig.

**Sammantaget:** "bara vinnare" är billigare, men billigare kostar hela
mätfunktionen. Nedskalningen som beslutats ovan (30–50 idéer, tre tiers,
2 sittningar) ger *samma* billighet på volym och tid — utan att offra
mekaniken som gör måttstocken till en måttstock.

### Alt C — 3-gradig skala
Övervägdes för snabbhet. Valdes bort eftersom projektet mäter *topp-tail*
(topp-10%). En hink för "topp" som inte skiljer "briljant" från "solid bra"
gör Fas 1:s viktigaste mätning suddig. 5-gradig + topp-tail-flagga är knappt
dyrare och bevarar den kritiska tail-informationen.

### Alt D — Separata axlar från början (originalitet / brand-fit / körbarhet)
Övervägdes eftersom Fas 3-urvalet kommer att arbeta på flera axlar. Valdes bort
för Fas 0 — vi introducerar inte apparatur som inte används än. Om Fas 0 visar
att en helhetsbedömning fungerar räcker det. Axlar läggs på i Fas 3 när de
faktiskt behöver användas för urval.

---

## Konsekvenser

- **`docs/phases/phase-0-yardstick.md`** skrivs om från placeholder till konkret
  playbook som speglar besluten här (görs i samma våg som denna ADR).
- **`README.md`** uppdateras så att "Fas 0" beskrivs med de konkreta
  parametrarna, och så att "varför inte bara vinnare"-invändningen besvaras
  synligt i texten — inte gömd i en ADR.
- **`data/schema/README.md`** är redan i linje med besluten
  (blind/source-split, tre `source_type`-värden). Ingen ändring behövs.
- **`src/radon/io.py`** (ännu ej byggd) måste när den kommer respektera
  reglerna: aldrig visa `source` vid rendering, generera neutrala id:n,
  validera schemat.
- **Frihetsgrad kvar:** CD:n får själv säga stopp vid 30 om det räcker, eller
  utöka mot 50 om det inte gör det. Detta är avsiktligt — vi mäter en människas
  smak, hon får ha en åsikt om när hon är kalibrerad.
- **Kill-signal:** om sittning 2 visar att CD:n betygsätter samma idé olika på
  ett sätt som inte kan förklaras av rimlig humör-drift, är linjalen inte
  stabil nog. Då stannar projektet innan Fas 1 startar och tar frågan tillbaka
  till planeringssamtalet — inte till ännu en Fas 0-runda på autopilot.
