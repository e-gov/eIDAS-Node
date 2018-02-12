---
permalink: Spetsifikatsioon
---

<img src='img/ee_cef_0.png'>

Märkus. Lahtised küsimused on markeeritud sümbolitega `//`. 
{:.teade}

# RIA eIDAS konnektorteenuse liidese spetsifikatsioon
{: .no_toc}
v 0.4

- TOC
{:toc}

## 1 Ülevaade

Käesolev juhend on suunatud riigiasutustele (edaspidi *teenusepakkuja*), kes soovivad välisriikide kodanikke oma infosüsteemi tarvis tuvastada otse eIDAS konnektorteenusega ühendudes. Juhend kirjeldab nõuded, isikustuvastusprotsessi tehnilisi detaile ja vajalikke arendustegevusi riikliku konnektorteenusega liidestumiseks.

Vt ka: [eIDAS siseriiklikud usaldus- ja krüptonõuded](Profiil)

## 2 Kontekst

RIA eIDAS konnektorteenus on Eesti riigiasutustele mõeldud vahendusteenus eIDAS-autentimisvõrku. eIDAS-autentimisvõrk on Euroopa Liidu liikmesriikide elektroonilist autentimist pakkuv ühisteenus. Võrk koosneb turvalise kanali abil ühendatud riiklikest sõlmpunktidest (ingl _eIDAS Node_). Sõlmpunktid vahetavad omavahel isikutuvastuspäringuid ja kinnitusi vastava riigi kodaniku identiteedi kohta. Iga Euroopa Liidu liikmesriik opereerib vähemalt ühte riiklikku sõlmpunkti ja pakub kohalikele teenusepakkujatele välisriigi kodanike tuvastamiseks konnektorteenust.

Eestis pakub eIDAS Node võrguga liitumiseks konnektorteenust RIA.

<img src='img/SUURPILT.PNG' style='width:700px'>

Joonis 1. eIDAS taristu

