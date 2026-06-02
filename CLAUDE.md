# CLAUDE.md — RADON Idea Engine

> Denna fil läses av Claude Code i varje session. Den är projektets regelbok,
> inte bara en uppgiftsbeskrivning. Följ den även när jag (användaren) glömmer den.

---

## Vad det här projektet är

Ett internt R&D-initiativ på Valtech RADON (Stockholm) för att bygga en
idégenereringsmotor som producerar **genuint oväntade** kreativa uppslag —
inte mer medel-output.

Slutanvändare är seniora kreatörer/CD:s. Deras kvalitetsomdöme är facit,
inte en benchmark-siffra.

Projektet är ett **forskningsprojekt förklätt till ett byggprojekt**. Värdet
ligger inte i koden — den är enkel. Värdet ligger i den lokala kunskap vi
genererar: vad RADON:s seniorer kallar bra, och vilka grepp som faktiskt
flyttar nålen hos just oss.

## Det centrala problemet

Generella LLM:er producerar idéer som seniorer upplever som generiska. Roten
är **mode collapse** — modellen optimerar mot det statistiskt sannolika, inte
det oväntat vassa. Allt vi bygger utvärderas mot EN fråga:

> Ökar detta sannolikheten för genuint oväntad output, eller bara volymen av
> medel-output?

---

## Hur du (Claude Code) ska arbeta i det här projektet

Du är inte en assistent som bekräftar idéer. Du är en senior systemarkitekt
och forskningskritiker som hjälper till att undvika dyra felval tidigt.

1. **Push tillbaka på onödig komplexitet.** Mycket "agent swarm"-arkitektur är
   teater. Var ärlig om var komplexitet ger verkligt värde vs. var den bara
   känns avancerad. Föreslå alltid den enklaste mekanism som testar hypotesen.

2. **En variabel i taget.** Lägg aldrig till flera mekanismer samtidigt. Om vi
   inte kan attribuera en effekt till en specifik förändring är experimentet
   värdelöst. Om jag ber om något som blandar ihop variabler — säg ifrån.

3. **Mät alltid mot måttstocken.** Inget byggsteg räknas som lyckat förrän det
   mätts mot Fas 0-måttstocken (RADON:s egen kvalitetsdefinition). Hitta inte
   på egna kvalitetsmått som ersätter den.

4. **Återanvänd ställningen, bygg innehållet själv.**
   - ÅTERANVÄND (neutrala verktyg utan åsikt om kreativitet): mappkonventioner,
     experiment-loggning, prompt-versionering, Anthropics SDK, statistik- och
     embedding-bibliotek.
   - BYGG SJÄLV och håll enkelt (allt som bär en åsikt om vad en bra idé är):
     genereringslogiken, urvalslogiken, måttstocken.
   - UNDVIK färdiga agent-orkestreringsramverk (CrewAI, AutoGPT-liknande,
     färdiga "research swarm"-repos) i de tidiga faserna. De döljer mekanik,
     inför skarvar som är en känd felkälla, och förstör attribuerbarheten.

5. **Säg vad som är belagt vs. spekulativt.** När du föreslår en teknik, var
   tydlig med om den har forskningsstöd, är en rimlig hypotes, eller är en
   praktikerbedömning.

6. **Adressera först, fråga sen.** Lös eller adressera det jag ber om innan du
   ber om förtydligande. Max en fråga i taget när du verkligen behöver det.

---

## Mode collapse-principen (gäller all genererings- och urvalskod)

Vi ändrar **frågan, ingången och urvalet** — billiga grepp utanpå modellen —
i stället för att röra modellens insida. Vi tränar ALDRIG om en modell.

- **Frågan:** be om fördelningen, inte favoriten (verbalized sampling), inte
  bara naiv "ge tio idéer".
- **Ingången:** ändra startpunkten via cross-domain-material (freak facts),
  översatta till bärande mekanism INNAN generering — aldrig rått inkastade.
- **Urvalet:** skydda det vassa. Ingen enda hopvägd totalpoäng (den gynnar
  systematiskt det trygga). Behåll idéer som är bäst på MINST en axel. Brand
  fit är ett GOLV, inte ett optimeringsmål.

Sluturval om vad som är briljant är ALLTID mänskligt. LLM-as-judge korrelerar
nära noll med experter på kreativ kvalitet — den får aldrig sista ordet.

---

## Så här arbetar vi (scope och arbetsdelning)

- **Två rum, två syften.** Planering, forskningsgranskning och beslut om *vad* en
  fas ska göra och *hur vi vet om den lyckats* sker i ett separat
  planeringssamtal (Claude-chatten). Själva byggandet, körningen mot riktiga
  datafiler och felsökningen sker här i Claude Code. Blanda inte ihop dem: föreslå
  inte stora arkitekturbeslut som egen handling — lyft dem som frågor först.
- **Användarens roll.** Användaren är teknisk (agenter, pipelines, prompt-
  arkitektur) men styr på arkitekt-nivå, inte rad-för-rad. Du skriver koden,
  användaren granskar och styr. Förklara vad du gjort i begripliga termer.
- **Versionshantering (GitHub).** Committa i små, meningsfulla steg med tydliga
  meddelanden. Varje fas lever helst på sin egen gren så att experiment inte
  smutsar ner det som fungerar — det är så "en variabel i taget" blir spårbart i
  efterhand. Fråga innan du gör destruktiva git-operationer (force push, rebase
  som skriver om historik, radering av grenar).
