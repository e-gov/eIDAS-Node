---
permalink: eIDASKlientMikro
---

# eIDAS Client mikroteenusena

Käesoleva dokument räägib küsimustest, mida tuleb lahendada eIDAS Client kasutamisel mikroteenusena. TARA näite põhjal, kuid käsitlus kehtib ka muudele sama mustriga kasutustele.

<img src='img/KlientTARAs.PNG' width='400'>

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

_pakub/kasutab_ | _välis- v siseliides_ | _nimetus_ | _selgitus_
pakub | välis | `/metadata` | SAML 2.0 standardijärgne metateabe otspunkt. Vajalik eIDAS konnektorteenuse ja kliendi vahelise usalduse loomiseks. Joonisel: "väljasta konnektorteenusele metateave".

pakub | sise | `/AuthReq` | Moodustab SAML autentimispäringusõnum
pakub | sise | `/AuthRes` | Töötleb SAML autentimisvastussõnumi
kasutab | välis | `/ConnectorResponderMetadata` | Loe RIA eIDAS konnektorteenuse metateavet

Mikroteenusena paigaldamisel tuleb arvestada:




