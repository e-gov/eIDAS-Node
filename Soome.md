---
permalink: Soome
---

`Proxy`

- keskne backend-i komponent
- loob, hoiab ja lõpetab SSO seansse
- suhtleb nii IdP-de kui ka SP-dega, samuti metateabeserveriga
- `SessionHandlingService` - keskne klass
- `MetadataService` - metateabeserverist loetud metateabe puhver
- `Session` - seansiandmete hoidja
- pakub REST-liideseid (`ProxyResourceImpl`), need teostavad projektis `Shared` määratletud liidest `ProxyResource`:
    - "/idp/init" - `fromIdPInitSession`
    - "/idp/session" - `fromIdPRequestSession`
    - "/idp/purge" - `fromIdPPurgeSession`
    - "/idp/attributes" - `getSessionAttributes`
    - "/sp" - `fromSPBuildSessionPost`

`SP`

- `bindingTemplateCX.html` - templiit, Shibboleth autentimispäringu saatmine SP-st redirect-ga
- `bindingTemplateFICountry.html` - "lipukesed"
- `ServiceProviderResource` - määrab otspunkti (`@Path`) `/saml`, millelt võtakse vastu SAML autentimisvastus
    - -> updateSessionAndGetRedirectUri
    - tid - token ID, pid - phase ID

Vtj - väestotietojärjestelmä
istunto - seanss
tunniste - token

