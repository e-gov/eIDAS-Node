---
permalink: Spetsifikatsioon
---


Märkus. Lahtised küsimused on markeeritud sümbolitega `//`. 
{:.teade}

# RIA eIDAS konnektorteenuse liidese spetsifikatsioon
{: .no_toc}
v 0.5

- TOC
{:toc}

Käesolev juhend on suunatud riigiasutustele (edaspidi *teenusepakkuja*), kes soovivad välisriikide kodanikke oma infosüsteemi tarvis tuvastada otse eIDAS konnektorteenusega ühendudes. Juhend kirjeldab nõuded, isikustuvastusprotsessi tehnilisi detaile ja vajalikke arendustegevusi riikliku konnektorteenuesga liidestumiseks.

## eIDAS Node võrk

RIA eIDAS konnektorteenus on Eesti riigiasutustele mõeldud vahendusteenus eIDAS Node võrku.

eIDAS Node võrk on Euroopa Liidu liikmesriikide vahelise koosvõimeraamistiku (eIDAS Interoperability Framework [EIF]) alusel elektroonilist isikutuvastust pakkuv ühisteenus. Eidas Node võrk koosneb turvalise kanali abil ühendatud riiklikest sõlmpunktidest (ingl k eIDAS Node). Sõlmpunktid vahetavad omavahel iskutuvastuspäringuid ja kinnitusi vastava riigi kodaniku identiteedi kohta. Iga Euroopa Liidu liikmesriik opereerib vähemalt ühte riiklikku sõlmpunkti ja pakub kohalikele teenusepakkujatele välisriigi kodenike tuvastamiseks nn konnektorteenust.

Eestis pakub eIDAS Node võrguga liitumiseks konnektorteenust RIA.

## 3.1 Teenuse kasutusvoog

<img src='img/SUURPILT.PNG' style='width:600px'>

Joonis 1. eIDAS taristu

eIDAS konnektorteenus on ühendajaks asutuse autentimisrakenduse ja EL eIDAS-taristu vahel.

Autentimisrakenduse ja eIDAS konnektorteenuse vaheline suhtlus on osa eIDAS autentimisvoost (vt lisa 1), hõlmates sellest kahte sõnumiedastust:

- autentimispäringut esitava SAML tõendi (_token_) saatmine autentimisrakendusest eIDAS konnektorteenusele (vt joonisel lisas 1 sõnumiedastus nr 4);
- autentimisvastust esitava SAML tõendi saatmine eIDAS konnektorteenuselt autentimisrakendusele (vt joonisel lisas 1 sõnumiedastus nr 9).

Sõnumiedastus teostatakse veebisirvija ümbersuunamise (_redirect_) abil.

## Isikutuvastuse protsess

Eduka piiriülese isikutuvastamise protsess eIDAS Node võrgus hõlmab mitut osapoolt, kes järgivad eIDAS profiili raames kirjeldatud koosvõime nõudeid [eIDAS-interop]. Sõnumivahetus osapoolte vahel toimib [SAML 2.0] protokolli alusel.

Järgnevas näidisstsenaariumis (vt Joonis 2) on välja toodud edukas isikutuvastamise protsess SAML HTTP POST näitel. lihtsuse mõttes ei ole näidatud SAML protokolli kohaseid metadata otspunktide poole pöördumisi.

Edukas isikutuvastus eIDAS võrgustikus näeb välja järgmisena:

<img src='img/Autentimisvoog-eIDAS_demorakenduses.png'>

Joonis 2.

1. Kasutaja navigeerib teenusepakkuja lehele, mis nõuab piiriülest isikutuvastust. Kasutaja isik on tuvastamata, puudub kehtiv sessioon. Teenusepakkuja jätab soovitud URL-i meelde ja suunab kasutaja lehele, kus kuvatakse eIDAS võrguga liidestatud riikide nimekiri.

2. Kasutaja on valinud riigi ja saadab vormi teenusepakkujale. Teenusepakkuja moodustab isikutuvastamiseks vajaliku `SAMLRequest` parameetri sisu ja saadab kasutajale vormi ümbersuunamisega riiklikusse eIDAS Node'i (vormi parameetriteks `SAMLRequest`, `country` ja vajadusel ka `RelayState`). NB! `RelayState` on mittekohustulik parameeter, mille teenusepakkuja võib kaasa panna oma päringu oleku hilisemaks tuvastuseks sammul 10 (algse päringu saab tuvastada ka vastuses oleva SAMLResponse parameetris sisalduvas XML atribuudis InResponseTo, kuid see info on kodeeritud kujul)

3. Kasutaja suunatakse automaatselt riiklikku eIDAS Node'i, kus kontrollitakse `SAMLRequest` parameetris oleva SAML XML päringu sisu valiidsust ja allkirja vastu teenusepakkuja metainfos olevat avalikku võtit. Siseriiklik eIDAS Node moodustab uue SAML XML päringu, võttes aluseks teenusepakkujalt tuleva info ja allkirjastab selle oma privaatvõtmega. Siseriiklik eIDAS Node saadab kasutajale vastuseks vormi, mis on suunatud sihtriigi eIDAS Node'i vastu koos `SAMLRequest` parameetriga ja vajadusel `RelayState`-ga.

4. Kasutaja suunatakse automaatselt koos vormiga edasi sihtriigi eIDAS Node'i koos `SAMLRequest` ja `RelayState` parameetritega. piiriülene eIDAS Node teenus valideerib `SAMLRequest`-i parameetris kodeeritud kujul oleva XML päringu sisu ja allkirja. Piiriülene eIDAS Node teenus saadab vastuseks vormi kasutaja nõusoleku küsimiseks.

5. Kui kasutaja oli vormil esitatud andmete jagamisega nõus, saadab kasutaja kinnitamiseks päringu piiriülesele eIDAS Node teenusele. Piiriülene eIDAS Node teenus täiendab saadud `SAMLRequest`-i sisu ja saadab kasutajale vastuseks piiriülesele isikutuvastusteenusele ümbersuunamiseks mõeldud vormi.

6. Kasutaja suunatakse automaatselt edasi piiriülese isikutuvastusteenusepakkuja lehele koos `SAMLRequest` ja `RelayState` parameetritega. Teenusepakkuja saadab kasutajale vastuseks autentimismeetmete valiku (ID-kaart, paroolikaart, parool, Mobiil-ID vastavalt sellele, mida antud riigi teenusepakkuja toetab).

7. Kasutaja autendib ennast isikutuvastusteenuses (näiteks ID-kaardiga). Eduka tuvastuse korral teenusepakkuja tagastab vastusena ümbersuunamisvormi piiriülesse eIDAS Node teenusesse, millest pärines algne isikutuvastuse päring. Ümbersuundamisvormi `SAMLResponse` parameetris on teenusepakkuja poolt palutud info isiku kohta. `SAMLResponse` allkirjastatakse ja isiku andmed krüpteeritakse teenusepakkuja privaatvõtmega.

