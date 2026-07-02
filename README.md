# RADON Idea Engine

Ett internt R&D-projekt på Valtech RADON (Stockholm) som försöker svara på en
enda fråga: **kan vi få AI att producera idéer som RADON:s seniorer faktiskt
kallar bra — inte bara fler medel-idéer?**

Värdet i det här projektet ligger inte i koden. Koden är avsiktligt enkel.
Värdet ligger i den lokala kunskap vi bygger upp om vad vår CD kallar bra,
och vilka grepp som faktiskt flyttar nålen hos just oss.

> Om du är helt ny: läs denna README från början till slut. Den är skriven
> för att en person utan bakgrund i projektet ska kunna gå in och bidra utan
> att först behöva fråga någon vad grejerna betyder.

---

## Från ursprungsbriefen till den här planen

Projektet startade med en brief till vår Creative Technologist: bygg *"ett
system som skapar världens mest attraktiva idéer"*. Skissen var konkret —
ett automatiserat AI-flöde med människan i loopen. En svärm scout-agenter
skulle skrapa nischade källor efter "freak facts", en brief-agent skulle
destillera kundens brief till en Single Minded Proposition, en tredje
input (Agency DNA) skulle bära RADON:s kreativa röst, en "mutation lab"
skulle producera 500 idéer, en "death match arena" med en konstitutionell
AI-domare skulle filtrera 500 → 10 på tre axlar (Surprise Score, Brand
Fit, Radon Style), och 10 idéer skulle levereras till Slack för människan.

### Vad briefen fick rätt

Innan vi säger något annat: briefen ställde rätt frågor. Det är viktigt.

- Den identifierade den centrala spänningen exakt: *hur infuserar vi
  RADON:s kollektiva kreativa röst i lösningarna, samtidigt som varje
  kreatör gör sin egen röst starkare — hur sätter vi upp ett lärande
  system?* Det är projektets själva syfte, och den frågan driver
  fortfarande hela planen.
- Den var **ärligt osäker**. "Effekt: bättre idéer?!" med frågetecken —
  den som skrev visste att detta var en hypotes att pröva, inte en
  garanti. Det är en styrka, inte en svaghet.
- **Grundsekvensen brief → insikt → idé** är sund. Den lever kvar
  oförändrad i den nya planen.
- **Freak facts som kreativ disruptor** är en stark intuition — det visar
  sig ha det starkaste forskningsstödet av alla grepp vi granskat för att
  skapa nya vinklar.
- **Människan i loopen fanns med från början.** Rätt instinkt.

Kort sagt: briefen var en skarp första skiss, inte ett misstag. Det som
följer är inte en kritik utan en uppdatering baserad på vad tre oberoende
forskningsdjupdykningar visade oss efteråt.

### Var briefen inte är optimalt utformad — och varför

Tre saker skulle brista om vi byggde exakt efter skissen. Varje punkt är
bekräftad av forskning, inte tycke.

**1. "Death match"-domaren skulle systematiskt välja fel.** Att låta en
AI-domare betygsätta 500 idéer på tre axlar (Surprise, Brand Fit, Radon
Style) och slå ihop till en ranking låter rigoröst. Problemet är att en
sammanvägd poäng gynnar systematiskt den *jämna, säkra* idén över den
*vågade, ojämna*. Maskinen blir tyst en mediokritetsförstärkare som rensar
bort exakt de sällsynta briljanta idéerna en byrå på RADON:s nivå lever
av. Dessutom: AI:ns omdöme om kreativ kvalitet korrelerar nära noll med
mänskliga experters — så även utan sammanvägningen är den fel instans att
låta bestämma vad som är briljant.

**2. Att bygga hela svärmen samtidigt gör det omöjligt att veta vad som
funkade.** Om scout-svärm, brief-agent, mutation lab, domare och Slack-
leverans byggs på en gång, och något inte funkar, kan man inte peka på
vilken del som orsakade det. Multi-agent-system fallerar dessutom i en
stor andel fall — och nästan alla fel sitter i *skarvarna* mellan
agenterna, inte i agenterna själva. Merparten av tiden skulle gå åt till
att laga skarvar innan vi ens vet om själva agenterna var värda att sätta
ihop.

