---
title: "Hämäävät merkit verkkotunnuksissa"
date: 2023-07-10T14:42:19+03:00
# tags: []
---

Tuki kansallisten kirjainten käytölle verkkotunnuksissa sallii ääkkösiä sisältävät osoitteet kuten [sää.fi](https://xn--s-0faa.fi), mutta samalla avautuu aukko verkkohuijareille. Kirjoitin [edellisessä blogipostauksessa miten kansallisten kirjainten käyttö toimii teknisesti](/blog/ääkköset_verkkotunnuksissa/). Tässä kirjoituksessa kerron miten huijarit voivat luoda hämääviä osoitteita käyttämällä helposti keskenään sekoittuvia kirjaimia.

## Hämääminen saman näköisillä merkeillä

Eräs verkkohuijausten muoto on pankkitunnusten kalastelu. Uhri houkutellaan syöttämään pankkitunnuksensa huijaussivustolle, ja huijari käyttää näin kaappaamiaan tunnuksia siirtämään uhrin rahat omalle tililleen. Huijaussivusto on tarkoituksella tehty näyttämään mahdollisimman paljon oikean pankin sivustolta, jotta uhrin hälytyskellot eivät alkaisi soimaan ainakaan ennen kuin hän on ehtinyt luovuttaa tunnuksensa. Sivuston ulkonäön lisäksi huijari yrittää saada myös verkko-osoitteen näyttämään mahdollisimman aidolta epäilyksien välttämiseksi.

Jos pankin oikea verkkosivusto olisi osoitteessa `pankki.net`, huijari voisi pystyttää huijaussivustonsa osoitteeseen `pаnkki.net`. Osoitteet näyttävät nopealla vilkaisulla täysin samoilta, mutta jälkimmäisessä on käytetty suomalaisten a-kirjaimen (Unicode-merkki U+0061, "LATIN SMALL LETTER A") sijasta kyrillisen kirjaimiston &#x430;-kirjainta (U+0430, "CYRILLIC SMALL LETTER A"). Kirjainten ulkoasut ovat täsmälleen samat (ainakin selaimeni käyttämällä fontilla), mutta ne ovat Unicode-merkistössä kuitenkin kaksi erillistä merkkiä. Ero käy selväksi, kun osoitteet muunnetaan nimipalvelujärjestelmän sisäisesti käyttämään Punycode-muotoon. Kyrillisen kirjaimen sisältävän osoitteen Punycode-muoto nimittäin on `xn--pnkki-4ve.net` eikä käyttäjän tarkoittama `pankki.net`.

Vastaavia [helposti keskenään sekoitettavia merkkipareja](https://www.unicode.org/Public/security/latest/confusables.txt) on Unicode-merkistössä paljon enemmänkin. Niitä hyödyntämällä huijari voi rekisteröidä aidolta verkko-osoitteelta näyttävän osoitteen ja pystyttää huijaussivustonsa sinne. Samankaltaisten merkkien hyödyntämiseen perustuvia huijauksia kutsutaan homografisiksi hyökkäyksiksi.

## Huijausten torjuntaa

Hämäävien, samalta näyttävien kirjainten tuomat uhat tunnistettiin jo, kun kansallisten merkkien käyttöä verkko-osoitteissa vasta suunniteltiin. Erilaisia huijausten torjuntakeinoja on ehdotettu siitä lähtien, mutta täydellistä torjuntamenetelmää ei valitettavasti ole onnistuttu kehittämään.

Nettiselaimet yrittävät tunnistaa useiden merkistöjen kirjaimia epäilyttävällä tavalla sekoittavia verkko-osoitteita (katso esimerkiksi [Google Chromen tunnistussäännöt](https://chromium.googlesource.com/chromium/src/+/main/docs/idn.md)). Säännöt eivät pysty koskaan täydellisesti kattamaan kaikkia tapauksia. Selaimet eivät esimerkiksi varoita käyttäjää, jos [verkko-osoite koostuu pelkästään latinalaisilta kirjaimilta näyttäviltä kyrillisistä kirjaimista](https://www.xudongz.com/blog/2017/idn-phishing/), koska on kyseessä voi olla aito kyrillisiä merkkejä käyttävä sivusto.

Kun selain luokittelee verkko-osoitteen epäilyttäväksi, se näyttää osoitteen Punycode-muodossa varoittaakseen käyttäjää tai jopa näyttää varoitustekstin. Jos selain siis näyttää oudon `xn--`-alkuisen osoitteen, kannattaa ennen sen klikkaamista varmistaa pariinkin kertaan, että osoite johtaa sinne minne haluat mennä.

Salasanamanagerin käyttäminen auttaa myös, sillä se huomaa pienetkin erot osoitteissa. Jos käyttäjä päätyy huijaussivulle, ei salasanamanageri suostu täydentämään salasanaa, koska huijausosoite ei ole salasanamanagerin muistissa. Salasanamanagerin käytöstä on tietenkin muitakin etuja.

Samalta näyttäviin kirjaimiin perustuvat huijaukset on estetty fi-verkkotunnuksissa, koska niissä on [sallittu vain suomen ja saamen kielien merkit](https://www.traficom.fi/fi/viestinta/fi-verkkotunnukset/millainen-hyva-verkkotunnus) numeroiden ja väliviivan lisäksi. Edellä kuvattu kyrillisen kirjaimiston merkkien käyttäminen samalta näyttävien latinalaisten merkkien sijaan ei siis ole mahdollista fi-verkkotunnuksissa. Valitettavasti com- ja net-tunnuksissa ja monissa muissakaan yleisissä tunnuksissa ei ole millään tavalla rajoitettu eri merkistöjen sekoittamista osoitteessa.
