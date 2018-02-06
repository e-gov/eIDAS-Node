---
permalink: Profiil
---

# eIDAS siseriiklikud usaldus- ja krüptonõuded
{:.no_toc}

versioon 0.3, 02.02.2018

* TOC
{:toc}

Käesoleva dokumendiga kehtestatakse usaldus- ja krüptonõuded RIA eIDAS konnektorteenusega liidestatavatele infosüsteemidele (eIDAS terminoloogias - teenusepakkujatele). Samuti täpsustatakse ja selgitatakse vahetatavate metaandmete tähendust.

Praegune versioon ei ole lõplik. Dokumenti täiendatakse liidestuste testimise tulemuste alusel.

Edaspidi nõuded, sh võtmepikkused ja algoritmide valik vaadatakse läbi vastavalt uutele teadmistele krüptoalgoritmide turvalisuse kohta. 

Vt ka: [eIDAS konnektorteenuse liidese spetsifikatsioon](Spetsifikatsioon)

## Mida on vaja?

Usaldusmehhanismi (_trust establishment mechanism_) RIA eIDAS konnektorteenuse ja liidestuva süsteemi (SAML terminoloogias - teenusepakkuja) vahel, mis arvestab piirangutega:

- konnektorteenuse tarkvara ei saa praktiliselt muuta, ainult seadistada
- sobib kõigile konnektorteenusest huvitunud teenusepakkujatele (teadaolevalt RIK, EMTA, KEMIT, TEHIK)
    - nii halduse lihtsuse seisukohalt
    - kui ka seetõttu, et konnektorteenuses ei saa metaandmete laadimist erinevate teenusepakkujate jaoks lahendada erinevalt (s.t kõigile peab kehtima üks kahest: teenusepakkuja metateave kas võetakse HTTPS otspunktist või laetakse lokaalsest failist)
- NB! ei piirdu üldiste määratlustega “vastavale eIDASele”, “vastavalt RIA krüptoalgo-de uuringule”, “vastavalt SAML-le” jne
- arvestab võimaliku tulevikuvajadusega krüptoalgoritme vahetada
- on programmeerija poolt u 1 nädalaga teostatav (töö teenusepakkuja liideses)
    - NB! seetõttu turva vm soovide "max-i" peale keeramine pole lahendus

Vaja on ka juhiseid teenusepakkujale SAML metateabe koostamiseks ja konnektorteenuse metateabest arusaamiseks. 

Siin räägime SAML sõnumivahetusest. TLS turvamine on eraldi kiht.

Selleks:

- tuleb anda täpsed juhised, mida teenusepakkuja oma SAML metateabes peab kirja panema 
- anda täiendavad juhised, kuidas teenusepakkuja konnektorteenuse SAML metateavet peab tõlgendama
- leppida kokku, milliseid krüptoalgoritme kasutatakse
    - sh nõuded võtmepikkustele
- leppida kokku nõuded sertidele
    - väljaandja v usaldusankur
    - tüüp
    - kehtivusparameetrid
    - nõuded konkreetsetele elementidele (konnektorteenus ei aktsepteeri muidu)
- metateabe transpordi viis
- metateabe uuendamisse puutuv

Profiili koostamisel on arvestatud eIDAS krüptonõuetega ja RIA krüptoalgoritmide uuringu 2017 soovitustega (vt [Viited](Viited)).

## Nõuded