eIDAS konnektorteenus on ühendajaks asutuse e-teenuse ja EL eIDAS-taristu vahel (joonisel 1 kasutusvoog 3b). Vt lähemalt [eIDAS autentimise lisamine e-teenusele](https://e-gov.github.io/TARA-Doku/files/TARA-tutvustus.pdf).

e-teenuse ja eIDAS konnektorteenuse vaheline suhtlus on osa eIDAS autentimisvoost, hõlmates sellest kahte sõnumiedastust:

- autentimispäringut esitava SAML tõendi (_token_) saatmine e-teenusest eIDAS konnektorteenusele
- autentimisvastust esitava SAML tõendi saatmine eIDAS konnektorteenuselt e-teenusele.

Sõnumiedastus teostatakse veebisirvija ümbersuunamise (_redirect_) abil.

## 3 Autentimisprotsess

Piiriülene autentimisprotsess eIDAS-autentimisvõrgus hõlmab mitut osapoolt, kes järgivad eIDAS profiili raames kirjeldatud koosvõime nõudeid [Viited](Viited). Sõnumivahetus osapoolte vahel toimib SAML 2.0 protokolli alusel.

Järgnevas näidisstsenaariumis (vt Joonis 2) on välja toodud edukas isikutuvastamise protsess SAML HTTP POST näitel. Lihtsuse mõttes ei ole näidatud SAML protokolli kohaseid metadata otspunktide poole pöördumisi.

Edukas autentimine eIDAS-autentimisvõrgus näeb välja järgmisena:

<img src='img/Autentimisvoog-eIDAS_demorakenduses.png'>

Joonis 2.

1. Kasutaja navigeerib teenusepakkuja lehele, mis nõuab piiriülest autentimist.
    - Kasutaja isik on tuvastamata, puudub kehtiv sessioon.
    - Teenusepakkuja jätab soovitud URL-i meelde ja suunab kasutaja lehele, kus kuvatakse eIDAS võrguga liidestatud riikide nimekiri.

2. Kasutaja on valinud riigi ja saadab vormi teenusepakkujale.
    - Teenusepakkuja moodustab isikutuvastamiseks vajaliku `SAMLRequest` parameetri sisu
    - ja saadab kasutajale vormi ümbersuunamisega riiklikusse eIDAS Node'i (vormi parameetriteks `SAMLRequest`, `country` ja vajadusel ka `RelayState`).
        - NB! `RelayState` on mittekohustuslik parameeter, mille teenusepakkuja võib kaasa panna oma päringu oleku hilisemaks tuvastuseks sammul 10 (algse päringu saab tuvastada ka vastuses oleva `SAMLResponse` parameetris sisalduvas XML atribuudis `InResponseTo`, kuid see info on kodeeritud kujul)

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
    - ja saadab kasutajale vastuseks piiriülesele autentimisteenusele ümbersuunamiseks mõeldud vormi.

6. Kasutaja suunatakse automaatselt edasi piiriülese autentimisteenusepakkuja lehele koos `SAMLRequest` ja `RelayState` parameetritega.
    - teenusepakkuja saadab kasutajale vastuseks autentimismeetmete valiku (ID-kaart, paroolikaart, parool, Mobiil-ID vastavalt sellele, mida antud riigi teenusepakkuja toetab).

7. Kasutaja autendib ennast autentimisteenuses (näiteks ID-kaardiga).
    - eduka tuvastuse korral teenusepakkuja tagastab vastusena ümbersuunamisvormi piiriülesse eIDAS Node teenusesse, millest pärines algne autentimispäring.
    - ümbersuundamisvormi `SAMLResponse` parameetris on teenusepakkuja poolt palutud info isiku kohta.
    - `SAMLResponse` allkirjastatakse ja isiku andmed krüpteeritakse teenusepakkuja privaatvõtmega.

8. Kasutaja suunatakse automaatselt tagasi piiriülese eIDAS Node teenusesse.
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

## 4 Nõuded liituvale teenusepakkujale

Liidestuv teenusepakkuja peab vastama eIDAS profiilis toodud koostöövõime nõuetele SAML protokolli kasutuse ja krüptoalgoritmide osas. eIDAS Node kasutab suhtlemiseks täiendavate kitsendustega SAML 2.0 protokolli. Vt [Viited](Viited).

Kokkuvõtlikult peab teenusepakkuja:

- pakkuma SAML metateabe otspunkti teenust
- omama SAML päringute ja vastuste töötlemise võimekust.

Vt lähemalt [eIDAS siseriiklikud usaldus- ja krüptonõuded](Profiil).

## 5 Sõnumivahetusmeetod

Siseriiklik eIDAS konnektorteenus toetab teenusepakkuja poolt algatatud `HTTP POST` põhinevat sõnumivahetusmeetodit (vt. `HTTP POST binding` [SAML 2.0] standardikirjelduses - ptk 5.12).

## 6 Metateabe otspunkt

Teenusepakkuja SAML metateave on XML dokument, mis sisaldab konnektorteenuse jaoks ühendumiseks ning usalduse loomiseks vajaliku info. Sealhulgas kirjeldab sertifikaadi päringu allkirjastamiseks, autentimise algatamise ning vastuse vastuvõtu URL-id ja soovi korral teenusepakkuja kontaktid.

Teenusepakkuja peab konnektorteenusele kättesaadavaks tegema oma metaandmed üle HTTPS protokolli.

Metateabe XML peab olema koostatud ja valideeruma vastavalt [SAML 2.0 metadata xml skeemile](https://docs.oasis-open.org/security/saml/v2.0/saml-schema-metadata-2.0.xsd).

Metateave peab olema allkirjastatud, kasutades krüptoalgoritme, mis on toodud dokumendis [eIDAS siseriiklikud usaldus- ja krüptonõuded](Profiil).

Konnektorteenusega liidestumise seisukohalt olulised nõutud väljad koos kirjeldusega on toodud Tabelis 1 (vt ka näidisvastust - Näidis 1)


| XML elemendi või atribuudi nimi (Xpath notatsioonis)        | Kohustuslik | Selgitus  |
|:-------------|:-------------:|-----|
| /md:EntityDescriptor/@Issuer | Jah | Nõutud väärtuseks teenusepakkuja metateabe otspunkti URL (HTTPS).|
| /md:EntityDescriptor/@ValidUntil | Jah | Määrab metateabe kehtivusaja. Aegumisel küsib konnektorteenus metateave uuesti. |
| /md:EntityDescriptor/ds:Signature | Jah | Kogu metateavet hõlmav digitaalallkiri, koos allkirjastamissertifikaadiga. Vastavalt [SAML 2.0 metadata spetsifikatsioonile](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)) |
| /md:EntityDescriptor/md:Extensions/eidas:SPType | Jah | Asutuse tüüp. Nõutud väärtus `public`. |
| /md:EntityDescriptor/md:SPSSODescriptor/@AuthnRequestsSigned | Jah | Deklareerib, et teenusepakkuja poolt saadetavad SAML päringud peavad olema allkirjastatud. Nõutud väärtus `true` |
| /md:EntityDescriptor/md:SPSSODescriptor/@WantAssertionsSigned | Jah | Deklareerib, et konnektorteenuselt tuleva vastuse sisu peab olema allkirjastatud. Nõutud väärtus `true` |
| /md:EntityDescriptor/md:SPSSODescriptor/@protocolSupportEnumeration | Jah | Deklareerib SAML protokolli versiooni toe. Nõutud on minimaalselt `urn:oasis:names:tc:SAML:2.0:protocol` (võib lisaks toetada ka SAML 1.0 ja 1.1) |
| /md:EntityDescriptor/md:IDPSSODescriptor/md:KeyDescriptor[@use="signing"] | Jah | Teenusepakkuja poolt päringu allkirjastamiseks kasutatud võtme kirjeldus. Võtmeinfo peab sisaldama X509 sertifikaati. |
| /md:EntityDescriptor/md:IDPSSODescriptor/md:KeyDescriptor[@use="encryption"] | Jah | Kirjeldab võtme, mida konnektorteenus kasutab vastuses oleva info krüpteerimiseks. Võtmeinfo peab sisaldama X509 sertifikaati. |
| /md:EntityDescriptor/md:SPSSODescriptor/md:NameIDFormat | Jah | Nõutud väärtused: `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`, `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` ja `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` |
| /md:EntityDescriptor/md:SPSSODescriptor/md:AssertionConsumerService/@Binding | Jah | Deklareerib SAML suhtlusmeetodi. Nõutud väärtus `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST` |
| /md:EntityDescriptor/md:SPSSODescriptor/md:AssertionConsumerService/@Location | Jah | Viitab vastuse tarbimise otspunktile. HTTPS otspunkti URL teenusepakkuja süsteemis. |


Lisaks on soovituslik asutusel kirjeldada metateabes ka oma organisatsiooni kohta käiv info ning kontaktandmed.

| XML elemendi või atribuudi nimi (Xpath notatsioonis)        | Kohustuslik | Selgitus  |
|:-------------|:-------------:|-----|
| /md:EntityDescriptor/md:Organization | Ei | XML struktuur, mis kirjaldab ära info liidestuva organisatsiooni kohta. Kasutus vastavalt [SAML 2.0 metadata spetsifikatsioonile](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)) |
| /md:EntityDescriptor/md:ContactPerson | Ei | XML struktuur, mis kirjeldab organisatsiooni kontaktisikute info. Kasutus vastavalt [SAML 2.0 metadata spetsifikatsioonile](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)) |


