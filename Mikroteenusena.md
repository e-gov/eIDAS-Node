---
permalink: Mikroteenusena
---

# eIDAS Client mikroteenusena
{: .no_toc}
v 0.2

- TOC
{:toc}

[eIDAS Client](https://github.com/e-gov/eIDAS-Client) on tarkvara, mis aitab teostada e-teenuse (nimetame ka teenusepakkujaks) liidest RIA eIDAS konnektorteenusega. eIDAS Clienti võib kasutada kolmel viisil:
- eIDAS Client teegi lõimimisega e-teenuse tarkvarasse
- paigaldamisega ja kasutamisega mikroteenusena
- demorakendusena.

Käesolev dokument käsitleb eIDAS Client kasutamist mikroteenusena. Kirjeldame eIDAS Client mikroteenuse olulised omadused, liidesed ja töötlusloogika ja mikroteenuse paigaldamiseks muu olulise. Kirjeldus on koostatud TARA silmas pidades, kuid käsitlus kehtib ka muudele sama mustriga kasutustele.

Miks eIDAS Clienti kasutada mikroteenusena? Võimaldab:
- vältida sõltuvuskonflikte (eIDAS Clienti ja e-teenuse alusteekide vahel)
- hoida e-teenus vaba SAML sõnumitöötlusest.
- vähendada eIDAS loogika e-teenusesse sissekirjutamise vajadust.

Mikroteenust iseloomustab: 1) selgepiirilisus - väike arv, selgepiirilisi liideseid; pakub konkreetset, lihtsalt hoomatavat teenust; 2) suhteline sõltumatus - väike arv sõltuvusi; eraldi paigaldatavus. Samas ei tähenda mikroteenus järeleandmisi turvalisusele.

## Omadused

Ülevaade eIDAS Client mikroteenuse omadustest:

_omadus_ | _väärtus_ | _selgitus_
olek | _stateless_ | ei kasuta andmebaasi ega muud püsimälu.
avatud pöördumistele välisvõrgust | jah | `/metadata` otspunkt, vt allpool
pöördub välisvõrku | jah | RIA konnektorteenuse metateabe otspunkti `/ConnectorResponderMetadata` poole. (TARA puhul pöördumine ei välju välisvõrku)
UI | ei | otsesuhtlus kasutajaga (sirvikuga) puudub 
seadistus | seadistusfaili abil, vt allpool 
logimine | ei | // TODO Analüüsida logimise vajadust //
isikuandmete töötlus | töödeldakse, teenusepakkuja poolt kasutatava eIDAS autentimisteenuse kontekstis

## Infoturve 

**Ligipääsu `/login` ja `/AuthRes` otspunktidele tohib anda ainult teenusepakkujale (s.t sisevõrgus olevale rakendusele).  Kaitstakse HTTPS-ga, sertidega ja usaldusankru seadmisega mikroteenuse seadistuses.**

## Liidesed

eIDAS Client mikroteenusena pakub ja tarbib järgmisi otspunkte:

<img src='img/Mikroteenusena.PNG' width='600'>

Joonis 1. eIDAS Client liidesed

| _nr_ | _pakub/kasutab_ | protokoll [, meetod] |  _välis- v siseliides_ | _URL v selle osa_ | _selgitus_  |
|:----:|:---------------:|:--------------------:|:----------------------:|:-----------------:|-------------|
| 1 | pakub | välis | HTTPS, GET | `/metadata` | Otspunkt pakub e-teenuse SAML metateavet, vastavalt [RIA eIDAS konnektorteenuse spetsifikatsiooni nõuetele](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon) |
| 2 | pakub | sise | HTTP(S), GET | `/login` | Tagastab eIDASe toetatud riikide nimekirja. |
| 3 | pakub | sise | HTTP(S), POST | `/login` | Moodustab SAML autentimispäringusõnumi. Parameetrid ja töötlusloogika vt allpool. |
| 4 | pakub | sise | HTTP(S), POST | `/AuthRes` | Töötleb SAML autentimisvastussõnumi. Ligipääs otspunktile on ainult ühel, sisevõrgus oleval rakendusel (teenusepakkujal). |
| 5 | kasutab | välis | HTTPS, GET | `/ConnectorResponderMetadata` | Loeb RIA eIDAS konnektorteenuse metateavet |

