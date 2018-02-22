---
permalink: Mikroteenusena
---

# eIDAS Client mikroteenusena
{: .no_toc}
v 0.2, 22.02.2018

- TOC
{:toc}

## 1 Ülevaade

[eIDAS Client](https://github.com/e-gov/eIDAS-Client) on tarkvara, mis hõlbustab e-teenuse (teenusepakkuja) liidestamist RIA eIDAS konnektorteenusega. eIDAS Clienti võib kasutada kolmel viisil:
- eIDAS Client teegi lõimimisega e-teenuse tarkvarasse
- paigaldamisega ja kasutamisega mikroteenusena
- demorakendusena.

Käesolev dokument käsitleb eIDAS Client kasutamist mikroteenusena. Kirjeldame eIDAS Client mikroteenuse olulised omadused, liidesed ja töötlusloogika ning mikroteenuse paigaldamiseks muu olulise. Kirjeldus on koostatud TARA silmas pidades, kuid kehtib ka muudele sama mustriga kasutustele.

Miks kasutada eIDAS Clienti mikroteenusena? Mikroteenus võimaldab:
- vältida sõltuvuskonflikte (eIDAS Clienti ja e-teenuse alusteekide vahel)
- hoida e-teenus vaba SAML sõnumitöötlusest, sh sellega seotud võtmehaldusest ja keerukast seadistamisest
- hoida eIDAS loogika teenusepakkujasse sissekirjutamist minimaalsena
- hoida üldiselt asju lihtsana (mikroteenust iseloomustab: 1) selgepiirilisus - väike arv, selgepiirilisi liideseid; pakub konkreetset, lihtsalt hoomatavat teenust; 2) suhteline sõltumatus - väike arv sõltuvusi; eraldi paigaldatavus).

Mikroteenus ei tähenda järeleandmisi turvalisusele,dokumenteerimisele, testimisele jm arendus- ja käitlusnõuetele.

eIDAS Client mikroteenus:
- annab e-teenusele nimekirja RIA eIDAS konnektorteenuse poolt toetatud riikidest
- publitseerib konnektorteenusega suhtluseks vajaliku metateabe otspunkti
- moodustab nõuetekohase SAML autentimispäringu, sh allkirjastab
- töötleb konnektorteenuselt saadud SAML autentimisvastust - kontrollib allkirja, dekrüpteerib ja teisendab hõlpsamini tarbitavale JSON-kujule.

eIDAS Client mikroteenus seadistatakse teenindama ühtainust teenusepakkujat. **SAML autentimispäringut allkirjastades tegutseb mikroteenus teenusepakkuja nimel.** 

Teenusepakkuja ülesandeks on:
- küsida kasutajale kust riigist ta on ja edastada valik koos minimaalse tagatistasemega mikroteenusele
- mikroteenuse poolt moodustatud autentimispäringu edastamine konnektorteenusele (sirviku ümbersuunamiskorraldusega)
- autentimisvastuse vastuvõtmine konnektorteenuselt ja edastamine mikroteenusele töötlemiseks
- autentimistulemuste vastuvõtmine mikroteenuselt (ja eduka isikutuvastuse korral kasutajaga seansi loomine).

## 2 Käitlusomadused

eIDAS Client mikroteenuse tähtsamad omadused (infoturve vt jaotis 2):

_omadus_ | _väärtus_ | _selgitus_
olek | _stateless_ | ei kasuta andmebaasi ega muud püsimälu (kuid loomulikult sõltub seadistusest. Seadistuse osaks on usaldusankur ja privaatvõti)
otspunkte | 4 + 2 | pakub 4, ise kasutab 2
protokoll(id) | HTTP(S) |
avatud pöördumistele välisvõrgust | jah | `/metadata` otspunkt, vt allpool
pöördub välisvõrku | jah | RIA konnektorteenuse metateabe (`/ConnectorResponderMetadata`) ja toetatud riikide nimekirja poole. (TARA puhul pöördumised ei tarvitse väljuda välisvõrku)
kasutajaliides (UI) | ei | otsesuhtlus kasutajaga (sirvikuga) puudub 
seadistus | seadistusfaili abil, vt allpool 
logimine | ei | // TODO Analüüsida logimise vajadust //
isikuandmete töötlus | töödeldakse, teenusepakkuja poolt kasutatava eIDAS autentimisteenuse kontekstis
testimine |   | // TODO //

## 3 Infoturve 