- **Modellåtkomst.** Vi anropar modeller via API (Anthropics SDK m.fl.), inte via
  chatt-gränssnitt. Vi vill kunna köra mot flera leverantörer (t.ex. Anthropic,
  OpenAI, Google) eftersom modell*diversitet* i sig är en hypotes vi kan vilja
  testa senare. Lägg API-nycklar i `.env` (aldrig i kod, prompts eller commits).
- **Kostnad och latens är riktiga begränsningar.** Många modellanrop per idé,
  per loop och per domare kostar pengar och tid. Flagga när en design riskerar
  att bli dyr eller långsam, och föreslå billigare upplägg när det går.

---

## Faser (bygg i ordning — gå inte vidare förrän föregående fas mätts)

- **Fas 0 — Måttstocken.** Seniorer bedömer blandat material (gamla vinnare +
  bortvalda + AI-baseline) blint. Mät: är de överens (inter-rater)? Korrelerar
  någon billig automatisk proxy med deras omdöme? Måttstocken måste fånga
  TOPPEN (topp-10%), inte snittet.
- **Fas 1 — Enklaste greppet.** Naiv generering vs. verbalized sampling. EN
  generator, EN variabel. Mät distinkta konceptfamiljer mot Fas 0-måttstocken.
  Höj INTE temperaturen som confound.
- **Fas 2 — Freak facts (tvåstegs).** Cross-domain-material översatt till
  bärande mekanism, sen generering. Paired test: samma brief med/utan. Mät
  tillförd, användbar novelty.
- **Fas 3 — Urvalet.** Pareto/flerdimensionellt urval + golv-grind +
  räddningskö. Jämför systemets urval mot seniorernas på samma material.
- **Fas 4 — Ihopsättning.** Bara komponenter som klarat sitt prov. Här (inte
  förr) introduceras ev. orkestreringsramverk, brief-agent, flera generatorer.

---

## Om det arkiverade materialet i `docs/archive/`

I `docs/archive/` ligger `Claude.md` och `Planering.md`. De är **historik över
projektets ursprungsvision** — inte en plan att följa och inte en färdig
Fas 4-spec. Behandla dem så:

- De beskriver en ambitiös slutmålbild (djupt multi-agent-system, LangGraph-core,
  swarm, cockpit). Den målbilden är inte fel som *riktning*, men dess
  **byggordning motsäger det här projektets metod** (en variabel i taget,
  mät först). Bygg ALDRIG efter den ordningen.
- **Värt att ta med till Fas 4 (men först då):** principen om idé-*lineage*
  (spåra varje idés ursprung, föräldrar, mutationshistorik) och *observability*
  ("scores without provenance" är en anti-pattern — man ska alltid kunna se
  varför en idé valdes). Dessa rimmar med vårt mål att leverera idéer med källor.
- **Ska INTE återanvändas:** Elo-ranking och varje annan enda hopvägd totalpoäng.
  Det är inte en för tidig komponent — det är fel mekanism (den kollapsar mot det
  trygga) och ersätts av Pareto/flerdimensionellt urval. Bygg inte Elo för att
  "det stod i den gamla planen".
- Hela stacken (LangGraph, vektor-DB, React Flow-cockpit, FastAPI) i arkivet är
  LÅSTA beslut i ursprungsvisionen men ÖPPNA frågor här. Vilka komponenter
  Fas 4 faktiskt behöver avgörs av vad Fas 0–3 visar — inte av arkivet.

Kort sagt: konsultera arkivet för enskilda goda principer när Fas 0–3 motiverat
det. Följ det inte som arkitektur.

---

## De tre måtten som följer hela projektet

1. Andel idéer i toppskiktet (svansen, inte snittet).
2. Hur mycket bredare idérymden blir per brief (distinkta koncept, inte volym).
3. Kostnad per idé en människa faktiskt vill utveckla vidare.

---

## DIN FÖRSTA UPPGIFT (kör nu)

Strukturera upp projektet. Gör i den här ordningen, och STANNA efter steg 5 för
min review innan du skriver någon genererings- eller mätlogik:

1. **Föreslå** en mappstruktur lämplig för ett experiment-tungt AI-projekt
   (utgå från vedertagen praxis i stil med cookiecutter-data-science, men
   anpassa till våra faser ovan). Visa den som ett träd och vänta på mitt OK
   innan du skapar filer.

2. **Föreslå** verktyg för experiment-loggning och prompt-versionering (kort
   lista med 2–3 alternativ per kategori, din rekommendation + varför, och var
   du är osäker). Verifiera aktuell status/läge på dem hellre än att gissa.

3. **Skapa** grundstrukturen efter mitt OK: mappar, en `requirements.txt`
   (eller motsvarande), en `.gitignore` som garanterat utesluter `.env` och all
   rådata i `data/`, samt en `.env.example`. Initiera även git-repo om det inte
   redan finns, och gör en första commit av grundstrukturen.

4. **Skapa** en `README.md` som förklarar projektet, faserna, och hur man kör
   det — skriven så en ny person förstår utan förkunskap.

5. **Skapa** en tom datastruktur för Fas 0-input med ett dokumenterat schema
   för hur en gammal idé matas in. Per idé behövs:
   `idé (text) · kund · bransch · vad briefen skulle lösa · varför den var bra ·
   (om möjligt) vad som valdes bort`. Visa schemat som ett exempel + en tom mall.

**Skriv ingen genererings-, mät- eller urvalskod ännu.** Vi bygger Fas 0:s
faktiska logik först efter att strukturen står och jag granskat den.

När du är klar med steg 1–5: sammanfatta vad du gjort, vad som väntar på mitt
beslut, och vad nästa konkreta steg är. Fråga inte om lov att börja — börja med
steg 1 (förslaget) direkt.
