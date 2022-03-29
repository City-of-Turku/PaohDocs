# Jatkokehitys: NLP

Tälle sivulle on koottu ajatuksia mahdollisesti NLP:n jatkokehityksestä.

---

Loppukäyttäjänä haluan, että botti suosittelee vapaatekstikuvaukseni perusteella paremmin sopivia palveluita, jotta löydän tilanteeseeni parhaiten sopivat palvelut heti.

Palvelusuosittelijan nykyinen ns. baseline NLP-kohtaanto perustuu valmiiksi opetettuun monikieliseen NLP malliin paraphrase-multilingual-mpnet-base-v2. NLP-kohtaantoa voidaan jatkokehittää ainakin muutamalla eri tavalla mahdollisesti paremmaksi:

- Nykyisen mallin opetus/finetunaus meidän domain datalla (PTV palvelukuvaustekstit):
  - https://www.sbert.net/examples/unsupervised_learning/README.html
  - ja https://www.sbert.net/examples/training/data_augmentation/README.html#scenario-2-no-annotated-datasets-only-unlabeled-sentence-pairs
- Suomenkielisen sentence-transformer mallin opetus (tosin tarvitaanko myös ruotsin ja englannin kielen tukea botille?): https://www.sbert.net/examples/training/multilingual/README.html
- Retrieve & re-rank kokeilu: nyt toteutettu NLP-kohtaanto on käytännössä "retrieve" osa. Voisi kokeilla sen jatkoksi "re-rank" osan, mikä voisi mahdollisesti parantaa suosituksia: https://www.sbert.net/examples/applications/retrieve_rerank/README.html
- Re-rank mallina voisi kokeilla valmista monikielistä re-rank mallia: https://huggingface.co/amberoad/bert-multilingual-passage-reranking-msmarco
  - Tässä voi tosin tulla haasteeksi suosittelijan nopeus loppukäyttäjälle: jokainen "retrievattu" palvelu esim. yht. 20kpl pitää ajaa erikseen "re-rank" mallin läpi, joka rankkaa suositukset paremmuusjärjestykseen. Yhden suosituksen "re-rank" ajo mallin läpi voi kestää about 100-1000ms eli 20kpl voi olla yhteensä about 10 sekuntia.
- Pienemmän NLP mallin kokeilu. Pienempi malli säästäisi RAM-käyttöä AKS:ssa ja olisi nopeampi. Voisi kokeilla esim. paraphrase-multilingual-MiniLM-L12-v2 mallia
- Nykyisen NLP mallin pienentäminen: nykyistä mallia voisi koittaa pienentää
  - quantization-tekniikalla: https://www.sbert.net/examples/training/distillation/README.html#quantization
  - tai distillation-tekniikalla: https://www.sbert.net/examples/training/distillation/README.html#knowledge-distillation
- Nykyisen NLP mallin nopeuden tehostaminen muuntamalla se Pytorch-mallista ONNX-malliksi + ONNX optimoinnit
