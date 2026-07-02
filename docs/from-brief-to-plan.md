# Från ursprungsbrief till nuvarande plan — och varför

Den här texten finns för att en kollega eller chef ska förstå varför repot är
upplagt som det är. Projektet började med en annan plan än den vi bygger nu.
Ändringen är inte en kursändring bort från visionen — den är en kursändring
mot att faktiskt nå den, byggd på forskning snarare än intuition.

## Ursprungsplanen (briefen)

Den första briefen satte målet: ett system som skapar världens mest
attraktiva idéer. Konkret skissades:

- Ett automatiserat AI-system med människan i loopen som ständigt genererar
  idéer, proaktivt och på brief.
- En arkitektur som bygger på byråns befintliga arbetssätt — brief → insikt
  (freak facts) → idé — fast på mycket större skala.
- Ett flöde: scout-svärm hämtar freak facts → creative brief → "mutation lab"
  som producerar 500 idéer → "death match" där 500 blir 10 via en
  konstitutionell AI-domare som betygsätter på Surprise, Brand Fit och Radon
  Style → 10 idéer levereras till människa i Slack → paketering → en agent
  skickar till klient → klientens egen brand-agent scorar idén.

## Vad ursprungsplanen fick rätt

Det här är viktigt: briefen var en skarp första skiss, inte ett misstag.
Den ställde rätt frågor.

- Den identifierade den centrala spänningen exakt. "Utmaning"-rutan frågar:
  hur infuserar vi vår kollektiva kreativa röst i lösningarna, samtidigt som
  varje kreatör gör sin röst starkare — hur sätter vi upp ett lärande system?
  Det är precis rätt fråga, och den driver fortfarande hela projektet.
- Den var ärligt osäker. "Effekt"-rutan slutar med "bättre idéer?!" — med
  frågetecken. Den som skrev visste att detta var en hypotes att pröva, inte
  en garanti.
- Grundsekvensen brief → insikt → idé är sund och lever kvar i nuvarande
  plan.
- Freak facts som kreativ disruptor är en stark intuition — den har, visar
  det sig, det starkaste forskningsstödet av alla grepp för att skapa nya
  vinklar.
- Människan i loopen fanns med från början. Rätt instinkt.

## Var den brast — och varför

Tre saker, var och en bekräftad av forskning vi gått igenom (tre oberoende
djupforsknings-rapporter som konvergerade på samma punkter).

1. **"Death match" med en hopvägd domarpoäng kollapsar mot det trygga.**
   Att betygsätta 500 idéer på Surprise + Brand Fit + Radon Style och slå
   ihop till en ranking känns rigoröst — men en sammanvägd poäng gynnar
   systematiskt den jämna, säkra idén över den vågade ojämna. Maskinen blir
   en mediokritetsförstärkare som tyst rensar bort precis det en byrå på
   RADON:s nivå lever av: de sällsynta, briljanta idéerna. Forskningen är
   tydlig här, och det är därför nuvarande plan ersätter death match med
   flerdimensionellt (Pareto-)urval: en idé överlever om den är bäst på
   minst en axel.
2. **En stor agent-svärm direkt är fel startpunkt.** System med många
   samverkande agenter fallerar i en stor andel fall, och nästan alla fel
   sitter i skarvarna mellan agenterna, inte i agenterna själva. Att bygga
   scout-svärm + mutation lab + domare + paketerings-agent + klient-agent
   på en gång betyder att man inte kan veta vilken del som faktiskt skapade
   värde — och att man lägger merparten av tiden på att laga skarvar.
   Därför bygger nuvarande plan en variabel i taget och sätter ihop
   helheten först sist, av bara de delar som bevisat sig.
3. **Att låta en AI-domare avgöra vad som är briljant vilar på en svag
   grund.** AI:ns omdöme om kreativ kvalitet korrelerar nära noll med
   mänskliga experters. En "konstitutionell domare" som sista filter skulle
   alltså systematiskt välja fel. Därför har människan sista ordet i
   nuvarande plan, och AI-domaren får bara triage:a och föreslå — aldrig
   avgöra.

Den gemensamma roten: ursprungsplanen gissade arkitektursvaret innan
frågorna var besvarade. Den hoppade till "bygg hela maskinen" utan ett sätt
att mäta om maskinen faktiskt producerar bättre idéer. Utan en måttstock kan
man inte veta om något hjälpte — bara känna att det gjorde det.

## Hur vi processade vägen framåt

1. Tre oberoende forskningsrapporter togs in och jämfördes. Där de
   konvergerade (mode collapse är verkligt; diversitet skapas vid källan;
   hopvägd poäng kollapsar mot tryggt; AI-domare opålitlig på kreativ
   kvalitet; svärmar fallerar i skarvarna) behandlas slutsatsen som robust.
   Där de var oense markeras osäkerheten.
2. Planen vändes upp och ned: i stället för att bygga maskinen och hoppas,
   bygger vi en måttstock först och testar sedan en mekanism i taget mot
   den.
3. En stresstest (red team) kördes mot den nya planen för att hitta dess
   egna svagheter — dokumenterad i `docs/decisions/`.
4. Varje större vägval skrevs ned som en ADR så att kedjan av beslut, och
   vad vi valde bort, är spårbar.

## Varför detta är vägen framåt

Visionen är oförändrad: ett lärande system som producerar idéer vassare än
vad en standard-AI ger, med RADON:s röst inbakad. Det som ändrats är hur vi
tar oss dit utan att bygga in de fel forskningen varnar för.

Genom att lägga till en mekanism i taget och mäta vad den bidrar med, bygger
projektet två saker samtidigt: ett verktyg, och bevisad kunskap om vad som
faktiskt skapar oväntade idéer hos just RADON. Den kunskapen — inte koden —
är det enda en konkurrent inte kan kopiera, eftersom den är byggd på RADON:s
egen smak och ingen annans.

Ursprungsplanens målbild står alltså kvar som just det: en målbild
(`docs/archive/` bevarar den i sin helhet). Den nuvarande planen är vägen
dit som är hållbar, mätbar och ärlig — inte den som ser mest imponerande ut
på en slide.
