---
title: "Diagnostiikan kerääminen OpenTelemetryllä Googlen pilvifunktioissa"
date: 2024-10-15T20:59:33+03:00
tags: ["ohjelmointi"]
---

Vianselvityksessä on tärkeää nähdä mahdollisimman paljon tietoa siitä, mitä sovellus teki virheen sattuessa. Tämä postaus kertoo miten Googlen pilvifunktiossa pyörivän Node.js-softan toimintaa voi seurata OpenTelemetry-työkaluilla ja mitä tietoja niillä on mahdollista kerätä.

Google Cloud Platform (GCP) suosittelee käyttämään avoimen OpenTelemetry-määrittelyä noudattavia työkaluja diagnostiikkatiedon keräämiseen. GCP toimiikin hyvin yhteen OpenTelemetryn kanssa, mutta harmi kyllä Googlen dokumentaatio on puutteellista. [OpenTelemetry.io](https://opentelemetry.io/)-sivusto puolestaan sisältää paljon ohjeistusta, mutta sivusto kertoo OpenTelemetrystä yleisluontoisesti eikä painota GCP-käytössä oleellisia yksityiskohtia.

OpenTelemetry taipuu moneen erilaiseen käyttötarkoitukseen ja -ympäristöön. Sen konfigurontiin on useita tapoja. Tämä postaus esittää yhden tavan, sen jonka onnistuin saamaan toimimaan. Muitakin ja mahdollisesti parempi tapoja voi olla.

## Monitorointi

Aloitetaan katsomalla esimerkkejä siitä minkälaista diagnostiikkatietoa sovelluksista on mahdollista kerätä.

### Logit

Ohjelmat yleensä kirjoittavat erilaisia logiviestejä tai virheilmoituksia suorituksestaan. Pilvessä tapana on kerätä eri ohjelmien tai mikropalveluiden tuottaman logiviestit keskitettyyn logipalveluun. GCP tarjoaa tähän tarkoitukseen [Cloud Logging](https://cloud.google.com/logging/docs/overview) -palvelun.

OpenTelemetry rikastaa logeja siten, että Cloud Logging pystyy niputtamaan samaan tapahtumaan liittyvät viestit yhteen. Ilman rikastusta logiviestit näkyvät yksittäisinä riveinä ilman tietoa mitkä ovat peräisin samasta funktion suorituskerrasta.

Alla on kuvakaappaus Cloud Loggingistä. Siinä on avattu yksi logiviesti tarkempaan tarkasteluun. Rikastetun datan ansiosta Cloud Logging näyttää muut saman funktiokutsun aikana kirjoitetut logiviestit linkitettynä `>|`-merkeillä.

![Kuvakaappaus Cloud Logging -käyttöliittymästä. Kuvassa näkyy useita pilvifunktion tuottamia logiviestejä allekkain. Yksi viesteistä on avattu tarkempaan tarkasteluun. Viestistä näkyy metadaa ja linkit muihin samaan operaatioon liittyviin logiviesteihin.](cloud_logging_screenshot.png#center)
*Kuvakaappaus: Cloud Logging -käyttöliittymä*

### Jäljet

Ohjelman suorituksen jälki (trace) näyttää mitä operaatioita ohjelma suoritti, milloin ja kuinka kauan niissä kesti. Tällainen tieto auttaa vianselvityksessä ja optimoinnissa. HTTP-palvelinsovelluksesta kerätty jälki voisi esimerkiksi näyttää mitä taustapalveluja HTTP-pyyntöä käsiteltäessa kutsuttiin.

Googlen pilvessä on myös jälkien keräämiseen tarkoittettu palvelu: [Cloud Trace](https://cloud.google.com/trace/docs/overview). Alla on kuvakaappaus Cloud Tracesta. Siinä on jälki yhden HTTP `GET /`-pyynnön käsittelystä. Vastauksena pyyntöön palvelin haki Wikipedian etusivun ja palautti HTML-dokumentin pituuden. Wikipedia-sivun lataaminen edustaa esimerkissä mikropalvelukutsua, tietokantahakua tai jotain muuta hyödyllistä toimintoa, jota oikea ohjelma tekisi.

![Kuvakaappaus Cloud Trace -käyttöliittymästä. Kuvassa on useita rivejä, jotka edustavat ohjelman suorittamia toimintoja. Jokaisella rivillä on toiminnon nimi ja sininen suorakaide. Suorakaiteen alkupiste vaakasuunnassa kertoo toiminnon alkamisajan ja pituus toiminnon keston.](cloud_trace_screenshot.png#center)
*Kuvakaappaus: Cloud Trace -käyttöliittymä*

Kuvakaappauksessa rivit ovat HTTP-pyynnön käsittelyn aikana suoritetut toiminnot ja siniset palkit vaaka-akseleilla näyttävät toimintojen keston ja tapahtuma-ajan suhteessa pyynnön käsittelyn alkamishetkeen. Kuvasta näkee, että palvelin teki aluksi nopean kutsun Express-kirjastoon valitsemaan oikean käsittelijän GET-kutsulle ("request handler - /*") ja latasi sitten Wikipedian etusivun (alin "HTTP GET"). Nämä asiat saa selville klikkaamalla rivejä, joilloin Cloud Trace näyttää enemmän metatietoja jäljistä.

Valkoiset ympyrät näyttävät milloin ohjelma kirjoitti viestejä logiin. Logiviesti avautuu ympyrää klikkaamalla.

En tiedä mistä "Missing span" -rivi tulee.

OpenTelemetry määrittelee myös miten diagnostiikkatietoja kuljetetaan läpi eri palveluiden. Jos sovellus koostuu useista mikropalveluista ja niiden kaikkien diagnostiikka kerätään Cloud Traceen tai muuhun keskitettyyn paikkaan, niin tulee mahdolliseksi seurata yhden kutsun matkaa läpi kaikkien mikropalveluiden.

## Automaattinen instrumentointi

Edellä esitellyn kaltaisten tietojen keräämiseksi sovelluksen pitää kirjata metadataa jokaisesta oleellisesta operaatiosta. Onneksi sovelluksen koodiin ei tarvitse ripotella metadatakutsuja ympäriinsä vaan asia voidaan hoitaa automaattisella instrumentoinnilla. Instrumentointi tarkoittaa sitä, että OpenTelemetry kytkeytyy koodissa käytetyihin verkko-, tietokanta-  ja muihin kirjastoihin, ja kerää tietoja niiden käytöstä. Ohjelmoija voi kutsua kirjastoja sovelluskoodissa täysin normaalisti tarvitsematta huomioida metadatan keräämistä mitenkään. OpenTelemetry-projekti on luonut instrumentoinnit monille yleisille kirjastoille.

Ohjelmoijan tarvitseen vain alustaa instrumentointi. Tämän voi tehdä ajamalla seuraavan kaltaisen pätkän ohjelman alussa.

```javascript
const { Resource } = require('@opentelemetry/resources');
const { ATTR_SERVICE_NAME } = require('@opentelemetry/semantic-conventions');
const { BatchSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { TraceExporter } = require('@google-cloud/opentelemetry-cloud-trace-exporter');
const { NodeTracerProvider } = require('@opentelemetry/sdk-trace-node');
const { registerInstrumentations } = require('@opentelemetry/instrumentation');
const { ExpressInstrumentation } = require('@opentelemetry/instrumentation-express');
const { FetchInstrumentation } = require('opentelemetry-instrumentation-fetch-node');

const provider = new NodeTracerProvider({
  resource: new Resource({
    [ATTR_SERVICE_NAME]: 'trace-sample-app',
  }),
});
const spanProcessor = new BatchSpanProcessor(new TraceExporter());
provider.addSpanProcessor(spanProcessor);
provider.register();

registerInstrumentations({
  instrumentations: [
    new ExpressInstrumentation({
      ignoreLayersType: ['middleware']
    }),
	new FetchInstrumentation(),
  ],
});
```

Huomionarvoisia kohtia esimerkkikoodissa:
- `registerInstrumentation`-kutsu määrittelee mihin kirjastoihin instrumentaatio kiinnitetään. Esimerkissä datan kerääminen kytketään päällä Express.js- ja fetch-kutsuihin. [OpenTelemetry-projekti tarjoaa instrumentoinnit](https://github.com/open-telemetry/opentelemetry-js-contrib/blob/main/metapackages/auto-instrumentations-node/README.md#supported-instrumentations) monelle muullekin kirjastolle.
- `TraceExporter`-luokka (`@google-cloud/opentelemetry-cloud-trace-exporter`-kirjasto) lähettää kerätyn datan GCP Cloud Traceen.

Edellinen koodipätkä on suoritettava mahdollisimman aikaisessa vaiheessa sovelluksen käynnistystä, jotta automaattinen instrumentointi saa kaapattua importattavat kirjastot. Tämän voi varmistaa esilataamalla instrumentaatiokoodin ennen varsinaista sovellusta. Nodessa esilataus tapahtuu asettamalla ympäristömuuttuja `NODE_OPTIONS="--require ./src/opentelemetry.js"`, missä `./src/opentelemetry.js` on polku ylläolevan alustuksen sisältävään tiedostoon.

Serverless-pilvifunktio sammuttelee palvelimia silloin, kun niille ei ole käyttöä. Ennen sammuttamista kaikki kerätyt diagnostiikat pitäisi saada lähetettyä eteenpäin kutsumalla `provider.shutdown()`. Pilvifunktio saa SIGTERM-signaalin vähän ennen sammumistaan. Diagnostiikan alasajon voi kytkeä signaaliin seuraavasti:

```javascript
process.on('SIGTERM', async () => {
  await provider.shutdown();

  process.exit(0);
});
```

Minulle on epäselvää onko SIGTERM-käsittelijä luotettava tapa varmistaa, että kaikki kerätty tieto päätyy Cloud Traceen, vai voiko pilvifunktio joskus sammua myös ilman SIGTERM-signaalia. Pienimuotoisen testin perusteella kaikki näyttää toimivan, mutta en ole varma tulisiko kaikki data kerätyksi talteen myös raskaammassa käytössä.

Kun instrumentointi tehdään yllä kuvatulla tavalla, sovelluskoodissa ei tarvitse tehdä mitään erityistä tietojen keräämiseksi. Alla on esimerkki yksinkertaisesta pilvifunktiosta. Esimerkki on toteutettu GCP functions frameworkilla. Pilvifunktio vastaa `GET /otel`-kutsuihin, lataa Wikipedian etusivun ja palauttaa HTML-sivun merkkimäärän. Instrumentoinnin ansiosta kaikki tarvittavat tiedot pilvifunktion kutsuista ja Wikipedia-latauksista napataan talteen automaattisesti.

```javascript
const functions = require('@google-cloud/functions-framework');
const { Logging } = require('@google-cloud/logging');

const logging = new Logging();
void logging.setProjectId();
const log = logging.log('app');

functions.http('otel', async (req, res) => {
  writeLog(`GET /otel called`);

  try {
    const wikires = await fetch('https://en.wikipedia.org/wiki/Main_Page');
    writeLog(`Wikipedia fetch status: ${wikires.status}`);
    const body = await wikires.text();

    res.send(`OK, ${body.length} characters`);
  } catch (err) {
    writeLog(String(err));
    res.status(500).send('Internal error');
  }
});

function writeLog(message) {
  const entry = log.entry(metadata, { severity: 'INFO' });
  void log.write(entry);
}
```

Pilvifunktio toimii myös ilman `--require ./src/opentelemetry.js`-vipua, mutta silloin diagnostiikkatietoa ei kerry.

[Kokonainen toimiva koodiesimerkki Githubissa](https://github.com/aajanki/gcp-cloud-function-otel).
