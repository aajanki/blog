---
title: "Tekstin ymmärrettävyyden mittaaminen"
date: 2023-08-13T18:14:46+03:00
tags: []
---

Viestin perillemenoon vaikuttaa oleellisesti sen muotoilu. Selkeästi ilmaistu viesti ymmärretään helpommin ja jää paremmin mieleen.

Tekstin ymmärrettävyyttä voidaan analysoida tilastollisesti. Esittelen tässä kaksi suomenkielisen tekstin ymmärrettävyyden mittaamiseen kehitettyä mallia.

## Ymmärrettävyyden osatekijöitä

Osmo A. Wiion mukaan tekstin ymmärrettävyyteen vaikuttavat mm. seuraavat tekijät \[1\]:
* kieliasu: vieraita sanoja ja monimutkaisia rakenteita sisältävää teksti on vaikea ymmärtää
* samastumisen mahdollisuus: tutuista asioista tai ihmisistä kertovaa tekstiä on helpompi seurata
* kiinnostavuus: lukija on valmis näkemään enemmän vaivaa kiinnostavasta aiheesta kertovan tekstin parissa
* käsitteellisyyden aste: abstraktin tai teoreettinen teksti on haastavampaa kuin havainnollinen kosketeltavissa tai kuultavissa olevista asioista kertova teksti

Kieliasu on näistä tekijöistä ehkä helpoiten kirjoittajan muokattavissa. Kieliasun vaikutusta onkin tutkittu paljon.

## Luettavuuskaavat

Wiio on kehittänyt tilastollisia "luettavuuskaavoja", joiden tarkoitus on arvioida numeerisesti kielen rakenteellisten piirteiden vaikutusta tekstin helppolukuisuuteen \[2\]. Nämä kaavat pyrkivät ennustamaan kuinka monta vuotta lukijan on täytynyt käydä koulua ymmärtääkseen tekstin sanoman. Kaavat antavan arvion ymmärtämiseen vaaditusta luokkatasosta lukuarvona 1--12 (arvot 1--9 vastaavat peruskoulun luokkia ja 10--12 lukion luokkia). Karkea tulkintaohje on, että alle 6 tarkoittaa erityisen helposti ymmärrettävää tekstiä ja 9 tai suurempi vaikeasti ymmärrettävää.

Wiio esittelee kaksi versiota luettavuuskaavastaan. Kaavat Wiio johti tilastollisesti aineistosta, jonka hän keräsi mittaamalla kuinka hyvin eri-ikäiset henkilöt ymmärsivät luettavaksi annettuja tekstejä.

Yksinkertaisin kaava laskee arvion ymmärtämiseen vaaditusta luokkatasosta LT seuraavasti:

```
LT = 2,7 + 0,3 * PS,
```

missä PS on pitkien sanojen lukumäärä 100 sanaa kohden. Pitkiksi sanoiksi katsotaan sanat, joiden perusmuodossa on vähintään neljä tavua. Siis esimerkiksi sana *koulutuksella* laskettaisiin lyhyeksi sanaksi, koska sen perusmuodossa *koulutus* on vain kolme tavua. Luotettavaan arvioon tarvitaan pitkähkö katkelma tekstiä, mielellään ainakin 100 sanaa.

Jos 100 sanan kirjoituksesta esimerkiksi 20 sanaa olisi pitkiä (eli perusmuodossaan neli- tai useampitavuisia), niin kaava antaisi tulokseksi `LT = 2,7 + 0,3 * 20 = 8,7`. Tällaisen tekstin ymmärtämiseen vaadittaisiin siis peruskoulun 9. luokkaa vastaava lukutaito.

Tarkempi mutta monimutkaisempi versio kaavasta huomio myös ns. sanaluokkien määräsuhteen MS:

```
LT = 0,7 + 0,3 * PS + 0,05 * MS
```