**3. Ingen linjal — bara en känsla av att det blev bättre.** Skissen
saknar en definierad måttstock för vad "bra" betyder hos RADON. Utan en
sådan kan man *känna* att ett nytt grepp hjälpte, men inte veta det. Det
är skillnaden mellan bevisad kunskap och gissning — och det är just den
bevisade kunskapen som är projektets faktiska värde för byrån (koden i
sig är enkel och kopierbar; kunskapen om vår smak är det inte).

### Vad vi gör i stället — enkelt förklarat

Vi bytte **inte målbild.** Visionen står: ett lärande system som gör varje
kreatör vassare och infuserar RADON:s röst. Vi bytte *vägen dit* så att
den blir mätbar, attribuerbar och håller över tid.

| Vad briefen föreslog | Vad vi gör i stället | Varför |
|---|---|---|
| Bygg hela svärmen först | Bygg en **linjal** först (Fas 0), testa sedan **ett grepp i taget** (Fas 1–3) | Vi vill veta *varför* något funkar, inte bara att det gör det. |
| AI-domare med hopvägd poäng | **Människa** som sista domare + flera axlar utan totalpoäng | Sammanvägning kollapsar mot det trygga; AI korrelerar nära noll med experter på kreativ kvalitet. |
| Freak facts direkt in i prompten | Freak facts **översatta till bärande kreativ mekanism** först | Rått inkastade sänker kvaliteten; översatta höjer den. |
| Svärm av agenter från dag 1 | **Svärm i sista fasen**, av bara delar som bevisat sig | Nästan alla svärm-fel sitter i skarvarna, inte i agenterna. |
| "Bättre idéer?!" som förhoppning | **Uppmätt effekt** mot CD:ns omdöme, i varje fas | Det är enda sättet att veta om något faktiskt hjälpte. |

Resultatet är att projektet levererar **två saker** i stället för ett:
verktyget som ursprungsbriefen bad om, *plus* bevisad kunskap om vad som
faktiskt får AI att producera oväntad kreativitet hos just RADON. Den
kunskapen är det som gör verktyget svårt att kopiera. Koden är det inte.

Hela detaljerade resonemanget med referenser till forskningen finns i
[`docs/from-brief-to-plan.md`](./docs/from-brief-to-plan.md).
Ursprungsbriefens fulla arkitektur är bevarad i
[`docs/archive/`](./docs/archive/) som historik.

---

## Problemet vi försöker lösa

Standard-AI (Claude, GPT, Gemini) producerar oftast idéer som seniorer
upplever som *generiska*. Modellerna optimerar mot det statistiskt sannolika,
inte det oväntat vassa. Forskningsvärlden kallar det "mode collapse":
modellen dras mot mitten och skriker aldrig ut något udda.

**Vi tränar aldrig om en modell.** Vi ändrar tre saker *runt* modellen:

1. **Frågan** vi ställer den (Fas 1).
2. **Ingången** vi ger den (Fas 2).
3. **Urvalet** som avgör vilka svar som går vidare (Fas 3).

Först i slutet sätter vi ihop de delar som bevisat sig (Fas 4). Innan något
av det görs bygger vi en linjal så vi vet vad "bra" betyder hos just oss
(Fas 0).

---

## Vad "bra" betyder här — och vem som bestämmer

I det här projektet är **RADON:s creative director** måttstockens auktoritet.
Hennes omdöme *är* linjalen — inte en benchmark, inte ett riktvärde. En andra
läsare gör lätta stickprovskontroller mot CD:ns blinda fläckar, som
komplement, inte som parallell domare.

- Vi mäter aldrig kreativ kvalitet automatiskt. En AI-domare får inte sista
  ordet — dess omdöme om kreativ kvalitet korrelerar nära noll med experters.
- Vi bygger ingen "totalpoäng" som slår ihop originalitet + brand-fit + …
  till en siffra. En sådan sammanvägning gynnar systematiskt det trygga
  över det vågade — precis det vi *inte* vill.
- Sluturvalet om vad som är briljant är alltid mänskligt.

Se [`docs/decisions/0002-mattstockens-auktoritet.md`](./docs/decisions/0002-mattstockens-auktoritet.md)
för hela resonemanget om varför en CD och inte en jury.

---

## De fem faserna — översikt

Byggs i ordning. Vi går inte vidare till nästa fas förrän den föregående är
uppmätt mot linjalen.

| Fas | Vad | Varför just nu | Slutar med |
|---|---|---|---|
| **0 — Bygg linjalen** | CD betygsätter en blandad batch (vinnare + bortvalda + AI-baseline) blint. | Utan en linjal kan vi inte veta om senare faser gör något bra. | Ett bedömt underlag som allt senare mäts mot. |
| **1 — Testa frågan** | Jämför en "naiv" prompt ("ge tio idéer") mot en som ber om bredden av tänkbara idéer. | Detta är det enklaste och billigaste greppet mot mode collapse — måste testas först innan vi lägger på något tyngre. | Har breddgreppet effekt? Om ja, hur mycket? |
| **2 — Testa ingången** | Mata in "överraskande fakta" från andra fält (freak facts), översätt dem till en bärande kreativ mekanism, generera sen. | Om Fas 1 hjälpte lite — hjälper detta *mer*, och gör det något Fas 1 inte gjorde? Måste testas separat. | Tillför freak facts användbar nyhet ovanpå Fas 1? |
| **3 — Testa urvalet** | Bygg ett urval som väljer på *flera axlar samtidigt* (inte en totalpoäng). En idé sparas om den är bäst på minst en dimension. | Hittills har vi bara genererat. Nu prövar vi om urvalslogiken själv skyddar det vassa bättre än en sammanvägd poäng. | Väljer systemets urval samma "topp" som seniorerna? |
| **4 — Sätt ihop** | Bara komponenter som klarat sitt prov. Här (inte förr) introduceras ev. orkestreringsramverk eller flera agenter. | Att sätta ihop delar innan vi vet vad de bidrar med gör alla fel osynliga. | Ett sammansatt system där varje del har ett dokumenterat bidrag. |

---

## Varför den här ordningen

Läs raden ovan, sedan denna:

- **Linjalen först.** Om vi byggde en idégenerator först och sedan hittade på
  ett mått i efterhand skulle måttet nästan garanterat råka gynna det vi just
  byggt. Detta är projektets viktigaste beslut — se
  [`docs/from-brief-to-plan.md`](./docs/from-brief-to-plan.md) för hur vi kom
  hit.
- **Enklaste greppet först.** Om det billiga (att bara ändra prompten) redan
  löser 80% av problemet är det slöseri att lägga på freak facts,
  urvalslogik och orkestrering. Vi vet inte förrän vi testat.
- **Freak facts *före* urvalslogik.** Freak facts påverkar *vad* som
  genereras. Urvalet påverkar *vilket* som väljs. De rör olika ändar av
  problemet — och om båda ändras samtidigt kan vi inte veta vilken som gjorde
  jobbet. En variabel i taget.
- **Ihopsättning sist.** Multi-agent-svärmar fallerar i skarvarna, inte i
  agenterna. Att bygga hela svärmen först är exakt att lägga tiden på att
  laga skarvar utan att veta om själva agenterna är värda att sätta ihop.

Om något går sönder i den här ordningen kan vi peka på *en* sak som gick
sönder. Om vi bygger allt på en gång blir varje fel omöjligt att attribuera —
och ett projekt utan attribuerbarhet är ett gissningsspel.

---

## Fas 0 i detalj — det vi bygger just nu

Se
[`docs/phases/phase-0-yardstick.md`](./docs/phases/phase-0-yardstick.md) för
den fullständiga playbook:en. Kort version:

1. **Samla 30–50 idéer** i tre tiers: ~10 gamla vinnare, ~10 bortvalda-men-
   mänskliga, ~10 AI-baseline (från en fryst svag prompt).
2. **Neutralisera dem** enligt schemat i `data/schema/` — bedömaren får aldrig
   se vilken tier en idé kommer från.
3. **CD bedömer** blint på 5-gradig skala (`usel / svag / okej / bra /
   briljant`) med extra "topp av topp"-flagga för de allra bästa. Ingen
   ranking, bara absolutbedömning per idé.
4. **Sittning 2, 2–3 veckor senare:** några idéer från sittning 1 läggs in
   igen — betygsätter CD:n dem likadant är linjalen stabil.
5. **Rapport i `results/reports/`** som beskriver vad vi såg.

Parametrarna är fastställda i
[`docs/decisions/0003-nedskalad-fas-0.md`](./docs/decisions/0003-nedskalad-fas-0.md).

### Vanlig fråga: "Varför inte bara stoppa in gamla vinnare som referens?"

Rimlig fråga — den skulle spara massor av tid. Men den bryter linjalen.

