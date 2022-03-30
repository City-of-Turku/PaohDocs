# Azure- ja infra-arkkitehtuuri <!-- omit in toc -->

### Sisällysluettelo <!-- omit in toc -->

- [Azure-ympäristö](#azure-ympäristö)
  - [Azure-resurssien nimeäminen](#azure-resurssien-nimeäminen)
  - [Projektin henkilöstö, joilla on pääsy Azureen:](#projektin-henkilöstö-joilla-on-pääsy-azureen)
- [Palveluohjaimen Azure-arkkitehtuuri](#palveluohjaimen-azure-arkkitehtuuri)

# Azure-ympäristö

## Azure-resurssien nimeäminen

Subscription: "Turun kaupunki, palveluohjain"

Perustetaan kolme resurssiryhmää (resource group). Joidenkin resurssien nimien pituus on rajoitettu 24 merkkiin, joten nimiä lyhennetään tarvittaessa.

- tku-paoh-test-<resource> (testiympäristö)
- tku-paoh-tuot-<resource> (tuotantoympäristö)
- tku-paoh-intra-<resource> (Expressroute-ympäristö)

Esimerkki: "tku-paoh-test-key-vault" tarkoittaa Key Vaultia testiympäristössä.

## Projektin henkilöstö, joilla on pääsy Azureen:

Contributor-oikeudet (read & write):

- Aapo Tanskanen (Gofore)
- Joonas Itkonen (Gofore)
- Ossian Rajala (Gofore)
- Kalle Rindell (VSSHP)
- Johannes Holvitie (VSSHP)

Lukuoikeus:

- Kirsi Kiviniemi (Turku)

# Palveluohjaimen Azure-arkkitehtuuri

Tuotantoympäristö:
Draw.io XLM-tiedosto: [azure-akkitehtuuri-tuotanto.drawio.xml](/.attachments/azure-akkitehtuuri-tuotanto.drawio.xml)

![azure-arkkitehtuuri-tuotanto.png](/.attachments/azure-arkkitehtuuri-tuotanto.png)
