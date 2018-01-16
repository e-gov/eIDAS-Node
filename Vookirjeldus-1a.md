---
permalink: Vookirjeldus-1a
---

# eIDAS päringuvoog - Demo SP - HTTP Redirect
{: .no_toc}

- TOC
{:toc}

# Ülevaade

Käesolev dokument esitab samm-sammulise HTTP päringute liikumise eIDAS-autentimisel, Demo SP näitel, HTTP Redirect binding-u kasutamisel.

| nr | kasutaja tegevus | HTTP päring | HTTP vastus |
|----|------------------|-------------|-------------|
| 1  | pöördub Demo SP avalehe poole | GET `https://eidastest.eesti.ee/SP/populateIndexPage` | server saadab avalehe |
| 2  | valib parameetrid | `POST https://eidastest.eesti.ee/SP/IndexPage.action` | server saadab lehe, millel näidatakse SAML sõnumit |
| 3  | valib saatmismeetodi `HTTP Redirect` | `POST https://eidastest.eesti.ee/SP/changeProtocolBinding.action` | server saadab ümbersuunamiskorralduse |
| 4  | aut-ne ümbersuunamine | `GET https://eidastest.eesti.ee/EidasNode/ServiceProvider?` 
`postLocationUrl=https://eidastest.eesti.ee/EidasNode/ServiceProvider&` 
`redirectLocationUrl=https://eidastest.eesti.ee/EidasNode/ServiceProvider&` 
`country=CD&` 
`RelayState=MyRelayState&` 
`SAMLRequest= ... &` 
`sendmethods=GET` | server saadab ümbersuunamiskorralduse |
| 5  | aut-ne ümbersuunamine | `GET https://ec.europa.eu/eid-integration-test/EidasNode/ColleagueRequest?` 
`SAMLRequest=...&` 
`RelayState=MyRelayState&` 
`token=...` | server saadab ümbersuunamiskorralduse | 
| 6  | `POST https://ec.europa.eu/eid-integration-test/IdP/AuthenticateCitizen` |   |
| 7  | `POST https://ec.europa.eu/eid-integration-test/EidasNode/SpecificIdPResponse` |  |
| 8  | `POST https://ec.europa.eu/eid-integration-test/AP/ConsentValue` |  |
| 9  | `POST https://ec.europa.eu/eid-integration-test/EidasNode/APSelector` |  |
| 10 | `POST https://eidastest.eesti.ee/EidasNode/ColleagueResponse` |  |
| 11 | `POST https://eidastest.eesti.ee/SP/ReturnPage` |  |
| 12 | `POST https://eidastest.eesti.ee/SP/populateReturnPage` |  |


Nr 2 vorm:

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

Nr 3 vorm:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| samlRequestBinding | get |
| samlRequestLocation |	https://eidastest.eesti.ee/EidasNode/ServiceProvider |
| citizenCountryCode |	CD |
| samlRequestXML | ... |

Nr 6 vorm:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLRequest | ... |
| signAssertion | false |
| encryptAssertion | true |
| messageFormat | eidas |

Nr 7 vorm:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | ... |
| username     | xavi |

Nr 8 vorm:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| callbackUrl | ... |
| strAttrList | |
| username | xavi |

Nr 9 vorm:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| strAttrList | |

Nr 10:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | |
| nodeType | |
| RelayState | |
| sAMLRequestTT | |
| samlResponseDecriptedXMLEdit | |

Nr 11 vorm:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | |
| RelayState | |

Nr 12 vorm:

| vormivälja nimetus | vormivälja väärtus |
|--------------------|--------------------|
| SAMLResponse | |


