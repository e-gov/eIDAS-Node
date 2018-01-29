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

## 1 Ülevaade

Käesolev juhend on suunatud riigiasutustele (edaspidi *teenusepakkuja*), kes soovivad välisriikide kodanikke oma infosüsteemi tarvis tuvastada otse eIDAS konnektorteenusega ühendudes. Juhend kirjeldab nõuded, isikustuvastusprotsessi tehnilisi detaile ja vajalikke arendustegevusi riikliku konnektorteenuesga liidestumiseks.

## 2 Suur pilt

RIA eIDAS konnektorteenus on Eesti riigiasutustele mõeldud vahendusteenus eIDAS-autentimisvõrku. eIDAS-autentimisvõrk on Euroopa Liidu liikmesriikide elektroonilist Autentimist pakkuv ühisteenus. Võrk koosneb turvalise kanali abil ühendatud riiklikest sõlmpunktidest (ingl _eIDAS Node_). Sõlmpunktid vahetavad omavahel iskutuvastuspäringuid ja kinnitusi vastava riigi kodaniku identiteedi kohta. Iga Euroopa Liidu liikmesriik opereerib vähemalt ühte riiklikku sõlmpunkti ja pakub kohalikele teenusepakkujatele välisriigi kodenike tuvastamiseks konnektorteenust.

Eestis pakub eIDAS Node võrguga liitumiseks konnektorteenust RIA.

<img src='img/SUURPILT.PNG' style='width:600px'>

Joonis 1. eIDAS taristu

