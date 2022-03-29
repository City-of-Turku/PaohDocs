# Tietoturva ja tietosuoja <!-- omit in toc -->

### Sisällysluettelo <!-- omit in toc -->

- [Käyttöympäristö ja verkko](#käyttöympäristö-ja-verkko)
- [NGINX palomuuri](#nginx-palomuuri)
- [Sertifikaattien ja salattavien tietojen hallinta](#sertifikaattien-ja-salattavien-tietojen-hallinta)
- [Tietokannat](#tietokannat)
  - [Palvelusuosittelijan tietokanta](#palvelusuosittelijan-tietokanta)
  - [Chatbotin tietokanta](#chatbotin-tietokanta)
- [Chatbotin kehittäminen ja ylläpito keskustelutiedon avulla](#chatbotin-kehittäminen-ja-ylläpito-keskustelutiedon-avulla)
- [Henkilötieto](#henkilötieto)
- [Linkkejä](#linkkejä)

## Käyttöympäristö ja verkko

Järjestelmä on toteutettu Microsoft Azureen ja käyttää Azuren virtuaaliverkkoja. Tietokantoihin ja palveluihin pääsyä rajoitetaan siten, että niihin pääsee vain tarkkaan määritetyistä osoiteista ja virtuaaliverkon sisältä. Chatbotin loppukäyttäjien käyttöliittymä (Azure Web app) ja bottikuiskaajien Botfront käyttöliittymä ovat ainoat liittymät julkiseen internetiin ja nekin suojataan solvetuvin osin palomuuripalvelulla. Yhteystapana on SSL-suojattu HTTPS. Myös yhteydet muihin järjestelmiin (PTV, KomPAssi-YTR) ovat SSL-salattuja.

Azure-ympäristöstä on luotu ExpressRoute-yhteys Turun konesaliverkkoon. Tästä privaattiverkosta on IP-yhteys testiympäristössä sijaitsevaan ulkomaailmalta suljettuun palvelusuosittelijan tietokantaan.

## NGINX palomuuri

Azure Kubernetes Servicen HTTPS-rajapinnan Nginx Ingress -määrityksessä on mukana ModSecurity-palomuurin käyttöönotto Owasp Core Rules -ominaisuuksien kanssa. Useita sääntöjä, jotka estävät Botfrontin tai palvelusuosittelijan käytön, on jätetty pois ottamalla joitain sääntöjä pois niiden ID:iden perusteella.

## Sertifikaattien ja salattavien tietojen hallinta

Järjestelmässä on useita verkko-osoitteita, kuten tietokantoja, virtuaalikoneita, palveluita ja sovelluskontainereita. Käyttäjätason tunnistautumista (tunnus/salasana/sertifikaatti) käytetään jos se on mahdollista. Salassapidettäviä tietoja, kuten SSL-sertifikaatteja, säilytetään ja hallitaan Azuren Key vaultissa, johon määritellyille sovelluksille annetaan lukuoikeudet. Näitä tietoja käytetään mm. järjestelmän komponenttien asentamisen (deployment pipeline) ja versiopäivitysten yhteydessä. Kehittäjät voivat käyttää ns. dummy-versioita ja todellisia salaustietoja käytetään vain Azuressa.

## Tietokannat

Järjestelmä sisältää kaksi tietokantaa: palvelutietoa sisältävänä palvelusuosittelijan tietokannan sekä chatbotin oppimistietoa ja käyttäjien kanssa käytyjä keskusteluja sisältävän chatbot-tietokannan.

Pääsyoikeus tietokantoihin on rajattu verkkotasolla (tietokannat vain Azuren privaattiverkossa) ja pääsy vaatii autentikoinnin. Pääsyoikeustietoja säilytetään Azure Key Vaultissa. Azure Cosmos DB-tietokanta on oletusarvoisesti salattu. Tietojen salaamaton tallennus ei ole teknisesti mahdollista. ([Linkki tietokantapalvelun dokumentaatioon englanniksi.](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest)).

### Palvelusuosittelijan tietokanta

Palvelusuosittelija hakee dataa PTV:stä. Tuleva tietovarasto on Turun parhaillaan kehittämä PLM. Palvelusuosittelija sisältää ainoastaan julkista organisaatiodataa eikä siten tule muodostamaan henkilörekisteriä.

### Chatbotin tietokanta

Chatbotin tietokantaan talletetaan käyttäjien ja botin välisiä keskusteluja. Tietokantaan ei talleteta mitään henkilötietoa, kuten IP-osoitteita, puhelinnumeroita, sähköpostiosoitteita tai henkilötunnuksia. Keskustelujen tunniste luodaan satunnaisesti eikä siitä siten voida päätellä keskustelun sisältöä tai ajankohtaa, eikä itse keskustelijaa. Palvelun verkkosivulla ohjeistetaan käyttäjää olla syöttämättä henkilötietoja keskusteluihin. Chatbot sisältää tästä huolimatta anonymisointitoiminnon, joka tunnistaa henkilötiedon ja estää sen tallettumisen keskustelulogeihin ja tietokantaan.

## Chatbotin kehittäminen ja ylläpito keskustelutiedon avulla

Keskustelubotin toimintaperiaatteena on keskustelulogeihin perustuva oppiminen. Käytännössä tämä tapahtuu botin opetusrajapintaa käyttäen, jossa keskustelulogeihin perustuen pyritään ymmärtämään käyttäjien tarpeita ja tarkoitusperiä. Opetusrajapinta on [Botfront-työkalu](https://botfront.io/), joka lukee chatbot-tietokannasta keskustelulokeja (kts. tietokantakuvaus yllä). Pääsy Botfront-käyttöliittymään rajataan roolitetuilla käyttäjätunnuksilla ([dokumentaatio](https://botfront.io/docs/administration/users-and-roles/)). Pääsyä voidaan rajata myös fyysisesti tiettyihin verkkoihin tai IP-osoitteisiin.

## Henkilötieto

Järjestelmässä ei säilytetä henkilötietoa. Käyttäjät ohjeistetaan olla syöttämättä henkilötietoa keskustelulogeihin.
Keskustelutietoja käytetään botin kehitystyöhön ja niitä säilytetään kehitystarpeiden edellyttämän ajan.

## Linkkejä

- Azure Web App tietoturvadokumentaatio: https://docs.microsoft.com/en-us/azure/app-service/security-recommendations
- Azure Kubernetes Service tietoturvadokumentaatio: https://docs.microsoft.com/en-us/security/benchmark/azure/baselines/aks-security-baseline?context=/azure/aks/context/aks-context
- Azure Cosmos DB tietoturvadokumentaatio: https://docs.microsoft.com/en-us/azure/cosmos-db/database-security

Kansallisia sovelluskehityksen tietoturvaohjeita:

- https://www.suomidigi.fi/ohjeet-ja-tuki/tyokalut/turvallisen-sovelluskehityksen-kasikirja
- https://www.suomidigi.fi/ohjeet-ja-tuki/vahti-ohjeet/vahti-12013-sovelluskehityksen-tietoturvaohje

Avoimia tietoturvamalleja:

- OWASP SAMM: https://owasp.org/www-project-samm/
- OWASP ASVS: https://owasp.org/www-project-application-security-verification-standard/
