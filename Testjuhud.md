---
permalink: Testjuhud
---

**ARHIVEERITUD** 
{:.teade}

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

## 4 Automaatne testikomplekt

[eIDAS klient makettrakenduse](https://github.com/e-gov/eIDAS-Client) raames on loodud automaatsete testide komplekt mida on võimalik kasutada eIDAS sõlmega liidestumise testimiseks. Antud testid on arenduses ning  muutuvad projekti edenedes. Praegusel hetkel on testide käivitamiseks vajalik eIDAS sõlmele ligipääs (kirjelduses punktid 2 ja 3)!

Kuidas teste käivitada:
1) Tõmba githubist alla [eIDAS klient makettrakendus](https://github.com/e-gov/eIDAS-Client)

2) Seadistada eIDAS sõlme (eIDAS Node) metadata otspunkt [eIDAS-client-webapp application.properties](https://github.com/e-gov/eIDAS-Client/blob/master/eidas-client-webapp/src/main/resources/application.properties)
failis.

`eidas.client.idpMetadataUrl = http://localhost:8080/EidasNode/ConnectorResponderMetadata`

3) Juhul kui kasutatakse muud eIDAS sõlme kui https://eidastest.eesti.ee/EidasNode/ServiceMetadata siis tuleb vastav sert lisada ka [eidas-client-webapp keystore-i](https://github.com/e-gov/eIDAS-Client/blob/master/eidas-client-webapp/src/main/resources/samlKeystore.jks)

4) Seadistada testid enda SAML liidese otspunktidele [eidas-client-test application.properties](https://github.com/e-gov/eIDAS-Client/blob/master/eidas-client-test/src/test/resources/application-test.properties) failis

`server.port = 8080` - testitava liidese port

`eidas.client.idpMetadataUrl = http://localhost:8080/EidasNode/ConnectorResponderMetadata` - eIDAS sõlme metaandmete otspunkt

`eidas.client.spEntityId = http://localhost:8080/SP/metadata` - tesitava liidese metaandmete otspunkt

`eidas.client.spStartUrl = http://localhost:8080/start` - testitava liidese autentimise alustamise otspunkt

`eidas.client.spReturnUrl = http://localhost:8080/SP/ReturnPage` - testitava liidese returnUrli otspunkt

5) Käivitada testid kasutades mavenit või maven wrapperit

`./mvnw -PrunIntegrationTests -Dit.test=Common* verify`

[Testide klassid](https://github.com/e-gov/eIDAS-Client/tree/master/eidas-client-test/src/test/java/ee/ria/eidas) mille nimed hakkavad Common algusega on universaalsed ning sobivad kasutamiseks ilma muutmata. Enda liidestumise testimisel võib uurida, täiendada ja kasutada ka teisi testiklasse. Kõikide testide käivitamiseks kasutada käsku:

`./mvnw -PrunIntegrationTests verify`
