# Palveluohjaimen ylläpito <!-- omit in toc -->

Ylläpitovaiheessa olisi hyvä tarkistaa ainakin seuraavien asioiden toimivuus säännöllisesti:

- [Azure-infra](#azure-infra)
- [AKS](#aks)
- [AKS:n palvelut](#aksn-palvelut)
  - [KEDA](#keda)
  - [Nginx-ingress](#nginx-ingress)
  - [Cert-manager](#cert-manager)
- [PTV](#ptv)
- [ModSecurity-palomuuri](#modsecurity-palomuuri)
- [WebFrontend](#webfrontend)

## Azure-infra

Suurin osa infrasta on vakaata, koska se muodostuu Azuren valmispalveluista. Palvelut, kuten Cosmos-tietokanta ja Azure Container Registry, ovat Azuren ylläpitämiä eikä niistä pitäisi käyttäjän joutua huolehtimaan. On mahdollista, että Azure jossain kohtaa muuttaa tuotteitaan, jolloin jokin osa ei toimikaan enää, kuten se on toiminut kehitysvaiheessa. Tällöin ylläpidon täytyy kehittää Palveluohjaaja toimimaan uusien muuttuneiden palveluiden kanssa. Tällaisten muutosten ennakointi on vaikeaa ja näiden riski luultavasti kasvaa vasta pidemmän ajan kuluttua isommaksi.

## AKS

Azure Kubernetes Services on palvelu, joka on ylätasolla Azuren oma palvelu, kuten muutkin, mutta se perustuu Open Source -työkaluun Kubernetes. Kubernetesistä julkaistaan säännöllisesti uusia versioita. Tämä Kubernetes-versio on nykyisin asetettu päivittymään automaattisesti viimeisimpään stable-versioon AKS:ssa, mutta sen voi päivittää myös manuaalisesti portaalista AKS -> Cluster Configuration -> Kubernetes version -> Update version. Tässä kannattaa käyttää harkintaa, että päivittää mahdollisimman uuteen, mutta ei välttämättä uusimpaan preview-versioon, joka voi sisältää kriittisiä bugeja. Automaattinen päivitys voi myös aiheuttaa ongelmia, jos se jättää AKS:n sisäisiä palveluita ei-yhteensopiviksi.

## AKS:n palvelut

Palveluohjaaja toimii pitkälti AKS:n sisällä olevien palveluiden myötä. Monet näistä eivät ole mitenkään Azuren ylläpitämiä vaan Kubernetes-versioon sidottuja palveluita, jotka voi joutua päivittämään, kun Kubernetesin päivittää.

### KEDA

Tämä kontrolloi AKS:ssä ajettavia funktioita. Katso Infran pystytysohjeesta Azuren infosivu KEDA:an liittyen, jos tämä vaatii päivityksen. (ohje: https://docs.microsoft.com/en-us/azure/azure-functions/functions-kubernetes-keda) Kehitysvaiheessa tämä KEDA on preview-vaiheessa. Jos muutoksia tulee, niin funktioina toimivien komponenttien (PTV-datan hakija, datan prosessoija) kohdalla saattaa joutua tekemään muutoksia.

### Nginx-ingress

Tämä on työkalu, jonka kautta AKS:n palvelut näkyvät ulospäin. Palvelu käyttää itse omaa määräämäänsä versiota itse Nginxsitä, joten siitä ei tarvitse erikseen huolehtia, kun päivittää tämän. Vaatii vastaavan tarkistuksen kuin edellinen. (ohje: https://docs.microsoft.com/en-us/azure/aks/ingress-static-ip)

### Cert-manager

Pitää yllä sertifikaatteja, katso vastaavasti Azuren infosivulta, jos on saatavilla uudempi versio.

## PTV

Palveluohjaaja päivittää kerran päivässä palvelut PTV-rajapinnan https://api.palvelutietovaranto.suomi.fi/swagger/ui/index.html kautta. Tässä tapahtuvat muutokset (esim mahdollinen API-avaimen käyttöönotto) voivat rikkoa integraation, jonka jälkeen palvelut eivät enää päivittyisi. Tällöin vaadittaisiin muutoksia `ServiceDataImport`-repositoriossa olevaan palveludatan haun tekevään komponenttiin.

## ModSecurity-palomuuri

Tämä on mukana nginx-ingressissä. Palomuurista on poistettu monia siinä oletuksena olevia sääntöjä, jotta Botfront voi toimia oikein. Tulevaisuudessa käytössä olevaa säännöstöä olisi ehkä tarve hienosäätää.

## WebFrontend

[Web-palvelun](https://github.com/City-of-Turku/PaohWebFrontend) kohdalla olisi hyvä vähintään ajaa ajoittain `npm audit` ja korjata siinä ilmeneviä ongelmia - toki kaikki annetut varoitukset eivät välttämättä todellisuudessa vaikuta palveluun.

Web-palvelussa voi myös ilmetä tarve päivittää riippuvuuksia uudempiin versioihin, esim.

- Web-palvelun frontendissä on käytössä React 17, joka on tällä hetkellä (kevät 2022) uusin stabiili React-versio. Enemmin tai myöhemmin voi kuitenkin tulla tarve päivittää Reactin versio.
- Material UI -komponenttikirjastosta on käytössä v4, mutta v5 on jo tätä kirjoitettaessa julkaistu
