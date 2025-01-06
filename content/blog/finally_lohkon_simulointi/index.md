---
title: "Finally-lohkon simulointi poikkeusten käsittelyssä"
date: 2025-01-06T16:20:12+02:00
tags: ["ohjelmointi"]
---

Useimmissa ohjelmointikielissä poikkeusten käsittelevään try-except-rakenteeseen (joissain kielissä try-catch) voi lisätä finally-lohkon, joka suoritetaan aina lopuksi. Kaikissa kielissä finally-ominaisuutta ei kuitenkaan ole. Oletetaan ([syistä, jotka eivät mahdu tämän blogimerkinnän marginaaliin](https://github.com/aajanki/ts2workflows)), että haluamme toteuttaa finally-lohkon kaltaisen toiminnallisuuden kielellä, joka ei sitä luonnostaan tue.

Finally-lohko suoritetaan riippumatta siitä tapahtuiko poikkeus vai ei. Sitä käytetään esimerkiksi tiedostojen sulkemiseen tai resurssien vapauttamiseen, kun halutaan varmistaa että siivous tapahtuu myös poikkeustilanteissa. Seuraavassa koodiesimerkissä tiedosto suljetaan vaikka tiedostoon kirjoittaminen (`f.write(message)`) aiheuttaisikin poikkeuksen:

```python
def logToFile(message):
  f = open('logs', 'a')

  try:
    f.write(message)
  finally:
    f.close()
```

Finally-lohko suoritetaan try- ja mahdollisen except-lohkon jälkeen. Finally-osa suoritetaan vaikka try- tai except-osuus olisi päättynyt koodin suorituksen varhaisessa vaiheessa katkaisevaan lauseeseen kuten `return`. Myös `break` ja `continue` siirtävät suorituksen finally-osaan, mutta vain siinä tapauksessa, että niiden vaikutuksesta suoritus siirtyy ulos try-except-lohkosta. (`continue` hyppää for-looppin alkuun. Jos for-looppi on kokonaan try-lohkon sisällä, niin silloin finallyä ei suoriteta `continue`:n yhteydessä.)

Jos try- tai except-osuudessa tapahtuu poikkeus, suoritetaan finally-osa, jonka jälkeen poikkeus heitetään uudelleen. Yhteenvetona siis `return`, `break`, `continue` tai poikkeus ikään kuin viivästetään: ensinnä suoritetaan finally-osan koodi ja vasta sen jälkeen alkuperäinen syy koodin keskeytykseen.

Seuraavaan taulukon kahteen ensimmäiseen sarakkeeseen on koottu kaikki mahdolliset tavat, joilla try-except-kokonaisuuden suoritus voi päättyä. Kolmas sarake kertoo mitä finally-lohkon suorituksen jälkeen tapahtuu kyseisissä tapauksissa.

|try-lohkon suoritus päättyy...|except-lohkon suoritus päättyy...|finally-lohkon jälkeen|
| ---------------------------- | ------------------------------- | -------------------- |
|lohkon loppuun|\-|\-|
|`return x`|\-|`return x`|
|`break`|\-|`break`|
|`continue`|\-|`continue`|
|poikkeukseen `e`|(ei except-lohkoa)|`raise e`|
|poikkeukseen `e`|lohkon loppuun|\-|
|poikkeukseen `e`|`return x`|`return x`|
|poikkeukseen `e`|poikkeukseen `f`|`raise f`|
|poikkeukseen `e`|`break`|`break`|
|poikkeukseen `e`|`continue`|`continue`|

Nyt kun ymmärrämme kaikki huomioon otettavat tapaukset, voimme suunnitella miten simuloida finally-lohkon vaikutusta. Esiteltävä menetelmä perustuu edellä mainittuun ajatukseen operaatioiden viivästämisestä. Menetelmä on mekaaninen koodimuunnos, jonka voi tehdä automaattisesti finally-lauseisiin.

Tapaukset, joissa try- tai except-lohkon suoritus menee loppuun asti ovat helppoja. Niissä vain yksinkertaisesti suoritetaan finally-lohkon koodi try-except-rakenteen jälkeen. Muissa tapauksissa meidän pitää ottaa talteen koodin suorituksen katkaiseva syy (`return`, `break`, `continue` tai poikkeus) ja toistaa se finally-lohkon jälkeen.

Käytännössä se tapahtuu muokkaamalla try- ja except-lohkojen koodia. Esimerkiksi `return x` korvataan muuttujan alustuksella `after_finally = { condition: 'return', value: x}` ja hypyllä finally-lohkon koodin alkuun. `after_finally`-muuttuja pitää kirjaa siitä tuleeko finally-lohkon jälkeen tehdä jokin viivästetty operaatio. `after_finally['condition']` on tunniste, joka kertoo viivästetyn operaation. Merkkijono `'return'` merkitsee, että on suoritettava viivästetty `return`-lause. `after_finally[value]` on mahdollinen parametri. `return`:in tapauksessa se on viivästetyssä `return`:issa käytettävä paluuarvo.

`break` ja `continue` voidaan käsitellä vastaavasti asettamalla `after_finally['condition']`-muuttujaksi joko `'break'` tai `'continue'`, hyppäämällä finally-lohkoon ja tekemällä sen lopuksi viivästetty `break` tai `continue`. Näihin lisämonimutkaisuutta tuo se, että korvaaminen kuuluu tehdä vain, jos `break` tai `continue` aiheuttaisi poistumisen try-except-lohkosta.

Poikkeusten käsittelemiseksi tarvitaan toinen try-lohko alkuperäisen ympärille. Se ottaa talteen mahdolliset poikkeukset sisemmissä try- tai except-lohkoissa, jotta ne voidaan heittää uudelleen finally-lohkon koodin suorituksen jälkeen. Viivästettävän poikkeuksen voi tallentaa esimerkiksi tähän tapaan: `after_finally = { condition: 'raise', value: exc }`.

Katsotaan miltä tämä näyttää käytännössä koodiesimerkin kautta. Tarkastellaan seuraavaa koodinpätkää:

```python
try:
  print('try-lohkossa')
  # ...
except:
  print('except-lohkossa')
  # ...
finally:
  print('finally-lohkossa')
  # ...
  
print('try ohi')
```

Ylläolevan voi kirjoittaa ilman finally-lohkoa seuraavanlaisena koodisapluunana:

```python
class DelayedReturn(Exception):
  pass

after_finally = { condition: None, value: None }

try:
  try:
    # Alkuperäisen try-lohkon sisältö tähän, mutta
	# jokainen `return x` korvataan seuraavalla:
	# 
    # after_finally = { condition: 'return', value: x }
    # raise DelayedReturn()
	print('try-lohkossa')
	# ...
  except DelayedReturn:
    pass
  except:
    # Alkuperäisen except-lohkon sisältö tähän, mutta
	# `return x` korvataan kuten yllä
	print('except-lohkossa')
	# ...
except DelayedReturn:
  pass
except Exception as exc:
  # Otetaan poikkeus talteen
  after_finally = { condition: 'raise', value: exc }

# Alkuperäisen finally-blockin sisältö tähän
print('finally-lohkossa')
# ...

# Lopuksi tarkistetaan after_finally:stä täytyykö tehdä jokin
# viivästetty toimenpide
if after_finally['condition'] == 'raise':
  raise after_finally['value']
elif after_finally['condition'] == 'return':
  return after_finally['value']

# Jos ei tehty viivästettyä returnia tai raisea, jatketaan
# try-lohkon jälkeisen koodin suoritusta
print('try ohi')
```

Yksinkertaisuuden vuoksi `break`- ja `continue`-tapaukset on jätetty tästä pois.

Tämä sama tapa soveltuu kaikkiin mahdollisiin try-finally-lauseisiin sijoittamalla try-, except- ja finally-lohkojen koodit niille varattuihin kohtiin.

`DelayedReturn` on räätälöity poikkeusluokka, jonka ainoa tarkoitus on siirtää koodin suoritus finally-lohkon alkuun `return`:n kohdalla. Sen heittäminen katkaisee sisemmän try-lohkon suorituksen kuten `return` tekisi. Except-lohkossa `DelayedReturn` käsitellään tekemällä `pass` eli jatkamalla finally-lohkoon. `DelayedReturn` pitää käsitellä molemmissa `except`-lohkoissa, koska se voidaan heittää joko sisemmässä try- tai except-lohkossa. Jos käytetyssä ohjelmointikielessä on `goto`, niin hypyn voi tehdä sen avulla ilman että tarvitsee käyttää erillistä poikkeusluokkaa.

Täydet tyylipisteet saa, jos viivästetyn poikkeuksen saa heitettyä finally-lohkon lopussa niin, että alkuperäinen stack trace säilyy.

Tämä suorittaa samat askeleet kuin alkuperäinen finally-versio. Sen voi nähdä käymällä edeltävän taulukon rivit läpi yksi kerrallaan.

Otetaan esimerkiksi taulukon toisiksi ylimmän rivin tapaus, jossa alkuperäisen koodin try-lohko päättyy `return`:iin ilman poikkeusta. Silloin muunnetussa versiossa asetetaan `after_finally['condition'] = 'return'`, otetaan talteen paluuarvo `after_finally['value']`-muuttujaan ja try-lohko keskeytetään `DelayedReturn`-poikkeuksella. Tämä poikkeus käsitellään sisemmässä `except DelayedReturn: pass` -kohdassa, jolloin sisempi try päättyy. Koska ulommassa try-lohkossa ei tule sen jälkeen muita komentoja, myös ulompi try päättyy. Try-lohkojen jälkeen päädytään alkuperäisen finally-lohkon koodiin ja sen suorittamisen jälkeen vielä `after_finally['condition'] == 'return'` -haaraan, jossa tehdään viivästetty `return after_finally['value']`. Analysoimalla taulukon muut rivit vastaavalla tavalla, todetaan että muunnettu koodi suorittaa aina samat askeleet kuin alkuperäinen finally-versio.