Ligipääsu `/login` ja `/AuthRes` otspunktidele tohib anda ainult teenusepakkujale (s.t sisevõrgus olevale rakendusele).  Kaitstakse HTTPS-ga, sertidega ja usaldusankru seadmisega mikroteenuse seadistuses.

Ligipääs otspunktile `/metadata` ei vaja piiramist (metateave on avalik).

## 4 Liidesed

### 4.1 Ülevaade

eIDAS Client mikroteenusena pakub ja tarbib järgmisi otspunkte (joonis 1). Otspunktid on spetsifitseeritud allpool. 

<img src='img/Mikroteenusena.PNG' width='600' style='margin-left: 6em;'>

Joonis 1. eIDAS Client liidesed

|  nr  |  pakub/kasutab  |  välis- v siseliides | protokoll [, meetod] |  URL v selle osa  | selgitus | ligipääs |
|:----:|:---------------:|:--------------------:|:----------------------:|:-----------------:|-------------|------|
| 1 | pakub | välis | HTTPS, `GET` | `/metadata` | Otspunkt pakub e-teenuse SAML metateavet, vastavalt [RIA eIDAS konnektorteenuse spetsifikatsiooni nõuetele](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon) | avalik |
| 2 | pakub | sise | HTTP(S), `GET` | `/login` | Tagastab eIDASe toetatud riikide nimekirja. | ainult teenusepakkujal |
| 3 | pakub | sise | HTTP(S), `POST` | `/login` | Moodustab SAML autentimispäringusõnumi. Parameetrid ja töötlusloogika vt allpool. | ainult teenusepakkujal |
| 4 | pakub | sise | HTTP(S), `POST` | `/AuthRes` | Töötleb SAML autentimisvastussõnumi. | ainult teenusepakkujal |
| 5 | kasutab | välis | HTTPS, `GET` | `/ConnectorResponderMetadata` | Loeb RIA eIDAS konnektorteenuse metateavet | avalik |

### 4.2 `POST` `/login`

Sisend:

| parameetri nimi        | kohustuslik           | selgitus  |
|:-------------:|:-------------:| :-----|
| `Country` |	Jah | Kohustuslik POST meetodi puhul. Parameeter määrab ära tuvastatava kasutaja riigi. Väärtus peab vastama [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) standardis toodule. |
| `LoA` |	Ei | Parameeter, mis määrab ära nõutava isikutuvastuse taseme. Lubatud väärtused: `substantial`, `high`, `low`. <br>Kui parameeter on määramata, siis vaikimisi loetakse väärtuseks `low`. |
| `RelayState` |	Ei | Parameeter, mis saadetakse edasi konnektorteenusele muutmata kujul. Väärtus peab vastama regulaaravaldisele `[a-zA-Z0-9-_]{0,80}`. |

Väljund:

// TODO JSON-struktuur saatmisvalmis URL-i ja POST-päringu kehaga? //

// TODO Näide. Või viidata konnektorteenuse speki näitele? //

### 4.3 `POST` `/AuthRes`

Sisend:

| parameetri nimi        | kohustuslik           | selgitus  |
| ------------- |:-------------:| :-----|
| `SAMLResponse` | Jah | Base64-kodeeritud SAML `Response` sõnum. Vastus peab olema allkirjastatud ja isiku kohta käivad väited krüpteeritud (eIDAS Node privaatvõtmega). |
| `RelayState` | Ei | Päringuga saadetud `RelayState` parameetri väärtus. |

Väljund:

// TODO - JSON-struktuur? //

// TODO Näide //

## 5 Seadistus

Teenusepakkuja metateabe seadistus

| Parameeter        | Kohustuslik | Kirjeldus, näide |
| :---------------- |:----------:| :----------------|
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

Konnektorteenuse metateabe seadistus

| Parameeter        | Kohustuslik | Kirjeldus, näide |
| :---------------- | :---------- | :----------------|
| `eidas.client.idpMetadataUrl`  | Jah | Konnektorteenuse metateabe asukoht. https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata |
| `eidas.client.idpMetadataSigningCertificateKeyId` | Ei | Konnektorteeenuse metateabe allkirjastamiseks kasutatud sertifikaadi alias võtmehoidlas. Vaikimisi alias: `metadata`. |

AuthnRequesti seadistus

| Parameeter        | Kohustuslik | Kirjeldus, näide |
| :---------------- | :---------- | :----------------|
| `eidas.client.providerName` | Jah | `/saml2p:AuthnRequest/@ProviderName` väärtus. |





