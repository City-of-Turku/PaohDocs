# Ohjaus kunnan sivuilta

Kunnat voivat omilta verkkosivuiltaan ohjata kuntalaisia palveluohjaaja.fi:hin sijoittamalla haluamallaan tavalla verkkosivuille linkin tai linkkejä palveluohjaaja.fi-sivulle.

Jotta kuntalaisen ohjaus oikeiden palveluiden äärelle olisi sujuvampaa, linkkiin on mahdollista lisätä parametreja: kunta ja aihe. Näin chatbot tarjoaa suoraan halutun kunnan palveluita (ja valtakunnallisia palveluita) ja voi aloittaa keskustelun käyttäjää kiinnostavasta aiheesta yleisten tervehdysten ja ohjeiden sijaan.

Linkkiformaatti on seuraavanlainen:
```
palveluohjaaja.fi/{kieli (fi|en|sv)}?municipality={kunnan nimi}&topic={keskustelun aihe}
```
Mikään mainituista arvoista (kielipolku, kunnan nimi tai aihe) ei ole pakollinen. Aihe- ja kuntaparametrien keskinäisellä järjestyksellä ei myöskään ole väliä.

## Kieli
Osoitteen polussa voidaan ensimmäisenä määritellä kieli: `fi` (suomi), `sv` (ruotsi) tai `en` (englanti).
Tieto ei ole pakollinen vaan sen puuttuessa käyttäjälle tarjotaan oletuksena palvelua suomeksi tai kielistä sillä, jota hän on viimeksi palvelussa käyttänyt. Jos kunta tarjoaa verkkosivunsa usealla kielellä, on kielitiedon kautta kuitenkin mahdollista tarjota käyttäjälle yhtenäinen kokemus, kun sivujen kieli pysyy palveluun ohjattaessa samana.

## Kunnan nimi

Kuntatiedon avulla voidaan ohjata käyttäjä suoraan oman kunnan palveluiden pariin. Kun kuntaparametri on asetettu Palveluohjaaja ehdottaa käyttäjälle palveluita kyseisestä kunnasta - sekä sopivia valtakunnallisia palveluita. Asetettu kuntarajaus näytetään käyttäjälle käyttöliittymässä ja hänen on mahdollisuus poistaa rajaus tai muuttaa sitä. 

Kunnan nimi tulee antaa ilman välejä (esim. Koski Tl on KoskiTl), suomeksi.
Kirjasinkoolla tai ä- ja ö-kirjainten käytöllä ei ole väliä, mutta selkeyden vuoksi alla arvot on kuvattu ilman ääkkösiä ja pienillä kirjaimilla.

| Kunta        | Parametri municipality |
| ------------ | ---------------------- |
| Aura         | aura                   |
| Kaarina      | kaarina                |
| Kemiönsaari  | kemionsaari            |
| Koski Tl     | koskitl                |
| Kustavi      | kustavi                |
| Laitila      | laitila                |
| Lieto        | lieto                  |
| Loimaa       | loimaa                 |
| Marttila     | marttila               |
| Masku        | masku                  |
| Mynämäki     | mynamaki               |
| Naantali     | naantali               |
| Nousiainen   | nousiainen             |
| Oripää       | oripaa                 |
| Paimio       | paimio                 |
| Parainen     | parainen               |
| Pyhäranta    | pyharanta              |
| Pöytyä       | poytya                 |
| Raisio       | raisio                 |
| Rusko        | rusko                  |
| Salo         | salo                   |
| Sauvo        | sauvo                  |
| Somero       | somero                 |
| Taivassalo   | taivassalo             |
| Turku        | turku                  |
| Uusikaupunki | uusikaupunki           |
| Vehmaa       | vehmaa                 |

## Aihe

Aiheparametrilla voidaan kertoa Palveluohjaajalle suoraan mitä käyttäjä on etsimässä, jolloin chatbot aloittaa keskustelun yleisen esittelyn ja kysymysten sijaan halutusta aiheesta. Kukin aiheparametri alla kuvatusta rajatusta joukosta vastaa botin tuntemaa käyttötapausta, kuten kirjastopalveluiden etsiminen.

Lista tulee päivittymään chatbotin kehityksen jatkuessa.

| Keskustelupolku    | Parametri topic  |
| ------------------ | ---------------- |
| Julkinen liikenne  | public_transport |
| Kirjastopalvelut   | library          |
| Kotitalouspalvelut | homeservices     |
| Lastenhoito        | childcare        |
| Muuttaminen        | moving           |
| Työttömyys         | unemployment     |
| Velkaantuminen     | in_debt          |

## Esimerkkejä:

Kirjastopalveluita Turussa
```
palveluohjaaja.fi/sv?municipality=Turku&topic=library
```

Liedon kunnan palveluita (ja valtakunnallisia palveluita).
```
palveluohjaaja.fi/sv?municipality=Lieto&topic
```

Käyttäjä voi halutessaan poistaa kuntarajauksen tai muuttaa sitä keskustelun edetessä. Mikään linkin kautta asetettu tieto ei siis sido käyttäjää koko keskustelun ajan, vaan ohjaa hänet vain määrättyyn alkutilanteeseen.