1. teenusepakkuja peab koostama oma süsteemi kohta metateabe, vastavalt eIDAS-e nõuetele (vt jaotis [Teenusepakkuja metateave](https://e-gov.github.io/eIDAS-Connector/Profiil#teenusepakkuja-metateave))
2. metateabe transport teisele osapoolele
    1. konnektorteenuse metateave publitseeritakse HTTPS otspunktis:
        - [https://eidastest.eesti.ee/EidasNode/ConnectorMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorMetadata) (testkeskkond)
        - [https://eidas.eesti.ee/EidasNode/ConnectorMetadata](https://eidas.eesti.ee/EidasNode/ConnectorMetadata) (toodangukeskkond)
    2. teenusepakkuja publitseerib metateabe samuti HTTPS otspunktis (vt märkus 3)
        - teatab otspunkti RIA-le
    3. soovi korral annab osapool (tulemüüriga) juurdepääsu metateabele ainult partnerile
    4. täiendavate metateabe transpordi kaitsemeetmete (IPSec, VPN) toetus põhjendatud turvavajaduse korral, läbirääkimisega RIA-ga
    5. manuaalne vm _out of band_ metateabe edastus - ei ole toetatud
3. metateabe allkirjastamine
    1. on kohustuslik
    2. allkirja tuleb metateabe võtmisel valideerida, kogu usaldusahela ulatuses
    3. usaldusankruks SK ID Solutions AS juursert
4. metateabe uuendamine
    1. osapool võib metateavet puhverdada, kuni `md:EntityDescriptor` atribuudis `validUntil` määratud ajamomendini
    2. metadatas tuleb määrata `validUntil` väärtus. Soovitatav väärtus on 24 h. 
5. serdid
    1. sertide ristkasutus:
        - on lubatud allkirjastamise puhul - metateabe ja SAML-sõnumi võib allkirjastada sama serdiga. Märkus: oma sõnumite krüpteerimist teenusepakkuja ei vajagi.
    2. väljaandja:
        - soovitame SK ID Solutions AS väljaantud serte; muu väljaandja serdi kasutamine kooskõlastada RIA-ga
            - NB! Sert peab ka tehniliselt eIDAS konnektorteenuse tarkvaraga sobima. 
    3. usaldusankur:
        - SK ID Solutions AS juursert
    4. serdi parameetrid:
        - asutusele antud, KLASS3 sert
            - NB! Sert peab ka tehniliselt eIDAS konnektorteenuse tarkvaraga sobima. Dokument võib selles osas täpsustuda.
    5. self-signed sertide kasutus
        - on lubatud testkeskkonnas
6. räsialgoritm
    - toetama peab algoritme:
        - `http://www.w3.org/2001/04/xmlenc#sha512` (peamine)
        - `http://www.w3.org/2001/04/xmlenc#sha256` (alternatiiv)   
7. allkirjastamine
    1. vahetatavad SAML-sõnumid allkirjastatakse
    2. toetama peab algoritme:
        - `http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512` (peamine)
        - `http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256` (alternatiiv)
            - võtmepikkus: 384 bitti
        - `http://www.w3.org/2007/05/xmldsig-more#sha512-rsa-MGF1` (alternatiiv)
        - `http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1` (alternatiiv)
            - võtmepikkus: 4096 bitti
            - Märkus. eIDAS krüptonõuetes esineb RSA-MGF1 nimetuse RSASSA-PSS all   
8. krüpteerimine
    1. teenusepakkuja poolt konnektorteenusele saadetavat SAML-sõnumit ei krüpteerita
    2. konnektorteenus krüpteerib teenusepakkujale saadetava SAML-sõnumi
    3. toetama peab algoritme:
        - `http://www.w3.org/2009/xmlenc11#aes256-gcm` (peamine)
        - `http://www.w3.org/2009/xmlenc11#aes128-gcm` (alternatiiv)   

## Otspunktid

Joonisel 1 on kujutatud liidestuva süsteemi seisukohalt olulised metateabe ja SAML-sõnumite vastuvõtu otspunktid:

- RIA eIDAS konnektorteenuse
    - metateabe otspunkt `/ConnectorResponderMetadata`
    - SAML autentimispäringsõnumite vastuvõtupunkt `/ServiceProvider`
- teenusepakkuja
    - metateabe otspunkt `/Metadata`
    - SAML autentimisvastussõnumite vastuvõtupunkt `/ReturnPage`.

Kõigi otspunktide URL-id on seadistatavad. Konkreetsed URL-id määravad osapooled metateabes.

<img src='https://e-gov.github.io/eIDAS-Connector/img/Otspunktid.PNG' style='width:500px;'>

Joonis 1. Metateabe otspunktid (punasega) ja SAML sõnumite vastuvõtupunktid

## Sõnumivahetuse ülevaade

Konnektorteenus määrab oma metateabega milliseid algoritme ta toetab (algoritmid määratakse ära konfiguratsioonifailis). Liidestujad peavad selle info arvesse võtma. Teenusepakkuja metadata kirjeldab milliseid algoritme tema ise SAML sõnumit vastu võttes toetab.

Autentimispäringu töötlemisel: 
1. Teenusepakkuja teeb enne SAML autentimispäringsõnumi koostamist päringu konnektorteenuse metaandmete otspunkti `/ConnectorResponderMetadata` (või kasutab puhverdatud metateavet).
2. Teenusepakkuja valib allkirjastamisalgoritmi, konnektorteenuse metadata alusel (peab olema üks neist, mis on toodud konnektorteenuse metadatas).
3. Teenusepakkuja allkirjastab autentimispäringu oma privaatvõtmega.
4. Teenusepakkuja saadab SAML autentimispäringsõnumi konnektorteenuse otspunkti `/ServiceProvider` (sirviku ümbersuunamisega).
5. Konnektorteenus teeb päringu Teenusepakkuja metaandmete otspunkti `/Metadata` (või kasutab puhverdatud metateavet).
6. Konnektorteenus otsustab, kas ta usaldab saatjat (kas tulnud päringu sees olev allkirja võti kuulub ka tegelikult saatjaga seotud metateabesse). Konnektorteenus valideerib `SAMLRequest` päringu allkirja ainult juhul, kui see on moodustatud algoritmiga, mis on tema lubatud allkirjameetodite nimekirjas (sama nimekiri, mida ta reklaamib välja oma metadatas - konfis toodud kui whitelist). Muul juhul annab vea.

Autentimisvastuse töötlemisel:
7. Sihtriigi vahendusteenusest tulev päring võetakse vastu, kontrollitakse ärireeglite vastu ja moodustatakse uus vastus, mis on allkirjastatud konnektorteenuse enda privaatvõtmega. NB! Allkirja algoritm otsustatakse konnektorteenuse seadetes oleva algoritmi alusel.
8. Konnektorteenus, enne SAML autentimisvastussõnumi saatmist, teeb päringu Teenusepakkuja metaandmete otspunkti `/Metadata`.
9. Konnektorteenus saadab SAML autentimisvastussõnumi Teenusepakkuja otspunkti `/ReturnPage`.
10. Teenusepakkuja teeb päringu konnektorteenuse metaandmete otspunkti `/ConnectorResponderMetadata` (sarnaselt autentimispäringu töötlemise p 6).

## eIDAS konnektorteenuse metateave

Selgitame eIDAS konnektorteenuse poolt liidestuvale süsteemile pakutava metateabe tähendust.

- testteenus: [https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata](https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata) 
- toodangteenus: [https://eidas.eesti.ee/EidasNode/ConnectorResponderMetadata](https://eidas.eesti.ee/EidasNode/ConnectorResponderMetadata) 

- `md:EntityDescriptor` - kirjeldatud on SAML olem (entity)
    - `entityID` - nimega `https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata`
    - `validUntil` - kirjeldus kehtib 24 h

- `ds:Signature` - kirjeldus on allkirjastatud XML allkirjaga
    - `ds:CanonicalizationMethod` - kanoniseerimisalgoritm - `xml-exc-c14n`
    - `ds:SignatureMethod` - allkirjaalgoritm on `rsa-sha512`
    - `ds:Transform` - _enveloped signature_, algoritm `xml-exc-c14n`
    - `ds:DigestMethod` - räsialgoritm `xmlenc#sha512`
    - `ds:Digestvalue` - räsiväärtus
    - `ds:SignatureValue` - allkirjaväärtus
    - `ds:KeyInfo` - X509 sertifikaat

- `md: Extensions` - metaandmete publitseerija ja tarbija vahel kokkulepitud spetsiifilised metaandmed

- `alg:Digestmethod` - konnektorteenus toetab räsialgoritme
    - `http://www.w3.org/2001/04/xmlenc#sha512` 
    - `http://www.w3.org/2001/04/xmlenc#sha256`

- `alg:SigningMethod` - toetatavad allkirjaalgoritmid
    - `http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512`
    - `http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256`
    - `http://www.w3.org/2007/05/xmldsig-more#sha512-rsa-MGF1`
    - `http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1`

- `md: IDPSSODescriptor` - “SSO võimekusega IdP” - kirjeldatava olemi “roll”
    - `WantAuthnRequestsSigned` - nõuab, et autentimispäringu sõnum p.o allkirjastatud
    - `protocolSupportEnumeration` - ütleb, et toetab SAML 2.0-i

- `md:KeyDescriptor` - avaldab konnektorteenuse sertifikaadid ja kirjeldab toetatavad krüpteerimisalgoritmid
    - `signing` > `KeyInfo` - allkirjastamissertifikaat
    - `encryption` > `KeyInfo` - krüpteerimissertifikaat

- `md:EncryptionMethod` - toetatavad algoritmid
    - `http://www.w3.org/2009/xmlenc11#aes256-gcm` 
    - `http://www.w3.org/2009/xmlenc11#aes128-gcm` 

- `md:NameIDFormat` - Määrata tuleb: 
    - `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`
    - `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`
    - `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`
    - Vt ka märkus 5. 

- `md:SingleSignOnService`
atribuutidega `Binding` ja `Location` määratakse, et konnektorteenus võtab SAML sõnumeid vastu URL-il `https://eidastest.eesti.ee/EidasNode/ServiceProvider`.  Saatmisviisiks on HTTP `POST` (HTTP Redirect ei ole toetatud).

- `saml2:Attribute`
atribuutidega `FriendlyName`, `Name` ja `Nameformat` kirjeldatakse eIDAS atribuudid, mida konnektorteenuse kaudu saab küsida (40+)

- `md:Organization` - teave RIA kohta

- `md:ContactPerson` - teave teenuse kontaktisiku kohta.

Vt ka [Metadata seletus](https://e-gov.github.io/eIDAS-Connector/MetadataSeletus#eidas-konnektorteenus-suhtluses-siseriikliku-liidestatud-s%C3%BCsteemiga), jaotis "Konnektorteenus suhtluses siseriikliku liidestatud süsteemiga".

## Teenusepakkuja metateave

Vt [teenusepakkuja metateabe näidet](TeenusepakkujaMetateave).

Teenusepakkuja peab koostama ja konnektorteenusele HTTPS-ga kättesaadavaks tegema järgmise SAML metateabe.

`md:EntityDescriptor` | kirjeldatava SAML-olemi (_entity_) ümbris-element 
    - `entityID` | kirjeldatava SAML-olemi identifikaator. Väärtuseks anda metateabe otspunkti URL, nt `https://eidas.asutus.ee/metadata` 
    - `validUntil` | kirjeldus kehtib 24 h.
`ds:Signature` | kirjeldus on allkirjastatud XML allkirjaga
    - `ds:CanonicalizationMethod` | kanoniseerimisalgoritm on `xml-exc-c14n`
    - `ds:SignatureMethod` | allkirjaalgoritm on `rsa-sha512`
    - `ds:Transform` | _enveloped signature_, algoritm `xml-exc-c14n`
    - `ds:DigestMethod` | räsialgoritm `xmlenc#sha512`
    - `ds:Digestvalue` | räsiväärtus
    - `ds:SignatureValue` | allkirjaväärtus
    - `ds:KeyInfo` | X.509 sertifikaat
`md: Extensions` | metaandmete publitseerija ja tarbija vahel kokkulepitud spetsiifilised metaandmed
    - `eidas:SPType`: `public` | liidestuja on avalikust sektorist
`alg:Digestmethod` | teenusepakkuja toetab räsialgoritme
    - `http://www.w3.org/2001/04/xmlenc#sha512` |
    - `http://www.w3.org/2001/04/xmlenc#sha256` |
`alg:SigningMethod` | määratleb algoritmid, mida teenusepakkuja SAML-autentimispäringu allkirjastamiseks kasutab. Seada järgmised 4 algoritmi:
    - `http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha512` |
    - `http://www.w3.org/2001/04/xmldsig-more#ecdsa-sha256` |
    - `http://www.w3.org/2007/05/xmldsig-more#sha512-rsa-MGF1` |
    - `http://www.w3.org/2007/05/xmldsig-more#sha256-rsa-MGF1` |
`md: SPSSODescriptor` | “SSO võimekusega teenusepakkuja” - kirjeldatava olemi “roll”
    - `AuthnRequestsSigned` | panna väärtus `true` - ütleb, et autentimispäringusõnum allkirjastatakse
    - `WantAssertionsSigned` | panna väärtus `true` - ütleb, et autentimispäringusõnum p.o allkirjastatud
    - `protocolSupportEnumeration` | panna väärtus `urn:oasis:names:tc:SAML:2.0:protocol` - ütleb, et toetab SAML 2.0-i
`md:KeyDescriptor` | allkirjastamissertifikaat
    - `use` |  panna väärtus `signing` - allkirjastamine 
    - `KeyInfo` | X.509 allkirjastamissertifikaat
`md:NameIDFormat` | autenditava isiku identifikaatori või nime "püsivust" iseloomustavad väärtused. Elemendil ei ole eIDAS kontekstis suurt tähendust, kuid Node tarkvara nõuab väärtust, seetõttu pange:
    - `unspecified` | “tähenduseta”, vt ka märkus 5
`md:AssertionConsumerService`
    - `Binding` | määratakse, et liidestuv süsteem võtab autentimisvastuse vastu URL-il (nt `https://eidas.asutus.ee/ReturnPage`) 
    - `Location` | ja autentimisvastus tuleb saata `HTTP-POST`-ga.
    - `index` | `0`
    -  `isDefault` | `true`
`md:Organization` | teave teenusepakkuja organisatsiooni kohta. Täitke näite eeskujul.
`md:ContactPerson` | teave teenusepakkuja kontaktisiku kohta. Täitke näite eeskujul. Siia võiks panna kasutajatoe kontaktandmed.

## Märkused

1 Usaldusse puutuv SAML tasemel tuleks võimalikult ära määrata SAML metateabega, vt [SAML V2.0 Metadata Interoperability Profile v1.0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-metadata-iop-cs-01.html) (2009). Samas tuleb olla realistlik: dünaamilist algoritmide kokkuleppimist e kätlust poolte vahel SAML standardid ei konkretiseeri. Me ei jõuaks ka teostada. Vrdl [SAML v2.0 Metadata Profile for Algorithm Support Version 1.0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-metadata-algsupport.html).

2 Vrdl Soome avaliku sektori keskse autentimisteenuse Suomi.fi-tunnistus juhised teenusepakkujate SAML metateabele: [https://esuomi.fi/palveluntarjoajille/tunnistus/tekninen-aineisto/asiointipalvelun-metadatatiedot/](https://esuomi.fi/palveluntarjoajille/tunnistus/tekninen-aineisto/asiointipalvelun-metadatatiedot/).

3 Alternatiiv võiks olla, et teenusepakkuja metateabe loetakse konnektorteenusesse sisse lokaalsest failist. See eeldaks metateabe _out of band_ (muu kanali kaudu) toimetamist RIA-sse. Eelistame transporti üle HTTPS-i.

4 Teenusepakkuja metateabe nõuete koostamisel on aluseks võetud eIDAS konnektorteenuse tarkvaraga kaasas oleva liidestuva süsteemi näiterakenduse Demo SP metaandmeseadistus, [https://eidastest.eesti.ee/SP/metadata](https://eidastest.eesti.ee/SP/metadata). Vt ka [Metadata seletus](https://e-gov.github.io/eIDAS-Connector/MetadataSeletus#demo-sp-metadata), jaotis "Demo SP metadata". 

5 `md:NameIDFormat` väärtused iseloomustavad autenditava isiku identifikaatori või nime "püsivust" (kestvust üle mitme sisselogimise vms). eIDASe kontekstis tähendus on segane. Teema kohta võib soovi korral lugeda [https://wiki.shibboleth.net/confluence/display/CONCEPT/NameIdentifiers](https://wiki.shibboleth.net/confluence/display/CONCEPT/NameIdentifiers) ja [http://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf](http://docs.oasis-open.org/security/saml/v2.0/saml-core-2.0-os.pdf), jaotised 8.3.7, 8.3.8 ja 8.3.1. 

## Muutelugu

| Versioon, kuupäev | Muudatus |
|-----------------|--------------|
| 0.4, 06.02.2018 | Täpsustatud `md:NameIDFormat` väärtusi. Lisatud teenusepakkuja metadata näide. |
| 0.3, 02.02.2018   | Täpsustatud metateabe transporti (lisatud nõuded 2.4, 2.5). Täpsustatud self-signed sertide kasutust (nõue 5). |
| 0.2, 29.01.2018   | Esimene kommenteerimiseks saadetud versioon |
