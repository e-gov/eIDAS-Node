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
| 1  | pöördub Demo SP avalehe poole | GET `https://eidastest.eesti.ee/SP/populateIndexPage` | Demo SP server saadab avalehe |
| 2  | valib: e-teenuse riigi - `SP Country` -> `EE`; oma koduriigi - `Citizen Country` -> `CD`; nõutavad kasutajat kirjeldavad atribuudid - `Requested Core Attributes` -> `Mandatory`. Rida valikuid on seatud vaikimisi. Vajutab `Submit`. | `POST https://eidastest.eesti.ee/SP/IndexPage.action` | Demo SP server saadab lehe, millel näidatakse koostatavat SAML sõnumit |
| 3  | valib saatmismeetodi: `HTTP Redirect` | `POST https://eidastest.eesti.ee/SP/changeProtocolBinding.action` | Demo SP server saadab ümbersuunamiskorralduse |
| 4  | aut-ne ümbersuunamine | SAML sõnumi saatmine RIA eIDAS konnektorteenusesse: `GET https://eidastest.eesti.ee/EidasNode/ServiceProvider?` `postLocationUrl=https://eidastest.eesti.ee/EidasNode/ServiceProvider&` `redirectLocationUrl=https://eidastest.eesti.ee/EidasNode/ServiceProvider&` `country=CD&` `RelayState=MyRelayState&` `SAMLRequest= ... &` `sendmethods=GET` | konnektorteenus saadab ümbersuunamiskorralduse |
| 5  | aut-ne ümbersuunamine | SAML sõnumi saatmine CEF test-Node-i: `GET https://ec.europa.eu/eid-integration-test/EidasNode/ColleagueRequest?` `SAMLRequest=...&` `RelayState=MyRelayState&` `token=...` | CEF test-Node saadab ümbersuunamiskorralduse | 
| 6  | aut-ne ümbersuunamine | `POST https://ec.europa.eu/eid-integration-test/IdP/AuthenticateCitizen` | CEF test-Node saadab ümbersuunamiskorralduse |
| 7  | aut-ne ümbersuunamine | `POST https://ec.europa.eu/eid-integration-test/EidasNode/SpecificIdPResponse` | CEF test-Node saadab lehe, kus teatab, et tegu on eIDAS autentimisteenusega ja palub kasutaja nõusolekut (consent) protsessiga edasiminekuks |
| 8  | vajutab "Edasi" | `POST https://ec.europa.eu/eid-integration-test/AP/ConsentValue` | CEF test-Node saadab ümbersuunamiskorralduse |
| 9  | aut-ne ümbersuunamine | `POST https://ec.europa.eu/eid-integration-test/EidasNode/APSelector` | CEF test-Node saadab lehe, millel teatab, et "Login succeeded" |
| 10 | vajutab "Tagasi e-teenusesse"  | `POST https://eidastest.eesti.ee/EidasNode/ColleagueResponse` | RIA eIDAS konnektorteenus saabab ümbersuunamiskorraldus |
| 11 | aut-ne ümbersuunamine | `POST https://eidastest.eesti.ee/SP/ReturnPage` | Demo SP server saadab lehe, kus näidatakse saabunud SAML-sõnumit |
| 12 | vajutab "Edasi" | `POST https://eidastest.eesti.ee/SP/populateReturnPage` | Demo SP server saadab lehe, kus teatab "Login succeeded" ja kuvab SAML-autentimisvastuses saadud atribuudid |


POST päringute sisud:

nr 2 vorm:

eidasconnector | https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata
nodeMetadataUrl | https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata
citizenEidas | CD
returnUrl | https://eidastest.eesti.ee/SP/ReturnPage
eidasNameIdentifier | urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified
eidasloa | http://eidas.europa.eu/LoA/low
eidasloaCompareType | minimum
eidasSPType | public
allTypeEidas | true
< atribuutide kirjeldused > |

nr 3:

samlRequestBinding | get
samlRequestLocation |	https://eidastest.eesti.ee/EidasNode/ServiceProvider
citizenCountryCode | CD
samlRequestXML | ...

nr 6:

SAMLRequest | ...
signAssertion | false
encryptAssertion | true
messageFormat | eidas

nr 7:

SAMLResponse | ...
username     | xavi

nr 8:

callbackUrl | ...
strAttrList | 
username | xavi

nr 9:

strAttrList |

nr 10:

SAMLResponse |
nodeType |
RelayState |
sAMLRequestTT |
samlResponseDecriptedXMLEdit |

nr 11:

SAMLResponse |
RelayState |

nr 12:

SAMLResponse |