Näidis 1. - Näidisvastus metateabe otspunktilt.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<md:EntityDescriptor xmlns:md="urn:oasis:names:tc:SAML:2.0:metadata" entityID="https://eidastest.eesti.ee/SP/metadata" validUntil="2018-02-10T08:57:21.953Z">
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
				<ds:DigestValue>jneOWVHD7TSBa8CUX47N5CQE5jscnVAS8olM89/nEZyG4fhWY9OfZjv431Xa/M0YGW3GmikpDhIowBJ4sEr1Hw==</ds:DigestValue>
			</ds:Reference>
		</ds:SignedInfo>
		<ds:SignatureValue>Pc1M2yc9ptVwko5vYR6EKVCJeMrxEt6BMGKrzsC3HTr8Mzx8dXASFNyy4ERpwrZxDjm48a/T1zqB6W4z4IdUvMyerlDx8KP0qtDGqVc9Dn4X/6CmjL+0oIwG4bmHgWrl4hx9UHUEQnFC5PLaF+RxJhP90at/pX0amG9vpuE7DOfFjIsyPxeyeDsVWObuI2sYjVGTq3DbUyfKKNaXH/F6dbBdqdokDLxqtdMivZtODZrxet9ZdBeMuCVSxf2R6LRGrHYpDZR2RnSScmM2gNopeHlzoUFw2HjZ+Pd8FOTxwC2Virreh8GV9sGMurYSmNwcgakl0sNQOJWqKT1jvhzPMddz2REwSUYQqiKffP7dhHQcGs3pMdqh7zYqyylDdqpZEvZFdBEYA/DMwWOOZgNlnnnTG85MF2DRpseBZZOKRDnul4kQkJkYcOIRYxHGCsjzEDATr6rEYNg/5WSCopk61Q+C3boU9AXCciD+kzBeD+bOvT64kJDTDGSB4p/1V7ScuLfRMNOoyyHyXOT+AgzyJ6+R0PFpQZX4YLCQAH8GZNsFO1LCpmCMAoQ7ftYiGtdD5qtxvs96d9MOZ6Br3hHp2fmy+CtAaiBuzAAEODRMAe1xCRNIPVUZ0YGFS6eOwwpkSrmKuL9Iu5F/ZdfPaczeEttUk5nOnDvnbWi2xzDyeRU=</ds:SignatureValue>
		<ds:KeyInfo>
			<ds:X509Data>
				<ds:X509Certificate>MIIFVTCCAz2gAwIBAgIEGNTPHDANBgkqhkiG9w0BAQ0FADBbMQswCQYDVQQGEwJFRTELMAkGA1UE
CBMCRVUxEDAOBgNVBAcTB1RhbGxpbm4xDDAKBgNVBAoTA01LTTEMMAoGA1UECxMDUklBMREwDwYD
VQQDEwhtZXRhZGF0YTAeFw0xNzA1MzExMTQ4MzlaFw0yMjA1MzAxMTQ4MzlaMFsxCzAJBgNVBAYT
AkVFMQswCQYDVQQIEwJFVTEQMA4GA1UEBxMHVGFsbGlubjEMMAoGA1UEChMDTUtNMQwwCgYDVQQL
EwNSSUExETAPBgNVBAMTCG1ldGFkYXRhMIICIjANBgkqhkiG9w0BAQEFAAOCAg8AMIICCgKCAgEA
rX2WocksJPHWtE3WqK+F+O1sioy6ysCTQAsZ0esIMSrb7096o1WGr2e+EE2aV9eayHOxiXc1j0JG
X1/ycAO/szCuLCgsjpayUHXC4a72vXCezYSkxuld59CEgST+C5q5zr/gHUrJg+b2dHtrvA5rBh4U
9bcYHZftUbU+uGIYjzFwAs2D1+smDUwiJQcQ+KBwUmMvhdeQ6CLhJwZB13C+wX5wdDu3CgOlBCwD
3fYI3HcAIxvRPPdPLutc75rLmk6H4+h4DVsjg0qlY4Ck4m6XMccnoEZhj0mtWJEBcoPw+kT6HiKU
amuYPnPfEf5NKARpqFZXukZbnPk3tStny1349rTI2bq4adaPr0KNETBNLNoeuFM61833ZhI3u7q0
w5y9sw0R+RTf/C+OfydZ7iWNKrH59vtBcKkt4b/7Qd6bn5pL35lLvzz3bZAnv0+/Sd7y3/OGsWsD
eksVKjehO4ieONFt9VH2Za2af+cJcq4DNy5P/69bqWz8a1o3a4qANZ5YyKGu90+LQ3NunhFh9Eml
kL9ka+NLsN2gDxQZcDB/GpeXdkl6ojPpcEuoSQ/REu9Eb4KbC6gPqeOmot/AnAu5rwzYidE4Bfor
+ZasVRt+BbqYf7TAzQ4hZ5pDh0Vn4yFLYSXaXf2LZLmG0I1thsheNfiyKfNAgvAr5mZbNr2HjeEC
AwEAAaMhMB8wHQYDVR0OBBYEFB6te2Gbr29UPL8Ksb0tmgq6Q5nwMA0GCSqGSIb3DQEBDQUAA4IC
AQBrvo1ynYDQa2CxxhcZbkehgk5fwCyA9ksFtpXgo3cgLHJgoFmkrYnHgsV908Rga5WfZTOFqzrr
7zVTpHyGWUFt2BL35QNU8k1CTiCxC5NhgcrxBeCIczBeTg2pv+kLvD0lLyGvHFZXx8qLCpNgIYJa
un3nkY6PuQWJCS9j5GKcT7o5UKuemecKHIiWSKCa7OHnTkCzvPLzNlpt5kFIs3Vi+Tc5DfPAP/eb
o5dZ8rq2UL3ra+iKFIT/iXl7JteRmPRDcBc0HvhtH8u27wm+y/CyruuGWZ13EPGvNVCxwzJv7CVe
W+HqAMAGsVvAV8zknbjl8K11Ry0kH2GOBtPXzO639x1xpQ39T/9kTdkmwiD6ZM/60lZx8chP/nV/
rrwyv/r+z4/oMehbCOFdUYHO1Agp6FmAP6Ck1+x/I95ANJOUt1dJc9xizcZ2012LEXO7cpt3DHie
1bHlV4d8o/53VWlofdeDS/VyWSbdkL2lLWd8JKlTAAUwfg+SW1CjmQZ5Jr3ew+yhMMvOzMzi/+ZE
A8c5MwMb8jP1wP9+vqIRg6U8RRBbY96sq9zxPMaGeM6Eli1zEuNYqOv0rIBXts6I2FQYOZSLLxgR
P2lHqMx9u4JMHGaj0sTDbHxz5BWw6MDMc08WtyqX5B2xOt0VZbAyR7fXrFvLmutpY0/nCKBfRXxx
Zw==</ds:X509Certificate>
			</ds:X509Data>
		</ds:KeyInfo>
	</ds:Signature>
	<md:Extensions>
		<eidas:SPType xmlns:eidas="http://eidas.europa.eu/saml-extensions">public</eidas:SPType>
		<alg:DigestMethod xmlns:alg="urn:oasis:names:tc:SAML:metadata:algsupport" Algorithm="http://www.w3.org/2001/04/xmlenc#sha256"/>
        <alg:DigestMethod xmlns:alg="urn:oasis:names:tc:SAML:metadata:algsupport" Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
		<alg:SigningMethod xmlns:alg="urn:oasis:names:tc:SAML:metadata:algsupport" Algorithm="http://www.w3.org/2007/05/xmldsig-more#sha512-rsa-MGF1"/>
		<alg:SigningMethod xmlns:alg="urn:oasis:names:tc:SAML:metadata:algsupport" Algorithm="http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1"/>
	</md:Extensions>
	<md:SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
		<md:KeyDescriptor use="signing">
			<ds:KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
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
		</md:KeyDescriptor>
        <md:KeyDescriptor use="encryption">
			<ds:KeyInfo xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
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
		</md:KeyDescriptor>
		<md:NameIDFormat>urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified</md:NameIDFormat>
		<md:AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://eidastest.eesti.ee/SP/ReturnPage" index="0" isDefault="true"/>
	</md:SPSSODescriptor>
	<md:Organization>
		<md:OrganizationName xml:lang="en">DEMO-SP</md:OrganizationName>
		<md:OrganizationDisplayName xml:lang="en">Näidisasutus</md:OrganizationDisplayName>
		<md:OrganizationURL xml:lang="en">https://naidisasutus.ee/info</md:OrganizationURL>
	</md:Organization>
	<md:ContactPerson contactType="support">
		<md:Company>Teenusepakkuja</md:Company>
		<md:GivenName>Mari-Liis</md:GivenName>
		<md:SurName>Männik</md:SurName>
		<md:EmailAddress>abi@sp.ee</md:EmailAddress>
		<md:TelephoneNumber>+342 123456</md:TelephoneNumber>
	</md:ContactPerson>
