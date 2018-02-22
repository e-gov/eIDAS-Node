---
permalink: Mikroteenusena
---

# eIDAS Client mikroteenusena

Käesoleva dokument räägib küsimustest, mida tuleb lahendada eIDAS Client kasutamisel mikroteenusena. TARA näite põhjal, kuid käsitlus kehtib ka muudele sama mustriga kasutustele.

<img src='img/Mikroteenusena.PNG' width='400'>

Joonis 1. eIDAS Client liidesed TARA-s kasutamisel

Mikroteenuse mõiste. Mikroteenust iseloomustab:
- selgepiirilisus
    - väike arv, selgepiirilisi liideseid
    - pakub konkreetset, lihtsalt hoomatavat teenust
- suhteline sõltumatus
    - väike arv sõltuvusi
    - eraldi paigaldatavus

[eIDAS Client](https://github.com/e-gov/eIDAS-Client) on tarkvara, mis aitab teostada e-teenuse (nimetame ka teenusepakkujaks) liidest RIA eIDAS konnektorteenusega. eIDAS Clienti võib kasutada kolmel viisil:
- eIDAS Client teegi lõimimisega e-teenuse tarkvarasse
- paigaldamisega ja kasutamisega mikroteenusena
- demorakendusena.

Miks eIDAS Clienti kasutada mikroteenusena? Võimaldab:
- vältida sõltuvuskonflikte (eIDAS Clienti ja e-teenuse alusteekide vahel)
- hoida e-teenus vaba SAML sõnumitöötlusest.
- vähendada eIDAS loogika e-teenusesse sissekirjutamise vajadust.

## Liidesed

eIDAS Client mikroteenusena pakub ja tarbib järgmisi otspunkte:

_nr_ | __pakub/kasutab_ | protokoll [, meetod] |  _välis- v siseliides_ | _URL v selle osa_ | _selgitus_
1 | pakub | välis | HTTPS, GET | `/metadata` | Otspunkt pakub e-teenuse SAML metateavet, vastavalt [RIA eIDAS konnektorteenuse spetsifikatsiooni nõuetele](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon)
2 | pakub | sise | HTTP(S), GET | `/login` | Tagastab eIDASe toetatud riikide nimekirja.
3 | pakub | sise | HTTP(S), POST | `/login` | Moodustab SAML autentimispäringusõnumi. Parameetrid ja töötlusloogika vt allpool.
4 | pakub | sise | HTTP(S), POST | `/AuthRes` | Töötleb SAML autentimisvastussõnumi
5 | kasutab | välis | HTTPS, GET | `/ConnectorResponderMetadata` | Loeb RIA eIDAS konnektorteenuse metateavet

| parameetri nimi        | Kohustuslik           | Selgitus  |
| ------------- |:-------------:| :-----|
| `Country` |	Jah | Kohustuslik POST meetodi puhul. Parameeter määrab ära tuvastatava kodaniku riigi. Väärtus peab vastama [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) standardis toodule. |
| `LoA` |	Ei | Parameeter, mis määrab ära nõutava isikutuvastuse taseme. Lubatud väärtused: `substantial`, `high`. <br>Kui parameeter on määramata, siis vaikimisi loetakse väärtuseks `substantial`. |
| `RelayState` |	Ei | Parameeter, mis saadetakse edasi konnektorteenusele muutmata kujul. Väärtus peab vastama regulaaravaldisele `[a-zA-Z0-9-_]{0,80}`. |

| Parameetri nimi        | Kohustuslik           | Selgitus  |
| ------------- |:-------------:| :-----|
| `SAMLResponse` | Jah | Parameeter, milles tagastatakse Base64-kodeeritud SAML `Response` päring. Vastus peab olema allkirjastatud ja isiku kohta käivad väited krüpteeritud (eIDAS Node privaatvõtmega). |
| `RelayState` | Ei | Päringuga saadetud `RelayState` parameetri väärtus. |

## 

Mikroteenusena paigaldamisel tuleb arvestada:




