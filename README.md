# ⚡ Elpriskoll – Tibber elprisindikator

En enkel, responsiv webbapp som visar om det aktuella elpriset är **bra**,
**mindre bra** eller **dyrt**, baserat på dina egna gränsvärden – och som
hjälper dig se när det blir billigare, t.ex. inför laddning av elbilen.

Bygger direkt på kravspecifikationen och är skriven som en enda
fristående `index.html`-fil (HTML/CSS/vanilla JavaScript). Ingen build-process,
inga npm-paket och inget ramverk krävs – filen fungerar precis som den är,
vilket gör den enkel att versionshantera och publicera via **GitHub Pages**.

## Innehåll

```
index.html   ← hela applikationen (öppna direkt i valfri webbläsare)
README.md    ← den här filen
```

## Kom igång lokalt

Du behöver ingen server – dubbelklicka på `index.html`, eller starta valfri
enkel lokal server om du vill testa i en miljö närmare produktion, t.ex.:

```bash
python3 -m http.server 8000
# öppna http://localhost:8000
```

Du behöver en **Tibber API-token**, som du hämtar gratis på
[developer.tibber.com](https://developer.tibber.com/) om du är Tibber-kund.
Klistra in den i appens startvy – tokenen skickas endast direkt till Tibbers
API (`https://api.tibber.com/v1-beta/gql`) och sparas lokalt i din
webbläsare (`localStorage`). Den skrivs aldrig till källkoden och checkas
aldrig in i något repo.

## Publicera på GitHub Pages

1. Skapa ett nytt repo på GitHub och lägg `index.html` (och gärna denna
   README) i repots rot – eller i en `docs/`-mapp, se steg 3.
2. Committa och pusha:
   ```bash
   git init
   git add index.html README.md
   git commit -m "Elpriskoll – initial version"
   git branch -M main
   git remote add origin https://github.com/<ditt-användarnamn>/<repo-namn>.git
   git push -u origin main
   ```
3. Gå till repots **Settings → Pages**. Under "Build and deployment", välj
   **Deploy from a branch**, branch `main` och mapp `/ (root)` (eller
   `/docs` om du la filen där). Spara.
4. Efter någon minut är appen live på
   `https://<ditt-användarnamn>.github.io/<repo-namn>/`.
   GitHub Pages serveras alltid över **HTTPS**, vilket uppfyller kravet på
   säker kommunikation.

Ingen ytterligare konfiguration, inga hemligheter/secrets och inget
backend-steg behövs – hela appen körs i användarens webbläsare.

## Vad appen täcker från kravspecifikationen

- **Plattform:** responsiv webbapp (dator/surfplatta/mobil), byggd för att
  versionshanteras i GitHub och publiceras via GitHub Pages, inga
  hårdkodade hemligheter i koden.
- **Tibber-inloggning:** användaren anger sin egen token i gränssnittet,
  tokenen valideras mot Tibbers API innan den sparas, tydligt
  felmeddelande vid ogiltig token, möjlighet att koppla från (raderar
  lokalt sparad data), tokenen visas aldrig i klartext efter att den
  sparats (endast maskerad, t.ex. `abcd •••• wxyz`).
- **Prisdata:** hämtar aktuellt pris samt resten av dagens och (när
  tillgängliga) morgondagens priser från Tibbers `priceInfo`/`total`,
  visning i öre/kWh eller kr/kWh, tidsstämpel för senaste hämtning,
  konfigurerbart auto-uppdateringsintervall (standard 5 min) samt manuell
  uppdatering.
- **Prisnivåer:** standardgränser 0–100/101–200/>200 öre/kWh, helt
  konfigurerbara gränsvärden och meddelandetexter, sparas per
  enhet/webbläsare, återställning till standardvärden, validering som
  hindrar att den gröna gränsen sätts högre än den orange.
- **Indikator:** stor, tydlig statusvisning på startsidan med färg, ikon
  *och* text (så att färg aldrig är enda informationsbäraren) samt en
  visuell nivåmätare.
- **Nästa billiga period:** räknar fram nästa gröna period bland
  tillgängliga priser, visar starttid och pris, samt tydligt meddelande
  när ingen grön period hittas eller när morgondagens priser saknas.
- **Prisöversikt:** stapeldiagram med gröna/orange/röda trösklar
  inritade, lista per period med aktuell/billigaste/dyraste period
  markerad, samt flik för att växla mellan idag och imorgon.
- **Inställningsvy:** samlad vy för gränsvärden, texter, prisformat,
  uppdateringsintervall och Tibber-anslutning.
- **Felhantering:** hanterar att Tibbers API är otillgängligt, visar
  senast kända pris med "kan vara inaktuellt"-varning, tydlig
  återförsöksknapp utan att behöva ladda om sidan, tekniska fel översätts
  till begripliga meddelanden.
- **Säkerhet:** ingen token i konsolloggar eller URL-parametrar, all
  kommunikation sker över HTTPS, tokenen skickas aldrig till någon annan
  tjänst än Tibber.
- **Design:** mobilanpassat gränssnitt med fokus på de tre kärnfrågorna
  (Vad kostar elen nu? Är priset bra eller dåligt? När blir det
  billigare?), stöd för både ljust och mörkt tema (samt systemval).

## Läge utan Tibber-inloggning (tillägg utöver kravspecen)

Utöver Tibber-inloggningen finns numera även ett läge helt **utan konto eller
token**: användaren väljer sitt elområde (SE1–SE4), antingen manuellt eller
via "Använd min plats" (webbläsarens platstjänst, som bara används lokalt för
att gissa elområde – inga koordinater skickas någonstans). Priserna hämtas då
från det öppna, kostnadsfria API:et [elprisetjustnu.se](https://www.elprisetjustnu.se/elpris-api),
som levererar Nord Pool-spotpriset per elområde. Appen räknar upp med 25%
för att uppskatta moms, så att talen ska bli jämförbara med Tibbers `total`.

Tänk på att detta är en **approximation**:
- Priset gäller hela elområdet, inte din exakta adress.
- Det motsvarar inte nödvändigtvis vad du faktiskt betalar, t.ex. om du har
  fastprisavtal eller ett annat påslag än ren spotprisvidarefakturering.
- "Använd min plats" använder en enkel breddgradsindelning som grov
  uppskattning – elområdenas riktiga gränser följer länsgränser, inte en rak
  linje, så nära gränserna kan gissningen bli fel. Användaren kan alltid välja
  elområde manuellt istället.

Detta läge kräver ingen inloggning alls och är därför i praktiken ännu
enklare ur säkerhetssynpunkt än Tibber-läget (inga hemligheter att hantera).
Om appen visas publikt anger den källan "Elpriser tillhandahålls av Elpriset
just nu.se", i linje med deras användarvillkor.

**Priser inkl. skatter och moms:** elprisetjustnu.se levererar rent spotpris
(exkl. skatt och moms). Appen räknar därför upp priset med svensk energiskatt
på el (36,0 öre/kWh, 2026 års standardnivå) och därefter 25% moms, för att
likna en vanlig elräkning och vara jämförbart med Tibbers `total`-fält.
Observera att hushåll i vissa kommuner i norra Sverige har en lägre
energiskatt (ca 9,6 öre/kWh lägre) – det tas inte hänsyn till, så priset kan
bli något för högt där. Nätavgift och elhandlarens eventuella påslag ingår
aldrig, eftersom det inte finns någon öppen, nationell källa för det.

Utöver elområdesknappar och platsknapp går det även att ange sitt
**postnummer**, som slås upp mot en enkel, inbyggd tabell (byggd på PostNords
postnummerintervall) för att gissa elområde. Precis som platsgissningen är
detta en approximation, särskilt nära en elområdesgräns — inget postnummer
sparas eller skickas till någon server.

## "Just nu"-rutan och tidsupplösning

Grundorsaken till att Tibber-läget visade timpriser hittades till slut:
Tibbers GraphQL-API svarar bakåtkompatibelt med **timpriser** om frågan inte
uttryckligen begär `resolution: QUARTER_HOURLY` på `priceInfo` – oavsett om
hemmet egentligen stöder kvartspriser. Frågan i appen begär nu det argumentet
explicit, så `today`/`tomorrow`/`current` bör komma i kvartsupplösning för
konton som stöder det.

Utöver det räknas aktuell period fram lokalt från `today`-prislistan i BÅDA
lägena (samma metod som elområdesläget alltid använt), istället för att i
Tibber-läget lita på Tibbers eget `current`-fält, som visade sig kunna ligga
kvar på hel timme oavsett upplösning på `today`-listan.

## Automatisk uppdatering i takt med prisperioderna

Utöver det uppdateringsintervall som går att ställa in (1–60 min) uppdateras
priset alltid automatiskt **en minut efter varje ny prisperiod** (xx:01,
xx:16, xx:31, xx:46), oavsett vilket intervall som valts. Det garanterar att
"just nu"-rutan hänger med i takt med att nya kvartspriser börjar gälla, med
en liten säkerhetsmarginal så att källan garanterat hunnit uppdatera sig.

## Källhänvisning i elområdesläget

När appen körs utan Tibber-inloggning visas nu en tydlig ruta direkt under
"just nu"-kortet på startsidan (inte bara i inställningarna) som anger att
priset kommer från elprisetjustnu.se för valt elområde, samt en påminnelse om
att det faktiska priset kan skilja sig beroende på elbolag, avtalstyp (t.ex.
fastpris), nätavgift och eventuellt påslag.

## Cache, nollställning och autosparade inställningar

- Sidan skickar med `Cache-Control: no-cache`-metataggar, så en vanlig
  omladdning alltid hämtar senaste versionen av `index.html` – ingen manuell
  cacherensning ska behövas efter en uppdatering.
- Inställningar (gränsvärden, texter, enhet, uppdateringsintervall, tema)
  sparas nu direkt när du ändrar dem, utan en separat "Spara"-knapp.
- Under inställningarnas "Farlig zon" finns en knapp **Nollställ allt** som
  raderar all sparad data (token/elområde, inställningar, cachade priser) och
  startar om appen helt – bra att använda om något känns fastlåst.
- Startsidan visar en välkomstruta första gången, som länkar till
  inställningarna, tills den stängs eller inställningarna ändras.
- "Koppla från" syns nu även direkt under rubriken på startsidan, inte bara
  inne i inställningarna.

## GitHub Pages och Jekyll

Repot innehåller en tom fil `.nojekyll` i roten. Utan den kör GitHub Pages
filerna genom Jekyll (statisksidegenerator) som standard, vilket normalt
inte stör en enkel `index.html`, men kan i sällsynta fall ge oväntat
beteende för större, komplexa enfilsappar. `.nojekyll` stänger av det helt,
vilket är standardrekommendationen för statiska HTML/JS-appar som denna.
Se till att filen följer med när du pushar till GitHub (den är osynlig i
vissa filutforskare eftersom namnet börjar med en punkt).

## Versionsnumrering

`index.html` inleds med en HTML-kommentar med versionsnummer, datum och en
ändringslogg. Versionen visas också längst ner på anslutningsskärmen och i
inställningarna, så det går att bekräfta i webbläsaren vilken build som
faktiskt körs (bra att kontrollera vid cacheproblem efter en uppdatering —
gör i så fall en hård omladdning, Ctrl/Cmd+Shift+R).

## Grafen i prisöversikten

Stapeldiagrammet visar nu klockslag under varje hel timme, oavsett om
prisdatan kommer i timupplösning (24 perioder/dygn) eller kvartsupplösning
(96 perioder/dygn, vilket Tibber stödjer sedan de började leverera
15-minuterspriser). En liten etikett ovanför grafen visar vilken upplösning
den aktuella dagens data faktiskt har, och priset (i vald enhet) står nu även
utskrivet ovanför varje stapel.

För fliken "Idag" visas prisöversikten (både graf och lista) numera från och
med innevarande period och framåt, inte från midnatt — redan passerade
timmar döljs. Fliken "Imorgon" visar som vanligt hela dygnet, eftersom det
alltid ligger i framtiden.

## Begränsningar / naturliga nästa steg

- Tokenen lagras i klartext i webbläsarens `localStorage`, i linje med
  kravspecens krav (KRAV-015) för en första version. En framtida version
  kan flytta lagring/anrop till en serverless-funktion för extra säkerhet.
- Om Tibber-kontot har flera hem används det första hemmet automatiskt;
  appen visar en enkel väljare i inställningarna om fler än ett hem
  hittas.
- Appen förutsätter att Tibbers GraphQL-API tillåter anrop direkt från
  webbläsaren (CORS). Om detta någon gång skulle ändras krävs en enkel
  proxy/serverless-funktion mellan appen och Tibbers API.

## Licens

Fri att använda och anpassa efter eget behov.
