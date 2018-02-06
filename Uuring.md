---
permalink: Koodiuuring
---

## Koodiuuring

eIDAS Node koodi uurimise märkmed

***Teenusepakkuja SAML-autentimispäringu allkirja valideerimine konnektorteenuses***

3 sammu:
- valideerimine SAML-i profiili vastu. Tagab, et allkiri vastab XML allkirja standardile
- krüptograafiline valideerimine. Tagab, et allkirjastatud sõnumit ei ole muudetud
- usaldatavuse kontrollimine. Tagab, et allkirjastamisel kasutatud võti on usaldatav. Tehakse tavaliselt teist kanalit pidi saabunud võtmega võrdlemisel. Tavaliselt metateabes publitseeritud serdiga võrdlemisel. Vt [Verifying signatures with OpenSAML](http://blog.samlsecurity.com/2012/11/verifying-signatures-with-opensaml.html).

`processAuthenticationRequest` | klassis `SpecificEidasConnector`. "This class is specific on the connector side and should be modified by each member state if they want to use any different settings."
`unmarshallRequestAndValidate` | klassis `ProtocolEngine`. "The protocol engine is responsible for implementing the protocol between the eIDAS Connector and the eIDAS ProxyService."
`unmarshallRequest` |
`validateSignature`, parameetritega `signedObject` ja `trustedCertificates(Collection<X509Certificate>)` | klassis `AbstractProtocolSigner`
`validateSignatureWithCredentials` |
`verifyCryptographicSignature` |
`getTrustedCertificate` |
`checkTrust` (annab lihtsalt järje üle) | klassis `CertificateUtil`
`validate` | OpenSAML 2 klassis `ExplicitKeyTrustEvaluator`

...
päädib

````
boolean validate(Key untrustedKey, Key trustedKey) {
        return untrustedKey.equals(trustedKey);
    }
````

