---
permalink: Testjuhud
---

# Testijuhtude loetelu eIDAS konnektorteenusega liidestumiseks (DRAFT)
{: .no_toc}

- TOC
{:toc}

Antud testide eesmärk on kontrollida liidestuva teenuse võimekust suhelda eIDAS sõlme ning eidas võrgustikuga. Tegemist ei ole testikomplektidaga testimaks liidestuvat teenust.

## 1 Metaandmete publitseerimine
| ID | Testjuht | Tulem |
|----|----------|-----------|
| MET-1 | Metateabe XML peab vastama [SAML 2.0 schemale](https://docs.oasis-open.org/security/saml/v2.0/saml-schema-metadata-2.0.xsd) | Metaandmed valideeruvad vastu schemat |
| MET-2 | Kohustuslikud väljad ning väärtused on olemas vastavalt [spetsifikatsioonile](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon#53-teenusepakkuja-metateave) | Kõik kohustuslikud väärtused on olemas, lisaks võivad olla valikulised väärtused |
| MET-3 | Allkirjastamise ja krüpteerimise serdid peavad olema erinevad | Metateabe ja sõnumi allkirja sert võivad olla samad, krüpteerimise sert peab olema erinev |


## 2 Autentimispäringu saatmine
| ID | Testjuht | Tulem |
|----|----------|-----------|
| AUT-1 | Kohustuslikud väljad ning väärtused on olemas vastavalt [spetsifikatsioonile](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon#6-autentimisp%C3%A4ring) | Kõik kohustuslikud väärtused on olemas, lisaks võivad olla valikulised väärtused |


## 3 Autentimisvoo testimine

| ID | Testjuht | Tulem |
|----|----------|-----------|
| AUTS-1 | Kasutaja autenditakse õnnestunult | Kasutaja logitakse sisse. |
| AUTS-2 | Kasutaja ei nõustu andmete edastamisega (user consent not given) | Kasutajat ei logita sisse, kasutajale kuvatakse vastav teade. NB! see võib juhtuda kahes etapis. |
| AUTS-3 | Kasutaja katkestab autentimise | Kasutajat ei logita sisse, autentimisvoog katkeb. Võimalik peab olema autentimisvoo uuesti alustamine. |
