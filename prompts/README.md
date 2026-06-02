# Prompts

Alla prompts är committade och versionerade. **Git ÄR
prompt-versioneringen** tills något konkret visar att vi behöver mer
(t.ex. PromptLayer, Langfuse). Avsiktligt enkelt.

## Filformat

En prompt = en fil med YAML-frontmatter följt av prompt-text:

```markdown
---
name: naive_generator
version: 1
fas: 1
syfte: Baseline för Fas 1 — naiv "ge tio idéer"-prompt utan verbalized sampling
input_variabler: [brief, antal_idéer]
ändringslogg:
  - v1 (2026-06-02): första versionen
---

Du är en kreativ direktör. För följande brief, ge {antal_idéer} idéer:

{brief}
```

## Namnkonvention

`<roll>_<variant>_v<N>.md` — t.ex. `naive_generator_v1.md`,
`verbalized_generator_v1.md`, `freak_fact_translator_v2.md`.

Skapa **alltid en ny fil** för en ny version. Skriv aldrig över en
tidigare version som redan har körts — då tappar vi attribuerbarheten
i `experiments/`-loggarna som refererar till den.

## Vad som hör hemma här

- Genereringsprompts (Fas 1+).
- Översättningsprompts (Fas 2, freak fact → bärande mekanism).
- Eventuella judge-prompts om de används som *triage*, aldrig som facit.

## Vad som *inte* hör hemma här

- Hårdkodade prompt-strängar i Python. Allt som anropas mot en modell
  ska bo här som en fil med versionsnummer.
- LLM-as-judge-prompts som ersättning för seniorernas omdöme. Se
  `CLAUDE.md` — det är en låst regel.