8. Kasutaja suunatakse automaatselt tagasi piiriülese eIDAS Node teenusese. `SAMLResponse` parameetri sisus olev XML sisu koos allkirjaga valideeritakse. Sisu dekrüpteeritakse. Moodustatakse uus `SAMLResponse` parameeter, mis allkirjastatakse ja mille sisu krüpteeritakse piiriülese eIDAS Node privaatvõtmega. Kasutajale saadetakse siseriiklikusse eIDAS Node'ile adresseeritud ümbersuundamisvorm koos `SAMLResponse` parameetriga.

9. Kasutaja suunatakse automaatselt tagasi siseriiklikku eIDAS Node teenusesse. `SAMLResponse` parameetri sisus olev XML sisu koos allkirjaga valideeritakse. Sisu dekrüpteeritakse. Moodustatakse uus `SAMLResponse` parameeter, mis allkirjastatakse ja mille sisu krüpteeritakse siseriikliku eIDASNode privaatvõtmega. Kasutajale saadetakse siseriiklikule teenusepakkujale adresseeritud ümbersuundamisvorm koos `SAMLResponse` parameetriga.

10. Kasutaja suunatakse automaatselt tagasi siseriikliku teenusepakkuja lehele. Teenusepakkuja veendub `SAMLResponse` parameetri sisu vastavuses nõuetele (sh formaadi, sisu ja allkirja kontrollid). Teenusepakkuja dekrüpteerib isikuandmed ja otsustab, kas käesolev isik on õigustatud ligi pääsema algselt küsitud ressursile. Kui jah, siis luuakse sessioon ning tagastatakse kasutajale esialgselt URL-lt soovitud sisu.

## Vajalikud liidestamistööd teenusepakkujale

### Nõuded liituvale teenusepakkujale

