---
permalink: MetadataSeletus
---

# SAML metadata seletus

Käesolev dokument seletab RIA eIDAS konnektorteenuse SAML metaandmete otspunkti kaudu pakutavate andmete koosseisu ja tähendust.

Konnektorteenus kuulub eIDAS Node tarkvara koosseisus. eIDAS Node metaandmeotspunktid on järgmised:

eIDAS vahendusteenus (_proxy server_) | [https://eidastest.eesti.ee/EidasNode/ServiceMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata) <span style='color:green;'>&#10003;</span><br> [https://eidas.eesti.ee/EidasNode/ServiceMetadata](https://eidas.eesti.ee/EidasNode/ConnectorMetadata) <span style='color:red;'>&#10006;</span>
eIDAS konnektorteenus | [https://eidastest.eesti.ee/EidasNode/ConnectorMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata) <span style='color:green;'>&#10003;</span><br> [https://eidas.eesti.ee/EidasNode/ConnectorMetadata](https://eidas.eesti.ee/EidasNode/ConnectorMetadata) <span style='color:red;'>&#10006;</span>
eIDAS konnektorteenus, vastaja | [https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata) <span style='color:green;'>&#10003;</span><br> [https://eidas.eesti.ee/EidasNode/ConnectorResponderMetadata](https://eidas.eesti.ee/EidasNode/ConnectorMetadata) <span style='color:red;'>&#10006;</span>

[XML kujul](files/SAML-Metadata.xml) - [otse otspunktist](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata)

| kirjeldatav objekt | `entityID` | määrab kirjeldatava süsteemi (`https://eidastest.eesti.ee/EidasNode/ConnectorMetadata`) |
| (kehtiv kuni) | `validUntil` |  atribuudi abil juhitakse kui sageli metaandmete kasutaja peab käima värskendusi kontrollimas. Kas tasuks kasutada ka atribuuti `cacheDuration`? |
| metaandmete allkiri | `ds:Signature` |  |
| eIDAS tüüp | `eidas:SPType` | `public` Mida tähendab? |
| algoritmide toetus | (`md:Extensions` sees) | kasutades W3C standarditud XML turvaalgoritmide nimesid |
| räsimeetodid | `alg:DigestMethod` | 3 meetodit |
| allkirjastamismeetodid | `alg:SigningMethod` | 8 meetodit |
| rolli kirjeldus | `SPSSODescriptor` | rolli kirjeldus - Service Provider, SSO (?) |


| määrab, et autentimispäring p.o allkirjastatud | `@AuthnRequestsSigned: true` | |
| määrab, et tõend (_assertion_) on allkirjastatud | `@WantAssertionsSigned: true` | |
| toetatav protokoll | `@protocolSupportEnumeration: urn:oasis:names:tc:SAML:2.0:protocol` | |

| autentimisvastuse allkirjastamise võti (sertifikaat)  | `KeyDescriptor -> signing -> KeyInfo` | |
| autentimisvastuse krüpteerimise võti (sertifikaat)  | `KeyDescriptor -> encryption -> KeyInfo` | |
| krüpteerimisalgoritmid | `md:EncryptionMethod` | 3 tk - miks nii palju? |

| ??? | `md:NameIDFormat` | |
| ??? | `md:AssertionConsumerService` | |
| kontaktteave | `md:Organization`, `md:ContactPerson` | tuleks panna RIA reaalsed andmed |


Struktuur (teisendatud XML -> JSON -> YAML-laadne vorming)

```yaml
md:EntityDescriptor: 
  @entityID: https://eidastest.eesti.ee/EidasNode/ConnectorMetadata
  @validUntil: 2018-01-24T11:32:44.858Z

  ds:Signature: 
    ds:SignedInfo: 
      ds:CanonicalizationMethod: 
        @Algorithm: http://www.w3.org/2001/10/xml-exc-c14n#
      ds:SignatureMethod: 
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha512
      ds:Reference: 
        @URI: 
        ds:Transforms: 
          ds:Transform: 
              @Algorithm: http://www.w3.org/2000/09/xmldsig#enveloped-signature
              @Algorithm: http://www.w3.org/2001/10/xml-exc-c14n#
        ds:DigestMethod: 
          @Algorithm: http://www.w3.org/2001/04/xmlenc#sha512
        ds:DigestValue: <räsiväärtus>

    ds:SignatureValue: <allkirjaväärtus>

    ds:KeyInfo: 
      ds:X509Data: 
        ds:X509Certificate: <sertifikaat>

  md:Extensions: 
    eidas:SPType: public

    alg:DigestMethod: 
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#sha384
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha512
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha256
    
    alg:SigningMethod: 
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha512
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-ripemd160
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha384
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha256
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha384
        @Algorithm: http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512

  md:SPSSODescriptor: 
    @AuthnRequestsSigned: true
    @WantAssertionsSigned: true
    @protocolSupportEnumeration: urn:oasis:names:tc:SAML:2.0:protocol

    md:KeyDescriptor: 
        @use: signing
        ds:KeyInfo: 
          ds:X509Data: 
            ds:X509Certificate: <sertifikaat>
        @use: encryption
        ds:KeyInfo: 
          ds:X509Data: 
            ds:X509Certificate: <sertifikaat>
        md:EncryptionMethod: 
            @Algorithm: http://www.w3.org/2009/xmlenc11#aes192-gcm
            @Algorithm: http://www.w3.org/2009/xmlenc11#aes256-gcm
            @Algorithm: http://www.w3.org/2009/xmlenc11#aes128-gcm

    md:NameIDFormat: 
      urn:oasis:names:tc:SAML:2.0:nameid-format:persistent
      urn:oasis:names:tc:SAML:2.0:nameid-format:transient
      urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified

    md:AssertionConsumerService: 
        @Binding: urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST
        @Location: https://eidastest.eesti.ee/EidasNode/ColleagueResponse
        @index: 0
        @isDefault: true
        @Binding: urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect
        @Location: https://eidastest.eesti.ee/EidasNode/ColleagueResponse
        @index: 1

  md:Organization: 
    md:OrganizationName: 
      #text: Sample Country Connector
      @xml:lang: en
    md:OrganizationDisplayName: 
      #text: Connector
      @xml:lang: en
    md:OrganizationURL: 
      #text: https://connector.sample/info
      @xml:lang: en

  md:ContactPerson: 
      @contactType: support
      md:Company: eIDAS Connector Operator
      md:GivenName: John
      md:SurName: Doe
      md:EmailAddress: contact.support@eidas-connector.eu
      md:TelephoneNumber: +40 123456
      @contactType: technical
      md:Company: eIDAS Connector Operator
      md:GivenName: John
      md:SurName: Doe
      md:EmailAddress: contact.technical@eidas-connector.eu
      md:TelephoneNumber: +41 123456

      
```

Kirjandus

[SAML V2.0 Metadata Guide](https://www.oasis-open.org/committees/download.php/51890/SAML%20MD%20simplified%20overview.pdf)

[XML Security Algorithm Cross-Reference](https://www.w3.org/TR/xmlsec-algorithms/)
