# ADR 0001 — Stresstest av projektplanen (red team-pass)

- **Status:** Antagen
- **Datum:** 2026-06-02
- **Beslutsfattare:** [Creative Technologist, RADON]
- **Kontext:** Innan Fas 0-bygget. Planen, fasupplägget och Fas 0-schemat fanns
  på plats. Ett red team-pass kördes för att hitta luckor tidigt — med den
  uttryckliga motvikten att planen kan vara rätt och att överbyggnad är en
  egen risk.

---

## Bakgrund

Projektet (RADON Idea Engine) vilar på en medveten metod: bygg en måttstock
först, testa en mekanism i taget, sätt inte ihop den fulla maskinen förrän
delarna bevisat sig. Frågan som ställdes: var spricker det här, och var är
risken i stället att vi gör det för avancerat och aldrig kommer till
prototyping?

Slutsatsen från passet: **planen står stadigt.** Den motstår de tre vanligaste
sätten ett sånt här projekt dör på (bygger inte svärmen först, mäter innan den
optimerar, isolerar variabler). Inget i passet motiverar att riva upp den.
Träffarna nedan är justeringar, inte en omstart — sorterade i tre hinkar.

---

## Beslut

### Hink 1 — Fixa nu (innan Fas 0 startar)

**1a. Dödlägesplan vid låg samstämmighet mellan bedömare.**
Hela måttstocken vilar på att seniorerna är tillräckligt överens
(inter-rater reliability). Låg samstämmighet är ett *sannolikt* utfall, inte
ett osannolikt — och utan en plan står projektet still efter att tid redan
lagts.
**Beslut:** Bestäm i Fas 0-designen, i förväg, vad lågt utfall innebär.
Alternativ att välja mellan: (a) mät per-bedömare i stället för konsensus,
(b) dela upp i subgrupper som är internt överens, (c) ompröva premissen.
Ett nedslående resultat ska vara *användbart*, inte ett stopp.

**1b. Frys baseline-prompten explicit.**
"AI-baseline" är inte en sak — en svag prompt får gapet att se konstgjort
stort ut, en stark prompt får det att försvinna. Premissen (att vi slår
mittfåran) kan se sann eller falsk ut beroende på ett godtyckligt promptval.
**Beslut:** Dokumentera baseline-prompten som en fryst, versionerad artefakt.
Helst två nivåer: en naiv ("ge tio idéer") och en kompetent. Baseline är ett
*val* som färgar allt och ska behandlas som en variabel, inte en självklarhet.

### Hink 2 — Bär med dig (känd risk, för tidig att lösa)

**2a. Proxy-korrelationen kommer troligen att misslyckas — och det är okej.**
Forskningen vi lutar oss på säger att automatiska mått korrelerar nära noll
med expertomdöme om kreativ kvalitet. Det mest sannolika Fas 0-utfallet är
att ingen billig proxy fungerar. Det är inte projektets misslyckande — det
*bekräftar* att människan måste vara kvar i loopen. Tolka inte ett negativt
proxy-resultat som ett stopp.

**2b. "Distinkta konceptfamiljer" är en mjukare metrik än den låter.**
Att räkna genuint olika koncept kräver ett avgörande om vad som är olika.
Embedding-likhet ≠ kreativ distinkthet — två idéer kan vara semantiskt nära
men kreativt olika, och tvärtom. Risk: Fas 1:s centrala metrik mäter kanske
inte det vi tror. Var skeptisk mot snygga diversitetssiffror; korskolla mot
mänsklig bedömning på ett stickprov.

### Hink 3 — Medvetet avstå (giltig invändning, enkel väg ändå rätt)

**3a. "Testa fler diversitetstekniker parallellt i Fas 1."**
Avstår. Att köra en teknik i taget är långsammare men är hela poängen — det
är skillnaden mellan att veta och att gissa. Giltigt i en värld med obegränsad
tid; fel i vår.

**3b. "Schemat/strukturen är överarbetad för en prototyp."**
Avstår. Den lilla strukturinvesteringen är just det som gör experimenten
attribuerbara senare. Den kostade timmar, inte veckor. Rätt mängd struktur,
inte för mycket.

### Den tyngst vägande träffen — tidsboxa Fas 0 hårt

Den verkliga faran är inte fel metod — det är att metoden blir så fin att vi
aldrig kommer till bygget. Jakten på en perfekt måttstock kan svälja månader.
**Beslut:** Fas 0 får en hård tidsbox (riktvärde två veckor) och ett medvetet
lågt krav: *"tillräckligt bra för att gå vidare"*, inte "vetenskapligt
vattentät". Är samstämmigheten hyfsad och en grov metrik finns — gå vidare
till Fas 1, även om allt inte är löst. Måttstocken kan skärpas senare med mer
data. Att vänta på perfektion är hur metodiskt sunda projekt dör utan att ha
producerat något.

---

## Konsekvenser

- Fas 0-designen (görs i planeringssamtalet, inte i Claude Code) måste innehålla:
  dödlägesplan (1a), fryst baseline-prompt med två nivåer (1b), en hård tidsbox
  och ett uttalat "good enough"-tröskelvärde.
- Hink 2 är inte att-göra-punkter utan vakthundar: ta upp dem igen när Fas 0
  respektive Fas 1 utvärderas.
- Hink 3 dokumenterar medvetet bortvalda vägar, så att de inte återuppstår som
  "borde vi inte ändå…" längre fram.

## Uppföljning

En andra, *extern* granskning (en fräsch agent utan projekthistorik) körs
efter att Fas 0-designen är klar — det är där de verkliga metodfällorna sitter
(samstämmighet, proxy-korrelation, urvalsbias) och de förtjänar friska ögon
utan investering i de val vi redan gjort.
