---
permalink: Usaldusprofiil
---

# Usaldusprofiil

## Mida on vaja?

Usaldusmehhanismi (_trust establishment mechanism_) RIA eIDAS konnektorteenuse ja liidestuva süsteemi (SAML terminoloogias - teenusepakkuja) vahel, mis mahub piirangutesse:

- konnektorteenuse tarkvara ei saa praktiliselt muuta, ainult seadistada
- sobib kõigile teenusepakkujatele (teadaolevalt RIK, EMTA, KEMIT, TEHIK)
    - nii halduse lihtsuse seisukohalt
    - kui ka seetõttu, et konnektorteenuses ei saa metaandmete laadimist erinevate teenusepakkujate jaoks lahendada erinevalt (kõigile p.o üks kahest: kas otspunkti või lokaalsest failist laadimine)
- ei piirdu üldiste määratlustega “vastavale eIDASele”, “vastavalt RIA krüptoalgo-de uuringule”, “vastavalt SAML-le” jne
- arvestab võimaliku tulevikuvajadusega krüptoalgoritme vahetada
- on programmeerija poolt u 1 nädalaga teostatav (teenusepakkuja liideses)

Siin räägime SAML sõnumivahetusest. TLS turvamine on eraldi kiht.

Selleks:

- tuleb anda täpsed juhised, mida teenusepakkuja oma SAML metateabes peab kirja panema 
- anda täiendavad juhised, kuidas teenusepakkuja konnektorteenuse SAML metateavet peab tõlgendama.
- leppida kokku, milliseid krüptoalgoritme kasutatakse
    - sh nõuded võtmepikkustele
- leppida kokku nõuded sertidele
    - väljaandja v usaldusankur
    - tüüp
    - kehtivusparameetrid
    - nõuded konkreetsetele elementidele (konnektorteenus ei aktsepteeri muidu)
- metateabe transpordi viis
- metateabe uuendamisse puutuv

## Ettepanekud

- metateabe transport
    - konnektorteenuse metateabe publitseeritakse HTTPS otspunktis:
        - https://eidastest.eesti.ee/EidasNode/ConnectorMetadata (testkeskkond)
        - https://eidas.eesti.ee/EidasNode/ConnectorMetadata (toodangukeskkond)
    - teenusepakkuja publitseerib metateabe samuti HTTPS otspunktis
        - teatab otspunkti RIA-le
    - soovi korral annab osapool (tulemüüriga) juurdepääsu metateabele ainult partnerile
- metateabe allkirjastamine
    - on kohustuslik
    - allkirja tuleb metateabe võtmisel valideerida
    - usaldusankruks SK ID Solutions AS juursert
- metateabe uuendamine
    - md:EntityDescriptor atribuudi validUntil abil
    - minimaalne validUntil väärtus - ? 
- serdid
    - usaldusankruks SK ID Solutions AS juursert
- allkirjastamisalgoritm
    - ettepanek on, et  lepime kokku 1 peamise ja 1 alternatiivse
- krüpteerimisalgoritm
    - ettepanek on, et  lepime kokku 1 peamise ja 1 alternatiivse

## Teenusepakkuja metateabe nõutav struktuur


![](img/Otspunktid)

## Märkused

1 Usaldusse puutuv SAML tasemel tuleks võimalikult ära määrata SAML metateabega, vt [SAML V2.0 Metadata Interoperability Profile v1.0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-metadata-iop-cs-01.html) (2009). Samas tuleb olla realistlik: dünaamilist algoritmide kokkuleppimist e kätlust poolte vahel SAML standardid ei konkretiseeri. Me ei jõuaks ka teostada. Vrdl [SAML v2.0 Metadata Profile for Algorithm Support Version 1.0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-metadata-algsupport.html).

2 Vrdl Soome avaliku sektori keskse autentimisteenuse Suomi.fi-tunnistus juhised teenusepakkujate SAML metateabele: [https://esuomi.fi/palveluntarjoajille/tunnistus/tekninen-aineisto/asiointipalvelun-metadatatiedot/](https://esuomi.fi/palveluntarjoajille/tunnistus/tekninen-aineisto/asiointipalvelun-metadatatiedot/).

3 Alternatiiv: teenusepakkuja metateabe loetakse konnektorteenusesse sisse lokaalsest failist. See eeldab metateabe out of band (muu kanali kaudu) toimetamist RIA-sse.


