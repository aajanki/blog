---
title: "Zipfin laki"
date: 2024-05-10T20:36:01+03:00
tags: ["kieliteknologia"]
---

Suomenkielessä on pieni joukko usein toistuvia sanoja (esim. *ja*,
*on*, *ei*, *että*) ja valtavasti harvinaisempia sanoja (esim.
*hienoviritteinen*, *pimitys*, *kilkutin*). Tämä ei sinällään ole
yllättävää. Yllättävämpää on se, että sanojen esiintymistiheydet
noudattavat varsin tarkasti yksinkertaista sääntöä.

Toiseksi yleisin sana esiintyy usein noin puolet yleisimmän sanan
esiintymiskerroista, kolmanneksi yleisin noin kolmasosan ja niin
edelleen. Tätä säännönmukaisuutta on havaittu kaikissa kielissä ja se
pätee aina yleisimmistä sanoista harvinaisimpiin.

Matemaattisesti ilmaistuna sanojen esiintymistodennäköisyys on
suunnilleen kääntäen verrannollinen sanan järjestyslukuun, kun sanat
on järjestetty yleisyyden mukaan:

```
sanan esiintymistodennäköisyys ∝ 1/sanan järjestysluku
```

Tätä sääntöä kutsutaan Zipfin laiksi. Se on saanut nimensä
kielitieteilijä George Zipfin mukaan, jonka 1930-luvulla julkaistu
kirja nosti ilmiön kielitieteilijöiden tietoisuuteen. Zipf ei tosin
ollut ensimmäinen riippuvuuden havainnut vaan samankaltaisia
havaintoja oli esitetty jo 1910-luvulta lähtien.

## Zipfin laki suomen kielessä

Seuraavaan kuvaan olen piirtänyt sinisinä pisteinä laajasta
suomenkielisestä tekstiaineistosta laskemani sanojen esiintymisten
todennäköisyydet (pystyakselilla) ja järjestysluvut (vaaka-akselilla).
Molemmat akselit ovat logaritmisella skaalalla. Aineistona käytin
C4-tekstikokoelmaa, ja [sanojen taajuuksien laskemisesta kerroin
enemmän edellisessä postauksessani](../taajuussanasto/). Punainen
katkoviiva on Zipfin lain mukainen ennuste.

Havainnot seuraavat kohtuullisella tarkkuudella teorian ennustetta.

![Kuvaaja sanojen esiintymisestä suomenkielisessä aineistossa. Vaaka-akselilla on sanan sijoitus, kun sanat on järjestetty yleisyysjärjestykseen. Pystyakselilla on sanan esiintymistodennäköisyys. Kummatkin akselit esitetty logaritmisella skaalalla. Kuvaan on piirretty kaikkia aineistosta löytyviä sanoja vastaavat pisteet ja katkoviivalla suora, jolle pisteiden tulisi Zipfin lain mukaan osua.](zipf-finnish-c4.png#center)

## Selityksiä

Kukaan ei osaa tyhjentävästi selittää miksi kirjoitetun kielen sanat
noudattavat tällaista matemaattista kaavaa. On esitetty erilaisia
ehdotuksia tilastollisista syistä psykologisiin selityksiin.

George Zipfin oma selitys ilmiölle oli viestinnän vaivan minimointi.
Yleisten sanojen käyttö vähentää viestin lähettäjän vaivaa, kun taas
tarkat termit ovat tärkeitä viestin ymmärtämiselle. Voidaan osoittaa,
että viestin lähettäjän ja vastaanottajan yhteinen vaiva minimoituu,
kun sanojen jakauma seuraa Zipfin lakia.

Toinen selitysyritys olettaa, että viestin laatijalla on tapana
toistaa aikaisemmin käyttämiään sanoja. Valitessaan uutta sanaa
viestin jatkoksi, kirjoittaja (oletuksen mukaan) valitsee aikaisemmin
tekstissä käyttämänsä sanan sitä todennäköisemmin mitä useammin sana
on jo esiintynyt viestissä. Kaikilla viestissä aikaisemmin
esiintymättömillä sanoilla on sama vakiotodennäköisyys tulla
valituksi. Tällaisella [takertuvan kiinnittymisen
mallilla](https://en.wikipedia.org/wiki/Preferential_attachment)
(engl. preferential attachment) on selitetty Zipfin lain kaltaisia
potenssilakeja myös muilla aloilla kuin kielitieteessä.

Tutkijoiden välille syntyi aikaisemmin kiistoja siitä, mikä selittää
Zipfin lain. He olettivat, että oikean selityksen löytäminen auttaisi
ymmärtämään ihmismielen toimintaa. Toiveet tästä murenivat, kun
huomattiin, että myös hyvin yksinkertainen tilastollinen malli tuottaa
saman sanajakauman.

Kuvitellaan apina, joka hakkaa näppäimistöä satunnaisesti niin, että
apina osuu mihin tahansa kirjaimeen tai välilyöntiin on yhtä suurella
todennäköisyydellä. Syntyvässä kirjainpuurossa on toistuu paljon
lyhyitä, muutamasta kirjaimesta koostuvia "sanoja" (eli välilyöntien
rajaamia satunnaisia kirjainjonoja) ja harvinaisempia pidempiä
kirjainryhmiä. Jokaisen tällaisen "sanan" todennäköisyys riippuu vain
sen kirjainten lukumäärästä. Jos todennäköisyydet lasketaan auki,
huomataan että "sanojen" todennäköisyydet noudattavat täsmälleen
Zipfin lakia!

Mikään yksinkertainen syy ei yksinään selitä miksi kielen sanojen
esiintymistodennäköisyydet näyttävät usein noudattavan yksinkertaista
sääntöä vaan taustalla täytyy olla monimutkaisempia prosesseja. Zipfin
laki on kuitenkin yksi avain, kun tutkijat yrittävät ymmärtää kielen
rakennetta ja ihmisten kielitajua.
