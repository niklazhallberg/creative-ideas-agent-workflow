# Data schema — Fas 0 idé-input

Filer:
- `idea.schema.json` — JSON Schema (formellt kontrakt)
- `idea.example.json` — ett fullt ifyllt exempel (källtyp: winner)
- `idea.template.json` — tom mall att utgå från när nya idéer matas in

## Varför schemat är delat i `blind` och `source`

Fas 0 mäter måttstocken via **blindbedömning**: seniorer betygsätter
blandat material utan att veta om en idé är en gammal vinnare, en bortvald
eller en AI-baseline. Om källan läcker under bedömningen är studien
värdelös — det är därför splitten är inbyggd i schemat, inte en
efterhandsfix.

Schemat har två block:

- `blind` — det bedömaren ser. Bara fält som **inte avslöjar källan**
  (idéns text, bransch, vad briefen skulle lösa).
- `source` — metadata som **hålls dold** under bedömningen och bara
  används för analys efteråt (källtyp, kund, varför vinnaren var bra,
  vad som valdes bort).

> Verktyget som renderar idéer för bedömning ska aldrig visa något från
> `source`. Det är en regel, inte en stilfråga.

## ID-konvention

Varje idé får ett **neutralt** id på formen `idea_<8 lowercase
alphanum>` (regex i schemat: `^idea_[a-z0-9]{8}$`). Slumpgenererat. Inget
semantiskt innehåll — annars riskerar id:t självt avslöja källan. Ett id
som `idea_winner_2024_xyz` är inte ok.

## Fältplacering — varför vad ligger var

| Fält | Var | Varför |
|---|---|---|
| `idea_text` | blind | Det här är vad bedömaren bedömer. |
| `industry_category` | blind | Kreativ kvalitet bedöms i kontext, men kategorin är **grov** och får inte i sig identifiera kunden. |
| `brief_problem` | blind | "Löser den briefen?" är del av bedömningen. |
| `source_type` | source | Vetskap förstör experimentet direkt. |
| `client` | source | Kundnamn läcker källtyp och påverkar bedömningen. |
| `industry_specific` | source | Granulär bransch (t.ex. "nisch-fintech B2B norra Europa") kan identifiera kunden — dold. |
| `why_it_was_good` | source | Finns ofta bara för vinnare — läcker direkt. |
| `what_was_rejected` | source | Finns bara för "discarded" — läcker direkt. |
| `notes` | source | Kan innehålla läckage; renderas aldrig blint. |

Bransch är delad i två fält för att hålla blindbedömningen ren. Den grova
kategorin ger bedömaren kontext utan att avslöja kunden. Den specifika
branschen — där läckage faktiskt kan ske — bor i `source` och används bara
i analysen efteråt.

## Villkorad regel (enforced i schemat)

För `source_type: "ai_baseline"` är `why_it_was_good` och `what_was_rejected`
**förbjudna** — JSON Schemats `if/then` förkastar instanser som har dem.
Båda fälten är källspecifika annoteringar som bara finns för riktiga gamla
idéer; om de smyger sig in på en AI-baseline pekar det på en buggig
pipeline, inte på en konstig idé. Skydd som regel, inte som instruktion
i prosa.

## Tillåtna grova branschkategorier (`blind.industry_category`)

Normerad lista. Välj **alltid** ett av dessa värden:

- `finans` — bank, försäkring, kapitalförvaltning, fintech, betalningar
- `FMCG` — livsmedel, dryck, hygien, hushåll
- `detaljhandel` — fysisk handel, e-handel, marketplaces (icke-FMCG-varor)
- `tech` — mjukvara, SaaS, hårdvara, AI-tjänster
- `telecom` — operatörer, bredband, mobiloperatörer
- `media` — broadcast, streaming, förlag, gaming
- `automotive` — fordon, mobilitet, laddinfrastruktur
- `energi` — el, gas, förnybart, energitjänster
- `hälsa` — pharma, medtech, vårdgivare, hälsotjänster
- `offentlig sektor` — myndigheter, kommuner, statliga bolag
- `ideell` — välgörenhet, NGO, opinionsbildning
- `B2B-tjänster` — konsultbolag, professional services, industri-B2B
- `konsument-tjänster` — resor, mat ut, prenumerationstjänster, försäkringsbroker

**Behövs en kategori som inte finns?** Lägg först till den i den här
listan (via en commit som bara gör det), motivera kort varför den behövs,
och mata sen in idén. Lagen är: schemats blindhet får aldrig ad hoc-spädas
ut genom drift mot mer specifika kategorier.

## Var data faktiskt lagras

Schemafilerna här är committade. Själva idé-instanserna (en JSON per idé
eller en JSONL med en idé per rad) hör hemma i `data/raw/` eller
`data/processed/` — **båda gitignored**. Originalmaterialet från RADON
är sekretessbelagt; det får aldrig committas.

## Validering

JSON Schema-filen är gjord för standard validators (Python: `jsonschema`,
Node: `ajv`, etc.). En valideringshelper kommer i `src/radon/io.py` när
Fas 0-arbetet startar — för nuvarande räcker det att en mänsklig läsare
kan följa schemat när idéer matas in manuellt.
