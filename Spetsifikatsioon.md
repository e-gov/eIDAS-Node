---
permalink: Spetsifikatsioon
---

# RIA eIDAS konnektorteenus. Tehniline spetsifikatsioon

(väljatöötamisel)

Mida on vaja oma rakenduse RIA eIDAS konnektorteenusega liidestamiseks?

__Tutvu RIA-s ülespandud Demo SP rakendusega__ (soovitatav). Demo SP (Demo Service Provider) on näide e-teenuse liidestamisest eIDAS konnektorteenusega. Rakenduse aadress on:

  [https://eidastest.eesti.ee/SP/populateIndexPage](https://eidastest.eesti.ee/SP/populateIndexPage)

- Uuri, kuidas SAML-päring moodustatakse, uuri päringu struktuuri
- taustaks uuri eIDAS Node-i dokumentatsiooni [https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+-+Current+release](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+-+Current+release), sh dokumenti "eIDAS-Node Demo Tools Installation and Configuration Guide".

__Tutvu konnektorteenuse SAML metaandmetega__. RIA eIDAS Node SAML metaandmete otspunkti aadress on:

  [https://eidastest.eesti.ee/EidasNode/ServiceMetadata](https://eidastest.eesti.ee/EidasNode/ServiceMetadata)

__Proovi rakenduse ühendamist RIA test-konnektorteenusega__.
Kõigepealt tuleb rakendus liidestada konnektorteenuse testinstantsi vastu. Rakenduseks võib olla:
- eIDAS Node tarkvara koosseisus tarnitud Demo SP (Demo Service Provider) või
- RIA pakutav eIDAS klient [https://github.com/e-gov/eIDAS-Client](https://github.com/e-gov/eIDAS-Client) või
- enda väljatöötatud rakendus.

RIA-le tuleb esitada:
- metadata sertifikaat; RIA infraosakond lisab selle eIDAS Node võtmehoidlasse (_keystore_)
- aadress, kus liidestatav süsteem asub.

Liidestuja poolt võib olla vajalik:
- tulemüüris läbipääsu tegemine, et RIA poolt tulevad meta ja returnUrl oleks väljaspoolt kättesaadavad.


