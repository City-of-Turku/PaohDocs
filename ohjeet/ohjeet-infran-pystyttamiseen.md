# Ohjeet Palveluohjaimen ja teknisen infran pystyttämiseen <!-- omit in toc -->

- [1. Azure infran ja resurssien pystytys](#1-azure-infran-ja-resurssien-pystytys)
  - [Resources_intra](#resources_intra)
  - [Resources](#resources)
  - [Resources_test ja Resources_prod](#resources_test-ja-resources_prod)
  - [Templaattien käyttö](#templaattien-käyttö)
  - [Pystytysjärjestys:](#pystytysjärjestys)
    - [Translator](#translator)
    - [Virtuaaliverkko](#virtuaaliverkko)
    - [Cosmos-tietokanta](#cosmos-tietokanta)
    - [Tietokannan yhteys virtuaaliverkkoon](#tietokannan-yhteys-virtuaaliverkkoon)
    - [Container Registry](#container-registry)
    - [Strorage Account](#strorage-account)
    - [Static Web App](#static-web-app)
    - [AKS](#aks)
    - [DNS Zone](#dns-zone)
- [2. Azure Kubernetes Servicen konfigurointi](#2-azure-kubernetes-servicen-konfigurointi)
  - [KEDA](#keda)
  - [AKS Static Public IP, HTTPS ja Nginx Ingress -asennusohjeet](#aks-static-public-ip-https-ja-nginx-ingress--asennusohjeet)
  - [NGINX palomuuri](#nginx-palomuuri)
  - [AKS ja Azure Fileshare volumet](#aks-ja-azure-fileshare-volumet)
    - [Rasojen File Share volume mounttaukset](#rasojen-file-share-volume-mounttaukset)
    - [NLP-vektorisoijan tarvitsema File Share volume -mounttaus](#nlp-vektorisoijan-tarvitsema-file-share-volume--mounttaus)
- [3. Repojen pipelinejen deployaukset Azureen](#3-repojen-pipelinejen-deployaukset-azureen)
  - [Muuttujat](#muuttujat)
- [4. Palveluohjaaja-botin luonti Botfrontissa](#4-palveluohjaaja-botin-luonti-botfrontissa)
  - [1. Uuden projektin luominen](#1-uuden-projektin-luominen)
  - [2. Projektin yhdistäminen Rasaan](#2-projektin-yhdistäminen-rasaan)
  - [3. Palveluohjaajabotti-projektin importtaus](#3-palveluohjaajabotti-projektin-importtaus)
  - [4. Projektin asetusten määritys](#4-projektin-asetusten-määritys)
- [5. Tietokantataulut](#5-tietokantataulut)
    - [intent_to_services](#intent_to_services)
    - [translations](#translations)
- [6. Hälytykset](#6-hälytykset)
- [Ylläpito](#ylläpito)

Tällä sivulla kuvataan tarvittavat vaiheet Palveluohjaimen teknisen infran pystyttämiseen tyhjästä Azureen.

Itse arkkitehtuuri on rakennettu avoimen lähdekoodin kontitetuin ratkaisuin, joten tarvittaesssa järjestelmän voi pystyttää toiseenkiin pilveen, kuten AWS, tai konesaliin, mutta tämä ohje keskittyy Azureen.

# 1. Azure infran ja resurssien pystytys

Palveluohjaimen yleinen arkkitehtuuri on kuvattu wikissä sivulla [Arkkitehtuuri](/arkkitehtuuri.md) ja tarkempi Azure arkkitehtuuri sivulla [Azure ja infra arkkitehtuuri](/azure-arkkitehtuuri.md).

Helpoiten Azure infran ja tarvittavien resurssien pystytys onnistuu hyvin pitkälti automatisoidusti käyttäen Azuren Bicep-pohjaisia ns. infra-as-code (IaC) templaatteja. Templaatit ovat saatavilla [AzureInfra -reposta](https://github.com/City-of-Turku/PaohAzureInfra).

Bicep-templaatit ovat pohjia, joissa määritellään Azure-resurssit, joita botin infra tarvitsee. Templaattien tarkoitus on tehdä ympäristön pystytys helpoksi, jotta sitä ei tarvitsisi pystyttää manuaalisesti hankalien yksityiskohtaisten ohjeiden ja asetusten kanssa. Ympäristö pystytytetään käyttämällä templaatteja määritellyssä järjestyksessä, jolloin ympäristö syntyy pohjaresursseista alkaen, joiden päälle rakentuvat näistä pohjaresursseista riippuvat ylemmän tason resurssit.

Resurssit on jaoteltu useaan eri kategoriaan:

## Resources_intra

Tämä sisältää yleiset resurssit, jotka ovat yhteisiä eri ympäristöjen yli. Jos haluaa nostaa Azureen sekä testi- että tuotantoympäristöt, niin näillä luotavia resursseja luodaan vain yksi kutakin kokonaan näille tarkoitettuun omaan resurssiryhmään. Älä luo omaa resurssia kullekin ympäristölle tästä ryhmästä. Tämä vaatii pohjaksi yhteisille resursseille luodun resurssiryhmän.

## Resources

Täällä olevat resurssit ovat muodoltaan samanlaisia riippumatta ympäristöstä, mutta toisin kuin intra-ryhmässä olevia, näistä pitää luoda oma resurssi kutakin ympäristöä kohden. Nämä vaativat pohjaksi kyseiselle ympäristölle luodun resurssiryhmän.

## Resources_test ja Resources_prod

Täällä olevat resurssit luodaan vastaavasti kuin edellisiä yksi jokaista ympäristöä kohden. Erona on se, että näiden resurssien muoto riippuu pystytettävän ympäristön tyypistä, ja siksi testille ja tuotannolle on omat hieman eroavat templaattinsa. Käytä testiympäristöön Resources_test-templaatteja ja tuotantoympäristöön Resources_prod-templaatteja. Luo kutakin resurssia vain yksi ympäristöä kohden. Vaatii pohjalle saman ympäristökohtaisen resurssiryhmän, joka luotiin edellisessä.

## Templaattien käyttö

Templaatit ovat helposti luettavissa olevassa `.bicep`-muodossa. Käyttöä varten ne on muutettava `.json`-muotoon ARM-templaateiksi `bicep-cli` -työkalulla. (https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/bicep-cli).
Näillä muunnetuilla templaateilla resursseja voi nostaa olemassaolevaan resurssiryhmään. Esim. Azuren portaalista Deploy Custom Template -käyttöliittymän kautta näillä syntyneillä ARM-templaateilla voi nostaa Azuren resursseja (https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/quickstart-create-templates-use-the-portal).

Templaatteja käytettäessä pitää varmistaa jokaista templaattia deployatessa, että niissä olevat muuttujat `subscriptionId` (Azure-tilauksesi ID), `resourceGroupName` (ympäristökohtaisen resurssiryhmän nimi), `intraResourceGroupName` (yhteisen resurssiryhmän nimi) ja `environment` (ypäristölle antamasi nimi) on asetettu oikein. Ympäristön nimen on oltava mielellään lyhyt 1-6 pientä kirjainta, esim `test` tai `tuot`. Näin siksi, koska Azure ei salli pitkiä nimiä kaikkien resurssien kohdalla. Muut muuttujat ovat johdannaisia, älä aseta näitä itse.

## Pystytysjärjestys:

Alussa on oltava olemassa yhteisten resurssien resurssiryhmä sekä yksi resurssiryhmä jokaista ympäristöä kohden. Jos pystytetään sekä testi että tuotanto, niin oma ryhmä kummallekin, yhteensä 3.

### Translator

Lisää intra-resurssiryhmään Translator-resurssi `Translator.bicep`-templaatilla.

### Virtuaaliverkko

Lisää ympäristökohtaiseen resurssiryhmään virtuaaliverkko `VN.bicep`-templaatilla.

### Cosmos-tietokanta

Lisää ympäristökohtaiseen resurssiryhmään Cosmos-tietokantapalvelin `DB.bicep`-templaatilla.

### Tietokannan yhteys virtuaaliverkkoon

Lisää ympäristökohtaiseen resurssiryhmään Cosmos-tietokantapalvelimelle Private endpoint virtuaaliverkkoon `DBPEP.bicep`-templaatilla. Tämä templaatti vaatii, että edelliset virtuaaliverkko ja tietokanta ovat jo olemassa ryhmässä. Tämän jälkeen virtuaaliverkon resurssit saavat yhteyden tietokantaan.

### Container Registry

Lisää ympäristökohtaiseen resurssiryhmään Azure Container Registry `ContainerRegistry.bicep`-templaatilla. Tätä käytetään ympäristöön vietävien Docker-konttien rekisterinä.

### Strorage Account

Lisää ympäristökohtaiseen resurssiryhmään Storage Account `StorageAccount_test.bicep`- tai `StorageAccount_prod.bicep`-templaatilla riippuen ympäristöstä.

### Static Web App

Lisää ympäristökohtaiseen resurssiryhmään Static Web App `StaticWebApp_test.bicep`- tai `StaticWebApp_prod.bicep`-templaatilla riippuen ympäristöstä.

### AKS

Lisää ympäristökohtaiseen resurssiryhmään Azure Kubernetes Services `AKS_test.bicep`- tai `AKS_prod.bicep`-templaatilla riippuen ympäristöstä. AKS on ympäristön ydin ja pitää sisällään suurimman osan toiminnallisuuksista.

### DNS Zone

DNS Zonea asetuksineen ei voi sujuvasti konfiguroida suoraan templatella vaan se täytyy luoda manuaalisesti. Luo DNS Zone -resurssi intra-resurssiryhmään käyttämällesi domainille. DNS Zone antaa listan DNS-palvelimia, jotka täytyy liittää rekisteröityyn domain-nimeesi. Nyt voit säätää domainisi asetuksia suoraan Azuresta.

DNS Zonessa voit asettaa Static Web App:lle määrittelemäsi domainin (ohje: https://docs.microsoft.com/en-us/azure/static-web-apps/custom-domain). Vastaavasti voit asettaa domainin AKS:n julkiselle IP-osoitteelle (ohje: https://docs.microsoft.com/en-us/azure/dns/tutorial-alias-pip). Julkinen IP on jo olemassa, mutta ei ole käytössä ennen, kun teet seuraavat asennukset.

# 2. Azure Kubernetes Servicen konfigurointi

Bicep-templaattien pitäisi luoda AKS hyvin pitkälle halutuilla konfiguraatioilla, mutta muutamia asioita joudutaan tekemään käsin.

Voit ensiksi varmistaa, että luodussa tuotannon AKS:ssa on yksi nodepool nimeltään esim. `agentpool` ja siinä vähintään 2kpl nodea, joiden `Node size` on `Standard_E2as_v4`. Palveluohjaimen tuotantokonfiguraatio tarvitsee AKS:ssa toimiakseen mielellään vähintään yhteensä 32gb RAM-muistia, jonka takia käytämme Azuren muistioptimoitua `Standard_E2as_v4` nodea, jossa yhdessä on 16gb RAM-muistia. Testiympäristössä AKS:ssä on käytössä vain yksi Rasa-palvelu verrattuna kahteen Rasaan tuotannossa, joten testiympäristön AKS:ssa riittää juuri ja juuri yksi node 16gb RAM muistilla.

AKS:n Kubernetes-klusterin versio tulee päivittää mm. tietoturvan takia muutaman kuukauden välein uusimpaan stable-versioon. Sitä varten kannattaa asettaa Azuren Kubernetes-klusterin automaattinen päivitys päälle seuraamalla AKS:n dokumentaatiota: https://docs.microsoft.com/en-us/azure/aks/upgrade-cluster#set-auto-upgrade-channel

Näin päivitys tapahtuu itsestään automaattisesti. On myös järkevää asettaa käyttöön "Planned maintenance, jolloin päivitys voidaan asettaa tapahtumaan vain tiettyinä ajanjaksoina, kuten lauantaisin aamuyöstä. Planned maintenance -ohjeet löytyvät myös dokumentaatiosta: https://docs.microsoft.com/en-us/azure/aks/upgrade-cluster#using-cluster-auto-upgrade-with-planned-maintenance

## KEDA

Osa AKS:ssä ajettavista palveluista on muodoltaan ns. funktioita. Tällaisia ei ole voi ajaa sellaisenaan Kubernetesissa, vaan niitä varten täytyy asentaa klusteriin palvelu nimeltä KEDA. Sen voi asentaa esim [Helm](https://helm.sh/) avulla (ohje: https://docs.microsoft.com/en-us/azure/azure-functions/functions-kubernetes-keda).

## AKS Static Public IP, HTTPS ja Nginx Ingress -asennusohjeet

Pääosin seuraa Azuren dokumentaatiota: https://docs.microsoft.com/en-us/azure/aks/ingress-static-ip

Huomioita ja poikkeuksia dokumentaatioon:

1. Jotta AKS saa yhdistettyä itsensä virtuaaliverkosta erilliseen Azuren Static Public IP -resurssiin, virtuaaliverkko tarvitsee `Network Contributor` oikeudet AKS:lle. Lisää oikeus virtuaaliverkon resurssin, esim. `tku-paoh-tuot-virtualnetwork`, IAM valikon kautta:

- Klikkaa `Add role assignment` nappia
- Hae rooleista `Network Contributor` ja valitse se
- Members-välilehdellä kohtaan `Assign access to ` valitse `Managed identity` ja `Members` kohdassa klikkaa `Select members`. Aukeavaan valikkoon `Managed identity` kohtaan valitse `Kubernetes Service` ja valitse haluttu AKS resurssi, esim. `tku-paoh-tuot-aks`
- Lopulta klikkaa `Review + assign` lisätäksesi oikeuden (tämä operaatio voi vaatia sinulta Admin oikeudet Azuressa)

2. Alun IaC-vaiheessa pitäisi olla luotu jo valmiiksi Azureen Static Public IP -resurssi kutakin ympäristöä kohden. Varmista, että Azure Static public IP -resurssi on luotuna haluttuun resource grouppiin, esim. `tku-palveluohjain-tuotanto`.
3. Lisää Static Public IP -resurssille `DNS name label` resurssin `Configuration` asetuksista. Label voi olla esim. `palveluohjain-tuot-aks`, jolloin IP:n koko DNS name on `palveluohjain-tuot-aks.westeurope.cloudapp.azure.com`
4. Asenna `nginx-ingress` palvelu [Helm](https://helm.sh/) -työkalulla. Asennusta varten aseta Static Public IP -resurssin IP-osoite ja DNS **label** name (eli `palveluohjain-tuot-aks` eikä koko DNS namea) alla olevaan Helm install komentoon parametreihin `loadBalancerIP` ja `azure-dns-label-name`. Tämän lisäksi alla olevan Helm install -komennon pitää sisältää seuraava parametri tai muuten asennus ei toimi: ` --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-resource-group"="tku-palveluohjain-tuotanto"` (koska luotu Static Public IP -resurssi on eri resource groupissa kuin AKS:n "sisäinen resource group", komennolle pitää kertoa mistä resource groupista Static Public IP löytyy.
   <br>Täten koko ajettava Helm install komento on seuraava:

```
helm install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-basic --set controller.service.externalTrafficPolicy=Local --set controller.replicaCount=1 --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux --set controller.service.loadBalancerIP="40.91.209.43" --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="palveluohjain-tuot-aks" --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-resource-group"="tku-palveluohjain-tuotanto"
```

Tarvittaessa `nginx-ingress` palvelun version voi päivittää seuraavalla Helm-komennolla:

```
helm upgrade --install nginx-ingress ingress-nginx/ingress-nginx --namespace ingress-basic --set controller.service.externalTrafficPolicy=Local --set controller.replicaCount=1 --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux --set controller.service.loadBalancerIP="40.91.209.43" --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="palveluohjain-tuot-aks" --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-resource-group"="tku-palveluohjain-tuotanto"
```

1. Dokumentaatiota seuraamalla AKS-asennukset tehdään `ingress-basic` namespaceen AKS:ään. Se on ok, mutta [Create ingress route](https://docs.microsoft.com/en-us/azure/aks/ingress-static-ip#create-an-ingress-route) -kohdassa luotava Ingress route pitää asentaa AKS:n `default` namespaceen, mistä löytyy routessa käytettävät muut AKS servicet (botfront, rasa, service-match-engine, yms). Palveluohjaimessa käytettävä Ingress route -konfiguraatio löytyy [AzureInfra -repon Nginx kansiosta](https://github.com/City-of-Turku/PaohAzureInfra?path=/Nginx). Huomaa tarkistaa, että konfiguraatiossa käytössä olevat AKS:n julkisen IP:n domainnimi vastaa omaasi, oletuksena `palveluohjaaja.fi`. Tämä konfiguraatio tulee siis asentaa `default` namespaceen esim. seuraavalla komennolla:
   `kubectl apply -f aks-ingress-routes-split-prod.yaml --namespace default`

2. Tämän jälkeen tulee klusteriin asentaa vielä `cert-manager` Azuren [dokumentaation](https://docs.microsoft.com/en-us/azure/aks/ingress-static-ip?tabs=azure-cli#install-cert-manager) mukaisesti, jotta AKS:n julkinen IP saa automaattisesti ylläpidetyn HTTPS-sertifikaatin. Klusteriin lisätään ClusterIssuer, johon on viitattu myos Ingressin route konfiguraatiossa. Tämä luo sertifikaatin, jota Ingress käyttää. Cert-manager ylläpitää ja uusii automaattisesti sertifikaattia. Huomaa, että Azuren ohjeissa luodaan ClusterIssuer demomielessä Let's Encrypt:n staging-ympäristössä, mutta Palveluohjaimelle se tulee luoda oikeasti toimivaan Let's Encrypt tuotantoympäristöön. Korvaa siis dokumentaatiossa olevat `letsencrypt-staging` arvot `letsencrypt-prod` arvolla ja `https://acme-staging-v02.api.letsencrypt.org/directory` osoitteet `https://acme-v02.api.letsencrypt.org/directory` osoitteella.

AKS:n Static Public IP ja Nginx Ingress -asennusten jälkeen AKS:n julki-internettiin auki oleviin palveluihin, kuten Botfront, pitäisi päästä Static Public IP -resurssin osoitteesta, esim. `palveluohjain-tuot-aks.westeurope.cloudapp.azure.com` käsin. Jos tässä kohtaa lisäksi määritelet DNS Zoneen oman domainin viittaamaan tähän Public IP:hen, niin myös se on käytössä.

## NGINX palomuuri

Nginx Ingress -määrityksessä on mukana ModSecurity-palomuurin käyttöönotto Owasp Core Rules -ominaisuuksien kanssa. Useita sääntöjä, jotka estävät Botfrontin tai palvelusuosittelijan käytön, on jätetty pois ottamalla joitain sääntöjä pois niiden ID:iden perusteella.

## AKS ja Azure Fileshare volumet

AKS:ään deployattavat palvelut eivät oletuksena sisällä persistoitua datan tallennusta vaan palveluiden (konttien) käynnistyessä uudestaan niiden "tila" nollaantuu. Esim. Rasan opettama ja käyttämä botin "tekoälymalli" täytyy persistoida talteen, jotta sitä ei tarvitse joka Rasan uudelleenkäynnistyksen yhteydessä opettaa uudestaan. Tämä onnistuu mounttaamalla Azure Storage Account -resurssin `File share` hakemistoja AKS:n palveluihin. AKS:n [dokumentaatiossa](https://docs.microsoft.com/en-us/azure/aks/concepts-storage) on myös lisätietoa eri vaihtoehdoista persistoiduille hakemistoille.

File Sharet syntyvät automaattisesti, kun luot Storage Acoountin, mutta sen lisäksi ne pitää mountata AKS:ään. Yritimme saada Azuren File Sharejen volumen mounttauksia toimimaan automaattisesti osana repojen pipelinejen deployauksia, mutta siinä oli ongelmia. Tämän takia tarvittavat File Share volume -mounttaukset tulee tehdä yhden kerran manuaalisesti ennen repojen pipelinejen deployauksia.

Dokumentaation [Azure- ja infra-arkkitehtuuri](/azure-arkkitehtuuri.md) -kuvasta nähdään, että Palveluohjain tarvitsee tuotannossa kolme erillistä File Sharea, jotka mountataan volumeina AKS:n `rasa`, `rasa-dev` ja `nlp-vectorizer `palveluihin. Varmista että alun IaC-vaiheessa on luotu automaattisesti Azureen Storage Account -resurssi ja sinne kolme File Sharea nimeltään `fileshare-botfront-models`, `fileshare-botfront-models-dev` ja `fileshare-nlp-models`.

Huom: testiympäristössä on käyttössä vain yksi Rasa `rasa-dev`, jolloin testiympäristöön tarvitaan vain `fileshare-botfront-models-dev` ja `fileshare-nlp-models` File Sharet.

### Rasojen File Share volume mounttaukset

Rasojen tarvitsemien File Share volume -mounttauksien kubernetes konfiguraatiot löytyvät [RasaPlatfrom repon kube -hakemistosta](https://github.com/City-of-Turku/PaohRasaPlatform/?path=/kube). Huom: konfiguraatioissa `azure-fileshare-volume.yaml` ja `azure-fileshare-volume-dev.yaml` on muuttujat `shareName: __azurefilesharebotfrontmodels__` ja `shareName: __azurefilesharebotfrontmodelsdev__`, joihin olisi tarkoitus automaattisesti osana repojen pipelinejen deployauksia tulla Key Vaultista oikeat File Sharejen nimet, mutta koska emme voineet käyttää pipelineä näiden konfiguraatioiden ajamiseen, tulee nuo muuttujat muuttaa käsin oikeiksi ennen kyseisten Kubernetes-konfiguraatioiden ajamista. Muuta ne siis muotoon `shareName: fileshare-botfront-models` ja `shareName: fileshare-botfront-models-dev` vastaamaan luotujen Azuren File Sharejen nimiä.

Luo tuotantoympäristön AKS:ään File Share volume mounttaukset seuraavilla komennoilla hyödyntäen repon konfiguraatioita:

```
kubectl apply -f azure-fileshare-volume.yaml
kubectl apply -f models-persistentvolumeclaim.yaml

kubectl apply -f azure-fileshare-volume-dev.yaml
kubectl apply -f models-persistentvolumeclaim-dev.yaml
```

Huom: testiympäristössä on käyttössä vain yksi Rasa `rasa-dev`, jolloin testiympäristön AKS:ään ajetaan vain komennot:

```
kubectl apply -f azure-fileshare-volume-dev.yaml
kubectl apply -f models-persistentvolumeclaim-dev.yaml
```

Voit varmistaa, että Rasojen tarvitsemat File Share volumet on lisätty onnistuneesti AKS:ään esim. tarkistamalla Azuren AKS resurssin "Storage" valikosta, että luodut `fileshare-botfront-models` ja `fileshare-botfront-models-dev` löytyvät sekä "Persistent volume claims" että "Persistent volumes" välilehdiltä.

### NLP-vektorisoijan tarvitsema File Share volume -mounttaus

NLP-vektorisoijan tarvitsema File Share volume -mounttauksen Kubernetes-konfiguraatiot löytyvät [ServiceMatchEngine repon kube -hakemistosta](https://github.com/City-of-Turku/PaohServiceMatchEngine?path=/kube). Huom: konfiguraatioissa `skip-nlp-models-azure-fileshare-volume.yaml` on muuttuja `shareName: __azurefilesharenlpmodels__`, johon olisi tarkoitus automaattisesti osana repojen pipelinejen deployauksia tulla Key Vaultista oikea File Share -nimi, mutta koska emme voineet käyttää pipelineä näiden konfiguraatioiden ajamiseen, tulee tuo muuttuja muuttaa käsin oikeaksi ennen kyseisen Kubernetes-konfiguraation ajamista. Muuta se siis muotoon `shareName: fileshare-nlp-models` tai vastaamaan luomasi Azuren File Sharen nimeä.

Luo AKS:ään File Share volume mounttaus seuraavilla komennoilla hyödyntäen repon konfiguraatioita:

```
kubectl apply -f skip-nlp-models-azure-fileshare-volume.yaml
kubectl apply -f skip-nlp-models-persistentvolumeclaim.yaml
```

Voit varmistaa, että NLP-vektorisoijan tarvitsema File Share volume on lisätty onnistuneesti AKS:ään esim. tarkistamalla Azuren AKS-resurssin "Storage"-valikosta, että luotu `fileshare-nlp-models` löytyy sekä "Persistent volume claims" että "Persistent volumes" välilehdiltä.

# 3. Repojen pipelinejen deployaukset Azureen

Kaikissa repoissa, joista on tarkoitus tehdä deployauksia Azureen, löytyy repon juuresta `azure-pipelines.yml` pipelinekonfiguraatio sekä `README.md` tiedostossa ohjeistusta. Azure DevOpsin **Pipelines**-valikosta voidaan luoda pipelinet valitsemalla haluttu repo ja sen sisältä löytyvä `azure-pipelines.yml`-konfiguraatio.

Jotta pipelinet toimivat, DevOps vaatii service connectionien luomisen Azureen. Se onnistuu DevOpsin `Project Settings -> Service Connections` valikosta. Palveluohjaimen tapauksessa käytössä on `Azure Resource Manager`, `Kubernetes` ja `Docker Registry ` -tyyppisiä service connectioneja kaikkiin käytössä oleviin Azuren resource grouppeihin, joihin halutaan ajaa pipeline deployauksia. Service Connectionien luominen vaatii todennäköisesti Azuren/Devopsin Admin tason käyttäjän, jolla on oikeuksia konfiguroida Azuren AD sekä myös DevOpsia.

Repojen `azure-pipelines.yml` konfiguraatiot hyödyntävät DevOpsin Pipelinen `Variable Groups` -ominaisuutta, jossa voidaan määritellä pipelineille yhteisiä muuttujia ja niiden arvoja. Esim. tuotannon pipelinet hyödyntävät Variable Grouppia nimeltä `tku-paoh-tuot-variable-group`. Variable Groupit ovat myös yhdistetty Azuren Key Vaulttiin, josta voidaan lukea esim. autentikaatiotokeneita. Täten suurin osa pipelinejen käyttämistä muuttujista tulee Azuren Key Vaultista ja ne on asetettu siellä.

## Muuttujat

- azuresubscription - Tilauksen ID (ei selväkielinen merkkijono)
- resourcegroup - Ympäristön resurssiryhmän nimi
- armconnection - DevOpsissa luodun Azure Resource Manager Service Connectionin nimi, esim muotoa `tku-palveluohjain-{environment}-sc`
- environment - Edellä mainittu ympäristölle annettu nimi
- aksclustername - Luodun klusterin nimi, muotoa `tku-paoh-{environment}-aks`, jossa environment on alun templaateissa annettu ympäristön nimi
- envprefix - Ympäristön nimen etuliite, muotoa `tku-paoh-{environment}`, jossa environment on alun templaateissa annettu ympäristön nimi
- aksingressrooturl - AKS:n ingressiin määritelty DNS-nimen pohja-URL, esim `https://palveluohjain-test-aks.westeurope.cloudapp.azure.com`
- botfrontrooturl - AKS:n ingressin oman domainin DNS-nimen pohja-URL, esim `https://testbotfront.palveluohjaaja.fi
- azurestorageaccountname - Alussa luodun Storage Accountin nimi, muotoa `tkupaoh{environment}storage`. Azure vaatii yhteenkirjoitetun merkkijonon tässä.
- azurestorageaccountkey - Luodun Storage Accountin avaintunnus, tämä pitää hakea Storage Accountin tiedoista Access Keys -osiosta.
- dockerregistryserverurl - Azure Container Registryn osoite, muotoa `https://tkupaoh{environment}containerregistry.azurecr.io`.
- dockerregistryserverusername - käyttäjänimi muotoa `tkupaoh{environment}containerregistry`
- dockerregistryserverpassword - Luodun rekisterin avaintunnus, tämä pitää hakea ACR:n tiedoista Access Keys -osiosta.
- dockerregistryname - rekisterin nimi muotoa `tkupaoh{environment}containerregistry`
- staticwebappdeploymenttoken - Ympäristön Static Web Appin avaintunnus, tämä pitää hakea Web App:n tiedoista Overview -> Manage deployment token -osiosta.
- translatorsubscriptionkey - Translator-resurssin avain, tämä pitää hakea Translatorin:n tiedoista Keys and Endpoint -osiosta.
- botfrontauthtoken - Järjestelmän sisäinen tunnus, jolla jotkin operaatiot rajataan vain järjestelmän sisäisiksi toiminnoiksi
- rasaauthtoken - Järjestelmän sisäinen tunnus, jolla jotkin operaatiot rajataan vain järjestelmän sisäisiksi toiminnoiksi
- mongohost - Cosmos-tietokannan osoite virtuaaliverkossa. löytyy alussa luodun private endpointin tiedoista
- mongousername - Mongon käyttäjänimi Cosmos-yhteyteen, muotoa `tku-paoh-{environment}-cosmosdb`
- mongopassword - Mongon salasana Cosmos-yhteyteen, haettava Cosmosin Connection String -osiosta
- mongoport - Mongon portti Cosmos-yhteyteen, oletettavasti 10255
- servicedbmongodb - Tietokanta, johon palvelusuosittelija kirjautuu, oletettavasti service_db
- bfmongodb - Tietokanta, johon Botfront kirjautuu, oletettavasti bf

Ensimmäistä kertaa deployatessa pipelinejä, ne voisi ajaa seuraavassa järjestyksessä:

1. [ServiceDataImport](https://github.com/City-of-Turku/PaohServiceDataImport)
2. [ServiceMatchEngine](https://github.com/City-of-Turku/PaohServiceMatchEngine)
3. [Botfront](https://github.com/City-of-Turku/PaohBotfront)
4. [RasaForBotfront](https://github.com/City-of-Turku/PaohRasaForBotfront)
5. [RasaPlatform](https://github.com/City-of-Turku/PaohRasaPlatform)
6. [WebFrontend](https://github.com/City-of-Turku/PaohWebFrontend)

WebFrontEndin pipelinen ajon saattaa joutua tkeemään 2 kertaa, että kaikki osat asentuvat oikein.

# 4. Palveluohjaaja-botin luonti Botfrontissa

## 1. Uuden projektin luominen

Avatessa Botfront ensimmäistä kertaa selaimessa, se pytää luomaan ensimmäisen Admin käyttäjätunnuksen Botfronttiin. Tämän tunnuksen luonnin jälkeen Botfrontin Admin valikosta on mahdollista lisää uusia käyttäjiä eri tasoisilla oikeuksilla.

Botfront luo oletuksena valmiiksi yhden "ChitChat"-projektin. Tämän projektin voi poistaa turhana projektina klikkaamalla Botfrontin Admin -> Projects -valikon "Edit" sarakkeen editointi-ikonia ChitChat-projektin kohdalta.

Tämän jälkeen luo uusi projekti oikean yläkulman "Add project" -napin kautta. Projektin nimi voi olla esim. "Palveluohjaajabotti". Projektin luonnissa Botfront haluaa myös "Namespace" tiedon projektille, jota ei oikeasti käytetä meillä mihinkään, joten siihen voi kirjoittaa esim "bf-palveluohjaajabotti".

## 2. Projektin yhdistäminen Rasaan

Mene luotuun projektiin klikkaamalla Admin -> Projects valikosta projektin nimeä. Näytön vasemmasta reunasta aukeavasta valikosta avaa Settings ja avautuvasta Settings valikosta laita päälle "Deployment environments" raksittamalla sen "production" checkbox ja tallenna muutokset "Save changes" napista.

Settings valikossa mene "Instances" välilehdelle ja aseta "Host" kenttään Azure AKS:n (Kubernetes Service) sisäinen osoite ja portti, josta löytyy Rasa-palvelu (meidän tuotantoympäristössä Rasoja on 2kpl, joten tähän valitaan Rasoista "rasa-dev"). Meidän tapauksessa osoite on `http://rasa-dev.default.svc.cluster.local:5005`.

Jos olet Rasan AKS deploymentissa asettanut Rasalle sen rajapintaan autentikaatio-tokenin (meillä se on asetettu oletuksena päälle [RasaPlatform](https://github.com/City-of-Turku/PaohRasaPlatform) repon `RasaPlatform\kube\rasa-dev-deployment.yaml` konfiguraatiossa olevassa Rasa kontin käynnistyksen argumenteissa, niin että autentikaatio-token tulee deploymentin yhteydessä Azure Key Vaultista konfiguraatiossa olevan `__rasaauthtoken__` placeholder-muuttujan tilalle), niin aseta Botfrontin Settings valikon "Instances" välilehden "Token" kenttään oikea autentikaatio-token.

AKS:n Rasa-palvelut tarvitsevat oikean Botfront projektin ID:n ympäristömuuttujinaan, jotta palvelut voivat käynnistyä oikein. Tässä vaiheessa on hyvä mennä Botfrontin Admin-valikon Projects-näkymään ja kopioida luodun esim. "Palveluohjaajabotti" projektin ID. Mene [RasaPlatform](https://github.com/City-of-Turku/PaohRasaPlatform) repon `RasaPlatform\azure-pipelines.yml` pipeline konfiguraatioon ja aseta kopioitu ID konfiguraation alussa määritettävään `botfrontprojectid` muuttujaan. Tämän jälkeen aja RasaPlatfrom-repon deployment pipeline uudestaan, jotta lopulta AKS:n Rasa-palvelut käynnistyvät uudelleen oikealla Botfrontin projekti ID:llä.

Botfrontin ja Rasan yhteys toimii oikein, jos Botfrontin esim. NLU -valikossa oikean yläkulman "Train" -nappi on tumman sininen.

## 3. Palveluohjaajabotti-projektin importtaus

Settings valikossa mene "Import/Export" välilehdelle. [BotfrontChatbotProject](https://github.com/City-of-Turku/PaohBotfrontChatbotProject) repossa on Botfrontilla luodun Palveluohjaajabotin varmuuskopio. Luo repon sisällöstä yksi zip-tiedosto. Botfrontin "Import/Export" valikosta laita päälle asetukset "Delete existing data" ja nyt ensimmäisellä kerralla myös asetus "Import project specific configs". Lisää luotu zip-tiedosto import valikossa Botfronttiin. Jos kaikki menee hyvin, valikko näyttää kaikkien importtavien zip-tiedoston sisällä olleiden tiedostojen olevan "vihreitä" tai mahdollisesti "keltaisia". Tässä tapauksessa klikkaa valikon alareunasta löytyvää "Import" nappia, jolloin projektin importtaus alkaa. Jos taas esim. `fi.yml` tiedosto on punaisena, se johtuu todennäköisesti siitä, että aiemmassa vaiheessa asetettu "Instances"-välilehden "Host" ja/tai "Token" kentät ovat väärin eikä Botfront saa yhteyttä AKS:n Rasa-palveluun (tai Rasa ei ole käynnistynyt oikein AKS:ssa). Botfrontin ja Rasan yhteys toimii oikein, jos Botfrontin esim. NLU-valikossa oikean yläkulman "Train"-nappi on tummansininen.

Jos import onnistui, Palveluohjaajabotti pitäisi sisältää kaikki tarvittavat botin määritykset Botfrontin mm. "Dialogue", "NLU" ja "Responses" -välilehdillä. Voit lukea lisää Botin määrittelystä, kehittämisestä ja Botfrontin toiminallisuuksista Wikin [Bottikuiskaajan opas](/ohjeet/bottikuiskaajan-opas) sivulta.

## 4. Projektin asetusten määritys

Käy myös muuttamassa Settings valikon "Credentials" välilehdeltä `base_url` osoite samaksi osoitteeksi, josta AKS:n palvelut avautuvat. Palveluohjaimen tapauksessa se on siis `https://botfront.palveluohjaaja.fi` eli sama kuin Botfrontin osoite. Asetuksen `socket_path` arvo pitäisi olla valmiiksi oikein, jos AKS on pystytetty samalla tavalla kuin Palveluohjaimen repoissa se on määritetty. `socket_path` arvo tulee osoittaa AKS:n Rasa palvelun socketio-rajapinnan pathiin eli kun yhdistetään `base_url` ja `socket_path`, niin saadaan yhteys AKS:n Rasan socketio-rajapintaan.

Voit myös lisätä git-integraation Settings-valikon "Git credentials" -välilehdeltä asettamalla oman git-reposi osoitteen sekä SSH avaimet, joilla luku- ja kirjoitusoikeudet git-repoon. Palveluohjaimen tapauksessa asetuksen git repoksi laitetaan esim. `git@{repositorion-osoite}`. Git-integraation toimiessa Botfront puskee automaattisesti bottiprojektin varmuuskopion jokaisen botin "Deploy to production" napin painalluksen aikana. Halutessaan voi myös itse manuaalisesti puskea varmuuskopion repoon tai palauttaa vanhempi versio reposta. Lue lisää wikin bottikuiskaajan ohjeista [Bottikuiskaajan opas](bottikuiskaajan-opas.md#botin-versionhallinta)

Jotta wikin Bottikuiskaajan oppaassa esitelty "Deploy to production" -napin kautta tapahtuva [bottiversion tuotantoonvienti](./bottikuiskaajan-opas.md#uuden-bottiversion-tuotantoonvienti) toimii oikein, tulee Botfrontin Admin -valikon Settings valikossa kohdassa "Webhooks" määrittää `DeployProject` osoite. Osoite tulee olla AKS:n Rasa-palvelun osoite, jonka lopussa on path `/botfront/deploy`. Tuotantoympäristössä AKS:ssa on 2kpl Rasa-palveluita (ns. "kuiskaus/dev" Rasa ja "tuotanto" Rasa), joten tuotantoympäristössä Palveluohjaimen tapauksessa osoitteeksi laitetaan `http://rasa.default.svc.cluster.local:5005/botfront/deploy`. Testiympäristössä kustannussäästöjen takia AKS:ssa on vain yksi Rasa (ns. "kuiskaus/dev" Rasa), jollon testiympäristön Botfrontissa osoitteeksi laitetaan `http://rasa-dev.default.svc.cluster.local:5005/botfront/deploy`. Asetuksen `HTTP method` kohtaan valitaan oletuksena oleva `GET`.

# 5. Tietokantataulut

Suurin osa datasta populoituu tietokantaan itsestään, kun datanhakija ja datanprosessoija ajavat läpi ensi kerran.

Kaksi taulua pitää kuitenkin ottaa erikseen huomioon:

### intent_to_services

Tämä sisältää määrityksiä, joissa botin intentiin on yhdistetty siihen liittyviä PTV-luokkia, luonnollisen kielen hakulauseita sekä suoraan PTV-palveluita. Tämä pitää erikseen lisätä tietokantaan `service_db`-kannan collectioniksi.

```
[{
  "_id": {
    "$oid": "61655e8e6532222483853fea"
  },
  "intent": "ke8_losing_job",
  "ptv_services": [
    "9e47824b-3d05-4f74-9be5-6d14aca2bc5e",
    "8c6e25e9-e186-49fd-852c-f6f168d1351f",
    "6c415cf0-827d-47d0-86e4-866100bc86a8",
    "2c9cf971-a4b4-4e66-aa6e-d3cb97d8fab4"
  ]
},{
  "_id": {
    "$oid": "61655e8e6532222483853feb"
  },
  "intent": "ke8_find_jobs",
  "ptv_services": [
    "9e47824b-3d05-4f74-9be5-6d14aca2bc5e",
    "6c415cf0-827d-47d0-86e4-866100bc86a8",
    "67384a79-8541-4c1e-b78e-bec4c57a8f6b",
    "871c5b63-914c-45b8-8e74-a2258007dcda",
    "ee93e704-9b33-4c38-abd2-5c13c9e63241"
  ]
},{
  "_id": {
    "$oid": "61655e8e6532222483853fec"
  },
  "intent": "ke8_unemployment_benefits",
  "ptv_services": [
    "9e47824b-3d05-4f74-9be5-6d14aca2bc5e",
    "8c6e25e9-e186-49fd-852c-f6f168d1351f",
    "fd48ae84-e65b-4e37-84b9-54745ee33b99"
  ]
},{
  "_id": {
    "$oid": "61655e8e6532222483853fed"
  },
  "intent": "ke8_unemployment_registration",
  "ptv_services": [
    "9e47824b-3d05-4f74-9be5-6d14aca2bc5e"
  ]
},{
  "_id": {
    "$oid": "61655e8e6532222483853fee"
  },
  "intent": "ke8_other",
  "ptv_services": [
    "598f8e56-d437-4173-b1f7-2a5912f4510a",
    "f35e3814-dd72-4cdb-8f3d-296b8f65f496",
    "f3a9e069-40b8-4415-857b-1b091a079eb8"
  ]
},{
  "_id": {
    "$oid": "61655e8e6532222483853fef"
  },
  "intent": "ke9_debt",
  "ptv_services": [
    "91f7d0fc-0844-4d2b-94d3-5d9d7ad8c2b9",
    "c23247c4-a158-419d-b7b4-0e8f435cde85",
    "ff059acf-de3f-468d-8b6a-9a492d301cda"
  ]
},{
  "_id": {
    "$oid": "61655e8e6532222483853ff0"
  },
  "ptv_services": [
    "234861f6-28d2-4671-8f50-269592a92148",
    "1dfe4d37-1b74-49fb-9eca-28fdb56d2c9c",
    "76472df7-25ed-4c55-94dd-fa3dd98ee862",
    "eac1ab49-27e9-4d8a-b5cf-f5daec4d9065"
  ],
  "intent": "ke14_death"
},{
  "_id": {
    "$oid": "61655e8e6532222483853ff1"
  },
  "intent": "P4.10_social_assistance_benefit",
  "ptv_services": [
    "ff059acf-de3f-468d-8b6a-9a492d301cda"
  ]
},{
  "_id": {
    "$oid": "61655e8e6532222483853ff2"
  },
  "intent": "P1.5_moving",
  "ptv_services": [
    "cb196ca7-7283-4741-8a19-784335da1dcc",
    "86f39ee0-4138-4c35-a2c8-085c1b5c33f1"
  ]
},{
  "_id": {
    "$oid": "61a4d55e1e0865f54c6deb52"
  },
  "intent": "P4.1_housing_allowance",
  "ptv_services": [
    "579991be-f40f-4913-8130-0e07592b50c4"
  ],
  "intent_text": "Asumistuki asumisen tuki"
},{
  "_id": {
    "$oid": "61a73e467de51c737446f784"
  },
  "intent": "P3.1_divorce",
  "ptv_services": [
    "90078a8b-9d21-432f-9a07-2a842f1308dd",
    "e74a04df-6ae6-4ab2-ba44-73805b79fed4",
    "39df3b53-fce5-4a8c-b4cc-0d88b239c817",
    "bf0ca80d-f212-4c62-9a62-c7df0c906d1e",
    "51f239fe-60e1-44e3-9736-980792c63d07"
  ],
  "intent_text": "Avioero ero avioliiton purkaminen lopettaminen lopetus"
},{
  "_id": {
    "$oid": "61a789397de51c737446f78d"
  },
  "intent": "P3.1_marriage",
  "ptv_services": [
    "4c8311b0-ae3d-4304-88af-81fbfca262a7",
    "d4272e80-a5f9-4338-a67a-cb9e0b70024a",
    "ed108c8b-c9f6-4cad-b441-1ffa4ef133e3",
    "554b7de5-14f2-4640-b1e8-8b8cc3dde154",
    "4cd33fc2-0224-4337-b87e-2a94f6e7cb58",
    "ca14ff0d-a172-4e46-8359-806fd5358ae6",
    "87a571ce-be2a-4682-a474-dbd85bef5fd5"
  ],
  "intent_text": "Avioliitto liitto naimisiin häät vihkiminen parisuhde avio puoliso"
},{
  "_id": {
    "$oid": "61bb214f86af0c2fe4b6411f"
  },
  "intent": "ke10_pension",
  "ptv_services": [
    "30fe7757-32ad-4ea7-a8e4-857b44f81160",
    "a45b8957-8982-44fe-9a0d-f6b3bbbda6ff",
    "95c246ed-9a65-41ea-9902-81d3bc6aa98d",
    "3b872596-ee51-4eff-9f46-be925afc094e",
    "e31a3af2-34b7-45e0-9d96-5d928ab2e99c",
    "11be9878-7232-45ed-85a8-30859a3fc499"
  ],
  "intent_service_classes": [
    "P13",
    "P13.1",
    "P13.2",
    "P13.3",
    "P13.4",
    "P13.5"
  ]
},{
  "_id": {
    "$oid": "61bc3ec0d3fed82f4a210484"
  },
  "intent": "P6_financial_aid_for_students",
  "ptv_services": [
    "e0556386-0ffa-40f2-98aa-770b42dd792a"
  ]
},{
  "_id": {
    "$oid": "61c07aa2d6e0cd0b64b3f1de"
  },
  "intent": "P6.9_university_of_applied_sciences",
  "ptv_services": [
    "e0556386-0ffa-40f2-98aa-770b42dd792a",
    "0582e444-52d9-468d-93c1-86c0602ac9f6",
    "f3a9e069-40b8-4415-857b-1b091a079eb8"
  ],
  "intent_service_classes": [
    "P6.9"
  ]
},{
  "_id": {
    "$oid": "61c07accd6e0cd0b64b3f1df"
  },
  "intent": "P6.9_university",
  "ptv_services": [
    "e0556386-0ffa-40f2-98aa-770b42dd792a",
    "0582e444-52d9-468d-93c1-86c0602ac9f6",
    "f3a9e069-40b8-4415-857b-1b091a079eb8"
  ],
  "intent_service_classes": [
    "P6.9"
  ]
},{
  "_id": {
    "$oid": "61d42754afd7254e70d55f29"
  },
  "intent": "P8.3_citizens_initiative",
  "ptv_services": [
    "d2354b59-51f1-4051-bb7c-7558d164a949",
    "3f7ac847-7265-411c-9526-83adbcbbf4a9"
  ],
  "intent_service_classes": [
    "P8.3"
  ]
},{
  "_id": {
    "$oid": "61d57e4f836d316503934d29"
  },
  "intent": "P16.5_public_transport",
  "ptv_services": [
    "ecb1c03d-6460-4405-80bc-b9fb1e0358ac"
  ],
  "intent_service_classes": [
    "P16.5"
  ]
},{
  "_id": {
    "$oid": "61e16ad59d0cfbe5b0264b37"
  },
  "intent": "P3.2_having_child",
  "ptv_services": [
    "c9c8aaa5-c958-4d44-8f88-a2416bf1f76a",
    "a2fc81c1-4b59-4ada-80eb-36339957d013",
    "003dca0e-9ae9-4362-ba93-78a9d5f127d7",
    "bddb2ffb-6864-41ee-9436-0e013eea5b69"
  ],
  "intent_service_classes": [
    "P3.2"
  ]
},{
  "_id": {
    "$oid": "62021c867e764040cce2b95a"
  },
  "intent": "P4.10_social_assistance_benefit_municipality",
  "intent_priorization": "local",
  "intent_service_classes": [
    "P4.10"
  ]
},{
  "_id": {
    "$oid": "621e20e372212d52b03cbac9"
  },
  "intent": "P6.1_daycare",
  "intent_service_classes": [
    "P6.1"
  ],
  "intent_text": "Päivähoito päiväkoti varhaiskasvatus"
},{
  "_id": {
    "$oid": "621e20e372212d52b03cbaca"
  },
  "intent": "P6.1_preschool",
  "intent_service_classes": [
    "P6.1"
  ],
  "intent_text": "Esikoulu esiopetus"
}]
```

### translations

Käännökset populoituvat itsestään, mutta niitä on jo tätä kirjoitettaessa niin paljon, ettei käytössä oleva ilmaisversio kääntäjästä (2 miljoonaa merkkiä/kuukausi) riitä koko palvelulistan käännettävien pelveluiden kääntämiseen kerralla. Kääntäjästä täytyy ehkä käyttää alkuvaiheessa maksullista versiota, jotta alkukäännökset voidaan tehdä. Jatkuvassa käytössä Free Tier on riittävä.

# 6. Hälytykset

Azuren portaalissa voi asettaa AKS:lle erilaisia hälytysehtoja AKS -> Alerts -> Alert Rules, kuten levytilan, muistin ja suorittimen käytön suhteen. Sellaisia on asettetuna nykyisessä ympäristöissä. Voit käydä katsomassa mahdollisia hälytystiloja portaalissa tai käydä asettamassa hälytyksille ilmoituskanavia, kuten sähköposti-ilmoituksia. Sellaisia ei ole tällä hetkellä käytössä.

# Ylläpito

Kun infra on pystyssä ja toiminnassa, niin se vaatii todennäköisesti yhä säännöllistä ylläpitoa joidenkin komponenttien versioiden yms. osalta, katso sivulla [Palveluohjaimen ylläpito](/ohjeet/yllapito.md)
