---
title: "Ääkköset verkkotunnuksissa"
date: 2023-06-17T20:49:03+03:00
---

Verkkotunnuksissa on mahdollista käyttää ääkkösiä. Esimerkiksi Jämsän kaupungin nettisivut löytyvät osoitteesta [www.jämsä.fi](https://www.jämsä.fi) (vaikkakin osoite on vain ohjaus rinnakkaisosoitteeseen [www.jamsa.fi](https://www.jamsa.fi)). Kovin ahkerassa käytössä ääkköselliset verkko-osoitteet eivät ole vaikka [ääkköset fi-päätteisissä verkkotunnuksissa otettiin käyttöön jo vuonna 2005](https://yle.fi/a/3-5218594).

## Tuki ääkkösille lisättiin myöhemmin

Alunperin Internetin nimipalvelujärjestelmän kehittivät yhdysvaltalaiset insinöörit 1980-luvun alussa. Heidän mielestään riitti, että verkkotunnuksissa sai käyttää kirjaimia a:sta z:aan, numeroita 0--9, väliviivaa ja pistettä.

Kun myöhemmin havahduttiin siihen, että englanninkielisen maailman ulkopuolella toivottiin mahdollisuutta käyttää verkko-osoitteissa kielten kansallisia kirjaimia, muutos päätettiin toteuttaa toimimaan saumattomasti yhdessä silloin jo vakiintuneen nimipalvelujärjestelmän kanssa. Kansallisten kirjainten käyttöä varten ei haluttu rakentaa täysin uutta järjestelmää, koska se olisi käytännössä pilkkonut Internetin kahdeksi erilliseksi verkoksi. Tällainen taaksepäinyhteensopivuuden vaatimus tulee usein vastaan, kun järjestelmää laajennetaan tavoilla, joita alkuperäisissä määrittelyssä ei ole huomioitu.

Ratkaisu ongelmaan oli älykäs. Käyttäjän nettiselain muuntaa kansallisia kirjaimia sisältävät osoitteet tiettyjen sääntöjen mukaisesti vain alkuperäisen nimipalvelujärjestelmän tukemia merkkejä sisältäväksi ja päinvastaiseen suuntaan. Näin Internetin perusinfran palvelimia ei tarvinnut päivittää. Riitti, että käyttäjät päivittivät selaimensa muunnosta tukevaan versioon. Käyttäjät saivat päivittää selaimensa omaan tahtiinsa. Useiden eri tahojen ylläpitämien nimipalvelinten päivitystä olisi ollut paljon vaikeampi koordinoida. Käytännössä päivitysrumba tapahtui jo 2000-luvun alkupuolella ja kaikki nykyselaimet tukevat ääkkösiä osoitteissa.

Jokaisen maan kansallisia verkkotunnuksia hallinnoiva taho päättää itse mitkä lisäkirjaimet ovat sallittuja perusaakkosten lisäksi. Suomen fi-tunnuksissa voi käyttää å-, ä-, ö-kirjaimia ja Suomessa puhuttavan saamen kielen merkkejä. Saksalaisissa de-tunnuksissa puolestaan on sallittu esimerkiksi ü-kirjain.

## Esimerkki ääkkösten koodaamisesta

Muunnos ääkkösiä sisältävästä verkkotunnuksesta vain a--z-kirjaimia, numeroita ja väliviivoja sisältävään muotoon tapahtuu Punycode-algoritmilla, joka on määritelty [RFC 3492 -standardissa](https://www.rfc-editor.org/rfc/rfc3492). Selostan algoritmin toimintaa käyttämällä esimerkkinä osoitetta [www.jämsä.fi](https://xn--jms-qlac.fi). Punycode-algoritmi muuntaa tämän muotoon www.xn--jms-qlac.fi. Algoritmi käsittelee verkkotunnuksen pisteillä erotellut osat erikseen. Jos osa ei sisällä ääkkösiä tai muita erikoismerkkejä, sille ei tehdä mitään. Esimerkissä www- ja fi-osat jäävät siksi alkuperäisiksi ja vain jämsä-osa muuttuu.

Koodattu muoto `xn--jms-qlac` koostuu neljästä palasesta: `xn--`, `jms`, `-` ja `qlac`. Etuliite `xn--` ilmaisee, että tämä on Punycodella koodattu verkkotunnus. Etuliite on aina sama merkkijono `xn--`. Normaaliksi verkkotunnukseksi ei ole sallittua rekisteröidä nimeä, jossa kolmas ja neljä merkki ovat väliviivoja, joten Punycode-koodattu verkkotunnus on helppo erottaa normaalista verkkotunnuksesta.

Seuraava osa `jms` on verkkotunnuksen kirjaimet ilman ääkkösiä. Jämsä-sanan ä-kirjaimet on siis jätetty pois.

Viimeinen `-` toimii erottimena ja sen jälkeen tulee tieto ääkkösten sijainneista alkuperäisessä osoitteessa. Esimerkissä merkkijono `qlac` sisältää koodatussa muodossa tiedon, että alkuperäisessä osoitteessa toinen ja viides merkki ovat ä-kirjaimia.  Yksityiskohdat ääkkösten sijaintien koodaamisesta on selostettu edellä mainitussa [RFC-dokumentissa](https://www.rfc-editor.org/rfc/rfc3492).

Punycode-muunnoksen jälkeen selain lähettää vain nimipalvelun tukemista merkeistä koostuva koodatun osoitteen eteenpäin nimipalvelimelle. Nimipalvelimen ei tarvitse ymmärtää mitään ääkkösistä. Lisäksi algoritmi on rakennettu niin, että nettiselain pystyy aina muuntamaan koodatun osoitteen takaisin alkuperäiseen ääkköselliseen muotoon näyttäessään sen käyttäjälle.

Voit kokeilla koodausta [Punycoder](https://www.punycoder.com/)-työkalulla.

Yllä esitelty Punycode-koodaus pätee vain verkkotunnukseen eli osoitteen alkuosaan. Verkko-osoitteen polkuosan määrittelee toinen standardi, joka on seurannut erilaista historiallista kehityspolkua. Sen määräämänä ääkköset osoitteen polkuosassa koodataan täysin eri tavalla kuin verkkotunnuksessa! Polussa käytetään ns. [URL-enkoodausta](https://en.wikipedia.org/wiki/URL_encoding). Esimerkiksi osoite `https://esimerkki.fi/jämsä.html` muuntuu muotoon `https://esimerkki.fi/j%C3%A4ms%C3%A4.html`.