eIDAS konnektorteenus on ühendajaks asutuse e-teenuse ja EL eIDAS-taristu vahel (joonisel 1 kasutusvoog 3b). Vt lähemalt [eIDAS autentimise lisamine e-teenusele](https://e-gov.github.io/TARA-Doku/files/TARA-tutvustus.pdf).

e-teenuse ja eIDAS konnektorteenuse vaheline suhtlus on osa eIDAS autentimisvoost, hõlmates sellest kahte sõnumiedastust:

- autentimispäringut esitava SAML tõendi (_token_) saatmine e-teenusest eIDAS konnektorteenusele
- autentimisvastust esitava SAML tõendi saatmine eIDAS konnektorteenuselt e-teenusele.

Sõnumiedastus teostatakse veebisirvija ümbersuunamise (_redirect_) abil.

## 3 Autentimisprotsess

Piiriülene Autentimisprotsess eIDAS-autentimisvõrgus hõlmab mitut osapoolt, kes järgivad eIDAS profiili raames kirjeldatud koosvõime nõudeid [Viited](Viited). Sõnumivahetus osapoolte vahel toimib SAML 2.0 protokolli alusel.

Järgnevas näidisstsenaariumis (vt Joonis 2) on välja toodud edukas isikutuvastamise protsess SAML HTTP POST näitel. lihtsuse mõttes ei ole näidatud SAML protokolli kohaseid metadata otspunktide poole pöördumisi.

Edukas Autentimis eIDAS-autentimisvõrgus näeb välja järgmisena:

<img src='img/Autentimisvoog-eIDAS_demorakenduses.png'>

Joonis 2.

1. Kasutaja navigeerib teenusepakkuja lehele, mis nõuab piiriülest Autentimist.
    - Kasutaja isik on tuvastamata, puudub kehtiv sessioon.
    - Teenusepakkuja jätab soovitud URL-i meelde ja suunab kasutaja lehele, kus kuvatakse eIDAS võrguga liidestatud riikide nimekiri.

2. Kasutaja on valinud riigi ja saadab vormi teenusepakkujale.
    - Teenusepakkuja moodustab isikutuvastamiseks vajaliku `SAMLRequest` parameetri sisu
    - ja saadab kasutajale vormi ümbersuunamisega riiklikusse eIDAS Node'i (vormi parameetriteks `SAMLRequest`, `country` ja vajadusel ka `RelayState`).
        - NB! `RelayState` on mittekohustulik parameeter, mille teenusepakkuja võib kaasa panna oma päringu oleku hilisemaks tuvastuseks sammul 10 (algse päringu saab tuvastada ka vastuses oleva `SAMLResponse` parameetris sisalduvas XML atribuudis `InResponseTo`, kuid see info on kodeeritud kujul)

3. Kasutaja suunatakse automaatselt riiklikku eIDAS Node'i,
    - kus kontrollitakse `SAMLRequest` parameetris oleva SAML XML päringu sisu valiidsust ja allkirja vastu teenusepakkuja metainfos olevat avalikku võtit.
    - siseriiklik eIDAS Node moodustab uue SAML XML päringu, võttes aluseks teenusepakkujalt tuleva info
    - ja allkirjastab selle oma privaatvõtmega.
    - siseriiklik eIDAS Node saadab kasutajale vastuseks vormi, mis on suunatud sihtriigi eIDAS Node'i vastu koos `SAMLRequest` parameetriga ja vajadusel `RelayState`-ga.

4. Kasutaja suunatakse automaatselt koos vormiga edasi sihtriigi eIDAS Node'i koos `SAMLRequest` ja `RelayState` parameetritega.
    - piiriülene eIDAS Node teenus valideerib `SAMLRequest`-i parameetris kodeeritud kujul oleva XML päringu sisu ja allkirja.
    - piiriülene eIDAS Node teenus saadab vastuseks vormi kasutaja nõusoleku küsimiseks.

5. Kui kasutaja oli vormil esitatud andmete jagamisega nõus,
    - saadab kasutaja kinnitamiseks päringu piiriülesele eIDAS Node teenusele.
    - piiriülene eIDAS Node teenus täiendab saadud `SAMLRequest`-i sisu
    - ja saadab kasutajale vastuseks piiriülesele Autentimisteenusele ümbersuunamiseks mõeldud vormi.

6. Kasutaja suunatakse automaatselt edasi piiriülese Autentimisteenusepakkuja lehele koos `SAMLRequest` ja `RelayState` parameetritega.
    - teenusepakkuja saadab kasutajale vastuseks autentimismeetmete valiku (ID-kaart, paroolikaart, parool, Mobiil-ID vastavalt sellele, mida antud riigi teenusepakkuja toetab).

7. Kasutaja autendib ennast Autentimisteenuses (näiteks ID-kaardiga).
    - eduka tuvastuse korral teenusepakkuja tagastab vastusena ümbersuunamisvormi piiriülesse eIDAS Node teenusesse, millest pärines algne Autentimise päring.
    - ümbersuundamisvormi `SAMLResponse` parameetris on teenusepakkuja poolt palutud info isiku kohta.
    - `SAMLResponse` allkirjastatakse ja isiku andmed krüpteeritakse teenusepakkuja privaatvõtmega.

8. Kasutaja suunatakse automaatselt tagasi piiriülese eIDAS Node teenusese.
    - `SAMLResponse` parameetri sisus olev XML sisu koos allkirjaga valideeritakse.
    - sisu dekrüpteeritakse.
    - moodustatakse uus `SAMLResponse` parameeter,
    - mis allkirjastatakse
    - ja mille sisu krüpteeritakse piiriülese eIDAS Node privaatvõtmega.
    - kasutajale saadetakse siseriiklikusse eIDAS Node'ile adresseeritud ümbersuundamisvorm koos `SAMLResponse` parameetriga.

9. Kasutaja suunatakse automaatselt tagasi siseriiklikku eIDAS Node teenusesse.
    - `SAMLResponse` parameetri sisus olev XML sisu koos allkirjaga valideeritakse.
    - sisu dekrüpteeritakse.
    - moodustatakse uus `SAMLResponse` parameeter,
    - mis allkirjastatakse
    - ja mille sisu krüpteeritakse siseriikliku eIDASNode privaatvõtmega.
    - kasutajale saadetakse siseriiklikule teenusepakkujale adresseeritud ümbersuundamisvorm koos `SAMLResponse` parameetriga.

10. Kasutaja suunatakse automaatselt tagasi siseriikliku teenusepakkuja lehele.
    - teenusepakkuja veendub `SAMLResponse` parameetri sisu vastavuses nõuetele (sh formaadi, sisu ja allkirja kontrollid).
    - teenusepakkuja dekrüpteerib isikuandmed
    - ja otsustab, kas käesolev isik on õigustatud ligi pääsema algselt küsitud ressursile.
    - kui jah, siis luuakse sessioon
    - ning tagastatakse kasutajale esialgselt URL-lt soovitud sisu.

## Vajalikud liidestamistööd teenusepakkujale

### Nõuded liituvale teenusepakkujale

Liidestuv teenusepakkuja peab vastama eIDAS profiilis toodud koostöövõime nõuetele SAML protokolli kasutuse ja krüptoalgoritmide osas (vt [eIDASprofiil](#eIDASprofiil)). eIDAS Node kasutab suhtlemiseks täiendavate kitsendustega [SAML 2.0] protokolli.

Kokkuvõtlikult peab teenusepakkuja:

- pakkuma SAML metadata otspunkti teenust
- omama SAML päringute ja vastuste töötlemise võimekust.

Vt lähemalt [eIDAS siseriiklikud usaldus- ja krüptonõuded](Profiil).

### Sõnumivahetusmeetod

Siseriiklik eIDAS konnektorteenus toetab teenusepakkuja poolt algatatud `HTTP POST` põhinevat sõnumivahetusmeetodit (vt. `HTTP POST binding` [SAML 2.0] standardikirjelduses - ptk 5.12).

### Metadata otspunkt

Teenusepakkuja SAML metadata on XML dokument, mis sisaldab konnektorteenuse jaoks kogu ühendumiseks vajaliku info (vt Näidis 1). Sealhulgas kirjeldab sertifikaadi päringu allkirjastamiseks, autentimise algatamise ning vastuse vastuvõtu URL-id ja soovi korral teenusepakkuja kontaktid. Metainfo on allkirjastatud.

Teenusepakkuja peab konnektoreenusele kättesaadavaks tegema oma metaandmed üle HTTPS protokolli.

Täpsemad nõuded metadata otspunktile on dokumendis [eIDAS siseriiklikud usaldus- ja krüptonõuded](Profiil).

### Autentimispäring

Nõutud parameetrite loetelu HTTP POST päringus on toodud Tabelis 1.

`SAMLRequest`  parameetris saadetav SAML XML päringu sisu (vt Näidis 2) peab vastama eIDAS sõnumiformaadi kirjeldusele (vt [eIDAS formaat]: ja [eIDAS-attr]).

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

Näidis 2 - SAMLRequest parameetris esitatav autentimispäring (dekodeeritud kujul)

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

### Autentimisvastus

Õnnestunud või ebaõnnestunud autentimise tulemus saadetakse teenusepakkuja vastus URL'le tagasi (toodud teenusepakkuja metadata's) `SAMLResponse` parameetris (vt Näidis 3 ja 4). Kõigi parameetrite loetelu on toodud Tabelis 3.

Tabel 3 - Autentimise vastuse parameetrid

| Parameetri nimi        | Kohustuslik           | Selgitus  |
| ------------- |:-------------:| -----:|
| `SAMLResponse` | Jah | Parameeter, mis sisaldab Bas64 kodeeritud SAML vormingus autentimise vastust. SAML vastuses olev `Response` on allkirjastatud ja isiku kohta käivad väited krüpteeritud (eIDAS Node privaatvõtmega). |
| `RelayState` | Ei | SAML protokolli spetsiifiline parameeter, fikseeritud pikkusega tekst, mille teenusepakkuja autentimispäringu algatamisel ette andis (peegeldatakse teenusepakkuja poolt tagasi töötlemata kujul). |


Näidis 3. Dekodeeritud `SAMLResponse` parameetri sisu eduka autentimise korral.

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
                            <ds:X509Certificate>MIIF... </ds:X509Certificate>
                        </ds:X509Data>
                    </ds:KeyInfo>
                    <xenc:CipherData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#">
                        <xenc:CipherValue>acy... </xenc:CipherValue>
                    </xenc:CipherData>
                </xenc:EncryptedKey>
            </ds:KeyInfo>
            <xenc:CipherData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#">
                <xenc:CipherValue>... </xenc:CipherValue>
            </xenc:CipherData>
        </xenc:EncryptedData>
    </saml2:EncryptedAssertion>
</saml2p:Response>
```

Näidis 4 - Dekodeeritud `SAMLResponse` parameetri sisu Autentimise ebaõnnestumise korral (isik ei andnud nõusolekut oma andmete avaldamiseks)

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
        <ds:SignatureValue>Vsv... </ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MII... </ds:X509Certificate>
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
| 202007 | Isiku nõusolek puudub | Isik keeldus Autentimise jaoks vajalikke andmeid avaldamast. Ei vaja teenusepakkuja poolseid lisategevusi |

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

[ISO 3166-1 alpha-2]: https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2

## Muutelugu

| versioon, kuupäev | muudatus |
|-------------------|---------------|
| 0.2, 22.01.2018   | Lisatud toetatud riikide nimekiri |
| 0.1, 16.01.2018   | Esimene versioon. |

