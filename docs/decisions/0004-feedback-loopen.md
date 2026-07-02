# ADR 0004 — Feedback-loopen (Slack + multi-CD + attribution)

- **Status:** Antagen
- **Datum:** 2026-07-02
- **Berör fas:** tvärgående (Fas 1+ attribution-krav, Fas 4-pilot Slack-mekanik)
- **Kompletterar:** ADR 0002 (CD som måttstockens auktoritet i *mätning*),
  ADR 0003 (nedskalad Fas 0).
- **Beslutsfattare:** Creative Technologist, RADON.

---

## Kontext

Briefens Utmaning-rad frågar bokstavligen *"Hur sätter vi upp ett lärande
system?"* Utan en definierad väg för feedback att komma tillbaka in i
systemet har vi byggt en enkelriktad generator med RADON-flavor — inte
ett lärande system. Det är inte vad briefen bad om.

Denna fråga kan inte skjutas till Fas 4. Logg-formatet från körning 1
avgör vad vi kan träna på retroaktivt: fält som saknas i första
Fas 1-körningen kan inte skapas i efterhand. Attribution måste vara
designad *innan* Fas 1 producerar sin första genererade idé.

---

## Beslut

Fem sammanlänkade val, som tillsammans utgör feedback-loopen.

### 1. Lärande-modell: manuellt, inte automatiskt

Signaler används för att **manuellt informera CT/CD** när prompts,
Skill.md eller urvalslogik ska uppdateras. Systemet uppdaterar sig aldrig
själv baserat på signaler. Detta bevarar:

- ADR-låsningen "vi tränar aldrig om en modell"
- Attribuerbarheten (en variabel i taget — auto-loopar ändrar tyst flera)
- Projektets värde som *bevisad kunskap hos oss*, inte som self-optimizing
  black box

### 2. Signal-källa: multi-CD i en Slack-kanal

Idéer levereras till en dedikerad kanal `#radon-ideation` där flera
seniora CD:er sitter. De reagerar när de har en åsikt.

**Skillnad mot Fas 0-linjalen:** ADR 0002 låser att linjalen bygger på
*en* kalibrerad CD (för att mäta). Fas 4-produktion är *användning*, inte
mätning — där är multi-CD det naturliga för byrån (kollektivt kreativt
beslut på seniornivå). Ingen konflikt: två separata funktioner.

**Multi-CD ger inbyggd signal-rikedom:** enighet = starkt kvalitetssignal,
oenighet = gränsfall/personlig-smak-signal. Bättre än en persons signal.

### 3. Signal-format: preferens (billigt) + adoption (dyrt), båda via Slack

**Emoji-vokabulär (fyra symboler, ingen fler):**

| Emoji | Betyder | Kostnad för CD |
|---|---|---|
| 🔥 | *"Topp — detta sticker ut"* | 1 sek |
| 👍 | *"Solid, kan användas"* | 1 sek |
| 👎 | *"Nej"* | 1 sek |
| 🚀 | *"Adoption — jag använde den här"* | Sätts när CD faktiskt tar idén vidare (deck, klient, produktion). Sällsynt, hög signal-värde. |

**Kommentarer:** valfria trådsvar på idé-messaget. Fri text. Vi
rekommenderar (inte tvingar) några ord vid 🔥/👎.

**Fallback vid tystnad:** 5 arbetsdagar utan reaktion → batchen märks
`no_signal`. Skiljs från `rejected` (👎). CT skickar *en* påminnelse i
kanalen dag 3. Inga naggnings.

**Bortvalt:** `🤔` och andra nyanser (ökar cognitive load, riskerar bli
default för "slipp ta ställning"). Ranking-baserade signaler (för dyra i
tid). LLM-as-judge på reaktionerna (låst i CLAUDE.md).

### 4. Attribution: full spårbarhet från Fas 1

Varje **genererad** idé bär rik metadata från körning 1. Detta är den
enda delen av beslutet som *måste* implementeras innan Fas 1 startar.

**Fält per genererad idé (JSONL i `experiments/<run_id>/results.jsonl`):**

```jsonc
{
  "idea_id": "idea_a1b2c3d4",       // neutralt id (samma konvention som Fas 0)
  "idea_text": "...",
  "phase": 1,                        // 0-4
  "run_id": "2026-07-15_fas1_naive-vs-verbalized",
  "prompt_name": "verbalized_generator",
  "prompt_version": 1,
  "generator_variant": "verbalized", // fri text som identifierar mekaniken
  "input_brief_id": "brief_x9y8z7",  // referens till brief-fil
  "freak_fact_ids": [],              // tomt i Fas 1, populerat i Fas 2+
  "provider": "anthropic",
  "model": "claude-opus-4-7",
  "tokens_in": 1234,
  "tokens_out": 567,
  "latency_ms": 4321,
  "timestamp_utc": "2026-07-15T10:23:44Z",
  // optional, tillkommer när idén postas i Fas 4:
  "slack_channel": "C0123456",
  "slack_message_ts": "1720123456.789012",
  "posted_at": "2026-07-15T11:00:00Z"
}
```

**Slack-attribution:** när `post_batch.py` postar en idé returnerar Slack
en `message_ts`. Den skrivs tillbaka in i `results.jsonl` (och även i
`experiments/<run_id>/slack_map.jsonl` för snabb lookup).

**Lineage-fält (reserverat, inte krav från Fas 1):** `parent_idea_ids`
för framtida mutation-baserad generering. Läggs till som optional-fält
i schemat men behöver inte populeras förrän Fas 4 eventuellt introducerar
mutation.

### 5. Tekniskt: Slack-polling, ingen server

**Ingen webhook. Ingen hosting. Inga skarvar.**