</md:EntityDescriptor>
```

Tabel 1

## 7 Autentimispäring

Nõutud parameetrite loetelu HTTP POST päringus on toodud Tabelis 2.

`SAMLRequest` parameetris saadetav SAML XML päringu sisu (vt Näidis 2) peab vastama eIDAS sõnumiformaadi kirjeldusele (vt [eIDAS formaat]: ja [eIDAS-attr]).


Tabel 2 - Autentimispäringu parameetrid

| Parameetri nimi        | Kohustuslik           | Selgitus  |
|:-------------|:-------------:|-----|
| `SAMLRequest` |	Jah | SAML protokolli spetsiifiline parameeter, mis sisaldab Base64 kodeeritud kujul SAML XML päringut (`AuthnRequest` koos detailidega). `AuthnRequest` päringus olevate kohustuslike elementide ja kitsenduste loetelu on toodud tabelis 3.  SAML `AuthnRequest` peab olema allkirjastatud teenusepakkuja privaatvõtmega ja moodustatud vastavalt eIDAS nõuetele.|
| `RelayState` | Ei | SAML protokolli spetsiifiline parameeter, fikseeritud pikkusega tekst, mille eIDAS Node vastuses töötlemata tagasi peegeldab. |
| `country` |	Jah | Kodaniku riigikood, kelle isikut tuvastatakse. ISO 3166-1 alpha-2 standardi alusel. |

Tabel 3 - SAML `AuthnRequest` parameetrid

| XML elemendi/atribuudi nimi (Xpath notatsioonis)        | Kohustuslik           | Selgitus  |
|:-------------|:-------------:|:----|
| `/saml2p:AuthnRequest/@Destination`	| Jah | Siseriikliku eIDAS Node-i SSO otspunkti aadress. Bindingule vastavad otspunktide aadressid on loetletud eIDAS Node metadatas (`/md:EntityDescriptor/md:IDPSSODescriptor/md:SingleSignOnService`) |
| `/saml2p:AuthnRequest/@ForceAuthn` | Jah | Konstantne väärtus: `true` |
| `/saml2p:AuthnRequest/@ID` | Jah | Unikaalne päringu XML ID. Peab vastama NCName kitsendustele (`https://www.w3.org/TR/1999/REC-xml-names-19990114/#NT-NCName`) |
| `/saml2p:AuthnRequest/@IsPassive` | Jah | Konstantne väärtus: `false` |
| `/saml2p:AuthnRequest/@IssueInstant` | Jah | Päringu koostamise aeg. |
| `/saml2p:AuthnRequest/@ProviderName` | Jah | Teenusepakkuja nimetus. Lepitakse kokku RIA-ga. |
| `/saml2p:AuthnRequest/@Version` | Jah | Konstantne väärtus: `2.0` |
| `/saml2p:AuthnRequest/saml2:Issuer` | Jah | Teenusepakkuja metadatale viitav URL. |
| `/saml2p:AuthnRequest/ds:Signature/*` | Jah | Teenusepakkuja privaatvõtmega antud allkiri ja sellega seotud detailid. Toetatud krüptoalgoritmid on loetletud eIDAS Node metadatas (`/md:EntityDescriptor/md:Extensions/alg:SigningMethod/*`) |
| `/saml2p:AuthnRequest/saml2p:Extensions/eidas:SPType` | Jah | Konstantne väärtus: `public` (avaliku sektori asutus) |
| `/saml2p:AuthnRequest/saml2p:Extensions/eidas:RequestedAttributes/*` | Jah | Kirjeldab, milliseid isikuandmete atribuute soovitakse ülepiirilise identiteedipakkuja käest. Nimekiri toetatud atribuutidest, on toodud eIDAS konnektorteenues metadatas (vt `/md:EntityDescriptor/md:IDPSSODescriptor/saml2:Attribute`). Kohustuslik info, mida identiteediteenuse pakkuja peab tagastama, peab olema märgistatud atribuudiga `isRequired` = `true` |
| `/saml2p:AuthnRequest/saml2p:NameIDPolicy` | Jah | Üks eIDAS konnektoreenuse metadatas kirjeldatud toetatud väärtustest (`/md:EntityDescriptor/md:IDPSSODescriptor/md:NameIDFormat`) |
| `/saml2p:AuthnRequest/saml2p:RequestedAuthnContext` | Jah |	// Variandid. Vajab analüüsi // |

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
 <ds:SignatureValue>Vyg... </ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MII... </ds:X509Certificate>
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