Liidestuv teenusepakkuja peab vastama eIDAS profiilis toodud koostöövõime nõuetele SAML protokolli kasutuse ja krüptoalgoritmide osas (vt [eIDASprofiil](#eIDASprofiil)). eIDAS Node kasutab suhtlemiseks täiendavate kitsendustega [SAML 2.0] protokolli.

Kokkuvõtlikult peab teenusepakkuja:

* pakkuma SAML metadata otspunkti teenust

* omama SAML päringute ja vastuste töötlemise võimekust.

### Sõnumivahetusmeetod

Siseriiklik eIDAS konnektorteenus toetab teenusepakkuja poolt algatatud `HTTP POST` põhinevat sõnumivahetusmeetodit (vt. `HTTP POST binding` [SAML 2.0] standardikirjelduses - ptk 5.12).

### Metadata otspunkt

Teenusepakkuja SAML metadata on XML dokument, mis sisaldab konnektorteenuse jaoks kogu ühendumiseks vajaliku info (vt Näidis 1). Sealhulgas kirjeldab sertifikaadi päringu allkirjastamiseks, autentimise algatamise ning vastuse vastuvõtu URL-id ja soovi korral teenusepakkuja kontaktid. Metainfo on allkirjastatud.

Teenusepakkuja peab konnektoreenusele kättesaadavaks tegema oma metaandmed üle HTTPS protokolli.

Täpsemad nõuded metadata otspunktile on toodud sertifikaatide ja usaldusahela kohta on toodud dokumendis [eIDAS siseriiklik usaldusprofiil].

Näidis 1. Teenusepakkuja metadata otspunkti vastus

```xml
<?xml version="1.0" encoding="UTF-8"?>
<md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata" validUntil="2018-01-13T11:28:40.275Z">
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
            <ds:Reference URI="">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
                <ds:DigestValue>TCdkR0r1TOBgyoVBtbsbrMRGqGstXOrpIwJZ2RtnC5riPaBFxcSjQ4N9ZJ7kYLLaZ4Xbn+wCqBfxruWB/hGZkw==</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>      <ds:SignatureValue>Txvbu8pUwo4t8l3ZOKUqfdYo9p6aSi+Oh+zdGCGRW6grLgOvzHWWevVaSG/1VA1MteEaYDGUh2uBPWsXGOiaEx5MbCHGqgbCSqL+aWn8M1rg1JRvpGD1VosS+nIr48RExfZorIQT8ajMMvzTdZy+t+xNS1vlLwy8BGRusOOTLZ/QPugOcI4rkRH1xaMgij/DRLNVr6us6DfHhnEAed64h3Tyh2tOwx/4VC7RhOCGrG88IG7f2Sp88ij25N9WVpqVz2NHTtiF3SNn+X9WQq3L6BiMFD8s4z6qeaBV6Opyk11xou2W3+chBQ246VicGkmRAyK8aDjMawxfCDX62XiS6y8TQ09W9/CTReZ2SlC4XLtHLXaa+fMniReKJLMM2FGVHO8C86I3D+Af3KvheK98EgpQBuIvvjB8DDAQOaQlMtVTCOmRDcEdiIbKypMqeKTuYVgi+DCH0K1YXXhWejaCHye52f44DEQ+y3PmbjHJ40SRQgyx94zDcVlkZwuAA9ei8dPZmz4Zw5kjXLa0cQsJY8uxzL6Yesdb1Tuojr19lGyA6cckM5x5VYA3Bf45YfEyMKpDSHla+cf+PmaAxr68BdS2P8/5C2570B9rqrV6yUoOImmicyRKxfjW4Tr7Zt5f1CH/O/GljHUbIzzeztpr7OHC7QvakfAlTdy64pRRtDc=</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MIIFVTCCAz2................fRXxxZw==</ds:X509Certificate>
            </ds:X509Data>
        </ds:KeyInfo>
    </ds:Signature>
    <md:Extensions>
        <eidas:SPType xmlns:eidas="http://eidas.europa.eu/saml-extensions">public</eidas:SPType>
    </md:Extensions>
    <md:IDPSSODescriptor WantAuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <md:KeyDescriptor use="signing">
            <ds:KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
                <ds:X509Data>
                    <ds:X509Certificate>MIIDdTCCAl2gAw....................SMUatg==</ds:X509Certificate>
                </ds:X509Data>
            </ds:KeyInfo>
        </md:KeyDescriptor>
        <md:KeyDescriptor use="encryption">
            <ds:KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
                <ds:X509Data>
                    <ds:X509Certificate>MIIDdTCCAl2gAwIBAg..................SMUatg==</ds:X509Certificate>
                </ds:X509Data>
            </ds:KeyInfo>
            <md:EncryptionMethod Algorithm="http://www.w3.org/2009/xmlenc11#aes192-gcm"/>
            <md:EncryptionMethod Algorithm="http://www.w3.org/2009/xmlenc11#aes256-gcm"/>
            <md:EncryptionMethod Algorithm="http://www.w3.org/2009/xmlenc11#aes128-gcm"/>
        </md:KeyDescriptor>
        <md:NameIDFormat>urn:oasis:names:tc:SAML:2.0:nameid-format:persistent</md:NameIDFormat>
        <md:NameIDFormat>urn:oasis:names:tc:SAML:2.0:nameid-format:transient</md:NameIDFormat>
        <md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat>
        <md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://eidastest.eesti.ee/EidasNode/ServiceProvider"/>
        <md:SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://eidastest.eesti.ee/EidasNode/ServiceProvider"/>
        <saml2:Attribute xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" FriendlyName="D-2012-17-EUIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/D-2012-17-EUIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"/>
        .................
        <saml2:Attribute xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" FriendlyName="RepresentativePlaceOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/representative/PlaceOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"/>
    </md:IDPSSODescriptor>
    <md:Organization>
        <md:OrganizationName xml:lang="en">Sample Country Connector</md:OrganizationName>
        <md:OrganizationDisplayName xml:lang="en">Connector</md:OrganizationDisplayName>
        <md:OrganizationURL xml:lang="en">https://connector.sample/info</md:OrganizationURL>
    </md:Organization>
    <md:ContactPerson contactType="support">
        <md:Company>eIDAS Connector Operator</md:Company>
        <md:GivenName>John</md:GivenName>
        <md:SurName>Doe</md:SurName>
        <md:EmailAddress>contact.support@eidas-connector.eu</md:EmailAddress>
        <md:TelephoneNumber>+40 123456</md:TelephoneNumber>
    </md:ContactPerson>
    <md:ContactPerson contactType="technical">
        <md:Company>eIDAS Connector Operator</md:Company>
        <md:GivenName>John</md:GivenName>
        <md:SurName>Doe</md:SurName>
        <md:EmailAddress>contact.technical@eidas-connector.eu</md:EmailAddress>
        <md:TelephoneNumber>+41 123456</md:TelephoneNumber>
    </md:ContactPerson>
</md:EntityDescriptor>

```

### Isikutuvastuse päring

Nõutud parameetrite loetelu HTTP POST päringus on toodud Tabelis 1.

`SAMLRequest`  parameetris saadetav SAML XML päringu sisu (vt Näidis 2) peab vastama eIDAS sõnumiformaadi kirjeldusele (vt [eIDAS formaat]: ja [eIDAS-attr]). Kasutatavad krüptoalgoritmid peavad vastama [eIDAS krüpto] dokumendis toodule.

Tabel 1 - Päringu parameetrid

| Parameetri nimi        | Kohustuslik           | Selgitus  |
| ------------- |:-------------:| -----:|
| `SAMLRequest` |	Jah | SAML protokolli spetsiifiline parameeter, mis sisaldab Base64 kodeeritud kujul SAML XML päringut (`AuthnRequest` koos detailidega). `AuthnRequest` päringus olevate kohustuslike elementide ja kitsenduste loetelu on toodud tabelis 2.  SAML `AuthnRequest` peab olema allkirjastatud teenusepakkuja privaatvõtmega ja moodustatud vastavalt eIDAS nõuetele.|
| `RelayState` | Ei | SAML protokolli spetsiifiline parameeter, fikseeritud pikkusega tekst, mille eIDAS Node vastuses töötlemata tagasi peegeldab. |
| `country` |	Jah | Kodaniku riigikood, kelle isikut tuvastatakse. ISO 3166-1 alpha-2 standardi alusel. |

Tabel 2 - SAML `AuthnRequest` parameetrid

| XML elemendi/atribuudi nimi       | Kohustuslik           | Selgitus  |
| ------------- |:-------------:| :-----|
| /saml2p:AuthnRequest/@Destination	| Jah | Siseriikliku eIDAS Node-i SSO otspunkti aadress. Bindingule vastavad otspunktide aadressid on loetletud eIDAS Node metadatas (/md:EntityDescriptor/md:IDPSSODescriptor/md:SingleSignOnService) |
| /saml2p:AuthnRequest/@ForceAuthn | Jah | Konstantantne väärtus: 'true' |
| /saml2p:AuthnRequest/@ID | Jah | Unikaalne päringu XML ID. Peab vastama NCName kitsendustele (https://www.w3.org/TR/1999/REC-xml-names-19990114/#NT-NCName) |
| /saml2p:AuthnRequest/@IsPassive | Jah | Konstantne väärtus: 'false' |
| /saml2p:AuthnRequest/@IssueInstant | Jah | Päringu koostamise aeg. |
| /saml2p:AuthnRequest/@ProviderName | Jah | Teenusepakkuja nimetus. Lepitakse kokku RIA-ga. |
| /saml2p:AuthnRequest/@Version | Jah | Konstantne väärtus: '2.0' |
| /saml2p:AuthnRequest/saml2:Issuer | Jah | Teenusepakkuja metadatale viitav URL. |
| /saml2p:AuthnRequest/ds:Signature/* | Jah | Teenusepakkuja privaatvõtmega antud allkiri ja sellega seotud detailid. Toetatud krüptoalgoritmid on loetletud eIDAS Node metadatas (/md:EntityDescriptor/md:Extensions/alg:SigningMethod/*) |
| /saml2p:AuthnRequest/saml2p:Extensions/eidas:SPType | Jah | Konstantne väärtus: 'public' (avaliku sektori asutus) |
| /saml2p:AuthnRequest/saml2p:Extensions/eidas:RequestedAttributes/* | Jah | Kirjeldab ära, milliseid isikuandmete atribuutide soovitakse ülepiirilise identiteedipakkuja käest. Nimekiri toetatud atribuutidest, on toodud eIDAS konnektorteenues metadatas (vt /md:EntityDescriptor/md:IDPSSODescriptor/saml2:Attribute). Kohustuslik info, mida identiteediteenuse pakkuja peab tagastama, peab olema märgistatud atribuudiga `isRequired = 'true'` |
| /saml2p:AuthnRequest/saml2p:NameIDPolicy | Jah | Üks eIDAS konnektoreenuse metadatas kirjeldatud toetatud väärtustest (/md:EntityDescriptor/md:IDPSSODescriptor/md:NameIDFormat) |
| /saml2p:AuthnRequest/saml2p:RequestedAuthnContext | Jah |	// Variandid. Vajab analüüsi // |

Näidis 2 - SAMLRequest parameetris esitatav isikutuvastuse päring (dekodeeritud kujul)

```xml
<saml2p:AuthnRequest xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:ds="http://www.w3.org/2000/09/xmldsig#" xmlns:eidas="http://eidas.europa.eu/saml-extensions" xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" Destination="http://localhost:8080/EidasNode/ServiceProvider" ForceAuthn="true" ID="_eLkJmjOUF8ONvJqZ9EznURG6sAR_xSBIotsa3oWp1ptBbGw3O0iRZPogyRsxbHx" IsPassive="false" IssueInstant="2018-01-05T13:42:08.036Z" ProviderName="DEMO-SP-CA" Version="2.0">
    <saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">http://localhost:8080/SP/metadata</saml2:Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
            <ds:Reference URI="#_eLkJmjOUF8ONvJqZ9EznURG6sAR_xSBIotsa3oWp1ptBbGw3O0iRZPogyRsxbHx">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
                <ds:DigestValue>Ux9px3sqV4ClQvz8SYaMCzAs4EHUc4jUVrmuDJwbF4iNzd5y2g/LJr9b4lZmU5JtVr5gXWW8ICYZZPo5dXx3PA==</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
 <ds:SignatureValue>Vygk7AAi54eP6hDtpLK5h2ThcIPjYMuD2DGXITxKoSmQGuGu530VDe/VpwSmIOHHPgkDNLqYxtFufZ355I60281Zx/tHrjG2mMF6Njrm/a175zLl5TqOEhHcEj2PuWdlRFA7NI2eUttFNSKYry1K165dHZMZMsKmpl38IObSzifcExaZwaOoevmRmks53Y3A4bzYhBEKh5FtEjsEiLLDPhqX9z9eLp6iqGpbISv42FVq+2o0OGT+4w2ibWym4RON1HtsZS9BODH1EhIhDyKJzhrPBrOCn0qNGucXYy+5JcKeqwUMf2qClCBVO/Iwo3gnX+gPrBrYHH6Vc98JVJE2OZlRwly9gThOdbTZj/9JAeH82/CaJg4rK0aanr/tT39Rs0ASrmkjDeD2eVaTMvgV7hNKJv1qgJY/MJXOlv3No9CHD4PasPwJi57IS5VhllWVI8bek0aVnyOb8WvXFtEEKdlmzfoUa81ydolN6MnnjhSEIRoo7FCgwY9rNrBqhHuGBnzJkQIAovd7Ktc8T/zbhRVsaJBlNRX/5JEqhtR0CmHlB+rcMH0X1lrMTzZ+dELahCVvekdCeQ2qItN+rlwbQJGgKhFbQCaywLhjgyIysNlyKHwpX9PZCQOfKvOFTE65RxKQkj8fXkxoDwIf04kaRQViY8WnGowXzZwAwxBvhhA=</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MIIFQTCCAykCBFTI+zMwDQYJKoZIhvcNAQENBQAwZTELMAkGA1UEBhMCQ0ExCzAJBgNVBAgMAkVV MQswCQYDVQQHDAJFVTELMAkGA1UECgwCU1AxDjAMBgNVBAsMBVNUT1JLMR8wHQYDVQQDDBZzcC1j YS1kZW1vLWNlcnRpZmljYXRlMB4XDTE1MDEyODE1MDczMVoXDTE2MDEyODE1MDczMVowZTELMAkG A1UEBhMCQ0ExCzAJBgNVBAgMAkVVMQswCQYDVQQHDAJFVTELMAkGA1UECgwCU1AxDjAMBgNVBAsM BVNUT1JLMR8wHQYDVQQDDBZzcC1jYS1kZW1vLWNlcnRpZmljYXRlMIICIjANBgkqhkiG9w0BAQEF AAOCAg8AMIICCgKCAgEAlBqiPPAgV/fMWR5xyXapUGiQ9bmbHhl9wUsthn4/NaQjuoY4q32iRWvy 6MWQbtxrfJ4NBlih4n4ZaCD0PiFxK9AZU2iWTqqFJd4Nol+0TB8YOP81a8XOLiu1E0gwjEe71Orc ppw6ZnALT63pDZbGmSfjGzdS0TurUkHNqLKcmanPJjaPQMajHwFlCteiswMrSunw4+wTcHgIl028 uoUx72sh/O69huPHbf1295HOq+pfU9edsk5r/Vxi3bjv6sPutB3jZjMYHfdioyJe6F0exggN+LqL K1WiT2k/RAkoSSQmC1DQqi3vbxVL4WocGJYRilPjhaEfhiEKv37FZ6WV43U5kd8V3tt/YcFmuEMK fXKKPQwZTZn1GXkSWHnhTDO9zBA8nIIljoq3Ue5DuUhT64GEGqGDvoJoAYtykecj+rxfvN2dYjKh bIoNpjDrkdlDq4xwf/wXRhTIWyDRCzLgU4vxgXJtBKrk4O5gI1prUjPtx6+rq7csb2DShDn+HFpd 5rmGK4iPn9+ss6X3yzls1NBQaL4O1MI8TSDTgZ+LuWzSqlASOwFEmoQWGZcHo5/ZxXBrYuQ7sw2x btmfvHbmvxpO/9U9KoKNyjrSp0jcFfRKf3iNvRU+iYL0AzgP5dLBQPWVxjAFOGjwNkju1VAtj/3H Mu1aBwbyKmghlQwW5DsCAwEAATANBgkqhkiG9w0BAQ0FAAOCAgEARe50F20MJPAqcyNiN3+XQizn yeZJ1MlJnSrHqq1yiBPFvD5fIKLAC07kPrSC9fnNQnt6J79mCA9IRJwVDYOs4SDRrwTikVmk1eVd 1m8fxvsJ8Yfzh7wqbC6Maf7bWeLqFDeq8EgycOrubROvRIuENbQQmWJfoe8iHx27XcxJPehPninY gGfPbOKHUd/08nzI3gnJnUMGvG9hNORqloM20wajTW3fC6xeiRqg/21S51tMW8ETInejNIE6NY+B 9r8E4DpRLigxc0XjzAdC4L80/Al+ImVOwGdpevomWJ1FWarINVtSEw2cDzvnQ+moHPM0Yk9wran0 6yx6uQlxo/7MZ+mG7wulRQ5vGWducw+ktM7tUodlebTI01iG09+9tadEQp/ryilNkbw+wT+xPVuF fCRfhqLxnjvuN6FeJF76/7y7D5RmBG5wBHo8qWnyHVzHPSGvTxZPrLupGB9vzxTbPt6pbDNv3VVA ky0/zpqw1vs0P3rmEb5KsFo9dwCAQELVCiqDle9xkGc0Jr6eCS6W2qMQ4QF4eUHVD8q5tNr0dhZA 3nsjLnyuvcWFiTdZnMOsozXfRiN3AKIIkxML3t7pp6VbKg50LgZGv3GvbbR+HJsRCACxHm88PSxu rfNo9ZTIPz25yJANDcHzWhQXWxJC3gcYvyW8HOvSLVlAzUFuCcE=</ds:X509Certificate>
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
```

### Isikutuvastuse vastus

Õnnestunud või ebaõnnestunud isikutuvastuse tulemus saadetakse teenusepakkuja vastus URL'le tagasi (toodud teenusepakkuja metadata's) `SAMLResponse` parameetris (vt Näidis 3 ja 4). Kõigi parameetrite loetelu on toodud Tabelis 3.

Tabel 3 - Isikutuvastuse vastuse parameetrid

| Parameetri nimi        | Kohustuslik           | Selgitus  |
| ------------- |:-------------:| -----:|
| `SAMLResponse` | Jah | Parameeter, mis sisaldab Bas64 kodeeritud SAML vormingus isikutuvastuse vastust. SAML vastuses olev `Response` on allkirjastatud ja isiku kohta käivad väited krüpteeritud (eIDAS Node privaatvõtmega). |
| `RelayState` | Ei | SAML protokolli spetsiifiline parameeter, fikseeritud pikkusega tekst, mille teenusepakkuja autentimispäringu algatamisel ette andis (peegeldatakse teenusepakkuja poolt tagasi töötlemata kujul). |


Näidis 3. Dekodeeritud `SAMLResponse` parameetri sisu eduka isikutuvastuse korral.

```xml
<saml2p:Response xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:ds="http://www.w3.org/2000/09/xmldsig#" xmlns:eidas="http://eidas.europa.eu/attributes/naturalperson" xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" Consent="urn:oasis:names:tc:SAML:2.0:consent:obtained" Destination="http://localhost:8080/SP/ReturnPage" ID="_yiiuyi.nIKvC24mjq693FymZFsmzVeryieoeDD7LRqrCX16OrT2I-cP7x63wfgu" InResponseTo="_eLkJmjOUF8ONvJqZ9EznURG6sAR_xSBIotsa3oWp1ptBbGw3O0iRZPogyRsxbHx" IssueInstant="2018-01-05T13:42:44.472Z" Version="2.0">

    <saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity" xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">http://localhost:8080/EidasNode/ConnectorResponderMetadata</saml2:Issuer>

    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">

        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
            <ds:Reference URI="#_yiiuyi.nIKvC24mjq693FymZFsmzVeryieoeDD7LRqrCX16OrT2I-cP7x63wfgu">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
                <ds:DigestValue>t9kt+8h5m3r0reIBKgy5KmfRmmQoXxQNpgSMMdn/PD2nb87VVFGqHP1qKEUMmXAjnwLmZzEfydRoG/IeUXbGkg==</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>TnUoQu+qzCQCw7Ka9QeVYG+iFPmpG2k8JBuAJb3ndzzHnJAiKXEajRWG6YNx6ryPRQWboxMeo2OSM6lzS+5tYQuSV/Tmfty+Xl08WkhNlEUxJSNE90jit6gUU/ChmpZ0lI4y+hYwsU93Phg/EE6buVltn7MhoNB9LSIDlOFPDt9iZKRNjPIXlQLNQtxL62RDN8+qUVeSSBpcmiTpqXaR6ki5noOL5xokuV4/h3MSd1QkzwxmwP7EYX6nJlcN80UEhO0Thp1xlIbuEBdhRT1FnF1ZkQN5rnmzmJTK9BnXbk45g4MWK76IoclZmZS07qYiZRZ4uZecxFrnhR/4x3g+H53mdMBYlMSRYPHH803U1Eu/b08yMAvz7A/NprO7Zky3304HOSNdv0jJpddxFkYtiEqvHS6jCxQQ+vfVdWpw5CMe+JHAzJr5Z3HoJDVjyRC2HakypmAXS/zLYRulC8aPj4zCp6r/2TWrISArzTCdlUnh/Y5Ygv1kpdAAfyD3xoU+vePtxZTu2OMiDicaWhc4phU5f+ScyNbbIUFJVpVs9M3Qj+eiphY23hAcc7Lk5fIO4+aKarCy8tttVDrWJh4MvR7MbUK8UQUTxiK3V/ihOsV/hKN+flCKs1Qr8CUD6HSM3XUbKIJRmWMQVyV1EYlqK2BhOsEje6dcpEeYZAmVA+0=</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MIIFTTCCAzUCBFTI/IgwDQYJKoZIhvcNAQENBQAwazELMAkGA1UEBhMCQ0ExCzAJBgNVBAgMAkVV MQswCQYDVQQHDAJFVTEOMAwGA1UECgwFU1BFUFMxDjAMBgNVBAsMBVNUT1JLMSIwIAYDVQQDDBlz cGVwcy1jYS1kZW1vLWNlcnRpZmljYXRlMB4XDTE1MDEyODE1MTMxMloXDTE2MDEyODE1MTMxMlow azELMAkGA1UEBhMCQ0ExCzAJBgNVBAgMAkVVMQswCQYDVQQHDAJFVTEOMAwGA1UECgwFU1BFUFMx DjAMBgNVBAsMBVNUT1JLMSIwIAYDVQQDDBlzcGVwcy1jYS1kZW1vLWNlcnRpZmljYXRlMIICIjAN BgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAhFXC/GDKjHA0aXwU+xIv+2GyfSjQ2L1ZS1asC5AY OLa1PC2TWiljbOj1vRXGlNXG4ue91nQkpMBNg3nMmZtdhP2vfEtT+9V2INQR+1aQ8plPGWNIOX1O 5NZ/F2M/daAZ2K3Y+WSKLin7FCdBL6Bw0IGFmQUeblIrumMoeRpQhXBfrDHyFW/ozNeB6M9x2bTD mk/hjJO8l/auxsPjYzU2+rENS+kEKr10HRy8MTqtfcqVft5blCAUOeDctvsf/5s37JK2MBGjgX6X mchLQylOpUZrBo7cNTfc77RjobdgN5rWUyZ8WeEYHtfyUo+nF7DHP2euiuhueFW7Gind+MrcAudI JI5r27x4jhneyul1a5OWbGUP84lOoIpzCkvE0w7HbpItDxbnuX09KBmm8lKUingB5CaqG5D+APCW T3emo1HIkLX3Jq8OLXNu7c9jPB1xGk4b6ZC2fhCLwvv37bAahWZ81WkqzWLaZpyAKZ+vRCa7WMtt HfhVEJmEU/wCgfvv2l1mvCz61H6AQWzEd8ZLwiZnljgDEtxq8sRNZHQVPvBnC9TKyzy6D1/+sZ7R 8p4AxFHX5UxG+qEtEiz5qXFswXU8gBsKpDO9cG6n7C8Bg8uhsx1TfWJeoe1mtJpKTAvJbqE/O2ak QMHu0C2D+fou3jzWjoDfrMJi+9CVqsyICRcCAwEAATANBgkqhkiG9w0BAQ0FAAOCAgEAbywum+pY trXnRmtI2zrun7Jum7UpxSlVjW1MUvt0vZHNgunAkDivF1eOXsXQR1boXdjsuvRw3ERMhdpibn7I mvFAmMrYMez5PNEusqQ+pzIz2OPa7JIjemx6emZToUKhJ5iqjDlL+J5O7ov6+FrjjlRjfPsMWJdC zu3Nk0TBX1pM2PWDs2NoITCOjMI8jv+Ur9gqH8mopYf1SF94RjNKqiSQxAeXr0BM6aWbZzeTXP5z Mwlbm0icqAF26YMZgEypCR3UhJmlhgFVd/fPuao6laWS5tsKoYhGXz7llheY1gPNVp28pE6PkD3S egbZu2RM9iry0G+v46O+gwYDIuXCyA9BRagiSek4NN9NoHFrLEtbN5PlxcV7UavIxVc+ql/DTq/9 2d/ENzay/MV1kF2ZHJz+TeOEOWXe0mErN2IDuDrJ1oXXAJZMsV3cn/EUn2NZ1d8mhQB1DpPIzWck 8PKzzo8+EEWMxYrL3vjrH9SPE0QW2TnMovSvcomg9Zqyd79NCpJkKCt2FKsBziKBcIcjoFj98+9B kXexiIiotyQKHKJSScDy6ruhidU5C7jinwrWDhTiEhkpycKviqUm40P3ZCEbFhy0IasjV+pN8edd gf4TBex/kF5TeuRD0fBZaQijozdeVlx4lej1SiiUZg+bONGvmrJrkLLlrrjft/7NdP4=</ds:X509Certificate>
            </ds:X509Data>
        </ds:KeyInfo>
    </ds:Signature>
    <saml2p:Status xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol">
        <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
        <saml2p:StatusMessage>urn:oasis:names:tc:SAML:2.0:status:Success</saml2p:StatusMessage>
    </saml2p:Status>
    <saml2:EncryptedAssertion xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">
        <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#" Id="_a056bea0846b4cff6ef971252c691d60" Type="http://www.w3.org/2001/04/xmlenc#Element">
            <xenc:EncryptionMethod Algorithm="http://www.w3.org/2009/xmlenc11#aes256-gcm" xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"/>
            <ds:KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
                <xenc:EncryptedKey Id="_5a8536fc20547e76206512fa4bd39711" xmlns:xenc="http://www.w3.org/2001/04/xmlenc#">
                    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-oaep-mgf1p" xmlns:xenc="http://www.w3.org/2001/04/xmlenc#">
                        <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" xmlns:ds="http://www.w3.org/2000/09/xmldsig#"/>
                    </xenc:EncryptionMethod>
                    <ds:KeyInfo>
                        <ds:X509Data>
                            <ds:X509Certificate>MIIFQTCCAykCBFTI+zMwDQYJKoZIhvcNAQENBQAwZTELMAkGA1UEBhMCQ0ExCzAJBgNVBAgMAkVV MQswCQYDVQQHDAJFVTELMAkGA1UECgwCU1AxDjAMBgNVBAsMBVNUT1JLMR8wHQYDVQQDDBZzcC1j YS1kZW1vLWNlcnRpZmljYXRlMB4XDTE1MDEyODE1MDczMVoXDTE2MDEyODE1MDczMVowZTELMAkG A1UEBhMCQ0ExCzAJBgNVBAgMAkVVMQswCQYDVQQHDAJFVTELMAkGA1UECgwCU1AxDjAMBgNVBAsM BVNUT1JLMR8wHQYDVQQDDBZzcC1jYS1kZW1vLWNlcnRpZmljYXRlMIICIjANBgkqhkiG9w0BAQEF AAOCAg8AMIICCgKCAgEAlBqiPPAgV/fMWR5xyXapUGiQ9bmbHhl9wUsthn4/NaQjuoY4q32iRWvy 6MWQbtxrfJ4NBlih4n4ZaCD0PiFxK9AZU2iWTqqFJd4Nol+0TB8YOP81a8XOLiu1E0gwjEe71Orc ppw6ZnALT63pDZbGmSfjGzdS0TurUkHNqLKcmanPJjaPQMajHwFlCteiswMrSunw4+wTcHgIl028 uoUx72sh/O69huPHbf1295HOq+pfU9edsk5r/Vxi3bjv6sPutB3jZjMYHfdioyJe6F0exggN+LqL K1WiT2k/RAkoSSQmC1DQqi3vbxVL4WocGJYRilPjhaEfhiEKv37FZ6WV43U5kd8V3tt/YcFmuEMK fXKKPQwZTZn1GXkSWHnhTDO9zBA8nIIljoq3Ue5DuUhT64GEGqGDvoJoAYtykecj+rxfvN2dYjKh bIoNpjDrkdlDq4xwf/wXRhTIWyDRCzLgU4vxgXJtBKrk4O5gI1prUjPtx6+rq7csb2DShDn+HFpd 5rmGK4iPn9+ss6X3yzls1NBQaL4O1MI8TSDTgZ+LuWzSqlASOwFEmoQWGZcHo5/ZxXBrYuQ7sw2x btmfvHbmvxpO/9U9KoKNyjrSp0jcFfRKf3iNvRU+iYL0AzgP5dLBQPWVxjAFOGjwNkju1VAtj/3H Mu1aBwbyKmghlQwW5DsCAwEAATANBgkqhkiG9w0BAQ0FAAOCAgEARe50F20MJPAqcyNiN3+XQizn yeZJ1MlJnSrHqq1yiBPFvD5fIKLAC07kPrSC9fnNQnt6J79mCA9IRJwVDYOs4SDRrwTikVmk1eVd 1m8fxvsJ8Yfzh7wqbC6Maf7bWeLqFDeq8EgycOrubROvRIuENbQQmWJfoe8iHx27XcxJPehPninY gGfPbOKHUd/08nzI3gnJnUMGvG9hNORqloM20wajTW3fC6xeiRqg/21S51tMW8ETInejNIE6NY+B 9r8E4DpRLigxc0XjzAdC4L80/Al+ImVOwGdpevomWJ1FWarINVtSEw2cDzvnQ+moHPM0Yk9wran0 6yx6uQlxo/7MZ+mG7wulRQ5vGWducw+ktM7tUodlebTI01iG09+9tadEQp/ryilNkbw+wT+xPVuF fCRfhqLxnjvuN6FeJF76/7y7D5RmBG5wBHo8qWnyHVzHPSGvTxZPrLupGB9vzxTbPt6pbDNv3VVA ky0/zpqw1vs0P3rmEb5KsFo9dwCAQELVCiqDle9xkGc0Jr6eCS6W2qMQ4QF4eUHVD8q5tNr0dhZA 3nsjLnyuvcWFiTdZnMOsozXfRiN3AKIIkxML3t7pp6VbKg50LgZGv3GvbbR+HJsRCACxHm88PSxu rfNo9ZTIPz25yJANDcHzWhQXWxJC3gcYvyW8HOvSLVlAzUFuCcE=</ds:X509Certificate>
                        </ds:X509Data>
                    </ds:KeyInfo>
                    <xenc:CipherData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#">
                        <xenc:CipherValue>acy6s01zPqJg3O6PpE6jPEUmiu+dI8ODETn2Mj8dFexjUrLla8eapzp1l6ljgQv4Vx4CBRRPDqN3IDImf9Opcxz1pYt6NYBu+Eu0Qm4oD3sqf1vsKHu+TCsqA/yDsIzDDDEmHeMER0ER1brBFYuAHOJD7GbWSS6dKee5fd3MIysIP3sptmSnyFky5aW6R5CEnsFtt2ZLk0VSV1JqQ4GRTwefAfv1WT0qflAInzRdnz+CzS3wZbRNLGyT4ozikUYjyD2sAhvtzUHNBnK3QPhRQHCRVTFaoSbnOPVZPN8L32TLeNZtAMnh0UR7wdHCvFQys4vSUDbrjpt8GCkuc9o96JxNqwQX5Z7YdUrwhZXK56ynUa7LdYe8KL6PLZNNro1VorK6dxvuCrkM1ja/hmqF7+aT9/L4W0CIPjZPQbrIUtdM90CM0Jk1G+p98riV4ZubVosnfAvL1frK/A9dJacvTwe19MNAMr8r4EVp69EJn9QLhUqCMAlhEL6S6Xl9HAjyr61BMXZoXmZdI21DCBPhF2e70G6T8J2M7VtF7X0nQeUi3zK6j6obNkI5mpkKglW8GSC8F/in5phSUsJQmp+d3ic+gruA/JJjo3JXIaAuEbd/gjSWMjYJNhkH8Xznp0bpIfTsgB1iiwhU6Z0G9J2LD598kdQl6fuoCoo+MANpgRs=</xenc:CipherValue>
                    </xenc:CipherData>
                </xenc:EncryptedKey>
            </ds:KeyInfo>
            <xenc:CipherData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#">
                <xenc:CipherValue>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</xenc:CipherValue>
            </xenc:CipherData>
        </xenc:EncryptedData>
    </saml2:EncryptedAssertion>
</saml2p:Response>
```

Näidis 4 - Dekodeeritud `SAMLResponse` parameetri sisu isikutuvastuse ebaõnnestumise korral (isik ei andnud nõusolekut oma andmete avaldamiseks)

```xml
<saml2p:Response xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:ds="http://www.w3.org/2000/09/xmldsig#" xmlns:eidas="http://eidas.europa.eu/attributes/naturalperson" xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" Consent="urn:oasis:names:tc:SAML:2.0:consent:obtained" Destination="http://localhost:8080/EidasNode/ColleagueResponse" ID="_lL971pgUcKgV.ifiv9eQHBBwzUSBFeirUFyxQUVJV_SLzEjETOVZzjU_GEM4CxI" InResponseTo="_FgE3IvzittrpDPIuOICAufDv8.ppNwVZuHpoO9ALPBZTsnOebKUC6gupqHxXVdY" IssueInstant="2018-01-05T13:42:11.944Z" Version="2.0">
    <saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">http://localhost:8080/EidasNode/ServiceMetadata</saml2:Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
            <ds:Reference URI="#_lL971pgUcKgV.ifiv9eQHBBwzUSBFeirUFyxQUVJV_SLzEjETOVZzjU_GEM4CxI">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
                <ds:DigestValue>RWmPm/iwS7R0wLsKEMT23etASwrZOIGlIDNAPK/6AbbXDo14308kMGknkluHuFRWnz3BQBeJMCpmFgZkfKcp3w==</ds:DigestValue>
            </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue>VsvTcN1QgZENI2nBJShaLEAfm8tTk5KBXnUD13Jnv+jfEgN8osn6Yq/4sT61ErvN/BdVwElbfVYZrd9pinX+YJVkQvPzwyvLoVIze/sUnL15760a9fv02xOeXNLeAJAAKb5LZDeX/A8c/ikcCyufoZsHnGdR5BYyIJ7LsYngiAbLadS+q/3uAUv0uWD+SjRYDMUeVpdIjGGh45tqCYZlrQ/Zo6pwFMelRQSHXByx4KICYf8oCNjBTobc0jQhkoBhWeMCA7d+sCzbJGFEClUgChXnQVUCo8OzXWGVPFGJMCan4NC0Gb8SL8QkUefG2PGfTUVnYR63Mwm6T40DIBWKYdQbF1dWJpY7JEMb6v3zarJBIwpgJ7vh5dR1fymAZeuB1AXX3+7ev+kE1eqlDQLzTH7MNojiv3uMpHQF/XKvNSShDQgfjUQSSqZvXg1EzL3cvD6t6sX61PBleAssS19AeUdB/vKEn24/eM/VA1ts/XL45NUfu7xoFxAqEB4Yeio10uJMfTNK84iDDYyPXGqDbzpxbn8/3JT3KmzayeQnEycPHEMmQPCwuRrpoI2LtyLp1BX6e8HBO7R4lezP8pSPKvWIZKqiUzIO6hc+Y8QV3BC2Hiv3laOOw+PUpJeAFRnEE/EEmPJPxuHLOd1Kke2tkNcRhhH9T8bWM0gev4gAOP0=</ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MIIFTTCCAzUCBFTI+H0wDQYJKoZIhvcNAQENBQAwazELMAkGA1UEBhMCQ0ExCzAJBgNVBAgMAkVV MQswCQYDVQQHDAJFVTEOMAwGA1UECgwFQ1BFUFMxDjAMBgNVBAsMBVNUT1JLMSIwIAYDVQQDDBlj cGVwcy1jYS1kZW1vLWNlcnRpZmljYXRlMB4XDTE1MDEyODE0NTU1N1oXDTE2MDEyODE0NTU1N1ow azELMAkGA1UEBhMCQ0ExCzAJBgNVBAgMAkVVMQswCQYDVQQHDAJFVTEOMAwGA1UECgwFQ1BFUFMx DjAMBgNVBAsMBVNUT1JLMSIwIAYDVQQDDBljcGVwcy1jYS1kZW1vLWNlcnRpZmljYXRlMIICIjAN BgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEAh0qBEBHhvQYnyM3zPbAnoJrvs7M+w93KmKbBEFSK FGRBJHdbof6tN4s220vSHN2SamgnnT0KqYyfyB0xHZ0jovl9R9GSnpMeBotZI+zZYBui7KeizDV2 CU3Re2gdczue93ED0h3rFDibGmlsN+vN3d5jjTHoshfWWxKplHtvW2N9BtipKwXUNe39Fj0OVYAL 7S4UTTqKsLFRgKBxYp3qwcHzOXM1xQIn90xzBT/bBYlPhCv4SlhWo1leV3fh2bSw6X0bFdmRe9VT 8VGlcOTpOHvMHogJUC6YteQwMsaLPXrSIq3dPLPfTThhsWvp6dclwnAXQq9u4yuTXGrZ2JzW7XiK 47E4Rv42+p7MbQ/U0FmuovUuTeQj/kewRPR9d0X9V0qRdl1goURGkKqLf7MF0RDdX0t8MYCqTcxg bmInwU7eCvraOykbBeTBySQ3RZEtwYvI5NSsqKa0T1LYv41XFS2wBNKlxHXnCwUWDD3E+TU0fpyu DXMcccjpjN+mOLtHOpf3hfxe7y4u6FyK7SyjkS9vROvZ6zmywBKnjpklzlAqKubyKfKAGS8yUEpS jYldfh9keYLnXJiTo/KNSh+l+8EAgaDs7IjFUG8Tz0+z65DQz0PdkSR7BXvQ/hBuIUI57bc0dIB0 O3PL2Dqj7KuyMcMyJKfpUukPmdMm6od1PWECAwEAATANBgkqhkiG9w0BAQ0FAAOCAgEABENhWuuJ phD13xWbYYsrG5wynlSkd1LuLSZJupKnhOmixzUrOVxLL7KtEtUsWssm7MzKljHrcQyUaGuxCef3 ESgwdDkdPkrtSGnbnay26YDq7TxS5HU4t2QmFFSWLtEC2z6gGPTx0sB6q6ptC9/5AcTHMqtN2gOF vLZvzu8AKutcWxSETOGh9nconSp8Qwtsxqi6TvqbbaxW+JL3zLpGly8jyggIIyRoLDnFfKDOhbCX UOorTmHKE47FkVuNnpdR4q1UIWOtomtX9P/MAKdbf9rMauYXlwxWPE+GQM/6wvywgN5zDEl60/sZ wUyD+yen+VY07kUpdBY1pNzNJwv1U53BXrIs8bLzutOfiZ/wlgMs+sYletBhVCzAUcKvkZRIl9dD 2GaOYnQeVVBRetKHv0O56GfD+rsCthDyL+cKnJRI1OmKJJxm80+1k4k7efLU5cixTZZbNiLx5l8s G3R4YZ+3mZcVn+QJB0sVl0jpN1G3nruBe2ihg0UXALU4Ygk0Mx3WXVh9e3nFgEUGTt3h2FDC/2l3 5ejny7IkWzJ4UXUTOgKtIfqFVV6BEML12SEIvxFnqGdZDny2qxKRxdmrTA3tOpZ4jIKKVfGnGQ0d UsWwWY2VO/U/Hp2nLqk73bXodvVLoQ/WV2bqo1HyTEhW4TasZwG4e0jx9hW23KKaQrc=</ds:X509Certificate>
            </ds:X509Data>
        </ds:KeyInfo>
    </ds:Signature>
    <saml2p:Status>
        <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
            <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestDenied"/>
        </saml2p:StatusCode>
        <saml2p:StatusMessage>202007 - Consent not given for a mandatory attribute.</saml2p:StatusMessage>
    </saml2p:Status>
    <saml2:Assertion ID="_ub5Gp8pKv32ZI08bFy-VSYWgCu8JP3HP1F2FXpre7BzJHkFPbfxLdxZniVMB0pv" IssueInstant="2018-01-05T13:42:11.944Z" Version="2.0">
        <saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">http://localhost:8080/EidasNode/ServiceMetadata</saml2:Issuer>
        <saml2:Subject>
            <saml2:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified" NameQualifier="http://C-PEPS.gov.xx">NotAvailable</saml2:NameID>
            <saml2:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
                <saml2:SubjectConfirmationData Address="127.0.0.1" InResponseTo="_FgE3IvzittrpDPIuOICAufDv8.ppNwVZuHpoO9ALPBZTsnOebKUC6gupqHxXVdY" NotOnOrAfter="2018-01-05T13:47:11.944Z" Recipient="http://localhost:8080/EidasNode/ColleagueResponse"/>
            </saml2:SubjectConfirmation>
        </saml2:Subject>
        <saml2:Conditions NotBefore="2018-01-05T13:42:11.944Z" NotOnOrAfter="2018-01-05T13:47:11.944Z">
            <saml2:AudienceRestriction>
                <saml2:Audience>http://localhost:8080/EidasNode/ConnectorMetadata</saml2:Audience>
            </saml2:AudienceRestriction>
        </saml2:Conditions>
        <saml2:AuthnStatement AuthnInstant="2018-01-05T13:42:11.944Z">
            <saml2:AuthnContext>
                <saml2:AuthnContextClassRef>http://eidas.europa.eu/LoA/substantial</saml2:AuthnContextClassRef>
                <saml2:AuthnContextDecl/>
            </saml2:AuthnContext>
        </saml2:AuthnStatement>
    </saml2:Assertion>
</saml2p:Response>
```

## Veaolukordade käsitlemine

Kõik konnektorteenuse poolt tagastatavad veakoodid on toodud eIDAS näidislahenduse dokumentatsioonis (vt [eIDAS-veakoodid]).

Tabel 4 - Loetelu võimalikest veaolukordadest konnektorteenuse poolt tagastatavatest vigadest

| Veakood | Lühikirjeldus            | Selgitus  |
| ------------- |:-------------:| -----:|
| 202007 | Isiku nõusolek puudub | Isik keeldus isikutuvastuse jaoks vajalikke andmeid avaldamast. Ei vaja teenusepakkuja poolseid lisategevusi |

// TODO vajab analüüsi - välja tuua vaid konnektorteenuse kasutajaid puudutav nimekiri veakoodidest - täiendada //

## Toetatud riikide nimekiri

Nimekiri riikidest, kelle autentimisteenuseid RIA eIDAS konnektorteenus vahendab, on masinloetavas vormingus avaldatud aadressil  

`https://www.ria.ee/eidasinfo`

Nimekiri on esitatud JSON-failina, milles on üks JSON-objekt. Objektis sisaldub objekt `CountriesSupported`, milles omakorda on objektid `Test` ja `Production`. Viimaste väärtusteks on massiivid riikide nimekirjadega. Riik esitatakse [ISO 3166-1 alpha-2] standardi kohaselt, kahekohalise koodiga. Ülemise taseme objektis võib olla ka muid välju.

- Toetatud riikide nimekirja URL PEAB olema liidestuvas rakenduses konf-is seatav.
- Liidestuv rakendus PEAB kasutama nimekirja, kuvades kasutajale ainult nende riikide "lipukesed", kelle autentimisi tegelikult suudetakse vahendada.
- Nimekirja uuendamine eeldatavalt saab olema harv. Liidestuv rakendus PEAKS nimekirja alla tõmbama ja seda puhverdama.
- Puhvri uuendamise intervall PEAKS olema liidestuva rakenduse seadistuses seatav.

 Näide.

```
{
   "CountriesSupported":{
      "Test":[
         "SE",
         "NO"
      ],
      "Production":[
      ]
   }
}
```

Näites eIDAS konnektori testteenus toetab Rootsit ("SE") ja Norrat ("NO"). Toodanguteenus ei toeta ühtki riiki.

([TARAEI-23](https://jira.ria.ee/browse/TARAEI-23)  &#128273;) 

<a id='eIDASprofiil'>eIDASprofiil</a> European Commission. ***eID eIDAS profile***. Technical specifications. [https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eID+eIDAS+profile](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eID+eIDAS+profile)

[eIDAS SAML Attribute Profile] ***eIDAS SAML Attribute Profile***. [https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eID+eIDAS+profile?preview=/23003348/35218928/eIDAS%20SAML%20Attribute%20Profile%20v1.1_2.pdf](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eID+eIDAS+profile?preview=/23003348/35218928/eIDAS%20SAML%20Attribute%20Profile%20v1.1_2.pdf).

[eIDAS SAML Message Format] ***eIDAS SAML Message Format***. [https://joinup.ec.europa.eu/sites/default/files/eidas_message_format_v1.0.pdf](https://joinup.ec.europa.eu/sites/default/files/eidas_message_format_v1.0.pdf).

[eIDAS Regulatsioon]: http://eur-lex.europa.eu/legal-content/ET/TXT/HTML/?uri=CELEX:32014R0910&from=EN

[eIDAS profiil]: https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS+Profile

[EIF]: https://ec.europa.eu/isa2/eif_en

[SAML 2.0]: http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html

[eIDAS formaat]: https://ec.europa.eu/cefdigital/wiki/download/attachments/46992719/eIDAS%20Message%20Format_v1.1-2.pdf?version=1&modificationDate=1497252919575&api=v2

[eIDAS krüpto]: https://ec.europa.eu/cefdigital/wiki/download/attachments/46992719/eidas_-_crypto_requirements_for_the_eidas_interoperability_framework_v1.0.pdf?version=1&modificationDate=1497252920224&api=v2

[eIDAS-attr]: https://ec.europa.eu/cefdigital/wiki/download/attachments/46992719/eIDAS%20SAML%20Attribute%20Profile%20v1.1_2.pdf?version=1&modificationDate=1497252920100&api=v2

[eIDAS-interop]: https://ec.europa.eu/cefdigital/wiki/download/attachments/46992719/eidas_interoperability_architecture_v1.00.pdf?version=1&modificationDate=1497252919857&api=v2

[eIDAS-veakoodid]: https://ec.europa.eu/cefdigital/wiki/download/attachments/46992189/eIDAS-Node%20Error%20Codes.pdf?version=1&modificationDate=1507296786056&api=v2

[ISO 3166-1 alpha-2]: https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2

[eIDAS siseriiklik usaldusprofiil]: https://e-gov.github.io/eIDAS-Connector/Profiil

## Muutelugu

| versioon, kuupäev | muudatus |
|-------------------|---------------|
| 0.2, 22.01.2018   | Lisatud toetatud riikide nimekiri |
| 0.1, 16.01.2018   | Esimene versioon. |

