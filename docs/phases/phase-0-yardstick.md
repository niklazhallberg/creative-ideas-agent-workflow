# Fas 0 — Bygg linjalen

> **En mening:** Innan vi bygger en idémaskin bygger vi ett sätt att mäta om
> idéerna den producerar är bra — vår CD:s omdöme, formaliserat.

**Status:** Aktiv. Konkreta parametrar beslutade i
[ADR 0003](../decisions/0003-nedskalad-fas-0.md).
Auktoritetsmodellen (en CD + sanity-check) beslutad i
[ADR 0002](../decisions/0002-mattstockens-auktoritet.md).

---

## Varför denna fas kommer först

Om vi först byggde en idégenerator och *sedan* frågade "är idéerna bra?" har vi
inget att jämföra mot. Vi skulle hitta på ett kvalitetsmått i efterhand — och
det måttet skulle nästan garanterat råka gynna precis det system vi just byggt.

Så vi vänder på det. **Vi definierar "bra" först.** Sedan kan varje mekanism vi
lägger på i Fas 1–3 mätas mot exakt samma linjal, och vi kan säga *ärligt* vad
som hjälpte och vad som inte gjorde det.

"Linjalen" är kort och gott: **CD:ns omdöme, gjort blint, kalibrerat mot ett
representativt sträck från usel till briljant.** Det är den vi återkommer till
i varje fas.

---

## Vad linjalen består av (tre "tiers")

Vi mäter en linjal genom att markera positioner längs den. Vi visar CD:n en
blandning av idéer från tre olika håll:

| Tier | Vad | Ungefär | Vad den representerar |
|---|---|---|---|
| **Vinnare** | Gamla RADON-idéer som lanserats och funkat bra | ~10 st | "Topp-mänskligt arbete på RADON" |
| **Bortvalda-men-mänskliga** | Idéer från gamla briefer som valdes bort, eller juniorförslag som inte gick vidare | ~10 st | "Mänskligt gjort men inte bra nog" |
| **AI-baseline** | AI-genererade idéer från en enkel, fryst "svag prompt" | ~10 st | "Maskin-standard-mediokert — det vi ska överträffa" |

**Totalt: 30–50 idéer.** Börja på 30. Utöka bara om CD:n själv säger
"jag hittar inte mönstret här — jag behöver se fler".

### Varför alla tre tiers behövs

Om vi bara stoppade in vinnare hade CD:n bara sett en typ av idé — hon hade
inte kunnat kalibrera "bra" mot "okej" och "svag". Då blir linjalen bara en
exempelsamling: den kan säga *"den där liknar en gammal vinnare"*, men inte
*"den där är en bra idé i sig själv"*. Skillnaden är hela projektet — det
utvecklas mer i [ADR 0003](../decisions/0003-nedskalad-fas-0.md).

Om vi bara hade vinnare + AI-baseline hade CD:n troligen betygsatt
"det här känns människoskrivet" i stället för "det här är en bra idé". Den
mittersta tiern (bortvalda mänskliga) är den som håller mätningen ärlig.

---

## Så här genomförs fasen — steg för steg

### Steg 1: Samla in materialet

- **Vinnare (~10):** be CD:n själv välja ut ~10 gamla RADON-idéer som hon
  betraktar som topp. Lagras enligt schemat i `data/schema/` som
  `source_type: "winner"`.
- **Bortvalda (~10):** gräv i arkivet efter idéer som fanns med i tidigare
  briefer men valdes bort, eller be juniorer om förslag som inte gick vidare.
  Lagras som `source_type: "discarded"`. Går det inte att gräva fram — då
  *konstruerar* vi materialet (t.ex. be en junior göra ett medelmåttigt
  förslag på samma brief som en vinnare-idé löste). Det är ett accepterat
  arbete, inte en genväg.
- **AI-baseline (~10):** genereras från en fryst, versionerad "svag prompt".
  Se ADR 0001 punkt 1b — baseline-prompten är själv en artefakt som ska bo
  under `prompts/` med versionsnummer. Lagras som `source_type: "ai_baseline"`.

**Var i repot:**
- Schemat idéerna ska följa: `data/schema/idea.schema.json`
  (se README där för fältuppdelning blind/source).
- Själva idé-instanserna: `data/raw/` (gitignored — sekretess).
- Baseline-prompten: `prompts/naive_baseline_v1.md` (skapas när den behövs).

### Steg 2: Neutralisera och blanda

- Varje idé får ett neutralt id (`idea_<8 slump>`). Aldrig `idea_winner_2024_x`
  eller något som avslöjar källa.
- En sammansatt lista skapas där ordningen är slumpad. Den delen kan (och bör)
  automatiseras när det finns kod, men görs manuellt så länge.
