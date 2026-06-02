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
| `industry` | blind | Kreativ kvalitet bedöms i kontext. |
| `brief_problem` | blind | "Löser den briefen?" är del av bedömningen. |
| `source_type` | source | Vetskap förstör experimentet direkt. |
| `client` | source | Kundnamn läcker källtyp och påverkar bedömningen. |
| `why_it_was_good` | source | Finns ofta bara för vinnare — läcker direkt. |
| `what_was_rejected` | source | Finns bara för "discarded" — läcker direkt. |
| `notes` | source | Kan innehålla läckage; renderas aldrig blint. |

`industry` är en gränsfråga: om branschen är så specifik att den
identifierar projektet, **normalisera** till en mer generell kategori
innan inmatning (`"nisch-fintech B2B norra Europa"` → `"banking/fintech"`).

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
