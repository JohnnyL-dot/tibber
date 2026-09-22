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
