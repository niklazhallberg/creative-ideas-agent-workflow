# ADR 0002 — Måttstockens auktoritet: en CD + sanity-check

- **Status:** Antagen
- **Datum:** 2026-06-02
- **Beslutsfattare:** [Creative Technologist, RADON]
- **Ersätter:** delvis punkt 1a i ADR 0001 (dödlägesplan vid låg inter-rater).
- **Kontext:** Innan Fas 0-designen. Frågan om vem som definierar "bra" behövde
  avgöras eftersom hela måttstocken vilar på det.

---

## Bakgrund

Den ursprungliga planen byggde på flerbedömar-validering: 3–4 seniorer bedömer
materialet blint, och samstämmigheten mellan dem (inter-rater reliability) mäts
för att avgöra om det finns en stabil måttstock. ADR 0001 lade till och med en
dödlägesplan för fallet att samstämmigheten blev låg.

Två saker ändrade förutsättningen:

1. **Resursverklighet.** Projektet drivs av en Creative Technologist tillsammans
   med en Creative Director, som ett utforskande prototyp-initiativ vid sidan av
   ordinarie arbete. Fyra–fem seniorer som ägnar tid åt att betygsätta idéer är
   inte realistiskt — de har inte tiden, och att kräva det skulle stoppa projektet
   innan det börjat.
2. **Auktoritet finns redan.** CD:n har det kreativa ledarskapet och äger i
   praktiken frågan "vad är en bra idé" på byrån. Den smaken *är* en legitim
   måttstock — den behöver inte valideras mot en grupp för att vara giltig i det
   här sammanhanget.

---

## Beslut

Måttstocken byggs på **en creative director som auktoritet**, inte på
flerbedömar-konsensus.

- **Huvudbedömare:** CD:n. Hens omdöme *är* måttstocken, definitionsmässigt.
- **Sanity-check:** en andra läsare gör lätta stickprovskontroller (en delmängd
  av materialet, oregelbundet) — inte som parallell domare och inte för statistik,
  utan för att fånga fall där CD:ns omdöme är idiosynkratiskt snarare än
  självklart. Oenighet mellan de två är en *signal* (här är "bra" subjektivt),
  inte ett problem att medla.
- **Stabilitetskontroll:** eftersom måttstocken nu vilar på i princip en person,
  mäts dess stabilitet som **CD:ns konsekvens med sig själv över tid** — några få
  idéer läggs in dubbelt, på olika plats och vid olika tillfälle. Betygsätter CD:n
  dem likadant är måttstocken stabil. Vinglar den vet vi det innan vi bygger
  ovanpå den. Detta ersätter inter-rater som stabilitetsmått.

---

## Risken vi medvetet tar

Med flera bedömare kunde oenighet *upptäckas*. Med en kan den inte det — vi har
per konstruktion inget utomstående perspektiv. Om CD:ns smak har en blind fläck
ärver hela systemet den. Det är ett accepterat byte: vi byter risken "projektet
står still i oenighet" mot risken "måttstocken = en persons smak, inklusive dess
blinda fläckar". Sanity-checken är den billiga försäkringen mot den andra risken,
inte en eliminering av den.

Detta påverkar hur resultat ska tolkas: måttstocken är giltig *för RADON, via
denna CD* — inte ett objektivt mått på kreativ kvalitet i största allmänhet. Det
är tillräckligt för projektets syfte (bygga ett verktyg som tjänar RADON), men det
bör inte presenteras som mer generellt än så.

---

## Konsekvenser

- **ADR 0001 punkt 1a** (dödlägesplan vid låg inter-rater) är till stor del
  obsolet — men raderas inte, för att bevara spåret av att vi en gång tänkte
  annorlunda. Den här ADR:n är den aktiva.
- **Filer som måste propageras** (samma våg):
  - `CLAUDE.md` — Fas 0-beskrivningen ("Seniorer bedömer... inter-rater") byts till
    CD + sanity-check + konsekvens-över-tid.
  - `docs/phases/phase-0-yardstick.md` — skriven utifrån gamla modellen, görs om.
  - `README.md` — redan uppdaterad (commit 84f23b4).
- **Rating-schemat** (ännu ej byggt): `rater_id` har nu i praktiken två värden;
  inget maskineri för flerbedömar-samstämmighet behövs. Lägg dessutom till en
  igenkännings-flagga (se nedan).

## Relaterat olöst — tas i Fas 0-designen

Eftersom CD:n kan känna igen idéer hon själv var med och valde, är blindheten
delvis hotad inifrån hennes minne. Det löses i Fas 0-designen, troligen genom
korskörning (CD bedömer material kopplat till den andra personens projekt och
vice versa) plus en "känner du igen den?"-flagga i rating-schemat som
säkerhetsventil. Detta är inte beslutat här — bara flaggat som nästa fråga.
