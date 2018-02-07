---
permalink: Usaldusehaldus
---

# Usaldusehaldus

## Vajadus

Süsteemis on vaja lahendada võtmehalduse (_key management_) ja "usaldusehaldus" (_trust management_) korraldus. Lahendus peab olema:
- piisavalt turvaline
- dokumenteeritud
- osapooltele kommunikeeritud
- tarkvaras teostatav
    - Märkus. Oluline piirang on meie tahtmine eIDAS Node-i tarkvara ümbertegemist vältida. 

Metoodika. Teatud (üpris üldisi) juhiseid saab väljaannetest:
- NIST 800-130 (2013) [A Framework for Designing Cryptographic Key Management Systems](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-130.pdf)
- NIST 800-57 Key Management Guidelines (2016)
    - [Part 1 Recommendation for Key Management](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-57pt1r4.pdf)
    - [Part 2 Best Practices for Key Management Organization](https://csrc.nist.gov/publications/detail/sp/800-57-part-2/final)
- vt ka [A Profile for U.S. Federal Cryptographic Key Management Systems](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-152.pdf)

## Lähteskeem

Tugineb koodi uurimisel

<img src='img/VOTMED-01.PNG' style='width:700px'>

## eIDAS Node koodi uurimise märkmed

***Teenusepakkuja SAML-autentimispäringu allkirja valideerimine konnektorteenuses***

3 sammu:
- valideerimine SAML-i profiili vastu. Tagab, et allkiri vastab XML allkirja standardile
- krüptograafiline valideerimine. Tagab, et allkirjastatud sõnumit ei ole muudetud
- usaldatavuse kontrollimine. Tagab, et allkirjastamisel kasutatud võti on usaldatav. Tehakse tavaliselt teist kanalit pidi saabunud võtmega võrdlemisel. Tavaliselt metateabes publitseeritud serdiga võrdlemisel. Vt [Verifying signatures with OpenSAML](http://blog.samlsecurity.com/2012/11/verifying-signatures-with-opensaml.html).

`processAuthenticationRequest` klassis `SpecificEidasConnector`. "This class is specific on the connector side and should be modified by each member state if they want to use any different settings."

->

`unmarshallRequestAndValidate`, klassis `ProtocolEngine`. "The protocol engine is responsible for implementing the protocol between the eIDAS Connector and the eIDAS ProxyService."

-> 

`unmarshallRequest` 

->

`validateSignature`, parameetritega `signedObject` ja `trustedCertificates(Collection<X509Certificate>)`, klassis `AbstractProtocolSigner`

->

`validateSignatureWithCredentials`

->

`verifyCryptographicSignature`

->

`getTrustedCertificate`

->

`checkTrust` (annab lihtsalt järje üle), klassis `CertificateUtil`

->

`validate`, OpenSAML 2 klassis `ExplicitKeyTrustEvaluator`

-> mitu edasisuunamist, päädib

````
boolean validate(Key untrustedKey, Key trustedKey) {
        return untrustedKey.equals(trustedKey);
    }
````

