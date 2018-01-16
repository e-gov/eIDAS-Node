---
permalink: Vookirjeldus-2
---

# eIDAS päringuvoog - Demo SP - HTTP POST
{: .no_toc}

- TOC
{:toc}

# Ülevaade

Käesolev dokument esitab samm-sammulise HTTP päringute liikumise eIDAS-autentimisel, Demo SP näitel, HTTP Redirect binding-u kasutamisel.

````
GET https://eidastest.eesti.ee/SP/populateIndexPage
````

````
POST https://eidastest.eesti.ee/SP/IndexPage.action
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| eidasconnector | https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata |
| nodeMetadataUrl | https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata |
| citizenEidas | CD |
| returnUrl | https://eidastest.eesti.ee/SP/ReturnPage |
| eidasNameIdentifier | urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified |
| eidasloa | http://eidas.europa.eu/LoA/low |
| eidasloaCompareType | minimum |
| eidasSPType | public |
| allTypeEidas | true |
| < atribuutide kirjeldused > |  |

````
POST https://eidastest.eesti.ee/EidasNode/ServiceProvider
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| postLocationUrl |	https://eidastest.eesti.ee/EidasNode/ServiceProvider |
| redirectLocationUrl |	https://eidastest.eesti.ee/EidasNode/ServiceProvider |
| country |	CD |
| RelayState | MyRelayState |
| SAMLRequest | ... |
| sendmethods | POST |

````
POST https://ec.europa.eu/eid-integration-test/EidasNode/ColleagueRequest
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLRequest | ... |
| RelayState | MyRelayState |
| token | ... |

````
POST https://ec.europa.eu/eid-integration-test/IdP/AuthenticateCitizen
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLRequest | ... |
| signAssertion | false |
| encryptAssertion | true |
| messageFormat | eidas |

````
POST https://ec.europa.eu/eid-integration-test/EidasNode/SpecificIdPResponse
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | ... |
| username     | xavi |

````
POST https://ec.europa.eu/eid-integration-test/AP/ConsentValue
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| callbackUrl | ... |
| strAttrList | |
| username | xavi |

````
POST https://ec.europa.eu/eid-integration-test/EidasNode/APSelector
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| strAttrList | |

````
POST https://eidastest.eesti.ee/EidasNode/ColleagueResponse
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | |
| nodeType | |
| RelayState | |
| sAMLRequestTT | |
| samlResponseDecriptedXMLEdit | |

````
POST https://eidastest.eesti.ee/SP/ReturnPage
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | |
| RelayState | |

````
POST https://eidastest.eesti.ee/SP/populateReturnPage
````

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | |



