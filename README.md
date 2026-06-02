# RADON Idea Engine

Internt R&D-projekt på Valtech RADON (Stockholm). Bygger en idégenererings-
motor som producerar **genuint oväntade** kreativa uppslag — inte mer
medel-output.

Slutanvändare är seniora kreatörer/CD:s. En *creative director* fungerar
som måttstockens auktoritet — hens kvalitetsomdöme är facit, inte en
benchmark-siffra. En andra läsare gör lätta stickprovskontroller mot
CD:ns blinda fläckar — som komplement, inte som parallell domare. Värdet
ligger inte i koden — det ligger i den lokala kunskapen vi genererar:
vad CD:n kallar bra, och vilka grepp som faktiskt flyttar nålen hos just
oss.

> Full regelbok för hur projektet ska byggas: se [`CLAUDE.md`](./CLAUDE.md).

## Centralt problem

Generella LLM:er producerar idéer som seniorer upplever som generiska.
Roten är **mode collapse** — modellen optimerar mot det statistiskt
sannolika, inte det oväntat vassa. Vi ändrar **frågan, ingången och
urvalet** runt modellen i stället för att röra modellens insida. Vi
tränar aldrig om en modell.

## Faser (byggs i ordning, en variabel i taget)

| Fas | Vad | Slutar med |
|---|---|---|
| **0 — Måttstocken** | CD bedömer blandat material blint; andra läsaren gör stickprovskontroll mot blinda fläckar. | CD:ns omdöme som måttstock; CD:ns konsekvens med sig själv över tid som stabilitetskontroll. |
| **1 — Enklaste greppet** | Naiv generering vs. verbalized sampling. | Mått: distinkta konceptfamiljer mot Fas 0-måttstocken. |
| **2 — Freak facts** | Cross-domain-material översatt till bärande mekanism, sen generering. | Paired test (samma brief med/utan). |
| **3 — Urvalet** | Pareto/flerdimensionellt urval + golv-grind + räddningskö. | Jämförelse: systemets urval vs. CD:ns på samma material. |
| **4 — Ihopsättning** | Bara komponenter som klarat sitt prov. | Här (inte förr) introduceras ev. orkestreringsramverk. |

Vi går inte vidare till nästa fas förrän den föregående mätts mot
måttstocken.

## De tre måtten som följer hela projektet

1. Andel idéer i toppskiktet (svansen, inte snittet).
2. Hur mycket bredare idérymden blir per brief (distinkta koncept, inte volym).
3. Kostnad per idé en människa faktiskt vill utveckla vidare.

## Centrala vägval — och varför

- **Måttstock först.** Vad bra är definieras *innan* generering och urval
  byggs. Annars hamnar vi i att mäta nya idéer mot proxy-mått som råkar
  gynna systemet vi just byggt. Måttstocken är facit; resten är hypoteser
  som ska prövas mot den.
- **En variabel i taget.** Två mekanismer i samma experiment går inte att
  attribuera. Naiv vs. verbalized sampling testas innan freak facts läggs
  på; freak facts innan urvalslogiken; urval innan ihopsättningen. Om vi
  blandar är resultatet värdelöst, hur lovande det än ser ut.
- **Människan har sista ordet.** LLM-as-judge korrelerar nära noll med
  experter på kreativ kvalitet. Modellen får triage:a och ge ledtrådar,
  men ingen automatisk poäng avgör vad som är briljant.
- **Måttstockens auktoritet: CD + sanity-check.** En *creative director*
  är måttstockens auktoritet. En andra läsare gör lätta stickprovs-
  kontroller mot CD:ns blinda fläckar — inte som parallell domare.
  Stabilitet mäts som CD:ns konsekvens med sig själv över tid, inte som
  inter-rater agreement mellan flera bedömare. Detta är ett medvetet byte
  från en tidigare modell med flera seniorer (se
  [`docs/decisions/`](./docs/decisions/), ADR 0002).
- **Elo och "svärm först" valdes bort.** Elo (och varje annan enda
  hopvägd totalpoäng) kollapsar mot det trygga — fel mekanism för ett
  system som ska skydda det vassa. Ersätts av Pareto/flerdimensionellt
  urval i Fas 3. Multi-agent-svärm hör hemma först i Fas 4 (om alls),
  inte som startarkitektur — mode collapse-greppen (frågan, ingången,
  urvalet) måste testas en variabel i taget innan det är meningsfullt
  att sätta ihop dem.

## Mappstruktur

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

## Komma igång

Förutsättningar: Python 3.11+.

```bash
# 1. Klona och gå in
git clone <repo-url> creative-ideas-agent-workflow
cd creative-ideas-agent-workflow

# 2. Skapa och aktivera venv
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

## Hur experiment körs

*Ingen kod för generering/mätning/urval ännu* — den byggs efter att
strukturen granskats och Fas 0-måttstocken har definierats i ett
separat planeringssamtal.

När de första skripten finns kommer en körning gå till så här:

1. Skapa en ny mapp i `experiments/` med namnet
   `YYYY-MM-DD_fasN_kort-namn/` och en `config.yaml` som anger prompt-
   version, provider, modell, datakälla.
2. Kör motsvarande skript i `scripts/` — det läser configgen, anropar
   modellen, skriver `run.log` + `results.jsonl` till experimentmappen
   och rådata till `results/runs/<run_id>/`.
3. Skriv `NOTES.md` i experimentmappen: vad du förväntade dig, vad du
   såg, vad du gör härnäst.

Se [`experiments/README.md`](./experiments/README.md) för obligatoriska
fält i `results.jsonl` (tokens, latens — krävs från första körningen för
att kostnad-per-idé ska bli mätbart).

## Arbetsdelning

- **Planering** (vad en fas ska göra, hur vi vet om den lyckats) sker i
  separat Claude-chatt.
- **Byggande, körningar, felsökning** sker här i Claude Code.
- **Versionshantering:** små commits, varje fas på egen gren. Fas 0 är
  mest datainsamling — egen gren valfritt.
- **Modellåtkomst:** via API. Multi-provider är en framtida hypotes
  (se [`CLAUDE.md`](./CLAUDE.md)). Nycklar i `.env`.

## Vad det här projektet INTE är

- Inte ett färdigt agent-orkestreringsramverk (CrewAI, AutoGPT etc.) —
  de döljer mekanik och förstör attribuerbarheten i tidiga faser.
- Inte en chatbot-wrapper.
- Inte ett system med en enda hopvägd totalpoäng för idékvalitet —
  flerdimensionellt urval (Pareto) i Fas 3 ersätter det. Brand fit är
  ett *golv*, inte ett optimeringsmål.
- Inte ett LLM-as-judge-system. Sluturval om vad som är briljant är
  alltid mänskligt.

[`docs/archive/`](./docs/archive/) innehåller en tidigare vision (djupt
multi-agent-system, LangGraph-core, React Flow-cockpit, Elo-ranking).
Den finns kvar som historik, **inte som en plan att följa**. Se
[`CLAUDE.md`](./CLAUDE.md) för detaljer.
