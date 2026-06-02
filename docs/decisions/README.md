# Architecture Decision Records (ADR)

Den här mappen samlar **beslut** vi tagit i projektet — och, lika viktigt,
**alternativ vi medvetet valde bort**. Det är ett forskningsprojekt; varför
något *inte* gjordes är ofta lika värdefullt som vad som gjordes.

## Format

En fil per beslut: `NNNN-kort-titel.md` med löpande nummer (`0001-…`,
`0002-…`). Använd följande mall:

```markdown
# ADR NNNN — Kort titel

- Status: Föreslagen | Antagen | Ersatt av ADR XXXX | Övergiven
- Datum: YYYY-MM-DD
- Berör fas: 0 | 1 | 2 | 3 | 4 | tvärgående

## Kontext

Vad är problemet? Vad gjorde att beslutet behövdes nu?

## Beslut

Vad valde vi, konkret?

## Alternativ vi övervägde

- **Alt A:** beskrivning. Varför valdes den bort?
- **Alt B:** beskrivning. Varför valdes den bort?

## Konsekvenser

Vad blir lättare? Vad blir svårare? Vad får vi inte längre göra utan att
ompröva detta beslut?
```

## Vad som hör hemma som ADR

- Val av urvalslogik (Pareto vs. annat) — när det görs.
- Val av leverantörer i ett experiment och varför just den mixen.
- Val mellan att bygga själv vs. återanvända ett bibliotek när det är en
  gränsfråga.
- Beslut att överge eller revidera ett tidigare val (skapas som *ny* ADR
  med status "Ersätter ADR XXXX", aldrig genom att skriva om historiken).

## Vad som *inte* hör hemma här

- Vanliga implementations-detaljer (de bor i koden eller `docs/phases/`).
- Beslut som redan står klart i `CLAUDE.md`. Hänvisa dit i stället för att
  dubbla.
