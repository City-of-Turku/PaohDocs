# Palvelusuosittelijan tietokanta <!-- omit in toc -->

### Sisällysluettelo

- [MongoDB-tietokanta](#mongodb-tietokanta)
- [Palvelut](#palvelut)
- [Kanavat](#kanavat)
- [Kunnat](#kunnat)
- [Vektorisoidut palvelut](#vektorisoidut-palvelut)
- [Konekäännökset palveluille](#konekäännökset-palveluille)
- [Intent -> palvelu](#intent---palvelu)
  - [Palvelukanavat rajapinnoista:](#palvelukanavat-rajapinnoista)
- [Datan haku Kompassi/YTR:stä](#datan-haku-kompassiytrstä)
  - [Kunnat haetaan rajapinnasta:](#kunnat-haetaan-rajapinnasta)

## MongoDB-tietokanta

Palvelusuosittelija käyttää MongoDB:tä palvelutietokantana. Tämä on NOSQL-dokumenttitietokanta, jossa yhteen elementtiin liittyvä data tallennetaan yhdessä dokumenttina. Tietokanta sallii dokumenteille jossain määrin vaihtelevan muodon.

Tällä hetkellä data tallennetaan Mongoon kahdessa kokoelmassa `services` ja `channels` Palvelut kokoelmassa `services` ovat pääasiallinen rakenne ja kanavat `channel`-kokoelmassa sisältävät lisätietoja palveluista.

MongoDB-datan malli:

## Palvelut

```
{
    "_id": {
        "$oid": "Technical ID"
    },
    "id": "Service id",
    "type": "Service type",
    "subtype": "Service subtype",
    "channelIds": ['Channel IDs that are related to this channel'],
    "name": {"<language>": "Service name"},
    "descriptions": {"<language>": [{"type":"Description type", "value": "Service description", "autoTranslated": "Indicator whether this description has been machine translated"}]},
    "serviceClasses": {"<language>": [{"name":"Service class name", "description": "Service class description", "code": "Service class code"}]},
    "targetGroups": {"<language>": [{"name":"Target group name", "code": "Target group code"}]},
    "areas": {"<language>": [{"name":"Area name", "type": "Area type", "code": "Area code"}]},
    "lifeEvents": {"<language>": [{"name":"Event name", "code": "Life event code"}]},
    "requirement": {"<language>": "Requirement text"]},
    "organizations": [{"id": "Organization ID", "name": "Organization name"}],,
    "nameAutoTranslated": {"<language>": "Indicator whether name of this service has been machine translated, not in the DB, formatted at the service recommender"}
    "lastUpdated": Timestamp of last update time of this service
}
```

## Kanavat

```
{
    "_id": {
        "$oid": "Technical ID"
    },
    "id": "Channel id",
    "type": "Channel type",
    "areaType": "Area type",
    "organizationId": "Channel organization ID",
    "serviceIds": ['Service IDs that refer to this channel'],
    "name": {"<language>": "Service name"},
    "descriptions": {"<language>": [{"type":"Description type", "value": "Channel description"}]},
    "webPages": {"<language>": ["Web page URLs"]},
    "phoneNumbers": {"<language>": [{"number":" Phone number", "prefixNumber": "Number prefix", "chargeDescription": "Description", "serviceChargeType": "Charge type"}]},
    "emails": {"<language>": ["Emails"]},
    "channelUrls": {"<language>": [{"url":"Url", "type": "Type of Url"}]},
    "areas": {"<language>": [{"name":"Area name", "type": "Area type", "code": "Area code"}]},
    "addresses": {"<language>": [{"type":"Address type", "subtype": "Address subtype", "streetNumber": "Street number", "postalCode": "Postal code", "streetName": "Street name", "latitude": "Latitude of address", "longitude": "Longitude of address", "postOffice": "Post office name", "municipalityName": "Name of municipality", "municipalityCode": "Code of municipality"}]},
    "lastUpdated": Timestamp of last update time of this channel
}
```

## Kunnat

```
{
    "id": "Municipality ID",
    "name": {"<language>": "Municipality name"}
}
```

PTV:tä käytetään nyt ensisijaisena datanlähteenä ja muiden lähteiden käyttäminen voi johtaa hieman eri mallisten dokumenttien syntymiseen, mutta pohjamallin pitäisi olla tarpeeksi,jotta palvelusuosittelija voisi tehdä suosituksia.

PTV-data haetaan PTV:n `/Service`- ja `/ServiceChannels`-rajapinnoista. API:n palauttamaa hierarkista rakennetta on yksinkertaistettu hieman matalammaksi ja helpommaksi datan hakemista palvelusuosittelijaan varten sekä eri kielisen datan hakemista varten.

## Vektorisoidut palvelut

Palvelusuosittelija käyttää semanttista vapaiden tekstien kohtaantoa suositusten tekemiseksi. Suuren määrän vektorisointi vie aikaa, joten Mongoa käytetään myös palveluiden vektoreiden tallennuspaikkana. Näitä vektoreita voidaan verrata käyttäjän vapaatekstisyötteeseen suositusten tekemiseksi. Vektorit tallennetaan seuraaviin kokoelmiin:

Palveluvektorit

```
{
    "_id": {
        "$oid": "Technical ID"
    },
    "id": Service id,
    "service_class_codes": ["List of service classes"],
    "municipality_codes": ["List of municipality codes"],
    "life_event_codes": ["List of life event codes"],
    "vector": ["Service vector in list form"]
}
```

Palveluluokkavektorit

```
{
    "_id": {
        "$oid": "Technical ID"
    },
    "name": "Service class name",
    "code": "Service class code",
    "vector": ["Service class vector in list form"]
}
```

## Konekäännökset palveluille

Osalle palveluista on saatavilla vain suomenkieliset nimet ja kuvaukset. Tällaisten palveluiden suositteleminen on joissain tapauksissa englannin- ja ruotsinkielisiin syöteteksteihin perustuvissa tekstisuosituksissa ongelmallista. Sen takia tällaisille palveluille on tehty kantaan konekäännökset Microsoft Translator -palvelulla, joita voidaan käyttää tekstisuosittelua tekevissä komponenteissa.

Käännökset

```
{
    "_id": {
        "$oid": "Technical ID"
    },
    "source_language": "Language that the text is translated from (always fi practically)",
    "target_language": "Language that the text is translated to (sv or en practically)",
    "source_text": "Source text that was translated",
    "target_language": "Target text which is the translation of source text"
}
```

## Intent -> palvelu

Jotkin palvelut voidaan suoraan liittää tiettyihin chatbotin intenteihin. Aina, kun käyttäjällä havaitaan tietty intent, kyseiseen intentiin liitetyt palvelut voidaan hakea tämän kokoelman tiedoista ilman vapaatekstisuositteluaskelta. Intentiin voi myös liittää kuvaavan tekstin ja palveluluokkarajauksen, joita voidaan käytää vapaatekstihakuun intentin yhteydessä. Tämä sallii dynaamisemman PTV-palveluiden suosittelun.

```
{
    "intent": "Intent name",
    "services": ["Service IDs"],
    "ptv_services": ["PTV service IDs"],
    "intent_text": "Free text describing the intent",
    "intent_service_classes": ["List of classes related to the intent"],
    "intent_priorization": "Order by national or local first if limited by service classes but no intent_text to fix the order"
}
```

PTV-datasta tuodut kentät:

```
    id -> id
    type -> type
    subType -> subtype
    serviceChannels -> channelIds
    serviceNames -> name
    serviceDescriptions -> descriptions
    targetGroups -> targetGroups
    areas -> areas
    lifeEvents -> lifeEvents
    requirements -> requirement
    organizations -> organizations

```

Haetut palvelut suodatetaan tämän jälkeen vielä paikan ja kohderyhmän mukaan. Mukaan otetaan vain palvelut, jotka ovat joko koko maan palveluita tai, jotka viittaavat johonkin Varsinais-Suomen kuntaan (areas). Lisäksi kohderyhmillä (targetGroups) suodatetaan niin, että mukaan otetaan palvelut, jotka kuuluvat luokkiin KR1 tai KR1.2, eivätkä mihinkään luokista KR1.1, KR1.3, KR1.4, KR1.5 tai KR1.6.

### Palvelukanavat rajapinnoista:

```
/ServiceChannel
/ServiceChannel/list

```

Ensimmäisestä haetaan palvelukanavien ID:t ja jälkimmäisestä kaikki kanavien tiedot ID:iden perusteella 100:n palvelun ryhminä.

PTV-datasta tuodut kentät:

```
    id -> id
    serviceChannelType -> type
    areaType -> areaType
    services -> serviceIds
    serviceChannelNames -> name
    serviceChannelDescriptions -> descriptions
    webPages -> webPages
    emails -> emails
    supportEmails -> emails
    phoneNumbers -> phoneNumbers
    supportPhones -> phoneNumbers
    addresses -> addresses
    channelUrls -> channelUrls
    areas -> areas
    organizationId -> organizationId

```

Palvelukanavat suodatetaan vielä niin, että mukaan otetaan vain ne, jotka viittaavat joko osoitetiedoissa tai aluetiedoissa (addresses, areas) Varsinais-Suomeen ja jotka viittaavat johonkin edellisessä vaiheessa löydettyyn palveluun.

## Datan haku Kompassi/YTR:stä

Botin käyttämä data on pohjimmiltaan YTR:n palvelutieto. Tähän palvelutietoon on lisätty PTV:stä löytyvää hyödyllistä tietoa niille palveluille, joille voidaan tunnistaa PTV:n ID.

Peruslogiikka toimii siten, että PTV:n hakija toimii erillisenä YTR:n hausta, ja päivittää tietoa PTV:n palvelutietotauluihin. YTR:n haun tapahtuessa PTV:n tietokannasta käydään hakemassa oleellista lisätietoa, jota lisätään YTR:stä saatuun tietoon erityisesti niiden palveluiden kohdalla, joille PTV:stä löytyy tietoa. Tämä yhdistetty tieto tallennetaan tauluihin `ytr_services` ja `ytr_channels`. Käytännössä PTV:stä löytyvät palvelut tallennetaan sellaisinaan PTV-muodossa YTR-puolen kantaan, mutta niihin muutetaan YTR:n ID-tiedot, jotta mahdolliset integraatiot muihin järjestelmiin pysyvät mahdollisina.

### Kunnat haetaan rajapinnasta:

```
/palvelutieto/api/v1/kunta
```

Tämä sisältää YTR:ssä käytössä olevat kunnat (Varsinais-Suomi). Kuntien ID:inä käytetään datassa kuntakoodeja, mutta YTR-dataa haettaessa tarvitaan YTR:n kunta-ID-tietoja kuntariippuvuuksien löytämiseen.

Palvelutarjoukset haetaan rajapinnasta

```
/palvelutieto/api/v1/palvelutarjous
```

Palveluiden tiedot muunnetaan seuraavasti botin tietokantaan.

```
    id -> id
    palvelukanavat -> channelIds
    nimi -> name
    kuvaus -> descriptions
    kohderyhmat -> targetGroups
    kuntasaatavuudet -> areas
    toimija -> organizations

```

Palvelukanavat haetaan rajapinnasta

```
/palvelutieto/api/v1/palvelukanava/{kanavan ID}
```

Palveluiden tiedot muunnetaan seuraavasti botin tietokantaan.

```
    id -> id
    <Palvelun ID> -> serviceIds
    nimi -> name
    kuvaus -> descriptions
    yhteystiedot -> webPages
    yhteystiedot -> phoneNumbers
    osoite -> addresses
    toimija -> organizationId

```

Jos PTV-alkuperäiselle palvelulle löytyy PTV:stä palvelukanavia, joita YTR:stä ei löydy, niin ne lisätään mukaan viittaamaan palveluun, mutta palvelulla ei ole `channelIds`-listassa viitettä kanavaan, mutta toiseen suuntaan kanavasta löytyy `serviceIds`-listasta oikea viite.
