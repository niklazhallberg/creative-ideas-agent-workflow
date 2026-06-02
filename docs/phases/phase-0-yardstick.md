# Fas 0 — Måttstocken

> Status: planeringsfas. Inget byggt än. Den här filen växer när vi gjort de
> faktiska besluten i planeringssamtalet.

## Syfte

Etablera *RADON:s egen kvalitetsdefinition* via blindbedömning. Allt senare
arbete (Fas 1–4) mäts mot vad seniorerna i denna fas kallar bra. Vi hittar
inte på egna proxy-mått som ersätter deras omdöme.

## Vad måttstocken måste klara

- Fånga **toppen** (topp ~10%), inte snittet. Mode collapse syns i svansen.
- Vara **blind** — bedömaren får inte veta om en idé är vinnare, bortvald
  eller AI-baseline.
- Ge **inter-rater agreement** vi kan rapportera (om seniorerna inte är
  överens om vad som är bra existerar ingen måttstock att mäta mot).

## Vad som bör fastställas i planeringssamtalet innan denna fas byggs

- Vilka senior-bedömare som deltar och hur många.
- Skalor och axlar — vad bedöms (originalitet, brand-fit, körbarhet, …) och
  hur många nivåer. Endast axlar som ska användas i Fas 3-urvalet införs här.
- Storlek på blindbatch (antal idéer per session, totalt antal sessioner).
- Mix mellan källtyper (winner / discarded / ai_baseline) — och om mixen
  ska vara dold eller balanserad.
- Hur bedömningarna logiskt sparas så att källan kan kopplas tillbaka
  *efter* bedömningen (se `data/schema/README.md` om blind/source-split).

## Vad fasen producerar

- Ett uppmätt inter-rater-mått.
- Ett underlag (rådata + bedömningar) som senare faser jämför sig mot.
- Eventuellt: en eller flera billiga automatiska proxy-mått som **korrelerar**
  med seniorernas omdöme. Aldrig som ersättning, bara som triage.
