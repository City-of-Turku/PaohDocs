# Arkkitehtuuri

- [Arkkitehtuuri](#arkkitehtuuri)
- [Yleiset käytännöt](#yleiset-käytännöt)
  - [Skaalautuvuus](#skaalautuvuus)
    - [Konttien skaalautuvuus](#konttien-skaalautuvuus)
    - [Tietokannan skaalautuvuus](#tietokannan-skaalautuvuus)
- [Palveluohjausalustan arkkitehtuuri](#palveluohjausalustan-arkkitehtuuri)
  - [Palveluohjausalustan ylätason järjestelmäarkkitehtuuri (C4 Level 1)](#palveluohjausalustan-ylätason-järjestelmäarkkitehtuuri-c4-level-1)
  - [Palveluohjausalustan tarkempi arkkitehtuuri (C4 Level 2)](#palveluohjausalustan-tarkempi-arkkitehtuuri-c4-level-2)
    - [Palveluluohjain webpalvelun tekninen arkkitehtuuri](#palveluluohjain-webpalvelun-tekninen-arkkitehtuuri)
    - [Chatbotin arkkitehtuuri (C4 Level 3)](#chatbotin-arkkitehtuuri-c4-level-3)
    - [Palvelusuosittelijan arkkitehtuuri (C4 Level 3)](#palvelusuosittelijan-arkkitehtuuri-c4-level-3)

# Yleiset käytännöt

Konttiteknologian käytöllä pyritään välttämään toimittajasidonnaisuus. Toiminnan kannalta välttämättömät Azure-sidonnaisuudet on tunnistettu.

## Skaalautuvuus

Odotettavissa olevia käyttäjämääriä ei ole tiedossa, mutta järjestelmä ja sen tekninen alusta voi skaalautua johihinkin tuhansiin samanaikaisiin käyttäjiin.

### Konttien skaalautuvuus

Aluksi ajateltiin käytettävän [Azure Web App for Containers](https://azure.microsoft.com/en-us/services/app-service/containers/) palvelua kaikkien konttien ajamiseen, minkä hyötyjä olisi ollut:

- Managed service (Azure hoitaa konttien ajamisen ja automaattisesti ylläpitää ja päivittää käyttöjärjestelmää yms)
- Alustan suorituskyvyn automaattinen skaalaus ja kuormantasaus (Azure automaattisesti skaalaa palvalua sekä horisontaalisesti että vertikaalisesti)
- CI/CD integraatio Azure DevOps repositorioihin ja Azure Container Registryyn

Kokeilemalla Azure Web App for Containers palvelua huomattiin nopeasti, että sen liittäminen tietoturvalliseen Azuren virtuaaliverkkoon kustannustehokkaasti osoittautui haastavaksi. Tämän takia chatbot ja pavelusuosittelijaan liittyvät kontit ajetaankin [Azure Kubernetes Servicessä](https://azure.microsoft.com/en-us/services/kubernetes-service/), joka tarjoaa vastaavia hyötyjä kuin Web App, mutta huomattavasti helpottaa palvelun ajamista virtuaaliverkossa.

### Tietokannan skaalautuvuus

Järjestelmässä on kaksi tietokantaa: chatbotin tietokanta (Botfront tarvitsee MongoDB-kannan) sekä palvelusuosittelijan tietokanta.

Azuresta tietokantateknologiana käytetään [Azure Cosmos DB](https://azure.microsoft.com/en-us/services/cosmos-db/) managed kantaa, joka tarjoaa MongoDB API:n. Azure Cosmos DB skaalautuu automaattisesti.

# Palveluohjausalustan arkkitehtuuri

HUOM: [Azure- ja infra-arkkitehtuuri](./azure-arkkitehtuuri.md) -sivulla on kuvattu tarkemmin Azuressa käytettäviä resursseja ja siltää osittain päällekkäisiä asioita alla olevien arkkitehtuurikuvien kanssa.

Arkkitehtuuri kuvattu [C4-mallia](https://c4model.com/) mukaillen. Turhan kompleksisuuden ja lisätasojen välttämiseksi tällä hetkellä Palveluohjausalustan tarkemman arkkitehtuurikuvan (C4 Level 2) kolmea Containeria "Palveluohjain Webpalvelu", "Chatbot" ja "Palvelusuosittelija" ei ole kuvattu C4:n Software Systeminä, vaikka olisi voinut kuvata, vaan suoraan C4:n Containerina.
Huom: C4:n "Container" ei tarkoita Docker konttia :)

## Palveluohjausalustan ylätason järjestelmäarkkitehtuuri (C4 Level 1)

Draw.io XML tiedosto: [arkkitehtuuri-level1-system.drawio.xml](/.attachments/arkkitehtuuri-level1-system.drawio.xml)

![arkkitehtuuri-level1-system.drawio.png](/.attachments/arkkitehtuuri-level1-system.drawio.png)

## Palveluohjausalustan tarkempi arkkitehtuuri (C4 Level 2)

Draw.io XML tiedosto: [arkkitehtuuri-level2-container.drawio.xml](/.attachments/arkkitehtuuri-level2-container.drawio.xml)

![arkkitehtuuri-level2-container.drawio.png](/.attachments/arkkitehtuuri-level2-container.drawio.png)

### Palveluluohjain webpalvelun tekninen arkkitehtuuri

Draw.io XML tiedosto: [arkkitehtuuri-webpalvelu.drawio.xml](/.attachments/arkkitehtuuri-webpalvelu.drawio.xml)

![arkkitehtuuri-webpalvelu.drawio.png](/.attachments/arkkitehtuuri-webpalvelu.drawio.png)

### Chatbotin arkkitehtuuri (C4 Level 3)

C4 mukainen hahmotelma chatbotin arkkitehtuurista.

Draw.io XML tiedosto: [arkkitehtuuri-chatbot-level3-komponentti.drawio.xml](/.attachments/arkkitehtuuri-chatbot-level3-komponentti.drawio.xml)

![arkkitehtuuri-chatbot-level3-komponentti.drawio.png](/.attachments/arkkitehtuuri-chatbot-level3-komponentti.drawio.png)

### Palvelusuosittelijan arkkitehtuuri (C4 Level 3)

Draw.io XML tiedosto: [arkkitehtuuri-level3-komponentti.drawio.xml](/.attachments/arkkitehtuuri-level3-komponentti.drawio.xml)

![arkkitehtuuri-level3-komponentti.drawio.png](/.attachments/arkkitehtuuri-level3-komponentti.drawio.png)
