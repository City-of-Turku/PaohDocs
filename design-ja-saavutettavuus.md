# Design ja saavutettavuus <!-- omit in toc -->

### Sisällysluettelo <!-- omit in toc -->

- [Palvelun ulkoasu](#palvelun-ulkoasu)
- [Palveluohjaimen logot ja muut graafiset resurssit](#palveluohjaimen-logot-ja-muut-graafiset-resurssit)
- [Saavutettavuus](#saavutettavuus)
  - [Tekstikoko](#tekstikoko)
  - [Värit](#värit)
  - [Ruudunlukijoiden tuki](#ruudunlukijoiden-tuki)

## Palvelun ulkoasu

Palveluohjaimen käyttöliittymä pohjautuu [Palvelupolut-projektissa tuotettuun komponenttikirjastoon](https://palvelupolut.finland.wdr.io/). Kirjastoa ei ole suoraan voitu hyödyntää, sillä se on suunniteltu Drupal-kielelle, mutta sen pohjalta on samoja komponentteja on toteutettu Palveluohjainta varten Reactilla.

Palvelupolut projekti: https://storybook.palvelupolut.fi
Kompoenttikirjasto: https://storybook.palvelupolut.fi

## Palveluohjaimen logot ja muut graafiset resurssit

Palvelun logo - tai lähinnä selaimessa näytettäväksi "faviconiksi" suunniteltu symboli:

![logo192.png](/.attachments/logo192.png)

Palvelun logo eri kokoisina tiedostoina:

- [logo64.png](/.attachments/logo64.png)
- [logo128.png](/.attachments/logo128.png)
- [logo192.png](/.attachments/logo192.png)
- [logo512.png](/.attachments/logo512.png)
- Ico-tiedostona: [favicon.ico](/.attachments/favicon.ico)

Logo suorilla kulmilla (muissa kulmia on hiukan pyöristetty)

- [logo512square.png](/.attachments/logo512square.png)
- [logo256square.png](/.attachments/logo256square.png)

Kuvat, jotka näytetään, kun käyttäjä jakaa palvelun linkin sosiaaliseen mediaan (Twitter, Facebook, Discord jne.)

- [palveluohjain-preview.png](/.attachments/palveluohjain-preview.png)
- [palveluohjain-preview-square.png](/.attachments/palveluohjain-preview-square.png)

Alkuperäinen Adobe Illustrator -tiedosto, joka sisältää logon ja sosiaalisen median jakokuvat:

- [palveluohjain-icons-and-previews.ai.zip](/.attachments/palveluohjain-icons-and-previews.ai.zip)

Logot, ikonit ja sosiaalisen median jakoja varten toteutetut kuvat löytyvät myös web-palvelun koodirepositoriosta, (`WebFrontend/frontend/public/`).

## Saavutettavuus

Palveluohjaimen koko kehitystyön ajan on pyritty ottamaan huomioon palvelun saavutettavuus.

### Tekstikoko

- Verkkosivun tekstien - ja muidenkin elementtien koko - on toteuttu niin, että ne mukautuvat jos käyttäjä vaihtaa tekstikokoa selaimen asetuksista tai käyttää selaimensa suurennustoimintoa

### Värit

- Kontrastivaatimusten täyttyminen on taskitettu [a11y:n kontrastityökalulla](https://storybook.palvelupolut.fi)
- Kohdissa, joissa kontrasti voisi olla puutteellinen elementteihin on lisätty tummempi rajaus (esimerkiksi chat-ikkunan painikkeet)
- Palvelu käyttää [Palvelupolut-komponenttikirjastossa käytettyä väripalettia](https://storybook.palvelupolut.fi).

### Ruudunlukijoiden tuki

- Verkkopalvelussa saatetaan täyttää puuttuvaa sisältöä sivun pääkielestä poikkeavilla sisällöillä. Ruudunlukijan käytön helpottamiseksi tällaisiin elementteihin on merkitty poikeavan sisällön kieli.
