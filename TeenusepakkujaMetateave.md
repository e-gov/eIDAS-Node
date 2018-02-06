---
permalink: TeenusepakkujaMetateave
---

## Teenusepakkuja metateabe näide

Näide selgitab teenusepakkuja [metateabele kehtestatud nõudeid](Profiil#teenusepakkuja-metateave).

````xml
<md:EntityDescriptor entityID="https://eidas.asutus.ee/metadata" validUntil="2018-02-07T11:47:20.948Z">
    <ds:Signature>
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
            <ds:Reference URI="">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
                <ds:DigestValue>... </ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>...</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>...
                </ds:X509Certificate>
            </ds:X509Data>
        </ds:KeyInfo>
    </ds:Signature>
    <md:Extensions>
        <eidas:SPType>public</eidas:SPType>
        <alg:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
        <alg:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256"/>
        <alg:SigningMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512"/>
        <alg:SigningMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256"/>
        <alg:SigningMethod Algorithm="http://www.w3.org/2007/05/xmldsig-more#sha512-rsa-MGF1"/>
        <alg:SigningMethod Algorithm="http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1"/>
    </md:Extensions>
    <md:SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <md:KeyDescriptor use="signing">
            <ds:KeyInfo>
                <ds:X509Data>
                    <ds:X509Certificate>...</ds:X509Certificate>
                </ds:X509Data>
            </ds:KeyInfo>
        </md:KeyDescriptor>
        <md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat>
        <md:AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://eidas.asutus.ee/ReturnPage" index="0" isDefault="true"/>
    </md:SPSSODescriptor>
    <md:Organization>
        <md:OrganizationName xml:lang="en">Asutus</md:OrganizationName>
        <md:OrganizationDisplayName xml:lang="en">Estonian Authority</md:OrganizationDisplayName>
        <md:OrganizationURL xml:lang="en">https://www.asutus.ee</md:OrganizationURL>
    </md:Organization>
    <md:ContactPerson contactType="support">
        <md:Company>Asutuse kasutajatugi</md:Company>
        <md:GivenName>Jean-Michel</md:GivenName>
        <md:SurName>Folon</md:SurName>
        <md:EmailAddress>help@asutus.ee</md:EmailAddress>
        <md:TelephoneNumber>+372 123456</md:TelephoneNumber>
    </md:ContactPerson>
</md:EntityDescriptor>
````
