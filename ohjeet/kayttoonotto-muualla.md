# Käyttöönotto muualla

Ohjeet sivulla [Pystytysohjeet](/ohjeet/ohjeet-infran-pystyttamiseen.md) koskevat lähinnä Palveluohjaimen käyttöönottoa Turun käyttämään muotoon ja pystytettynä Azure-ympäristöön.

## Pystytys muun kunnan kuin Turun ympäristöön

Pystytysohje on tehty sillä oletuksella, että käytetään domainia `palveluohjaaja.fi` ja pystytetään botti Turkua varten. Kokonaan erillisen instanssin pystyttäminen esim. kokonaan toisen Varsinais-Suomen kunnan käyttöön on kuitenkin jokseenkin sama prosessi. Keskeisin ero on se että tätä varten täytyy hankkia oma domain `palveluohjaaja.fi`:n tilalle. Korvaa ohjeessa olevat domain-viittaukset omalla domainillasi, jonka kanssa kaiken pitäisi mennä vastaavasti. Domain-viittauksia on ainakin taustajärjestelmässä Botfront-repossa, Chatbot-projektin asetuksissa sekä laajemmin frontendin puolella. Botti ei sovellu Varsinais-Suomen ulkopuolella olevien kuntien käyttöön nykyisellään.

Botin ulkoasu on pääasiassa määritelty WebFrontend-repossa. Pystytettäessä Palveluohjaajaa toisen kunnan käyttöön luultavasti halutaan tuoda esille esim. kyseisen kunnan värimaailma.

Botin oletuskielenä on tällä hetkellä suomi. Jos botti otetaan käyttöön jossain muussa kunnassa tai jotain toista kohderyhmää varten, voi olla mielekästä vaihtaa se ruotsiin tai englantiin.

Botissa ja FrontEndissa on palautelinkki Turun palautelomakkeelle. Siirryttäessä toisiin kuntiin nämä kannattaa korvata omilla.

Teknisissä nimissä on myös mallia `tku-paoh-test-aks` tai vastaavia viitteitä Turkuun. Älä muuta näitä, jotta integraatiot eivät hajoaisi. Tekniset nimet eivät näy ulospäin käyttäjälle.

WebFrontendissä ja sen Bicep-templaatissa on integraatio nykyiseen DevOps-ympäristöön. Toiseen paikkaan pystytettynä tämä pitää muuttaa toiseksi.

Nykyisessä chatbot-projektissa on myös Botfrontin puolella määritelty integraatiorepositorioksi chatbot-projektille nykyinen Turun DevOps-repositorio `Settings` -> `Git credentials` alta. Muualle pystytettäessä tämä täytyy muuttaa toiseksi.

## Pystytys muualle kuin Azureen

Palvelu on kehitetty käyttäen pääasiassa Docker-kontteja, joten sen pystytys toiseen ympäristöön voi olla suhteellisen suoraviivaista, mutta vaatii silti monien työkalujen korvaamista. Suositeltavaa on joka tapauksessa käyttää Kubernetesia konttien ajamiseen. Näiden väliset yhteydet ja kommunikaatio on pitkälti rakennettu niin, että ne ovat samassa klusterissa. Monien sivupalveluiden, kuten kääntäjän, DNS Zonen ym. kanssa joutuu kuitenkin etsimään korvaavat palvelut, mikä ei välttämättä ole helppoa tai edes mahdollista.

Nykyinen ympäristö on myös rakennettu niin, että se vaatii Azure DevOpsin lähdekoodien sijoituspaikaksi, jolloin CI-pipelinet deployaavat muutokset suoraan ympäristöihin. Tämä ei onnistu suoraan samoin muissa ympäristöissä.