PS on pitkien sanojen osuus kuten edellä. Määräsuhde MS mittaa sanaluokkien suhteellisia osuuksia tekstissä. Se lasketaan seuraavasti: `MS = 100*(adjektiivit + adverbit)/(verbit + substantiivit)`. Jälkimmäinen kaava ennusti Wiion kokeissa hieman tarkemmin lukijan todellista luokkatasoa. Sen laskeminen on kuitenkin työläämpää, koska siihen tarvitaan sanaluokkien tunnistamista.

Wiio kokeili tutkimuksessaan myös muita tekstin kieliasua kuvaavia tekijöitä kuten lauseiden ja kappaleiden pituutta ja lainasanojen osuutta. Nämä tai muut vastaavat tekijät eivät kuitenkaan parantaneet kaavojen tarkkuutta oleellisesti.

## Pitkät sanat haittaavat viestin ymmärtämistä

Miksi pitkien sanojen osuus olisi hyvä tai jopa yksinään riittävä tekijä luettavuuden arviointiin? Wiio selittää, miten pitkät sanat vaikeuttavat tekstin ymmärtämistä.

Lukiessaan ihminen ei etene tasaisesti tekstissä (vaikka lukijasta ehkä siltä tuntuukin). Yksinkertaistetun mallin mukaan lukija ymmärtää tekstin lyhyissä paloissa. Luetun tekstin sanat kertyvät aluksi työmuistiin. Työmuisti on rajallinen, sinne mahtuu kerrallaan noin 5--10 sanan mittainen pätkä. Työmuistin täytyttyä aivot yrittävät muodostaa työmuistin sisällöstä ymmärrettävän kokonaisuuden ja tallentaa sen pitkäkestoiseen säiliömuistiin. Samaan aikaan työmuistin sisältö nollautuu ja sinne alkaa kertymään jo seuraavan tekstipätkän sisältö.

Jos aivot eivät onnistu muodostamaan ymmärrettävää kokonaisuutta työmuistin sisällöstä, koko työmuistissa sillä hetkellä ollut sisältö jää ymmärtämättä. Yksikin vieras sana voi estää ymmärrettävän ajatuskokonaisuuden muodostumisen. Pitkät sanat ovat todennäköisemmin vieraita ja vaikeita. Siksi niiden määrä heijastaa epäsuorasti ymmärrettävyyttä.

## Luettavuuskaavojen soveltaminen

Esimerkiksi tekstinkäsittelysovellukset voisivat hyödyntää luettavuuskaavoja. Ohjelma voisi laskea tekstikappaleiden luettavuusindeksit käyttäjän muokatessa tekstiä ja korostaa vaikeasti ymmärrettävät tekstikohdat samaan tapaan kuin oikolukutoiminto korostaa väärin kirjoitetut sanat. Kirjoittaja saisi näin jatkuvasti palautetta kirjoittamansa tekstin ymmärrettävyydestä ja voisi muokata tekstiään helpommaksi. Mekaanisen laskentakaavan etuna on mahdollisuus automatisoida luettavuusanalyysi.

Wiion luettavuuskaavoja sovellettaessa pitää muistaa, että ne mittaavat tekstin helppolukuisuutta vain kieliasun kannalta. Ne eivät ota huomioon sitä eteneekö tekstin sisältö johdonmukaisesti ja huomioidaanko tekstissä tarkoitetun lukijakunnan näkökulma. Viime vuosina on kehitetty myös muita ymmärrettävyyden mittareita. Esimerkiksi [Selkokielen mittari](https://selkokeskus.fi/selkokieli/selkokielen-mittari/) auttaa arvioimaan onko asiateksti selkokieltä.

## Viitteet

* \[1\]: Wiio, Osmo A. (1989): *Viestinnän perusteet* (viides, uudistettu painos)
* \[2\]: Wiio, Osmo A. (1968): *Readability, comprehension and readership: An experimental study on the readability of Finnish magazine articles, with special reference to readership*