| parameetri nimi        | kohustuslik           | selgitus  |
| ------------- |:-------------:| :-----|
| `Country` |	Jah | Kohustuslik POST meetodi puhul. Parameeter määrab ära tuvastatava kodaniku riigi. Väärtus peab vastama [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) standardis toodule. |
| `LoA` |	Ei | Parameeter, mis määrab ära nõutava isikutuvastuse taseme. Lubatud väärtused: `substantial`, `high`. <br>Kui parameeter on määramata, siis vaikimisi loetakse väärtuseks `substantial`. |
| `RelayState` |	Ei | Parameeter, mis saadetakse edasi konnektorteenusele muutmata kujul. Väärtus peab vastama regulaaravaldisele `[a-zA-Z0-9-_]{0,80}`. |

| parameetri nimi        | kohustuslik           | selgitus  |
| ------------- |:-------------:| :-----|
| `SAMLResponse` | Jah | Parameeter, milles tagastatakse Base64-kodeeritud SAML `Response` päring. Vastus peab olema allkirjastatud ja isiku kohta käivad väited krüpteeritud (eIDAS Node privaatvõtmega). |
| `RelayState` | Ei | Päringuga saadetud `RelayState` parameetri väärtus. |

## Seadistus

Tabel 3.1 - Teenusepakkuja metateabe seadistus

| Parameeter        | Kohustuslik | Kirjeldus, näide |
| :---------------- | :---------- | :----------------|
| `eidas.client.keystore` | Jah | Võtmehoidla asukoht. Peab olema JKS tüüpi. classpath:samlKeystore.jks |
| `eidas.client.keystorePass` | Jah | Võtmehoidla parool. |
| `eidas.client.metadataSigningKeyId` | Jah | SAML metateabe allkirjastamisvõtme alias. |
| `eidas.client.metadataSigningKeyPass` | Jah | SAML metateabe allkirjastamisvõtme parool. |
| `eidas.client.requestSigningKeyId` | Jah | SAML autentimispäringu allkirjastamisvõtme alias. |
| `eidas.client.requestSigningKeyPass` | Jah | SAML autentimispäringu allkirjastamisvõtme parool. |
| `eidas.client.responseDecryptionKeyId` | Jah | SAML autentimisvastuse dekrüpteerimisvõtme alias. |
| `eidas.client.responseDecryptionKeyPass` | Jah | SAML autentimisvastuse dekrüpteerimisvõtme parool. |
| `eidas.client.spEntityId` | Jah | `/md:EntityDescriptor/@Issuer` väärtus metateabes. Näiteks: https://hostname:8889/metadata |
| `eidas.client.callbackUrl` | Jah | `/md:EntityDescriptor/md:SPSSODescriptor/md:AssertionConsumerService/@Location` väärtus metateabes. |

Tabel 3.2 - Konnektorteenuse metateabe seadistus

| Parameeter        | Kohustuslik | Kirjeldus, näide |
| :---------------- | :---------- | :----------------|
| `eidas.client.idpMetadataUrl`  | Jah | Konnektorteenuse metateabe asukoht. https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata |
| `eidas.client.idpMetadataSigningCertificateKeyId` | Ei | Konnektorteeenuse metateabe allkirjastamiseks kasutatud sertifikaadi alias võtmehoidlas. Vaikimisi alias: `metadata`. |

Tabel 3.3 - AuthnRequesti seadistus

| Parameeter        | Kohustuslik | Kirjeldus, näide |
| :---------------- | :---------- | :----------------|
| `eidas.client.providerName` | Jah | `/saml2p:AuthnRequest/@ProviderName` väärtus. |