## 8 Autentimisvastus

Autentimise tulemuse kohta saadetakse teenusepakkujale autentimisvastus (vt Näidis 3). Eduka autentimise korral on autentimisvastuses andmed autentimistoimingu ja autenditud isiku kohta. Ebaeduka autentimise korral saadetakse veakood. Autentimisvastus saadetakse teenusepakkuja vastus-URL'le. See URL peab olema määratud teenusepakkuja metateabes, `SAMLResponse` parameetris.

Autentimisvastuses tagastatakse järgmised andmed (tabel 4).

Tabel 4 - Autentimisvastuse parameetrid

| Parameetri nimi        | Kohustuslik           | Selgitus  |
|-------------|:-------------:|-----|
| `SAMLResponse` | Jah | Parameeter, mis sisaldab Base64 kodeeritud SAML vormingus autentimise vastust. SAML vastuses olev `Response` on allkirjastatud ja isiku kohta käivad väited krüpteeritud (eIDAS Node privaatvõtmega). |
| `RelayState` | Ei | SAML protokolli spetsiifiline parameeter, fikseeritud pikkusega tekst, mille teenusepakkuja autentimispäringu algatamisel ette andis (peegeldatakse teenusepakkuja poolt tagasi töötlemata kujul). |

### RelayState parameetri kontrollimine


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
        <ds:SignatureValue>TnU... </ds:SignatureValue>
        <ds:KeyInfo>
            <ds:X509Data>
                <ds:X509Certificate>MII... </ds:X509Certificate>
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

