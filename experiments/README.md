# Experiments

En mapp per körning. Manuellt, men attribuerbart — viktigare än
automatisering i de tidiga faserna.

## Mappnamn

`YYYY-MM-DD_fasN_kort-namn/`

Exempel:
- `2026-06-15_fas0_first-blind-batch/`
- `2026-07-02_fas1_naive-vs-verbalized/`

## Innehåll per experimentmapp

| Fil | Vad |
|---|---|
| `config.yaml` | Allt som behövs för att återskapa körningen: prompt-version, provider, modell, temperatur, max_tokens, seeds, datakälla, mått som ska rapporteras. |
| `run.log` | Stdout/stderr från körningen. Plain text. |
| `results.jsonl` | En rad per modellanrop. Se schema nedan. |
| `NOTES.md` | Vad vi förväntade oss, vad vi såg, vad vi gör härnäst. Skrivs av människa, inte av kod. |

## `results.jsonl` — obligatoriska fält per rad

```jsonc
{
  "run_id": "2026-06-15_fas0_first-blind-batch",
  "call_index": 0,                  // löpnummer inom körningen
  "provider": "anthropic",          // anthropic | openai | google | ...
  "model": "claude-opus-4-7",       // exakt modell-id
  "prompt_name": "naive_generator", // refererar till en fil i prompts/
  "prompt_version": 1,              // versionsnummer
  "input_hash": "sha256:…",         // för att kunna gruppera identiska anrop
  "output": "…",                    // rå modell-output (eller pekare om stor)
  "tokens_in": 1234,                // för mått 3 (kostnad per idé)
  "tokens_out": 567,
  "latency_ms": 4321,
  "timestamp_utc": "2026-06-15T10:23:44Z"
}
```

**Varför obligatoriskt redan från första körningen:** "Kostnad per idé en
människa faktiskt vill utveckla vidare" är ett av projektets tre permanenta
mått. Utan tokens/latens från första körningen kan vi inte räkna det
retroaktivt.

## Git-konvention

Varje fas lever helst på sin egen gren (`phase-1-...`, `phase-2-...`).
Körningar inom fasen committas på den grenen. Det är så "en variabel i
taget" blir spårbart i historiken.

För Fas 0, som är mest datainsamling, är egen gren *valfritt*. Ingen
git-ceremoni för dess egen skull.

## Vad som *inte* hör hemma här

- Stora rå-output-blobbar i sig själva — pekare till `results/runs/<run_id>/`
  i stället. Den mappen är gitignored.
- Mänskliga omdömen — de bor i `results/evals/` (också gitignored, kan
  innehålla PII).