En linjal utan markeringar mäter ingenting. Om vi bara stoppar in vinnare
har CD:n bara sett en typ av material. Hon kan då säga *"den där liknar en
gammal vinnare"* om nya AI-idéer, men inte *"den där är en bra idé i sig
själv"*. Skillnaden är hela projektets syfte:

- Det första är **likhet till RADON:s tidigare produktion** — vilket är
  exakt mode collapse i baklänges. Systemet skulle systematiskt filtrera
  bort just den sortens överraskande idéer projektet finns för att hitta.
- Det andra är en **kvalitetsfunktion** som kan användas på framtida idéer
  systemet aldrig sett förr.

Bortvalda-mänskliga idéer (den mittersta tiern) är också lastbärande. Utan
dem blir referensmaterialet bara vinnare + AI-baseline — och då mäter CD:n
troligen "det här känns människoskrivet" i stället för "det här är bra".
Vi mäter då AI-detektering, inte kreativ smak.

Nedskalningen till 30–50 idéer och två sittningar ger nästan samma
tidsbesparing som "bara vinnare" skulle ha gjort — utan att offra mekaniken.
Hela resonemanget finns i
[ADR 0003](./docs/decisions/0003-nedskalad-fas-0.md).

---

## De tre måtten som följer hela projektet

Oavsett fas rapporterar vi:

1. **Andel idéer i toppskiktet.** Vi mäter *svansen* (topp-10%), inte snittet.
   Mode collapse syns i vad som saknas i toppen — inte i mediansvaret.
2. **Bredd per brief.** Hur många genuint distinkta koncept fick vi per brief,
   inte hur många rader.
3. **Kostnad per idé en människa faktiskt vill utveckla vidare.** Räknas i
   modelltokens och latens. Uppmäts från *första* körningen — annars kan vi
   inte räkna det retroaktivt.

---

## Regler som gäller genom hela projektet

- **En variabel i taget.** Två mekanismer i samma experiment går inte att
  attribuera. Om det ser lovande ut men vi inte vet varför — är det värdelöst.
- **Måttstock först.** Vi hittar aldrig på ett kvalitetsmått i efterhand som
  ersätter CD:ns omdöme.
- **Ingen totalpoäng.** Brand fit är ett *golv* (idén får inte vara off-brand),
  inte ett optimeringsmål. Se Fas 3.
- **Människan har sista ordet.** LLM-as-judge korrelerar nära noll med
  experter på kreativ kvalitet. Modellen får triage:a och föreslå — aldrig
  avgöra.
- **Modeller anropas via API**, inte via chattgränssnitt. Nycklar i `.env`
  (aldrig i kod, prompts eller commits).

Full regelbok för Claude Code som arbetar i repot: [`CLAUDE.md`](./CLAUDE.md).

---

## Mappstrukturen — vad ligger var och varför

```
data/
  raw/         # gitignored — gamla idéer, briefer (sekretess)
  processed/   # gitignored — eval-redo material
  schema/      # committad — JSON Schema + exempel + tom mall för Fas 0-input
docs/
  archive/     # historik (ursprungsvision — följs INTE som arkitektur)
  phases/      # en kort doc per fas: mål, design, resultat
  decisions/   # ADR:er — vad vi valde och vad vi valde bort
prompts/       # versions-committade prompts (git ÄR prompt-versioneringen)
src/radon/     # importerbar Python-paketkärna — växer per fas
experiments/   # en mapp per körning: config + run.log + results.jsonl + NOTES.md
results/
  runs/        # gitignored — rå output från experiment
  evals/       # gitignored — mänskliga omdömen (kan innehålla PII)
  reports/     # committad — kort markdown-sammanfattning per experiment
scripts/       # tunna CLI-entrypoints, ingen affärslogik
```

**Vad hör hemma var — snabbregler:**

- Ska jag lägga in en gammal idé för Fas 0? → som JSON enligt schemat i
  `data/schema/`, filen läggs i `data/raw/` (gitignored).
- Ska jag ändra en prompt? → skapa **ny fil** i `prompts/` med versionsnummer
  (`naive_generator_v2.md`). Skriv aldrig över en tidigare version som redan
  körts — då bryts spårbarheten.
- Ska jag skriva en modul med logik? → i `src/radon/`. CLI-skript i
  `scripts/` bara *anropar* den logiken.