Näidis 3 - Dekodeeritud `SAMLResponse` parameetri sisu autentimise ebaõnnestumise korral (isik ei andnud nõusolekut oma andmete avaldamiseks)

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
                <ds:DigestValue>RWmP... </ds:DigestValue>
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

## 9 Veaolukordade käsitlemine

Kõik konnektorteenuse poolt tagastatavad veakoodid on toodud eIDAS näidislahenduse dokumentatsioonis (vt [eIDAS-veakoodid]).

Tabel 5 - Loetelu võimalikest veaolukordadest konnektorteenuse poolt tagastatavatest vigadest

| Veakood | Lühikirjeldus            | Selgitus  |
|:-------------:|-------------|-----|
| 202007 | Isiku nõusolek puudub | Isik keeldus autentimise jaoks vajalikke andmeid avaldamast. Ei vaja teenusepakkuja poolseid lisategevusi |

// TODO vajab analüüsi - välja tuua vaid konnektorteenuse kasutajaid puudutav nimekiri veakoodidest - täiendada //

## 10 Toetatud riikide nimekiri

Nimekiri riikidest, kelle autentimisteenuseid RIA eIDAS konnektorteenus vahendab, on masinloetavas vormingus avaldatud aadressil.

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

| Versioon, kuupäev | Muudatus |
|-----------------|--------------|
| 0.4, 09.02.2018   | Metateabe otspunkti vastuse täpsustused |
| 0.3, 29.01.2018   | Korrastus |
| 0.2, 22.01.2018   | Lisatud toetatud riikide nimekiri |
| 0.1, 16.01.2018   | Esimene versioon. |

