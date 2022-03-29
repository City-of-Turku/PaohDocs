# Julkaisut <!-- omit in toc -->

- [Maaliskuu 28, 2022](#maaliskuu-28-2022)
- [Maaliskuu 15, 2022](#maaliskuu-15-2022)
- [Maaliskuu 1, 2022](#maaliskuu-1-2022)
- [Helmikuu 14, 2022](#helmikuu-14-2022)
- [Tammikuu 31, 2022](#tammikuu-31-2022)
- [Tammikuu 17, 2022](#tammikuu-17-2022)
- [Joulukuu 20, 2021](#joulukuu-20-2021)

Julkaisutietojen pohja:

```
# [Kuukausi] [päivän numero], [vuosi]
---

## Chatbot
- ...

## Palvelusuosittelija
- ...

## Verkkosivu
-
```

# Maaliskuu 28, 2022

---

## Chatbot <!-- omit in toc -->

- Chatbotin kieliasua on hienosäädetty ja kirjoitusvirheitä korjattu
- Päivystys-käyttötapausta on siistitty
- Botin tarjoama palautelinkki päivitetty

## Palvelusuosittelija <!-- omit in toc -->

- Rajapinnasta palautettavaa konekäännösten käytöstä kertovaa tietoa muutettu vastaamaan paremmin web-palvelun tarpeita

## Verkkosivu <!-- omit in toc -->

- Konekäännetyt nimet ja kuvaukset: palveluilta puuttuvia ruotsin ja englanninkielisiä kuvauksia täydennettään suomesta konekäännetyillä nimillä ja kuvauksilla
  - Jos palvelun nimi tai kuvaus on konekäännetty, siitä kerrotaan palvelukortissa tai palvelun sivulla ikonilla (ja sen yhteydessä selventävällä tekstillä)
  - Käännöksestä kertovaa ikonia painamalla käyttäjä voi vaihtaa käännöksen ja alkuperäisen tekstin välillä - eli kytkeä automaattisen käännöksen pois ja takaisin päälle.
- Tietosuojaseloste lisätty sivuille
- Saavutettavuus arvioitu, muun muassa värikontrasteja korjattu ja saavutettavuusseloste lisätty sivulle
- Palautelinkki vaihdettu kehitysvaiheen palautteesta Turun kaupungin palautepalveluun
- Palvelukanavien yhteydessä olevat näytä kartalla -tekstillä varustetut Google Maps -linkit korvattu Open Street Map -linkeillä

# Maaliskuu 15, 2022

---

## Chatbot <!-- omit in toc -->

- Botin lähettämään aloitusviestiin lisätty (uudet) painikkeet, joita käyttäjä voi vapaan kirjoittamisen lisäksi käyttää botin kanssa keskustelemiseen
- Lisätty esiopetuksen käyttötapaus
- Lisätty opetustapaa chatbotin eri käyttötapauksiin ja näin parannettu botin mahdollisuuksia ymmärtää käyttäjää

## Palvelusuosittelija <!-- omit in toc -->

- Tekstihaussa (BM25) laajennettu hallittavia erikoismerkkejä ja parannettu näin luonnollisen kielen ymmärrystä erikoistapauksissa

## Verkkosivu <!-- omit in toc -->

- Etusivun palvelusuosituskorteissa näkyvää palvelukanavien listaa selkeytetty:
  - Kun palvelukanavia on yhdessä palvelussa paljon, kortissa näytetään vain osa kanavista ja loput ovat nähtävillä palvelun sivulla. Näin palvelukorttien selaaminen on nopeampaa.
  - Palvelukanavalistan luettavuutta on parannettu listaamalla palvelukanavat väljemmin
- Yksittäisen palvelun sivulle lisätty tieto palvelun sijainnista ja vastuussa olevasta organisaatiosta. Samat tiedot löytyivät jo aiemmin etusivun palvelusuosituskorteista.
- Yksittäisen palvelun sivulla olevaan palvelukanavalistasta tehty palvelukorteissa olevaa listaa yksityiskohtaisempi:
  - Listassa näytettäviä kanavia on mahdollista rajata palvelukanavan tyypin mukaan (verkkoasiointi, puhelinasiointi jne.)
  - Jokaisella palvelukanavalla on sen tyypin mukainen ensisijainen yhteystieto (linkki, palvelupaikan osoite, puhelinnumero tai lomake) ja lisäksi muita mahdollisesti saatavilla olevia yhteystietoja
  - Palvelukanavalla näytetään myös kuvaus, mikäli sillä on sellainen.

# Maaliskuu 1, 2022

---

## Chatbot <!-- omit in toc -->

- Asumispalveluiden tarkemmat polut
- Vapaaseen tekstihakuun perustuvan suosittelun bugien korjauksia

## Palvelut / palvelusuosittelija <!-- omit in toc -->

- PTV:stä kokonaan poistuneet palvelut poistuvat myös Palveluohjaajasta kuun alussa

## Verkkosivu <!-- omit in toc -->

- Työpöytänäkymässä suosituskortit näkyvät vain näytön oikeassa reunassa eikä niitä näytetä chat-keskustelussa - kuten pienemmillä näytöillä.
- Kehittäjille-sivulle lisätty:
  - Lomake palveluun ohjaavan linkin generointiin
  - Esimerkki komponentista, jollaiseen ohjauslinkki olisi mahdollista lisätä
  - Ohjeet valmiin linkkikomponentin käyttöönottoon
- Bugikorjaus: aiemmin palvelulistaan jäi keskustelun yhteydessä näkyviin automaattisen vierityksen nuoli, vaikka lista tyhjentyi suosituksista
- Keskusteluikkunassa näytetään nyt ilmoitus, jos yhteys chatbottiin on poikki

# Helmikuu 14, 2022

---

## Chatbot <!-- omit in toc -->

- Toimeentulotukipolku kysyy yksittäistä kuntaa, jos valittuna on vain koko maakunta

## Palvelusuosittelija <!-- omit in toc -->

- Perinteisen tekstihaun (BM25) parametrit optimoitu erikseen suomelle, ruotsille ja englannille

## Verkkosivu <!-- omit in toc -->

- Palvelulistassa on nyt painike, jolla käyttäjä voi hypätä suoraan listan loppuun tai alkuun
- Yksittäisen palvelun sivulle on lisätty palaa ylös -painike
- Palvelukortteihin on lisätty palvelusta vastaavan organisaation nimi
- Korjattu: mobiililaitteilla chat-ikkuna ei täyttänyt kaikkea sille varattua tilaa

# Tammikuu 31, 2022

---

## Chatbot <!-- omit in toc -->

- Aloitusviestistä poistettiiin käyttötapauksiin ohjaavat napit, jotta käyttäjä rohkenisi kysyä mitä vain eivätkä napeissa mainitut aiheet rajoittaisi häntä.
- Päivitetty automaattisesti lähetettävässä "palaa alkuun" -viestin yhteydessä näytetyt napit
- Uusi käyttötapaus: lapsen saaminen
- Lisätty Kelan tuet yhteen kokoava keskustelupolku
- Botin lähettämien viestien kieliasua tarkistettu ja puhekielisyyksiä muutettu yleiskielisiksi

## Palvelusuosittelija <!-- omit in toc -->

- Suosittelijan rajapintaan lisätty kieliparametri, jolla määritellään vapaatekstihaussa käytetty kieli
- Perinteinen tekstihaku (BM25) laajennettu toimimaan myös ruotsiksi ja englanniksi. Koska PTV:ssä on palveluitietoja ruotsiksi ja englanniksi varsin rajallisesti, on haussa käytettävää aineistoa laajennettu konekääntämällä suomenkielistä PTV-dataa.

## Verkkosivu <!-- omit in toc -->

- Chatissa eroteltu selkeämmin toisistaan linkit ja botin kanssa kommunikointiin käytettävät painikkeet (linkkeihin lisätty ikoni)
- Chatin tekstilaatikkoa korostettu
- Chatissa näytettävien korttien lue lisää -painikkeiden tyyleistä tehty vähemmän huomiota herättävät
- Saavutettavuusparannus: Verkkosivun pääkielestä poikkeaviin kohtiin on merkitty niissä käytetty kieli (WCAG-standardin pykälää 3.1.2 noudattaen).

# Tammikuu 17, 2022

---

## Chatbot <!-- omit in toc -->

Lisätty seuraavat käyttötapaukset:

- Terveyspalvelut alapolkuineen
- Vaalit ja puolueet
- Kansalaisaloitteet
- Lainat
- Lapsen saaminen

Päivitetty tai muokattu käyttötapauksia:

- Ulosotto omaksi polukseen
- Joukkoliikenne-tapaukseen PTV-palvelut näkyville

## Palvelusuosittelija <!-- omit in toc -->

- Palvelusuosittelijan vapaatekstiin perustuva suosittelutekniikka päivitetty toimimaan paremmin eri tilanteissa (esim. nuohoukseen liittyvät palvelusuositukset)
  - Lisätty perinteinen tekstihakuteknologia, joka täydentää aiemmin jo käytössä ollutta luonnollisen kielen käsittelyyn (NLP) perustuvaa tekoälyteknologiaa
  - Palvelusuosittelijan vapaatekstisuosittelija tukee suosittelua erikseen perinteisellä tekstihakuteknologialla, luonnollisen kielen käsittelyn (NLP) teknologialla tai näiden yhdistelmällä

## Verkkosivu <!-- omit in toc -->

- Kuntarajauksen viestintää selkeytetty ja rajauskomponentti siirretty sivun ylätunnisteesta suositusten yhteyteen - tai mobiililaitteilla keskusteluikkunan yläpuolelle.
- Kuntien ruotsinkielisten nimien käyttö käyttöliittymän ollessa ruotsiksi tai englanniksi (tällöin vain mikäli kunnan valtakieli on ruotsi). Perustuu Kotimaisten kielten keskuksen ohjeistukseen.
- Lisätty kehittäjille suunnattu dokumentaatiosivu palveluun linkittämisestä
- Lisätty tieto viimeisimmästä julkaisusta alatunnisteeseen ja tämä julkaisut esittelevä sivu
- Korjattu: kielivalikon polkujen muodostamisessa ollut virhe, jonka seurauksena käyttäjä ohjattiin virjeelliselle sivulle kielen vaihtamisen yhteydessä.

# Joulukuu 20, 2021

---

## Chatbot <!-- omit in toc -->

- Uusia käyttötapauksia lisätty:
  - eläkkeelle jääminen
  - koulutukseen liittyvät eri kouluasteita koskevat tapaukset ja keskustelupolut
- Pieniä muutoksia avioliitto- ja avioerokäyttötapauksiin
- Tuki frontin lähettämälle kuntatiedolle
- Botin taustajärtelmässä on parannettu kunta- ja kielitietojen käsittelyä
- Bugikorjaus: botin palautekyselyn jälkeinen automaattinen paluu alkuun -viesti tuli väärällä kielellä, jos kieli oli vaihtunut kesken keskustelun

## Palvelusuosittelija <!-- omit in toc -->

- Lisätty hybridisuosittelijat eli mahdollisuus tehdä palvelusuositusten haku samanaikaisesti
  - intentin ja tekstihaun
  - tai intentin ja palveluluokan perusteella
- Palveluiden hakua kannasta yhä hieman suoraviivaistettu ja nopeutettu

\* _Intentti on botin tunnistama käyttäjän tavoite_

## Verkkosivu <!-- omit in toc -->

- Kuntafiltteri (näytetään sillä hetkellä aktiivinen kuntarajaus ja se on mahdollista poistaa)
- Ruotsi kielivaihtoehdoissa
- Vähennetty suosituspaneelin (näkyviä) päivityksiä. Näytetään latausanimaatio vain kun saadaan oikeasti uutta dataa.
- Parannettu url-polkujen ohjausta.
