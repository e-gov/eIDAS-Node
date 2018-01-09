---
permalink: Spetsifikatsioon
---

# RIA eIDAS konnektorteenus. Tehniline spetsifikatsioon

Konnektorteenuse kasutuselevõtmiseks soovitame kõigepealt proovida RIAs ülepandud demorakendust. Seejärel võiks proovida demorakenduse või RIA arendatava eIDAS klientrakenduse enda taristus üles panna ja läbi teha liidestamine RIA eIDAS test-konnektorteenusega. Selle käigus saate aru, kuidas SAML-sõnumivahetus käib. Alles seejärel arendada välja oma rakendus. Eeskujuks võib võtta nii demorakenduse kui ka RIA eIDAS klientrakenduse. Lõpuks lõimida liidest teostab komponent oma e-teenusesse. Tehniliste tegevuste järjekord on vastavalt:

1. Tutvu RIA-s ülespandud Demo SP rakendusega
2. Tutvu konnektorteenuse SAML metaandmetega
3. Proovi rakenduse ühendamist RIA test-konnektorteenusega
4. Kohanda rakendust oma vajadustele
5. Lõimi rakendus oma e-teenusega

## 1. Tutvu RIA-s ülespandud Demo SP rakendusega

(Tutvumine on soovitatav, kuid mitte kohustuslik). Demo SP (Demo Service Provider) on näide e-teenuse liidestamisest eIDAS konnektorteenusega. Rakenduse aadress on:

  [https://eidastest.eesti.ee/SP/populateIndexPage](https://eidastest.eesti.ee/SP/populateIndexPage)

- Uuri, kuidas SAML-päring moodustatakse, uuri päringu struktuuri
- Taustaks uuri eIDAS Node-i dokumentatsiooni [https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+-+Current+release](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+-+Current+release), sh dokumenti "eIDAS-Node Demo Tools Installation and Configuration Guide".

Näide.

1. Sisene Demo SP rakendusse: [https://eidastest.eesti.ee/SP/populateIndexPage](https://eidastest.eesti.ee/SP/populateIndexPage)
2. Vali `SP country` väärtuseks `EE` (e-teenusepakkuja asub Eestis)
3. Vali `Citizen country` väärtuseks `EE` (autenditav isik on Eesti e-ID kasutaja)
4. Vali `Submit`
5. Uuri koostatud SAML-autentimispäringu sisu. Selleks võid:
  - kopeeri välja `SAML request` sisu töövahendisse [SAML Tool Decoder](https://www.samltool.com/decode.php)
  - dekodeeri XML-i ja vaadata seda süntaksivärvitud kujul samas töövahendis [SAML Tool Pretty Print](https://www.samltool.com/prettyprint.php)

````xml
<?xml version="1.0"?>
<saml2p:AuthnRequest xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:ds="http://www.w3.org/2000/09/xmldsig#" xmlns:eidas="http://eidas.europa.eu/saml-extensions" xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" Destination="https://eidastest.eesti.ee/EidasNode/ServiceProvider" ForceAuthn="true" ID="_laDKCXYDH8N5cTAXriM-mMGdfen8K72PRXB0IGFBbMKIX.RoMMc6eBIIfqe084z" IsPassive="false" IssueInstant="2018-01-09T16:04:02.336Z" ProviderName="DEMO-SP-EE" Version="2.0">
  <saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">https://eidastest.eesti.ee/SP/metadata</saml2:Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    <ds:SignedInfo>
      <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
      <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
      <ds:Reference URI="#_laDKCXYDH8N5cTAXriM-mMGdfen8K72PRXB0IGFBbMKIX.RoMMc6eBIIfqe084z">
        <ds:Transforms>
          <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
          <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
        </ds:Transforms>
        <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
        <ds:DigestValue>wgi7RbVagNkKwogOMqNQgPdgQxyKujoG8c7gZbaDAIyw9zuMH9spccM6SfYFLs0JSGkgjReQMiy7xtoFJjBrqQ==</ds:DigestValue>
      </ds:Reference>
    </ds:SignedInfo>
    <ds:SignatureValue>QzDkIOgcV39ob6t61ERbyG0VspK60QFe02a+IrgPDoNDdhQNXhcrW5PPZW799wNxoc0qGmT8MfLuuy6F9Z9+Xxx/tigR7U4ArNe1z/CDRGWNaLBm5/aohCX03surk6EhW4bFhNdn5PhQx78akA01S2LG2KFRqCK9s7aebSBGnlUvC3cPBg25IpL70yNGaTxtSYUVmg2cqNjE9c5EHHwIUMqyQihc2J6ijDa//LGfb46j4qBGsns62yt2RhxG6HKIG25AIfjU/HpTmwCvoHEN+VAdMQ+AGWVWc4V0/cmwPhj03AMzuFE/dWLuaUNPngHX+I4rIISmNm9NrUNGpO91DA==</ds:SignatureValue>
    <ds:KeyInfo>
      <ds:X509Data>
        <ds:X509Certificate>MIIDaTCCAlGgAwIBAgIEH5P86jANBgkqhkiG9w0BAQsFADBlMQswCQYDVQQGEwJFRTELMAkGA1UE
CBMCRVUxCzAJBgNVBAcTAkVVMQswCQYDVQQKEwJTUDEOMAwGA1UECxMFU1RPUksxHzAdBgNVBAMT
FnNwLWVlLWRlbW8tY2VydGlmaWNhdGUwHhcNMTcwNTE5MTI1MDA3WhcNMTkwNTA5MTI1MDA3WjBl
MQswCQYDVQQGEwJFRTELMAkGA1UECBMCRVUxCzAJBgNVBAcTAkVVMQswCQYDVQQKEwJTUDEOMAwG
A1UECxMFU1RPUksxHzAdBgNVBAMTFnNwLWVlLWRlbW8tY2VydGlmaWNhdGUwggEiMA0GCSqGSIb3
DQEBAQUAA4IBDwAwggEKAoIBAQCqZC7CJi3WVCzsSh3mV9FNoVYgeiOIR65EAOapyyt7Xgqchn1s
Sppq3hszCbjgQ8mK1huDkw8iL5qmXg4rd6TUV4nxPuEVbX348Q2P7JH6UmHcedWElYvzbI2Yw388
v/dOwzp7jza8DaKBtAJlk8hY1riPbe4CfiOr5aTYbpyG0CsbnozRXpij562SNVkbvWz4EdW/3C5W
i73vHNRzvIoMgwF28YzCpf6DFMk5QpejSc+F6zsYeK1uMqNtJVxGybGhlq61BBmGMxFBmt0LdLWt
UOnzjqgB5/Y8cNShEk+yxT/QBMJ8BbO8vgO2InhFUyEBlbxzqGsvdP6BZJ37lzBfAgMBAAGjITAf
MB0GA1UdDgQWBBSF+NmEgjDnVSNucn9cFWU28xHG8DANBgkqhkiG9w0BAQsFAAOCAQEAP/cJT+ti
HTJ7aGESfSouKUccnsS89VKY4zu1Cj6IuGBOtsi7ORx8iBid3mFeX3bS9XcnK0kV3vbIEfXr2U9L
YHtAeERNwMk111y0sU2pnwHpWae5YX7cCBjbEd72CV7BQ5cPExUEdORGrpHrEE445o2LC7Nif0Qx
kO/2BFMlKJWsr2HyccYXWSFdyie3ar1HzkMGbebyK7cmRVTHqohNwPtVmS+bLcyjY5OiL/NVArGR
VP6DSep3h+/G6GnmrpeQxsLwolhASNLQbylifA8v6E3toHu9ditx9qynFFn9CeDT3g1LKhwQkB6/
GBVtKvFEAC4+O4APvtnMvjKhABpOOg==</ds:X509Certificate>
      </ds:X509Data>
    </ds:KeyInfo>
  </ds:Signature>
  <saml2p:Extensions>
    <eidas:SPType>public</eidas:SPType>
    <eidas:RequestedAttributes>
      <eidas:RequestedAttribute FriendlyName="D-2012-17-EUIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/D-2012-17-EUIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="EORI" Name="http://eidas.europa.eu/attributes/legalperson/EORI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="LEI" Name="http://eidas.europa.eu/attributes/legalperson/LEI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="LegalAdditionalAttribute" Name="http://eidas.europa.eu/attributes/legalperson/LegalAdditionalAttribute" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="LegalName" Name="http://eidas.europa.eu/attributes/legalperson/LegalName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
      <eidas:RequestedAttribute FriendlyName="LegalAddress" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="LegalPersonIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
      <eidas:RequestedAttribute FriendlyName="SEED" Name="http://eidas.europa.eu/attributes/legalperson/SEED" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="SIC" Name="http://eidas.europa.eu/attributes/legalperson/SIC" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="TaxReference" Name="http://eidas.europa.eu/attributes/legalperson/TaxReference" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="VATRegistration" Name="http://eidas.europa.eu/attributes/legalperson/VATRegistrationNumber" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="AdditionalAttribute" Name="http://eidas.europa.eu/attributes/naturalperson/AdditionalAttribute" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="BirthName" Name="http://eidas.europa.eu/attributes/naturalperson/BirthName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="CurrentAddress" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="FamilyName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentFamilyName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
      <eidas:RequestedAttribute FriendlyName="FirstName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentGivenName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
      <eidas:RequestedAttribute FriendlyName="DateOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/DateOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
      <eidas:RequestedAttribute FriendlyName="Gender" Name="http://eidas.europa.eu/attributes/naturalperson/Gender" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
      <eidas:RequestedAttribute FriendlyName="PersonIdentifier" Name="http://eidas.europa.eu/attributes/naturalperson/PersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
      <eidas:RequestedAttribute FriendlyName="PlaceOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/PlaceOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
    </eidas:RequestedAttributes>
  </saml2p:Extensions>
  <saml2p:NameIDPolicy AllowCreate="true" Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified"/>
  <saml2p:RequestedAuthnContext Comparison="minimum">
    <saml2:AuthnContextClassRef>http://eidas.europa.eu/LoA/low</saml2:AuthnContextClassRef>
  </saml2p:RequestedAuthnContext>
</saml2p:AuthnRequest>
````

6. SAML-autentimispäringust leiad Demo SP SAML-metaandmete otspunkti aadressi:

  [https://eidastest.eesti.ee/SP/metadata](https://eidastest.eesti.ee/SP/metadata).
  
  - Uuri, kuidas Demo-SP metaandmed on kirjeldatud.

## 2. Tutvu konnektorteenuse SAML metaandmetega

RIA eIDAS Node SAML metaandmete otspunkti aadress on:

  [https://eidastest.eesti.ee/EidasNode/ServiceMetadata](https://eidastest.eesti.ee/EidasNode/ServiceMetadata)

## 3. Proovi rakenduse ühendamist RIA test-konnektorteenusega

Kõigepealt tuleb rakendus liidestada konnektorteenuse testinstantsi vastu. Rakenduseks võib olla:
- eIDAS Node tarkvara koosseisus tarnitud Demo SP (Demo Service Provider) või
- RIA pakutav eIDAS klient [https://github.com/e-gov/eIDAS-Client](https://github.com/e-gov/eIDAS-Client) või
- enda väljatöötatud rakendus.

RIA-le tuleb esitada:
- metadata sertifikaat; RIA infraosakond lisab selle eIDAS Node võtmehoidlasse (_keystore_)
- aadress, kus liidestatav süsteem asub.

Liidestuja poolt võib olla vajalik:
- tulemüüris läbipääsu tegemine, et RIA poolt tulevad meta ja returnUrl oleks väljaspoolt kättesaadavad.

## 4. Kohanda rakendust oma vajadustele

(koostamisel)

## 5. Lõimi rakendus oma e-teenusega

(koostamisel)