- **Källblocket (`source`) får aldrig visas för bedömaren.** Det är en regel
  som måste hålla i verktyget som renderar listan.

### Steg 3: Sittning 1 — hela batchen bedöms

CD:n får se listan i den slumpade ordningen. Per idé:

- **5-gradig skala:** `usel` / `svag` / `okej` / `bra` / `briljant`.
- **Topp-tail-flagga:** om en idé får `briljant` markeras den dessutom med en
  binär "topp av topp"-flagga. Detta är essentiellt eftersom projektet mäter
  toppskiktet (topp-10%), inte snittet.
- **Ingen ranking mellan idéer.** Bara absolutbedömning per idé — det är
  snabbare och mindre påverkat av ordningen.

**Sanity-checkaren** går parallellt igenom ~1/3 av materialet oberoende samma
vecka. Målet är inte att jämföra siffror utan att fånga fall där CD:ns omdöme
är idiosynkratiskt snarare än självklart. Oenighet är en *signal* om att just
den idén är gränsfall — inte ett problem att medla.

**Var i repot:**
- Bedömningarna skrivs till `results/evals/` (gitignored — kan innehålla PII).
  Formatet fastställs i första körningen och beskrivs där.

### Steg 4: Sittning 2 — stabilitetstest (2–3 veckor senare)

- ~5 av idéerna från sittning 1 läggs in igen — nytt id, ny plats i listan,
  gärna med några få nya idéer runtom så att sittningen inte är uppenbart
  bara en test.
- CD:n betygsätter dem utan att veta vilka som är återkommande.
- **Om samma idé får ungefär samma betyg** som förra gången → linjalen är
  stabil. Gå vidare till Fas 1.
- **Om samma idé får dramatiskt olika betyg** utan rimlig förklaring → linjalen
  vinglar. Stanna. Ta frågan tillbaka till planeringssamtalet innan Fas 1
  startar.

### Steg 5: Analys efter bedömningarna

Först *nu* öppnas `source`-blocket och kopplas till bedömningarna. Frågor att
besvara:

- Hur fördelade sig vinnare, bortvalda och AI-baseline över skalan? Landade
  vinnare typiskt högre?
- Fanns det AI-baseline-idéer som CD:n gav `bra` eller `briljant`? Om så — vad
  hade de gemensamt? (Det är en tidig signal om vad "att slå baseline" faktiskt
  betyder.)
- Fanns det vinnare som CD:n gav `okej` eller lägre? (Kan tyda på att CD:ns
  smak har utvecklats, eller att ideén blev framgångsrik av skäl som inte syns
  i själva idébeskrivningen.)
- Korrelerar någon billig automatisk proxy (t.ex. embedding-diversitet, längd,
  antal namngivna entiteter) med CD:ns omdöme? Om ja — det är en möjlig
  triage-signal för senare faser. Om nej — det är också ett giltigt resultat
  och bekräftar att människan måste vara kvar i loopen.

Resultatet skrivs som en kort rapport i `results/reports/`
(`YYYY-MM-DD_fas0_rapport.md`).

---

## Vad fasen producerar

När fas 0 är klar ska vi ha:

1. **En bedömd batch** (30–50 idéer × två sittningar × betyg per idé) — det
   *är* linjalen som Fas 1–3 sedan mäter sig mot.
2. **Ett stabilitetsresultat** — säger vi ja eller nej till att gå vidare?
3. **En kort rapport** — vad vi såg, vad som förvånade, vad som är kvar att
   nöta på.
4. **Eventuellt en billig proxy-signal** — bara om någon faktiskt korrelerar
   med CD:ns omdöme. Aldrig som ersättning för hennes bedömning, bara som
   triage för att snabba upp senare faser.

---

## Vad fasen medvetet *inte* producerar

- **Ingen "totalpoäng" eller ranking mellan idéer.** Absolutbedömning per idé,
  inget mer. Elo och sammanvägda poäng ligger utanför scope, permanent
  (`CLAUDE.md`).
- **Ingen automatisk domare.** LLM-as-judge introduceras inte i Fas 0 —
  människan är hela linjalen.
- **Inga separata axlar** (originalitet / brand-fit / körbarhet ännu). Vi
  börjar med helhetsbedömning. Axlar läggs på i Fas 3 där urvalet faktiskt
  behöver dem.

---

## När fasen är klar

Klart-kriterium är enkelt: **linjalen är stabil (sittning 2 stämmer) och vi
har en rapport som beskriver den.** Då — och först då — startar Fas 1.

Om stabiliteten inte håller stoppar vi. Det är inte ett projektfel; det är
information. Vi tar det tillbaka till planeringssamtalet innan vi bygger
vidare.