- Ska jag dokumentera ett vägval? → som ADR i `docs/decisions/`.
- Var lever själva idéerna som CD betygsätter? → `data/raw/` (gitignored,
  sekretess).

---

## Så här körs ett experiment (när koden finns)

Ingen kod för generering/mätning/urval finns ännu. Först ut är Fas 0. Så här
går en körning till när skripten finns:

1. **Skapa en ny experimentmapp:** `experiments/YYYY-MM-DD_fasN_kort-namn/`
   med en `config.yaml` som anger prompt-version, provider, modell, datakälla.
2. **Kör motsvarande skript i `scripts/`:** det läser configgen, anropar
   modellen, skriver `run.log` och `results.jsonl` till experimentmappen och
   rådata till `results/runs/<run_id>/`.
3. **Skriv `NOTES.md`** i experimentmappen: vad du förväntade dig, vad du
   såg, vad du gör härnäst. Skrivs av människa, inte av kod.

Obligatoriska fält i `results.jsonl` (tokens och latens från första körningen)
listas i [`experiments/README.md`](./experiments/README.md). Utan dem kan vi
inte räkna mått 3 retroaktivt.

---

## Kom igång

Förutsättningar: Python 3.11+.

```bash
# 1. Klona och gå in
git clone <repo-url> creative-ideas-agent-workflow
cd creative-ideas-agent-workflow

# 2. Skapa och aktivera virtual environment
python -m venv .venv
source .venv/bin/activate

# 3. Installera paketet i editable mode
pip install -e .
# Lägg till multi-provider-stöd (OpenAI, Google) när det behövs:
# pip install -e ".[multi-provider]"

# 4. API-nycklar
cp .env.example .env
# Fyll i ANTHROPIC_API_KEY (och ev. övriga)
```

---

## Arbetsdelning (två rum, två syften)

- **Planering** — *vad* en fas ska göra och *hur vi vet om den lyckades* —
  sker i en separat Claude-chatt. Där tas de tunga arkitekturbesluten.
- **Byggande, körningar, felsökning** sker här i Claude Code. Här skriver vi
  kod, kör experiment och dokumenterar utfall.

Vi blandar inte ihop dem. Stora arkitekturbeslut lyfts som frågor först,
byggs inte som egen handling.

**Git-hygien:**
- Små commits med tydliga meddelanden.
- Varje fas helst på egen gren (`phase-1-…`, `phase-2-…`).
- Fas 0, som är mest datainsamling, kan leva på main.
- Inga destruktiva git-operationer (force push, rebase av delad historik,
  radering av grenar) utan att fråga först.

---

## Vad det här projektet INTE är

- Inte ett färdigt agent-orkestreringsramverk (CrewAI, AutoGPT etc.). Färdiga
  ramverk döljer mekanik och förstör attribuerbarheten i de tidiga faserna.
- Inte en chatbot-wrapper.
- Inte ett system med en totalpoäng för idékvalitet. Fas 3 använder flera
  axlar samtidigt — brand fit är ett *golv*, inte ett optimeringsmål.
- Inte ett LLM-as-judge-system. Sluturvalet är alltid mänskligt.

---

## Beslutsdokument och historik

Vägvalen dokumenteras som ADR:er i [`docs/decisions/`](./docs/decisions/).
För en översikt av *varför* nuvarande plan skiljer sig från ursprungsbriefen,
se sektionen [Från ursprungsbriefen till den här planen](#från-ursprungsbriefen-till-den-här-planen)
högst upp — och för hela resonemanget med forskningsreferenser:
[`docs/from-brief-to-plan.md`](./docs/from-brief-to-plan.md).

De aktiva besluten:

- [ADR 0001](./docs/decisions/0001-stresstest-av-projektplanen.md) — red team
  mot nuvarande plan (Fixa nu, bär med dig, medvetet avstå).
- [ADR 0002](./docs/decisions/0002-mattstockens-auktoritet.md) — en CD som
  måttstockens auktoritet, inte flerbedömar-konsensus.
- [ADR 0003](./docs/decisions/0003-nedskalad-fas-0.md) — konkreta parametrar
  för Fas 0 (30–50 idéer, tre tiers, 5-gradig skala), och varför
  "bara vinnare"-genvägen inte fungerar.

Ursprungsbriefens fulla arkitektur finns bevarad i
[`docs/archive/`](./docs/archive/) som historik — den följs *inte* som
arkitektur.
