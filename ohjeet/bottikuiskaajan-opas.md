# Bottikuiskaajan opas <!-- omit in toc -->

- [Intro](#intro)
- [Bottikuiskaajan tehtävät ja prosessi](#bottikuiskaajan-tehtävät-ja-prosessi)
- [Botfrontin valikot ja niiden toiminnallisuudet](#botfrontin-valikot-ja-niiden-toiminnallisuudet)
  - [Dialogue](#dialogue)
  - [NLU](#nlu)
  - [Incoming](#incoming)
  - [Responses](#responses)
  - [Analytics](#analytics)
  - [Settings](#settings)
  - [Documentation](#documentation)
  - [Admin](#admin)
- [Bottikuiskaajan tehtävien toteutus Botfrontissa](#bottikuiskaajan-tehtävien-toteutus-botfrontissa)
  - [Käyttäjien keskustelulokien läpikäynti](#käyttäjien-keskustelulokien-läpikäynti)
    - [Select environment](#select-environment)
    - [Kielivalinta](#kielivalinta)
    - [New Utterances](#new-utterances)
    - [Conversations](#conversations)
  - [Botin vastausviestien päivitys](#botin-vastausviestien-päivitys)
    - [Select environment](#select-environment-1)
    - [Kielivalinta](#kielivalinta-1)
    - [Vastauslistaus](#vastauslistaus)
    - [Add bot response -nappi](#add-bot-response--nappi)
  - [Botin kielenymmärryksen opettaminen](#botin-kielenymmärryksen-opettaminen)
    - [Kielivalinta](#kielivalinta-2)
    - [Training Data](#training-data)
    - [Evaluation](#evaluation)
    - [Statistics](#statistics)
    - [Settings](#settings-1)
    - [Train-nappi](#train-nappi)
  - [Botin käyttötapausten/keskustelupolkujen luominen](#botin-käyttötapaustenkeskustelupolkujen-luominen)
    - [Tarinalistaus](#tarinalistaus)
    - [Tarinaeditori](#tarinaeditori)
    - [Train-nappi](#train-nappi-1)
  - [Botin kehitysversion testaaminen ennen tuotantoonvientiä](#botin-kehitysversion-testaaminen-ennen-tuotantoonvientiä)
  - [Uuden bottiversion tuotantoonvienti](#uuden-bottiversion-tuotantoonvienti)
  - [Botin versionhallinta](#botin-versionhallinta)

# Intro

Palveluohjaimen bottia kehitetään ja ylläpidetään (bottikuiskaaminen) [Botfront](https://botfront.io/docs/rasa/getting-started/)-nimisen työkalun avulla. Työkalun saat auki selaimella osoitteesta https://botfront.palveluohjaaja.fi/

Jos olet Admin-tason käyttäjä, sisäänkirjautumisen jälkeen pääset valitsemaan _Projects_ valikosta projektin, joka tässä tapauksessa on _palveluohjainbotti_. Klikkaa itsesi projektin nimestä sisään projektiin. Jos et ole Admin-tason käyttäjä, sisäänkirjautumisen jälkeen päädyt suoraan _palveluohjainbotti_ projektiin etkä pääse näkemään alla olevassa kuvassa näkyvää _Admin_-valikkoa.

![botfront-admin-valikko.png](/.attachments/botfront-admin-valikko.png)

Botfrontin oma englanninkielinen dokumentaatio esimerkkivideoineen löytyy täältä: [Botfront dokumentaatio](https://botfront.io/docs/rasa/getting-started/). Sen lisäksi tässä wikissä on esitelty tärkeimmät bottikuiskaajan tehtävät ja miten ne tehdään Botfrontissa.

# Bottikuiskaajan tehtävät ja prosessi

Bottikuiskaaminen voidaan jakaa kahteen hieman eri rooliin riippuen kuiskaajan teknisestä taitotasosta bottien kehittämiseen: sisältöjen bottikuiskaaja ja tekninen bottikuiskaaja.

Sisältöjen bottikuiskaajan tehtävät ovat pääasiassa seuraavia:

- Kokeilee itsekin botin toimintaa ja havannoi kehityskohteita
- Käy läpi botin käyttäjien keskustelulokeja ja havannoi niistä kehityskohteita
- Käy läpi muista kanavista tullutta palautetta ja havannoi kehityskohteita
- Raportoi tekniselle bottikuiskaajalle kehityskohteet
- Päivittää botin vastausviestien sisältöjä tarpeen tullen
- Mahdollisesti opettaa itsekin botille jonkin yksinkertaisen uuden käyttötapauksen (esim. usein kysytty kysymys-vastaus)?

Teknisen bottikuiskaajan tehtävät ovat pääasiassa seuraavia:

- Käy läpi botin käyttäjien keskustelulokeja ja havannoi niistä kehityskohteita
- Tarkastelee botin käyttöanalytiikkaa
- Päivittää botin vastausviestien sisältöjä tarpeen tullen
- Opettaa bottia ymmärtämään paremmin käyttäjien viestejä olemassa oleviin käyttötapauksiin liityen
- Opettaa botille uusia käyttötapauksia
- Siirtää uuden bottiversion tuotantoon
- Hallinnoi botin versionhallintaa ja tarvittaessa palauttaa botin aiempaan versioon

Kuiskaajien työmäärä riippuu palveluohjaimen käyttömääristä ja yleisesti käytettävissä olevasta työajasta ja tahtotilasta botin jatkuvaan kehittämiseen. Kuiskaustyötä voi tehdä esim. kerran viikossa, parin viikon välein tai vaikka joka päivä. Oletettavasti täysipäiväistä bottikuiskaustyötä ei tarvita toisin kuin esim. Kelalla, jossa Kelan botilla on huomattavasti isommat käyttövolyymit.

Kuiskaamisen "prosessi" voi olla esim seuraava:

1. Kirjaudu sisään Botfronttiin
2. Tarkastele uudet käyttäjien keskustelulokit
3. Huomaa lokeista kehityskohteita ja kirjaa ne DevOpsin boardin backlogille
4. Teknisenä kuiskaajana kehitä Botfrontissa backlogilta priorisoituja käyttötapauksia

# Botfrontin valikot ja niiden toiminnallisuudet

Alla olevasta kuvasta näkyvästä Botfrontin projektin vasemmasta reunasta "pomppaavassa" valikossa on useampi välilehti, joilta tehdään eri asioita bottikuiskaukseen liittyen.

![botfront-valikot.png](/.attachments/botfront-valikot.png)

## Dialogue

Dialogue -valikosta kehitetään botin käyttötapauksia/keskustelupolkuja. Käyttötapausten/keskustelupolkujen kehittäminen on kuvattu tarkemmin myöhemmässä [Botin käyttötapausten/keskustelupolkujen luominen](/ohjeet/bottikuiskaajan-opas.md#botin-käyttötapaustenkeskustelupolkujen-luominen) -kappaleessa.

## NLU

NLU (Natural Language Understanding, suomeksi "luonnollisen kielen tunnistaminen") -valikosta opetetaan bottia ymmärtämään käyttäjän viestejä eri käyttötapauksiin liittyen. Botin kielenymmärryksen opettaminen on kuvattu tarkemmin myöhemmässä [Botin kielenymmärryksen opettaminen](/ohjeet/bottikuiskaajan-opas.md#botin-kielenymmärryksen-opettaminen) -kappaleessa.

## Incoming

Incoming -valikosta tarkastellaan käyttäjien keskustelulokeja ja botin toimintaa käyttäjien viestien kanssa. Botin keskustelulokien tarkastelu on kuvattu tarkemmin myöhemmässä [Käyttäjien keskustelulokien läpikäynti](/ohjeet/bottikuiskaajan-opas.md#käyttäjien-keskustelulokien-läpikäynti) -kappaleessa.

## Responses

Responses -valikosta hallinnoidaan botin vastauksia. Botin vastausten hallinnointi on kuvattu tarkemmin myöhemmässä [Botin vastausviestien päivitys](/ohjeet/bottikuiskaajan-opas.md#botin-vastausviestien-päivitys) -kappaleessa.

## Analytics

Analytics -valikosta tarkastellaan botin käyttöanalytiikkaa

## Settings

Settings -valikosta hallinnoidaan botin asetuksia (ei pitäisi olla tarvettaa muuttaa mitään ja voi olla mahdollista jopa rikkoa botin toiminta muuttamalla asetuksia väärin)

## Documentation

Documentation -valikosta aukeaa [Botfrontin oma dokumentaatio](https://botfront.io/docs/rasa/getting-started/)

## Admin

Admin -valikosta Admin-tason käyttäjän pääsevät hallinoimaan bottiprojekteja, käyttäjätilejä ja muita asetuksia

# Bottikuiskaajan tehtävien toteutus Botfrontissa

## Käyttäjien keskustelulokien läpikäynti

Keskustelulokien läpikäynti tapahtuu _Incoming_ -valikosta. Alla olevasta kuvasta näkee Incoming-valikon yläpalkista sen päätoiminnallisuudet: _Select environment_, _kielivalinta_, _New Utterances_ ja _Conversations_. Incoming-valikossa on myös _Form results_ ja _Populate_ toiminnallisuudet, mutta niitä ei tarvita.
.![image.png](/.attachments/botfront-keskustelulokien-lapikaynti.png)

### Select environment

_Select environment_ pudotusvalikosta voit valita näytettävän ympäristön (development eli kehitys tai production eli tuotanto). Development-ympäristön ollessa valittuna, näet vain kehitysympäristön keskustelulokeja, joita tulee, kun bottikuiskaaja testaa uutta bottiversiota ennen sen tuotantoon vientiä käyttäen Botfrontin [testikäyttöliittymää](https://botfront.palveluohjaaja.fi/chat/adgucwSrxaCaNgaLL/?env=development&lang=fi). Production-ympäristön ollessa valittuna, näet vain tuotantoympäristön keskustelulokeja, joita tulee oikeilta loppukäyttäjiltä tuotantobotilta [palveluohjaimesta](https://www.palveluohjaaja.fi/).

Yleensä siis valittu ympäristö tulee olemaan _production_, jotta voit tarkastella oikeiden käyttäjien keskustelulokeja.

### Kielivalinta

Kielivalinta pudotusvalikosta voit tarkastella erikseen erikielisten käyttäjien keskustelulokeja, jos käyttäjä on vaihtanut palveluohjaimen kieltä suomesta englantiin tai ruotsiin.

### New Utterances

_New Utterances_ välilehti on valittuna oletuksena, kun saavut _Incoming_ valikkoon. Tästä näkymästä näet käyttäjien botille kirjoittamia viestejä, joita ei ole vielä aiemmin opetettu botille. Näkymästä näet käyttäjän viestin, sen vasemmalla puolella olevan botin tunnistaman intentin violetilla värillä ja sen vieressä myös prosenttilukeman, kuinka varma botti oli intentin tunnistuksesta.

Jos selkeästi botti on tunnistanut jonkin viestin väärään intenttiin, voit intentin nimeä klikkaamalla vaihtaa siihen oikean intentin, klikata vihreää "checkbox" merkkiä oikeasta reunasta ja sen jälkeen klikata vihreää "Add to training data" -nappia lisätäksesi uuden opetuslauseen botin opetusmateriaaliin.

Jos käyttäjän viestille ei löydy sopivaa olemassa olevaa intenttiä, voit myös intentin nimeä klikkaamalla kirjoittaa uuden luotavan intentin nimen, jolloin tämä viesti voidaan lisätä uuden intentin opetusmateriaaliin. Uuden intentin luominen liittyy botin uusien käyttötapausten luomiseen, joten ensiksi toki pitää päättää, että luodaan uusi botin käyttötapaus ennenkuin luodaan sille uusi intentti.

Kaikkia käyttäjien uusia viestejä ei ole pakko lisätä botin opetusaineistoon, vaan ne voi jättää silleen tai poistaa kokonaan New Utterances näkymästä. Botin kielenymmärrykseen usein riittää kymmenkunta erilaista opetuslausetta per intentti, joten hirveä määrä tosi samanlaisia opetuslauseita ei tuo enää parannusta bottiin.

### Conversations

_Conversations_ välilehdeltä voi tarkastella käyttäjien anonymisoituja kokonaisia keskusteluja botin kanssa. Tätä näkymää hyödyntäen pääsee paremmin perille, miten käyttäjän käyttävät bottia ja mitä he keskustelevat botin kanssa, ja onko botti toiminut eri tilanteissa oikein.

## Botin vastausviestien päivitys

Botin vastausviestien sisältöjen päivitus ja uusien vastausten luominen tapahtuu _Responses_ -valikosta. Alla olevasta kuvasta näkee Responses-valikon päätoiminnallisuudet: _Select environment_, _kielivalinta_, _vastauslistaus_ ja _Add bot response -nappi_.

![botfront-vastauksien-paivitys.png](/.attachments/botfront-vastauksien-paivitys.png)

### Select environment

_Select environment_ pudotusvalikosta voit valita näytettävän ympäristön (development eli kehitys tai production eli tuotanto). Development-ympäristön ollessa valittuna, näet vain kehitysympäristön botin vastauksia. Production-ympäristön ollessa valittuna, näet vain tuotantoympäristön botin vastauksia.

**HUOM!** Production-ympäristön ollessa valittuna kaikki muutokset botin vastauksiin vaikuttaa heti välittömästi tuotantoon ja voi mahdollisesti sotkea tuotantobotin toiminnan. Siksi _Responses_ -valikossa tulisi käytännössä aina olla valittuna Development-ympäristö, jonka vastauksiin voi huoletta tehdä muutoksia kehittäessä ja testaillessa uutta bottiversiota ennen sen tuotantoonvientiä. Development-ympäristön vastaukset siirtyvät automaattisesti Production-ympäristöön botin tuotantoonviennin yhteydessä.

Jos kuitenkin tiedät mitä olet tekemässä, Production-ympäristö on valittavissa tästä valikosta ja voit tehdä nopeita korjauksia botin vastauksiin suoraan tuotantoon, mutta tämä ei ole suositeltavaa, vaan kaikki pitäisi mennä aina Development-ympäristön ja sieltä tuotantoonviennin kautta tuotantoon (ja jos teet nopean korjauksen suoraan tuotantoon, etkä ollenkaan developmenttiin, niin seuraavan tuotantoonviennin yhteydessä tuo korjaus peruuntuu, koska botin vastaukset siirtyvät aina development-ympäristön puolelta automaattisesti tuotantoon ylikirjoittaen silloiset tuotannon vastaukset!)

### Kielivalinta

Botille tulee lisätä erilliset vastaukset jokaiselle halutulle tuetulle kielelle, koska botti ei osaa itse kääntää vastauksia kielestä toiseen google translate tyylisesti. Voit tarkastella eri kielien vastauksia valitsemalla halutun kielen kielivalikosta.

### Vastauslistaus

_Responses_ -valikon pääosassa on listaus kaikista botin olemassa olevista vastauksista. Jokaisella vastauksella on uniikki avain (key) ja siihen kuuluva itse vastaus. Botin vastauksia voi hakea kirjoittamalla hakulausekkeen joko avain- tai vastaus-sarakkeen yläreunassa olevaan tekstikenttään. Botin olemassa olevia vastauksia voi muokata klikkaamalla vastausrivin oikeassa reunassa olevaa pientä harmaata kynä-ikonia. Botin vastauksen voi poistaa klikkaamalla pientä harmaata ruksia.

### Add bot response -nappi

Add bot response -napista klikkaamalla voi lisätä täysin uuden vastauksen botille, mitä voi hyödyntää eri keskustelupoluissa käyttäen siellä vastauksen avainta (key). Uusien vastausten lisääminen, mitkä erityisesti liittyvät keskustelupolkuihin, on kuintenkin helpompaa ja intuitiivisempaa Botfrontin _Dialogue_ -valikon kautta keskustelupolkuja muokatessa tai luodessa.

## Botin kielenymmärryksen opettaminen

Botin kielenymmärryksen opettaminen tapahtuu _NLU_ -valikosta. Alla olevasta kuvasta näkee NLU-valikon päätoiminnallisuudet: _Kielivalinta_, _Training Data_, _Evaluation_, _Statistics_, _Settings_ ja _Train-nappi_.

![botfront-kielen-opettaminen.png](/.attachments/botfront-kielen-opettaminen.png)

### Kielivalinta

Botille voi lisätä opetuslauseita erikseen tuetuille kielille parantaakseen botin kielenymmärrystä eri tuetuilla kielillä. Botin kielenymmärryksen teknologian pellin alla on käytössä myös jo valmiiksi monikielistä tekoälyä, mikä mahdollistaa senkin, että bottia opetettaisiin vain yhdellä kielellä (esim. suomi), jonka jälkeen se tunnistaa aika hyvin oikein käyttäjien viestit muillakin kielillä. Toisaalta tätä monikielistä tekoälyn kielenymmärrystä voi vielä parantaa lisäämällä erikseen opetuslauseita eri kielille, joten sekin on suotavaa.

### Training Data

_Training Data_ -välilehti on valittuna oletuksena, kun saavut _NLU_ -valikkoon. _Training Data_ -välilehdeltä voit tarkastella, muokata ja poistaa olemassa olevia botin opetuslauseita _Examples_ -alivälilehdeltä.

Voit myös lisätä uusia opetuslauseita kirjoittamalla niitä yläreunan "User says..." tekstikenttään ja painamalla enteriä. Enterin painamisen jälkeen valitse oikea intentti (tai luo uusi intentti) klikkaamalla violettia intentin nimeä kirjoitetun uuden opetuslauseen vierestä. Lopuksi klikkaa "Save" nappia uuden opetuslauseen kohdalta tallentaaksesi sen botin opetusaineistoon.

Opetuslauseisiin voidaan myös merkata "entiteettejä" esimerkiksi paikkunnan nimen tunnistamista varten eli tässä tapauksessa entiteetti tarkoittaa paikkakunnan nimeä. Entiteettien merkitseminen onnistuu tuplaklikkaamalla opetuslauseen haluttua sanaa tai maalaamalla hiirellä haluttu kohta tekstistä entiteetiksi. Tämän jälkeen ilmestyvään valikkoon tule valita entiteetin nimi (esim. _municipality_) ja jos tekstistä maalattu osuus ei sellaisenaan ole oikea entiteentin lopullinen arvo (esim. maalattu teksti on "turussa", mutta entiteentin arvon tulee olla "Turku"), niin valikon _Value_ -kenttään voi syöttää halutun lopullisen entiteetin arvon alla olevan kuvan mukaisesti.

![botfront-entiteetti.png](/.attachments/botfront-entiteetti.png)

_Synonyms_ -alivälilehdeltä voit tarkastella botin synonyymeja, jotka palveluohjaimen tapauksessa käytännössä liittyvät botin kunnan nimen tunnistamiseen. Halutessassi voit muokata tai lisätä uusia synonyymejä jokaiselle listatulle kunnalle.

_Gazette_ -alivälilehdeltä voit tarkastella botin Gazette-ominaisuutta. Gazette-ominaisuus pyrkii tunnistamaan kunnan nimiä siitä huolimatta, että käyttäjä on tehnyt pienen kirjoitusvirheen kuntaa kirjoittassa (esim. Turkku -> Turku). Jotta Gazette voi toimia hyvin, tulee siinä olla listattuna kaikki kuntien nimet ja mielellään myös niiden synonyymit.

_Training Data_ -välilehdellä on vielä myös _Out of Scope_, _Regex_, _API_ ja _Chit Chat_ -alivälilehdet, mutta niistä ei tarvitse välittää.

### Evaluation

_Evaluation_ -välilehdeltä voi suorittaa botin kielenymmärrykseen erilaisia teknisiä evaluaatioita, joita hyödyntämällä voi esimerkiksi vertailla eri bottiversioiden kielenymmärryksen toimivuutta. Evaluaatioiden toteuttaminen ja sen mittarit vaativat teknistä ymmärrystä ja ovat haastavampia ei-tekniselle kuiskaajalle.

### Statistics

_Statistics_ -välilehdeltä voi tarkastella botin opetusaineiston yleistä statistiikkaa, kuten opetuslauseiden ja intenttien määriä.

### Settings

_Settings_ -välilehdeltä voi tarkastella botin kielenymmärryksen teknistä konfiguraatiota, joka on sama asian kuin Botfrontin pellin alla olevan [Rasa-bottimoottorin konfiguraatio](https://rasa.com/docs/rasa/tuning-your-model/). Konfiguraatiossa voi esimerkiksi määritellä, käytetäänkö kielenymmärryksesä mukana monikielistä tekoälymallia vai ei.

**HUOM!** Bontin kielenymmärryksen teknisen konfiguraation muuttaminen voi rikkoa botin toiminnan ja sen muuttamiselle ei pitäisi olla tarvetta kuin aniharvoin ja silloinkin vain hyvin teknisen kuiskaajan toimesta. Konfiguraatiota muuttaessa tulee tietää, miten Rasa toimii ja miten eri konfiguraatiovaihtoehtoja siihen on olemassa.

### Train-nappi

_Train_ -nappia painamalla voi opettaa uuden kehitysversion botista, jotta voi testata, kuinka esim. botin opetuslauseiden mukauttaminen vaikuttaa botin kielenymmärrykseen. _Train_ -nappia painamalla uusi bottiversio ei vielä mene tuotantoon, joten sitä voi huoletta painaa. Uuden bottiversion opetus voi kestää muutamasta minuutista jopa puoleen tuntiin.

## Botin käyttötapausten/keskustelupolkujen luominen

Botin käyttötapausten luominen tapahtuu _Dialogue_ -valikosta. Alla olevasta kuvasta näkee Dialogue-valikon päätoiminnallisuudet: _tarinalistaus_, _tarinaeditori_ ja _Train-nappi_.

![botfront-keskustelupolkujen-luominen.png](/.attachments/botfront-keskustelupolkujen-luominen.png)

Botfront ja sen pellinalla toimiva Rasa-bottimoottori käyttävät termiä "story" eli tarina yhdestä käyttötapauksesta tai keskustelupolusta.

### Tarinalistaus

_Tarinalistaus_ on Diaglogue-valikon vasemmassa reunassa oleva listaus kaikista bottiin luoduista tarinoista. Tarinoita voidaan myös ryhmitellä yläkategorioiden alle niiden hallinoimisen helpottamiseksi. Esim. ryhmään "KE8 Työttömyys" voidaan lisätä kaikki työttömyyteen liittyvät tarinat. Uusia ryhmiä saa luotua klikkaamalla yläreunan plussa-nappia. Tarinoita voi siirtää drag-and-drop tyyliin ryhmien välillä viemällä hiiren halutun tarinan päälle ja klikkaamalla sen vasempaan reunaan ilmestyvää harmaata "hampurilaisvalikko"-ikonia.

Viemällä hiiren ryhmän nimen kohdalle sen oikeaan reunaan ilmestyy pieni harmaa plussa-ikoni, josta klikkaamalla kyseiseen ryhmään voi lisätä uusia tarinoita. Uuden tarinan (story) lisäksi voit myös luoda uuden "säännön" (rule) tai lomakkeen (form).

Tarinalla (story) voit luoda monimutkaisemman keskustelupolun, jossa botti ja käyttäjä viestittelee useamman kerran toisilleen ja keskustelu voi myös vaikka haarautua useampaan alikeskustelupolkuun.

"Säännöllä" (rule) voit luoda yksinkertaisen kysymys-vastaus tyyppisen keskustelupolun, jossa käyttäjä sanoo jotain ja botti vastaa siihen liittyen takaisin, mutta keskustelupolku ei enää jatku sen pidemmälle.

Lomakkeella (form) voit luoda keskustelupolun osan, jossa käyttäjältä kysytään haluttuja tietoja, jotka tallennetaan botin muistiin ja niitä voidaan hyödyntää muissa keskustelupoluissa. Lomakkeita käytetään tällä hetkellä palveluohjainbotissa vain käyttäjän vapaapalveluhakutarpeen kysymiseen ja paikkakunnan kysymiseen.

### Tarinaeditori

_Tarinaeditori_ on Dialogue-valikon oikeassa reunassa oleva näkymä, josta voi muokata valittuja tarinoita (story), "sääntöjä" (rule) ja lomakkeita (form). Klikkaamalla haluttua tarinaa, sääntöä tai lomaketta vasemman reunan _tarinalistauksesta_, pääset muokkaaman kyseistä keskustelupolkua.

Tarinat ja "säännöt" alkavat käytännössä aina sillä, että käyttäjä sanoo jotain ja botti tunnistaa viestin intentin. Eli intentti käynnistää tarinan tai "säännön". VIemällä hiiren tarinaeditorin vaaleaan alueeseen, siihen ilmestyy alla olevan kuvan mukaisesti _User_, _Bot_, _Action_, _Slot_ ja _Loop_ napit.

![tarinaeditori.png](/.attachments/botfront-tarinaeditori.png)

Klikkaamalla _User_-nappia voit lisätä tarinan alkuun käyttäjän sanoman viestin ja siihen liittyvän intentin. Tämän jälkeen voit esimerkiksi klikata _Bot_-nappia lisätäksesi uuden botin vastauksen, jonka botti sanoo käyttäjän viestin jälkeen. Näin voit alkaa luomaan pidempääkin vuoroittelevaa keskustelupolkua.

_Action_, _Slot_ ja _Loop_ napit liittyvät teknisesti haastavampien käyttötapausten luomiseen, joten niihin ei kannata kiinnittää huomiota, jos et ole tekninen kuiskaaja.

Tarvittaessa keskustelupolun voi myös haarautta useampaan alikeskustelupolkuun klikkaamalla tarinaeditorin vasemassa alareunassa olevaa _Branch Story_ -nappia. Voit haarautta tarinan esimerkiksi niin, että riippuen käyttäjän viestin intentistä keskustelu jatkuu haaraan A tai B.

### Train-nappi

_Train_ -nappia painamalla voi opettaa uuden kehitysversion botista, jotta voi testata, kuinka esim. bottiin lisätty uusi keskustelupolku toimii käytännössä. _Train_ -nappia painamalla uusi bottiversio ei vielä mene tuotantoon, joten sitä voi huoletta painaa. Uuden bottiversion opetus voi kestää muutamasta minuutista jopa puoleen tuntiin.

## Botin kehitysversion testaaminen ennen tuotantoonvientiä

Botin kehitysversion testaaminen ennen tuotantoonvientiä tapahtuu Botfrontin testikäyttöliittymästä, jonka saa auki oikean yläkulman harmaasta "nuoli ->" -napista ja sen _Copy link_ -valinnasta, kun _Sharing enabled_ asetus on päällä. Tämän jälkeen voit liittää kopioidun testilinkin selaimen url-kenttään avataksesi botin kehitysversion testikäyttöliittymän. Vaihtoehtoisesti pääset suoraan testikäyttöliittymään tästä linkistä: https://botfront.palveluohjaaja.fi/chat/adgucwSrxaCaNgaLL/

![botfront-testilinkin-kopiointi.png](/.attachments/botfront-testilinkin-kopiointi.png)

## Uuden bottiversion tuotantoonvienti

Uuden bottiversion tuotantoonvienti tapahtuu _Train_ -napin oikeasta reunasta aukeavasta alasvetovalikon _Deploy to production_ -napista.

![botfront-tuotantoonvienti.png](/.attachments/botfront-tuotantoonvienti.png)

Kun olet kehittänyt uuden bottiversion, testannut sen toimivuuden ja haluat viedä sen tuotantoon, klikkaa _Deploy to production_ -nappia. Napin klikkaamisen jälkeen ensimmäisenä opetetaan uusi bottiversio, viedään sen hetkinen bottiprojekti versionhallintaan, korvataan _Production_ -ympäristön botin vastaukset sen hetkisillä _Development_ -ympäristön botin vastauksilla, ja lopulta tuotanto-Rasa kontti lataa opetetun uuden bottiversion käyttöönsä. Jos tuotantoonvienti on onnistunut, Botfrontin oikeaan yläkulmaan ilmestyy vihreä ilmoituslaatikko, jossa lukee "Successfully deployed to production". Jos tuotantoonviennissä tapahtui ongelmia, Botfrontin oikeaan yläkulmaan ilmestyy punainen ilmoituslaatikko virheilmoituksella.

## Botin versionhallinta

Botin versionhallinta tapahtuu _git_ -napista Botfrontin oikeasta yläkulmasta _Train_ -napin vierestä.

![botfront-versionhallinta.png](/.attachments/botfront-versionhallinta.png)

Botfrontin projektin asetuksissa (Settings -> Git cedentials) voidaan asettaa projektille git-repository ja sen ssh-avain. Tällä hetkellä _palveluohjainbotti_ projektissa asetuksiin on lisätty palveluohjaimen Azure Devopsin `BotfrontChatbotProject` -repository (vastaava Githubissa: [PaohBotfrontChatbotProject](https://github.com/City-of-Turku/PaohBotfrontChatbotProject)).

Uuden bottiversion tuotantoonviennin yhteydessä git-repositoryyn tallentuu automaattisesti sen hetkinen bottiprojekti. Halutessaan git-repositoryyn voi myös Botfrontin _git_ -napin kautta _Commit and push_ -valintaa käyttäen viedä manuaalisesti sen hetkinen bottiprojekti.

Jos bottia kehittäessä jotain menee pahasti pieleen ja halutaan palauttaa aikaisempi toimiva versio botista, niin se onnistuu Botfrontin _git_ -napin kautta _Revert to previous_ -valintaa käyttäen. Tässä tapauksessa näet kaikki aikaisemmat repositorystä löytyvät bottiversiot aikaleimoineen ja voit valita niistä mieleisen palautetavaksi.
