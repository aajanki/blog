---
title: "Voittoputken pituus"
date: 2023-06-03T20:27:15+03:00
description: Jos heitän kolikkoa kymmenen kertaa, kuinka monta peräkkäistä kruunaa saan? Simuloin voittoputken odotettua pituutta laskennallisesti.
tags: []
draft: true
---

Jos heitän kolikkoa kymmenen kertaa, kuinka monta peräkkäistä kruunaa saan? Voi tulla monta kruunaa ennen kuin klaava katkaisee putken tai mahdollisesti joka toinen heitto on kruuna ja joka toinen klaava, jolloin pisin kruunaputki olisi vain yhden heiton pituinen.

![Kolikon heitto](coin_toss.jpg#center)
*Kuva: [ICMA Photos/Wikimedia Commons](https://commons.wikimedia.org/wiki/File:Coin_Toss_%283635981474%29.jpg), CC-SA 2.0*

Yksittäisen heittosarjan tuloksen saa tietenkin selville vain heittämällä kolikkoa kymmen kertaa. Jos haluan sanoa jotain yleisempää esimerkiksi ymmärtääkseni minkälaisilla ehdoilla kannattaa lyödä vetoa peräkkäisten voittojen määrästä, voin arvioida tilastollisesti mikä olisi todennäköinen tulos, jos toistaisin heittosarjaa useita kertoja.

Arvioin pisimmän voittoputken pituutta simuloimalla kolikonheittoa tietokoneella. Arvoin kymmenen kertaa joko luvun 0 (= klaava) tai luvun 1 (= kruuna) tasatodennäköisyyksin. Laskin arvotusta sarjasta pisimmän ykkösistä koostuvan osajonon pituuden. Toistin sarjoja 10&#x202F;000 kertaa ja laskin toistoista pisimmän voittosarjan pituuden mediaanin. Satunnaisvaihtelun suuruuden hahmottamiseksi etsin myös raja-arvot, joiden välissä pisin voittosarja oli 5--95 prosentissa toistoista. Lopuksi laajensin kokeilua toistamalla saman myös pidemmille kuin kymmenen heiton sarjoille.

Alla olevassa kuvassa on piirretty tumman sinisellä viivalla havainnoista laskettu pisimmän voittoputken mediaanipituus tietyn pituisessa heittosarjassa. Vaaleammin sinisellä on varjostettu alue, johon pisimmän voittosarjan pituus osuu 90% todennäköisyydellä. Vaaka-akselilla on kolikonheittosarjan kokonaispituus ja pystyakselilla on pisimmän voittoputken pituus.

![Kuvaaja pisimmän voittoputken arvioidusta mediaanipituudesta ja todennäköisestä vaihteluvälistä eri pituisilla sarjoilla.](voittoputki_50.png#center)

Sinisen alueen vasen reuna on kymmenen kolikonheiton sarjan kohdalla. Pisin voittoputki kymmenen heiton sarjassa on todennäköisesti 1--5 heittoa pitkä (mediaani 3 heittoa). Jos heittäisin kolikkoa tuhat kertaa niin pisin voittoputki olisi todennäköisesti 7 ja 13 heiton välillä (mediaani 9 heittoa).

Entä jos kolikonheiton sijaan pelaankin pasianssia, jossa yksittäisen pelin voittotodennäköisyys onkin 90% eikä 50% kuten kolikonheitossa? Voin helposti vaihtaa voittotodennäköisyyttä simuloinnissa ja piirtää vastaavan kuvaajan tästä tilanteesta.

![Kuvaaja pisimmän voittoputken pituuden mediaanista ja vaihteluvälistä pelatessa peliä, jonka yksittäisen kierroksen voittaa 90% todennäköisyydellä.](voittoputki_90.png#center)

Nyt kymmenen pelin sarjassa voittoputken mediaani on 8 peliä ja todennäköinen vaihteluväli 4--10 peliä. Pelien määrän kasvaessa pisimmän voittoputken pituus kasvaa edelleen hitaasti. Esimerkiksi tuhannen pelin sarjassa voittoputki on todennäköisesti jossain 33 ja 70 pelin välillä. Vaikka jokaisen yksittäisen pelin voittotodennäköisyys onkin korkea, niin aina välillä pääsee kuitenkin epätodennäköinen häviö katkaisemaan voittoputken ja voittojen keräämisen joutuu aloittamaan alusta.

[Simulaatiokoodini on saatavilla.](https://github.com/aajanki/experiments/blob/main/voittoputki/simulate.py)