- **Slack-bot** i RADON:s workspace med scopes: `channels:history`,
  `reactions:read`, `chat:write`. Bot-token i `.env` som `SLACK_BOT_TOKEN`.
- **`src/radon/slack.py`** — modul med `post_batch()` och
  `pull_reactions()`. Använder `slack_sdk` (Python, officiellt).
- **CLI-skript:**
  - `scripts/post_batch.py <run_id>` — postar en färdig genererad batch.
  - `scripts/pull_reactions.py --since=YYYY-MM-DD` — pullar reaktioner
    + trådsvar. Skriver till `results/reactions/<pull_id>.jsonl`.
- **Reaktions-post-format:**

```jsonc
// per reaktion:
{
  "idea_id": "idea_a1b2c3d4",     // lookup via slack_message_ts
  "signal_type": "preference",     // eller "adoption"
  "emoji": "fire",                 // Slack-namn, inte tecken
  "user_id_hash": "a3b9…",         // hashat för lagring
  "timestamp": "2026-07-15T14:12:03Z"
}
// per trådsvar:
{
  "idea_id": "idea_a1b2c3d4",
  "signal_type": "comment",
  "user_id_hash": "a3b9…",
  "text": "Distributionen bär hela idén",
  "timestamp": "2026-07-15T14:13:45Z"
}
```

- **PII:** `results/reactions/` är gitignored (Slack user_ids är PII).
  User_ids hashas i lagringen. Riktiga id:s hålls i separat lokal
  översättningsfil om debugging kräver det.

### 6. Trigger-mekanik: CT-triggered MVP, slash-command senare

**Stadium 1 (Fas 4-pilot):** CD:er postar briefer i
`#radon-idea-triggers` (eller DM till CT). CT konfigurerar och kör
`scripts/post_batch.py`. Skriptet postar till `#radon-ideation`.
**Nollhosting.**

**Stadium 2 (om Fas 4-pilot bevisas):** `/radon-ideate` slash-command
med interaktiv Slack-modal. Kräver serverless-endpoint
(Vercel/Cloudflare Function). Byggs *när* signalen visar sig användbar
och CD:er ber om självbetjäning — inte förr.

---

## Alternativ vi övervägde

### Automatisk lärande-loop (avvisad)

Systemet ändrar prompts/parametrar själv baserat på scoring. Kräver
antingen LLM-as-judge (låst av CLAUDE.md) eller att CD:er sätter
strukturerade betyg på varje idé (orealistisk tidsbudget). Bryter också
attribuerbarheten. Avvisad.

### Agnes som enda signal-källa (avvisad)

Skulle göra loopen sårbar för en persons tillgänglighet och smak.
Multi-CD-kanalen ger både bredare signal och robusthet. Skickar också
"alla blir CD" (briefens Effekt-rad) i rätt riktning.

### Beställande kreatör som signal-källa (avvisad som primär)

Analytiskt vackert men ny person per brief → ingen stabil skala över
tid. Beställaren kan *också* reagera i kanalen (bonus), men är inte
primär.

### Rankning eller 1–5-betyg per idé (avvisat)

För dyrt tidsmässigt per idé. Risk att CD:er ger "3 på allt" för att
slippa ta ställning, eller hoppar över batchar. Emoji stackar ändå till
en implicit ranking.

### Webhook/realtids-events (avvisat för MVP)

Kräver server. För mycket infrastruktur för PoC. Polling räcker för
R&D-volym. Kan uppgraderas senare utan att bryta datamodellen.

### Slash-command från dag 1 (avvisat)

Premature optimization. Bygger UX för nåt som inte bevisat sig än.
CT-triggered MVP låter oss iterera snabbt på genereringen; slash-command
är rätt Stadium 2-investering.

---

## Konsekvenser

**Måste in innan Fas 1 startar:**

- `data/schema/idea_generated.schema.json` (nytt schema för genererade
  idéer — kompletterar existerande `idea.schema.json` som är för
  Fas 0-material). Innehåller attribution-fälten från beslut 4.
- `data/schema/reaction_event.schema.json` (schema för Slack-reaktions-
  och kommentar-poster).
- `experiments/README.md` uppdateras med de utökade obligatoriska fälten.
- `.gitignore` utökas med `results/reactions/*`.

**Aktiveras först i Fas 4:**

- `src/radon/slack.py`
- `scripts/post_batch.py`
- `scripts/pull_reactions.py`
- `.env.example` uppdateras med `SLACK_BOT_TOKEN`
- Faktisk Slack-bot registreras i RADON-workspacen
- Pinnad message i `#radon-ideation` med emoji-vokabulären

**Konsekvens för Skill.md-frågan (sekundär grill-punkt):** manuellt
lärande innebär att Skill.md uppdateras av CT/CD utifrån vad signalerna
visar. Frågan om *ägarskap och kadens* för Skill.md är fortfarande
öppen — hanteras i separat ADR när Fas 4-pilot bevisas.

**Konsekvens för kill-kriterium (sekundär grill-punkt):** feedback-
loopen ger ett användbart kill-signal: om `no_signal`-frekvensen är
hög eller 👎 dominerar över 🔥 + 👍 under en Fas 4-pilot, är det
sannolikt att systemet inte tillför värde och projektet bör omprövas.
Konkret tröskel bestäms i separat ADR när vi har data.

---

## Öppna frågor kvarstår

Från grillingens sekundära punkter — icke-blockerande, tas i separata
ADR:er när de blir akuta:

- Pipeline-aspekten (proaktiv generering utan brief) — hanteras när
  Fas 4-pilot bevisat att på-brief-loopen fungerar.
- Skill.md-ägarskap och underhållskadens.
- Kostnad per fas/idé över tid — budget-tak och ekonomisk kill-tröskel.
- Kvantitativ kill-tröskel på projektnivå.
