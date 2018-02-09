---
permalink: Testkasutajad
---

# Testkasutajate juhend (DRAFT)
{: .no_toc}

- TOC
{:toc}

Käesolev juhend kirjeldab test eIDAS konnektorteenuses autentimise võimalusi testimaks autentimist läbi eIDAS võrgustiku.

## 1 Partnerriikide autentimislahendused
Kõige toodangulähedasem viis testida täis autentimisvoogu. Kasutatakse teiste EU riikide autentimisteenuste testkeskondi.

<img src='img/Partner.PNG' width='600'>

| Riigi kood päringus | Kasutaja | Parool | Eeldatav tulem |
|---------------------|----------|--------|----------------|
| XX | TestUser | TestPass| Autentimine õnnestub andmetega: |

## 2 EU poolt pakutav validaatorteenus
CEF Digitali poolt pakutav autentimisteenus ning validaator. Teostab mõningaid kontrolle saabunud päringutele.

<img src='img/Validaator.PNG' width='600'>

| Riigi kood päringus | Kasutaja | Parool | Eeldatav tulem |
|---------------------|----------|--------|----------------|
| YY | puudub | puudub| Autentimine õnnestub andmetega: |

## 3 Eesti sisesed autentimisvahendid (ID-kaart ja Mobiil-ID)
Võimalik on kasutada ka Eesti autentimisteenust. Antud lähenemine ei võimalda testida korrektselt täis autentimisvoogu, kuid võimaldab kontrollida liidestust Eesti eIDAS nodega. Autentimisteenuses on kasutusel test ID-kaart ning test Mobiil-ID teenus.

<img src='img/Eesti.PNG' width='300'>

| Riigi kood päringus | ID-kaar | Mobiil-ID|
|---------------------|---------|----------|
| ZZ | Vajalik [test sertifikaatidega ID kaart](https://www.id.ee/index.php?id=30379) | [Test Mobiil-ID numbrid](https://www.id.ee/index.php?id=36373)|