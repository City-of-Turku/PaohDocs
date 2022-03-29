# Palvelusuosittelu ja suosittelijan API <!-- omit in toc -->

### Sisällysluettelo

- [NLP-malli](#nlp-malli)
- [BM25-malli](#bm25-malli)
- [Yhdistetty malli](#yhdistetty-malli)
- [API](#api)
- [Datan haku PTV:stä](#datan-haku-ptvstä)
  - [Kunnat haetaan rajapinnasta:](#kunnat-haetaan-rajapinnasta)
  - [Palvelut rajapinnoista:](#palvelut-rajapinnoista)

## NLP-malli

Tämä on ns. monikielinen semanttinen kielimalli, joka on käytännössä suurella tekstiaineistolla opetettu neuroverkkomalli. Malli kykenee poimimaan tekstistä yksittäisten sanojen tai rakenteiden täsmäävyyden sijaan tekstin merkityksen.

Malli muuntaa sille syötetyn tekstin korkeauloitteiseksi vektoriksi, joka kuvaa syötetyn tekstin ominaisuuksia. Samalle mallille voidaan syöttää sekä käyttäjältä tuleva syöteteksti että palvelua kuvaava teksti, ja näitä kahta vektoria voidaan verrata keskenään.

Suosittelija luo tällä tavalla scoret kunkin palvelun yhtenevyyteen sille annettuun tekstiin. Suosittelijan antamat suurimman scoren suositukset ovat tämän mallin antamia palvelusuosituksia.

## BM25-malli

Tämä on ns. perinteinen tekstin osien yhtenevyyteen perustuva tapa mallintaa tekstien samankaltaisuutta.

Semanttisen yhtenevyyden sijaan tämä tarkastelee tekstiä vain yksittäisinä sanoina ja ngrammeina, eli tietyn pituisina sanan osina.

Laskemalla tällaisten osien esiintymistä molemmissa verrattavissa teksteissä sekä näiden osien yleisyyttä yleisesti voidaan löytää palveluiden joukosta parhaiten annettua tekstiä vastaavat palvelut.

## Yhdistetty malli

Suosittelijassa on käytössä kahdesta edellisestä mudostuva painotettu yhdistetty malli. Tällä on kyky löytää sekä tekstistä semanttinen tarkoitus että painottaa teksteissä sisältyvää mahdollista täsmällistä yhtenevyyttä

## API

Palvelusuosittelijan API tarjoaa rajapintoja suositusten antamiseksi palveluille tai palveluluokille pohjautuen vapaatekstisyötteeseen tai keskustelun tietoihin, jotka haetaan botin tietokannasta keskustelun ID:n perusteella. Suosituksia voi suodattaa erilaisten parametrien avulla. Parametrien arvot voi antaa itse suositteijalle tai suosittelija voi hakea näitä tietoja keskustelun ID:n perusteella.

Syöte `/servicesFiltered` -rajapinnalle on:

```
{
    "include_channels": "True/False, to return channels or not",
    "municipalities": ["List of municipalities to filter with"],
    "service_classes": ["List of service classes to filter with"],
    "life_events": ["List of life events to filter with"],
    "limit_k": "Limit maximum number of services to return",
    "priorization": "local/national, to priorize services of municipality vs. services of the whole country",
    "translated_missing_text": "true/false, to translate possible missing names and descriptions"
}
```

Tämä toimii vain yksinkertaisena palveluiden hakemiseen soveltuvana rajapintana. Palveluja voi suodattaa kunnan, palveluluokan ja elämäntilanteen mukaan.

Syöte `/servicesFiltered` -rajapinnalle on:

```
{
    "include_channels": "True/False, to return channels or not",
    "municipalities": ["List of municipalities to filter with"],
    "service_classes": ["List of service classes to filter with"],
    "life_events": ["List of life events to filter with"],
    "limit_k": "Limit maximum number of services to return",
    "priorization": "local/national, to priorize services of municipality vs. services of the whole country",
    "translated_missing_text": "true/false, to translate possible missing names and descriptions"
}
```

Syöte `/services/recommendByIntent` -rajapinnalle on:

```
{
    "intent": "Name of the intent",
    "municipalities": ["List of municipalities to filter with"],
    "service_classes": ["List of service classes to filter with"],
    "life_events": ["List of life events to filter with"],
    "translated_missing_text": "true/false, to translate possible missing names and descriptions"
}
```

Tämä rajapinta paluttaa intentille tarkalleen määritellyt palvelut mahdollisesti suodatettuna esim. palveluluokkien ja kuntien mukaan.

Syöte `/services/recommendByIntentAndOptions` -rajapinnalle on:

```
{
    "intent": "Name of the intent",
    "municipalities": ["List of municipalities to filter with"],
    "service_classes": ["List of service classes to filter with"],
    "life_events": ["List of life events to filter with"],
    "limit_k": Number of recommendations,
    "need_text": "Optionally overwrite need text in database with this"
    "text_recommender": "nlp/lexical/all, NLP model, BM25 model or combination of the 2",
    "language": "Language of text input",
    "priorization": "local/national, to priorize services of municipality vs. services of the whole country"
    "translated_missing_text": "true/false, to translate possible missing names and descriptions"
}
```

Tämä rajapinta paluttaa intentille tarkalleen määritellyt palvelut sekä halutun määrän lisäpalveluita palveluluokkien tai hakutekstin perusteella haettuna mahdollisesti suodatettuna esim. palveluluokkien ja kuntien mukaan. Intentille on ensisijaisesti määriteltynä hakuehdot tietokannassa, mutta ne voi ylikirjoittaa kutsussa.

Syöte `/services/recommendByConversation/{conversation_id}` -rajapinnalle on:

```
{
    "mode": "Recommendation mode, acceptable values search, intent, conversation and infer. ",
    "municipalities": ["List of municipalities to filter with"],
    "service_classes": ["List of service classes to filter with"],
    "life_events": ["List of life events to filter with"],
    "top_k": Number of recommendations,
    "text_recommender": "nlp/lexical/all, NLP model, BM25 model or combination of the 2",
    "language": "Language of text input",
    "translated_missing_text": "true/false, to translate possible missing names and descriptions"
}
```

Tämä rajapinta saa tekstisyötteensä aina keskustelun tiedoista keskustelun ID:n {conversation_id} perusteella. Suodattimia voi käyttää samoin kuin edellisessä, mutta arvon puuttuessa kyseistä suodatusta yritetään tehdä taustalla keskustelun tietojen avulla, jos arvo annetaan, niin käytetään annettua arvoa ja keskustelun tiedoissa oleva sivuutetaan.

Kenttä "mode" määrää strategian, jolla suosittelu toimii:

- search käyttää keskustelun tiedoissa olevaa search_text -tyyppistä edellisen bottiformin nimen kanssa yhteensopivaa kenttää vapaatekstisyötteenä, jonka perusteella suosittelu tehdään
- intent käyttää ainoastaan keskustelun viimeisimpiä intentejä suositusten tekemiseen käyttäen intentiin manuaalisesti kytkettyjä palveluita suosituksina tai, jos näitä ei löydy, yrittää käyttää intentin nimestä pääteltyä palveluluokkaa ja näyttää tätä tietoa palveluiden hakemiseen yksinkertaisen suodattimen kautta
- conversation käyttää kaikkea keskustelun historian aikana käyttäjältä tullutta tekstisyötettä vapaatekstisuositteluun (erotuksena search:sta, joka on pistemäisempi vain yhtä slotin arvoa ja käyttäjän viestiä käyttäjä metodi)
- infer pyrkii itse päättelemään, että onko keskustelu sellaisessa tilanteessa, että suositellaanko search- vai intent -strategiab mukaisesti. Jos kumpikaan ei ole mahdollinen, niin ei suositella mitään. conversation-metodi on laajuutensa ja poikkeavuutensa takia aina käyttäjän itsensä valittava, jos sitä käytetään.

Syöte `/serviceClasses/recommend` -rajapinnalle on:

```
{
    "need_text": "Free text to vectorize. Either this or query_vector is mandatory.",
    "top_k": Number of recommendations
}
```

Syöte `/serviceClasses/recommendByConversation/{conversation_id}` -rajapinnalle on:

```
{
    "top_k": Number of recommendations
}
```

Ajantasainen dokumentaatio rajapinnasta on saatavilla osoitteessa`https://<Tuleva palvelin>/docs`.

## Datan haku PTV:stä

Yllä kuvattu data haetaan PTV:n API:sta `https://api.palvelutietovaranto.suomi.fi/api/v11` useasta eri rajapinnasta:

### Kunnat haetaan rajapinnasta:

```
/CodeList/GetMunicipalityCodes
```

### Palvelut rajapinnoista:

```
/Service
/Service/serviceWithGD/list

```

Ensimmäisestä haetaan palveluiden ID:t ja jälkimmäisestä kaikki palveluiden tiedot ID:iden perusteella 100:n palvelun ryhminä.
