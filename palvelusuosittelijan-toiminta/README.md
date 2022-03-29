# Palvelusuosittelijan toiminta

Palvelusuosittelija osaa suositella käytettävissä olevista palvelutietovarannoista palveluita loppukäyttäjän tarpeisiin. Suosittelija tarjoaa teknisen API-rajapinnan, jota kutsumalla sadaan suosituksia. API-rajapinnan ansiosta suosittelijaa voidaan hyödyntää monista eri järjestelmistä, kuten palveluohjaimen chatbotista ja web-sivulta tai tulevaisuudessa muistakin Turun järjestelmistä. Suosittelija voi koostua useista eri "kohtaanto"-palikoista, joita hyöydyntämällä suosittelija löytää oikeat palvelut. "Kohtaanto" tarkoittaa oikeiden palveluiden yhdistämistä loppukäyttäjän spesifeihin tarpeisiin ja elämäntilanteisiin. Kohtaanto-palikka voi olla tekoälykkäästi dynaaminen tai enemmän staattinen "kovakoodattu".

## Vapaateksti NLP kohtaanto-palikka

NLP (Natural Language Processing, luonnollisen kielen käsittely) on tekoäly-menetelmä ihmisen tuottaman tekstin käsittelyyn ja analysointiin. "Vapaateksti NLP kohtaanto" tarkoittaa palveluiden sisältämän tekstidatan (esim. kuvausteksti, nimi) kohtauttamista loppukäyttäjältä kerättyyn tekstidataan (esim. käyttäjän itse kirjoittama vapaamuotoinen tekstikuvaus hänen palvelutarpeestaan tai elämäntilanteestaan). Vapaateksti NLP kohtaanto muistuttaakin "Googlen hakua".

Vapaateksti NLP kohtaanto hyödyntää tekstin semanttisen samankaltaisuuden analyysia. Näin voidaan esimerkiksi tunnistaa semanttisesti samankaltaisia palveluita niiden kuvaustekstien perusteella. Teknisesti tämä tapahtuu niin, että tekoälykäs tekstin semantiikkaa ymmärtävä NLP-malli muuntaa tekstit numeeriseen ja matemaattiseen vektorimuotoon. Semanttisesti samankaltaiset tekstit saavat matemaattisesti lähellä toisiaan olevat vektorit. Semanttisesti hyvin erilaiset tekstit taas saavat matemaattisesti kaukana toisistaan olevat vektorit. Tämä mahdollistaa lähellä toisiaan olevien vektorien automaattisen ryhmittelyn. Vektoreiden etäisyyksiä voidaan myös matemaattisesti laskea ja vertailla, mikä mahdollistaa "semanttisen hakukoneen" rakentamisen niin, että hakutekstin vektoria lähimpänä olevat muut vektorit löydetään ja annetaan hakutuloksina.

Alla olevassa kuvassa on visualisoitu kaikki Varsinais-Suomen alueen julkiset PTV-palvelut niiden kuvausteksteistä NLP-mallilla muodostetuista vektoreista. Ylätasolla kuvasta voi huomata tummempia ryhmiä (klustereita), joihin on keskittynyt semanttisesti samankaltaisia palveluita.

![ptv-palveluiden-vektorit.png](/.attachments/ptv-palveluiden-vektorit.png)

Zoomaamalla ylläolevan kuvan ryhmiä voidaan huomata tarkemmin, että ryhmiin on keskittynyt hyvin samankaltaisia palveluita. Esimerkiksi alla olevassa kuvassa on zoomattu ryhmään, jossa on työllisyyteen liittyviä palveluita.

![ptv-palveluiden-vektorit-tyollisyys.png](/.attachments/ptv-palveluiden-vektorit-tyollisyys.png)

Palvelusuosittelijan vapaateksti NLP kohtaanto-palikka toimii siis niin, että loppukäyttäjältä kysytään chatbotissa esimerkiksi "kuvaile palvelutarpeesi", käyttäjän vapaamuotoisesti kuvailtu teksti vektorisoidaan NLP-mallilla ja etsitään vektoria lähimpinä olevat PTV-palveluiden vektorit. Löydetyt PTV-palvelut palautetaan suosittelijan palvelusuosituksissa.
