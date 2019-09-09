---
permalink: MetadataSeletus
---

# eIDAS Node SAML metadata seletus
{: .no_toc}

Käesolev dokument seletab RIA eIDAS Node SAML metaandmeotspunktide kaudu pakutavate andmete koosseisu ja tähendust.

## Sisukord
{: .no_toc}

- TOC
{:toc}


## Kirjandus

[Metadata for the OASIS Security Assertion Markup Language (SAML) V2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf) | SAML metaandmete standard
[SAML V2.0 Metadata Guide](https://www.oasis-open.org/committees/download.php/51890/SAML%20MD%20simplified%20overview.pdf) | SAML metaandmete ülevaade
[Assertions and Protocols for the OASIS Security Assertion Markup Language (SAML) V2.0](http://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf) | ei kirjelda otseselt SAML metateavet, vaid SAML sõnumivorminguid
[XML Signature](https://en.wikipedia.org/wiki/XML_Signature) (Wikipedia) | ülevaade XML allkirjast
[XML Security Algorithm Cross-Reference](https://www.w3.org/TR/xmlsec-algorithms/) | standarditud algoritminimed

## Metaandmeotspunktid

eIDAS Node metaandmeotspunktid on järgmised:

| eIDAS vahendusteenus (_Proxy Service_) - suhtluses teise eIDAS Node-ga | [https://eidastest.eesti.ee/EidasNode/ServiceMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata)[https://eidas.eesti.ee/EidasNode/ServiceMetadata](https://eidas.eesti.ee/EidasNode/ConnectorMetadata) | "Will be used as Issuer in the requests that eIDAS-Node Proxy Service sends" |
| eIDAS konnektorteenus - suhtluses teise eIDAS Node-ga | [https://eidastest.eesti.ee/EidasNode/ConnectorMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata) [https://eidas.eesti.ee/EidasNode/ConnectorMetadata](https://eidas.eesti.ee/EidasNode/ConnectorMetadata) | "The URL at which the metadata of eIDAS-Node Connector will be made available. Will be used as Issuer in the requests that eIDAS-Node Connector sends." |
| eIDAS konnektorteenus - suhtluses siseriikliku liidestatud süsteemiga | [https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata) [https://eidas.eesti.ee/EidasNode/ConnectorResponderMetadata](https://eidas.eesti.ee/EidasNode/ConnectorMetadata) | "The URL at which the metadata of the eIDAS-Node Connector (presenting itself as an IdP) will be made available. Will be used as Issuer in the responses that eIDAS-Node Connector sends to SP." |

Ülalolevad tsitaadid on juhendist [eIDAS-Node Installation and Configuration Guide](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+-+Current+release?preview=/46992189/52603174/eIDAS-Node%20Installation%20and%20Configuration%20Guide.pdf).

Esitame alljärgnevalt kõigi otspunktide metaandmete struktuurid (teisendatud XML -> JSON -> YAML-laadne vorming).

## Demo SP metadata

```yaml
md:EntityDescriptor:
@xmlns:md: urn:oasis:names:tc:SAML:2.0:metadata,
@entityID: https://eidastest.eesti.ee/SP/metadata,
@validUntil: 2018-01-26T14:23:22.178Z,
ds:Signature:
    @xmlns:ds: http://www.w3.org/2000/09/xmldsig#,
    ds:SignedInfo:
    ds:CanonicalizationMethod:
        @Algorithm: http://www.w3.org/2001/10/xml-exc-c14n#
    ds:SignatureMethod:
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha512
    ds:Reference:
        @URI: ,
        ds:Transforms:
        ds:Transform:
            @Algorithm: http://www.w3.org/2000/09/xmldsig#enveloped-signature
            @Algorithm: http://www.w3.org/2001/10/xml-exc-c14n#
        ds:DigestMethod:
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha512
        ds:DigestValue: <räsiväärtus>
    ds:SignatureValue: <allkiri>
    ds:KeyInfo:
    ds:X509Data:
        ds:X509Certificate:  <sertifikaat>
md:Extensions:
    eidas:SPType: #text: public,
    @xmlns:eidas: http://eidas.europa.eu/saml-extensions
    alg:DigestMethod:
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha512
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha256
    alg:SigningMethod:
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha512
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-ripemd160
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha256
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport,
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512
md:SPSSODescriptor:
    @AuthnRequestsSigned: true,
    @WantAssertionsSigned: true,
    @protocolSupportEnumeration: urn:oasis:names:tc:SAML:2.0:protocol,
    md:KeyDescriptor:
        @use: signing,
        ds:KeyInfo:
        @xmlns:ds: http://www.w3.org/2000/09/xmldsig#,
        ds:X509Data:
            ds:X509Certificate: <sertifikaat>
        @use: encryption,
        ds:KeyInfo:
        @xmlns:ds: http://www.w3.org/2000/09/xmldsig#,
        ds:X509Data:
            ds:X509Certificate:  <sertifikaat>
        md:EncryptionMethod:
            @Algorithm: http://www.w3.org/2009/xmlenc11#aes192-gcm
            @Algorithm: http://www.w3.org/2009/xmlenc11#aes256-gcm
            @Algorithm: http://www.w3.org/2009/xmlenc11#aes128-gcm
    md:NameIDFormat:
    urn:oasis:names:tc:SAML:2.0:nameid-format:persistent,
    urn:oasis:names:tc:SAML:2.0:nameid-format:transient,
    urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified
    ],
    md:AssertionConsumerService:
    @Binding: urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST,
    @Location: https://eidastest.eesti.ee/SP/ReturnPage,
    @index: 0,
    @isDefault: true
md:Organization:
    md:OrganizationName:
    #text: DEMO-SP,
    @xml:lang: en
    md:OrganizationDisplayName:
    #text: Sample SP,
    @xml:lang: en
    md:OrganizationURL:
    #text: https://sp.sample/info,
    @xml:lang: en
md:ContactPerson:
    @contactType: support,
    md:Company: eIDAS SP Operator,
    md:GivenName: Jean-Michel,
    md:SurName: Folon,
    md:EmailAddress: contact.support@sp.eu,
    md:TelephoneNumber: +555 123456
    @contactType: technical,
    md:Company: eIDAS SP Operator,
    md:GivenName: Alphonse,
    md:SurName: Michaux,
    md:EmailAddress: contact.support@sp.eu,
    md:TelephoneNumber: +555 123456
```

## eIDAS konnektorteenus, suhtluses teise eIDAS Node-ga

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

## eIDAS konnektorteenus, suhtluses siseriikliku liidestatud süsteemiga

```yaml
md:EntityDescriptor:
  @xmlns:md: urn:oasis:names:tc:SAML:2.0:metadata
  @entityID: https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata
  @validUntil: 2018-01-24T15:50:08.731Z
  ds:Signature:
    @xmlns:ds: http://www.w3.org/2000/09/xmldsig#
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

    alg:DigestMethod:
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha512
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha256
  
    alg:SigningMethod:
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha512
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-ripemd160
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha256
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512

  md:IDPSSODescriptor:
    @WantAuthnRequestsSigned: true
    @protocolSupportEnumeration: urn:oasis:names:tc:SAML:2.0:protocol
    
    md:KeyDescriptor:
        @use: signing
        ds:KeyInfo:
          @xmlns:ds: http://www.w3.org/2000/09/xmldsig#
          ds:X509Data:
            ds:X509Certificate: <sertifikaat>                    
        @use: encryption
        ds:KeyInfo:
          @xmlns:ds: http://www.w3.org/2000/09/xmldsig#
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

    md:SingleSignOnService:
        @Binding: urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST
        @Location: https://eidastest.eesti.ee/EidasNode/ServiceProvider
        @Binding: urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect
        @Location: https://eidastest.eesti.ee/EidasNode/ServiceProvider
        
    saml2:Attribute:
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: D-2012-17-EUIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/D-2012-17-EUIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: EORI
        @Name: http://eidas.europa.eu/attributes/legalperson/EORI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LEI
        @Name: http://eidas.europa.eu/attributes/legalperson/LEI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalName
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalPersonAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalPersonIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalPersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: SEED
        @Name: http://eidas.europa.eu/attributes/legalperson/SEED
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: SIC
        @Name: http://eidas.europa.eu/attributes/legalperson/SIC
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: TaxReference
        @Name: http://eidas.europa.eu/attributes/legalperson/TaxReference
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: VATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/VATRegistration
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: VATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/VATRegistrationNumber
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeD-2012-17-EUIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/D-2012-17-EUIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeEORI
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/EORI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLEI
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LEI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalName
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalPersonAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalPersonIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalPersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeSEED
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/SEED
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeSIC
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/SIC
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeTaxReference
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/TaxReference
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeVATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/VATRegistration
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeVATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/VATRegistrationNumber
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: BirthName
        @Name: http://eidas.europa.eu/attributes/naturalperson/BirthName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: CurrentAddress
        @Name: http://eidas.europa.eu/attributes/naturalperson/CurrentAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: FamilyName
        @Name: http://eidas.europa.eu/attributes/naturalperson/CurrentFamilyName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: FirstName
        @Name: http://eidas.europa.eu/attributes/naturalperson/CurrentGivenName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: DateOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/DateOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: EidasAdditionalAttribute
        @Name: http://eidas.europa.eu/attributes/naturalperson/EidasAdditionalAttribute
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: Gender
        @Name: http://eidas.europa.eu/attributes/naturalperson/Gender
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: PersonIdentifier
        @Name: http://eidas.europa.eu/attributes/naturalperson/PersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: PlaceOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/PlaceOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeBirthName
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/BirthName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeCurrentAddress
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/CurrentAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeFamilyName
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/CurrentFamilyName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeFirstName
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/CurrentGivenName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeDateOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/DateOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeGender
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/Gender
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativePersonIdentifier
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/PersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativePlaceOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/PlaceOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
  
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

## eIDAS vahendusteenus (Proxy Service), suhtluses teise eIDAS Node-ga

```yaml
md:EntityDescriptor:
@xmlns:md: urn:oasis:names:tc:SAML:2.0:metadata
@xmlns:xs: http://www.w3.org/2001/XMLSchema
@entityID: https://eidastest.eesti.ee/EidasNode/ServiceMetadata
@validUntil: 2018-01-24T15:50:49.087Z

ds:Signature:
    @xmlns:ds: http://www.w3.org/2000/09/xmldsig#
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
            ec:InclusiveNamespaces:
                @xmlns:ec: http://www.w3.org/2001/10/xml-exc-c14n#
                @PrefixList: xs
        ds:DigestMethod:
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha512
        ds:DigestValue: <räsiväärtus>
    ds:SignatureValue: <allkirjaväärtus>
    ds:KeyInfo:
    ds:X509Data:
        ds:X509Certificate: <sertifikaat>                

md:Extensions:
    mdattr:EntityAttributes:
    @xmlns:mdattr: urn:oasis:names:tc:SAML:metadata:attribute
    saml2:Attribute:
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @Name: urn:oasis:names:tc:SAML:attribute:assurance-certification
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        saml2:AttributeValue:
        #text: http://eidas.europa.eu/LoA/substantial
        @xmlns:xsi: http://www.w3.org/2001/XMLSchema-instance
        @xsi:type: xs:string
    alg:DigestMethod:
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha512
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmlenc#sha256
    alg:SigningMethod:
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha512
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-ripemd160
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha256
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#rsa-sha384
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1
        @xmlns:alg: urn:oasis:names:tc:SAML:metadata:algsupport
        @Algorithm: http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512

md:IDPSSODescriptor:
    @WantAuthnRequestsSigned: true
    @protocolSupportEnumeration: urn:oasis:names:tc:SAML:2.0:protocol
    md:KeyDescriptor:
        @use: signing
        ds:KeyInfo:
        @xmlns:ds: http://www.w3.org/2000/09/xmldsig#
        ds:X509Data:
            ds:X509Certificate: <sertifikaat>                    
        @use: encryption
        ds:KeyInfo:
        @xmlns:ds: http://www.w3.org/2000/09/xmldsig#
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
    
    md:SingleSignOnService:
        @Binding: urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST
        @Location: https://eidastest.eesti.ee/EidasNode/ColleagueRequest
        @Binding: urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect
        @Location: https://eidastest.eesti.ee/EidasNode/ColleagueRequest
    
    saml2:Attribute:
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: D-2012-17-EUIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/D-2012-17-EUIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: EORI
        @Name: http://eidas.europa.eu/attributes/legalperson/EORI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LEI
        @Name: http://eidas.europa.eu/attributes/legalperson/LEI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalName
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalPersonAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: LegalPersonIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/LegalPersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: SEED
        @Name: http://eidas.europa.eu/attributes/legalperson/SEED
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: SIC
        @Name: http://eidas.europa.eu/attributes/legalperson/SIC
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: TaxReference
        @Name: http://eidas.europa.eu/attributes/legalperson/TaxReference
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: VATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/VATRegistration
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: VATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/VATRegistrationNumber
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeD-2012-17-EUIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/D-2012-17-EUIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeEORI
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/EORI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLEI
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LEI
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalName
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalAddress
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalPersonAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeLegalPersonIdentifier
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/LegalPersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeSEED
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/SEED
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeSIC
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/SIC
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeTaxReference
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/TaxReference
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeVATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/VATRegistration
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeVATRegistration
        @Name: http://eidas.europa.eu/attributes/legalperson/representative/VATRegistrationNumber
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: BirthName
        @Name: http://eidas.europa.eu/attributes/naturalperson/BirthName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: CurrentAddress
        @Name: http://eidas.europa.eu/attributes/naturalperson/CurrentAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: FamilyName
        @Name: http://eidas.europa.eu/attributes/naturalperson/CurrentFamilyName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: FirstName
        @Name: http://eidas.europa.eu/attributes/naturalperson/CurrentGivenName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: DateOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/DateOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: EidasAdditionalAttribute
        @Name: http://eidas.europa.eu/attributes/naturalperson/EidasAdditionalAttribute
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: Gender
        @Name: http://eidas.europa.eu/attributes/naturalperson/Gender
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: PersonIdentifier
        @Name: http://eidas.europa.eu/attributes/naturalperson/PersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: PlaceOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/PlaceOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeBirthName
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/BirthName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeCurrentAddress
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/CurrentAddress
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeFamilyName
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/CurrentFamilyName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeFirstName
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/CurrentGivenName
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeDateOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/DateOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativeGender
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/Gender
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativePersonIdentifier
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/PersonIdentifier
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri
        @xmlns:saml2: urn:oasis:names:tc:SAML:2.0:assertion
        @FriendlyName: RepresentativePlaceOfBirth
        @Name: http://eidas.europa.eu/attributes/naturalperson/representative/PlaceOfBirth
        @NameFormat: urn:oasis:names:tc:SAML:2.0:attrname-format:uri

md:Organization:
    md:OrganizationName:
    #text: Sample Country Proxy Service
    @xml:lang: en
    md:OrganizationDisplayName:
    #text: Service
    @xml:lang: en
    md:OrganizationURL:
    #text: https://service.sample/info
    @xml:lang: en

md:ContactPerson:
    @contactType: support
    md:Company: eIDAS ProxyService Operator
    md:GivenName: John
    md:SurName: Doe
    md:EmailAddress: contact.support@eidas-proxyservice.eu
    md:TelephoneNumber: +42 123456
    @contactType: technical
    md:Company: eIDAS ProxyService Operator
    md:GivenName: John
    md:SurName: Doe
    md:EmailAddress: contact.technical@eidas-proxyservice.eu
    md:TelephoneNumber: +43 123456
```
