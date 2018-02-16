---
permalink: Liitumisjuhend
---

# Liitumisjuhend
{: .no_toc}

- TOC
{:toc}

Juhend kirjeldab mida peate eIDAS konnektorteenuse kasutuselevõtmiseks tegema.

1. Selgitage välja millistes oma e-teenustes soovite RIA eIDAS konnektorteenust kasutada.
    - selleks tutvuge [ärikirjeldusega](Arikirjeldus) ja [piiriülese eIDAS-autentimise liidestamismeetodi abimaterjaliga](Valik) 
2. Kavandage ja teostage arendustöö
    - otsustage, kas ühendate iga oma e-teenuse eraldi või teete ühe ühenduse, nt asutuse autentimislahenduse kaudu - mis võtab suhtluse konnektorteenusega enda peale - ja jagab autentimist e-teenustele
    - teil on vaja oma e-teenuses teha väike tarkvaraarendus:
        - metaandmete otspunkt
        - SAML autentimispäringu saatmine konnektorteenusele
        - SAML autentimisvastuse vastuvõtmine konnektorteenuselt
    - konnektorteenuse [spetsifikatsioonis](Spetsifikatsioon) leiate kõik vajalikud nõuded ja juhatused
        - kogenud arendajal võib liidese ehitamiseks kuluda u 2 nädalat
        . soovitatav, aga mitte vältimatu on SAML ja XML krüpto (allkirjastamine, dekrüpteerimine) kogemus
        - koodi eeskujuna võite kasutada RIA tellitud [eIDAS-Client](https://github.com/e-gov/eIDAS-Client) komponenti
3. Andke meile teada liitumissoovist
    - saatke vabas vormis sooviavaldus help@ria.ee
        - märkige, millises e-teenuses (-teenustes) soovite konnektorteenust kasutada (kui see on teada)
    - testkeskkonnaga liitumiseks sellest piisab
    - sooviavalduse võib saata juba enne teie arendustöö algust
    - toodangukeskkonnaga liitumiseks sõlmite pärast liidese edukat testimist meiega eraldi kokkuleppe
4. Saatke oma metateabe otspunkti allkirjasert
    - genereerige _self-signed_ X.509 sert
        - nt [onelogin veebiteenuse abil](https://developers.onelogin.com/saml/online-tools/x509-certs/obtain-self-signed-certs)
    - saatke sert e-postiga meile (help@ria)
    - meie admin paigaldab serdi konnektorteenuse võtmehoidlasse
    - sellega on juurdepääs konnektorteenuse testkeskkonnale teile avatud
5. Saage liides testkeskkonnas käima
    - seadistage oma e-teenus konnektorteenuse otspunktide vastu:
        - metateave: https://eidastest.eesti.ee/EidasNode/ServiceMetadata
        - autentimispäring: https://eidastest.eesti.ee/EidasNode/ServiceProvider
    - teie e-teenus peab suutma:
        - kuvada kasutajale "lipukesed"
        - võimaldama kasutajal valida sihtriigi
        - moodustama SAML autentimispäringu
        - saatma selle HTTP POST ümbersuunamisega konnektorteenusele
    - sihtriigiks valige esialgu Eesti (EE)
        - proovige, kas Eesti autentimisvahenditega töötab
            - Eesti autentimine võib olla seadistatud kasutama [mobiil-ID testnumbreid](https://www.id.ee/?id=36373). Proovige kõigepealt _happy path_ numbriga (´+37200000766´, isikukood ´11412090004´). Seejärel proovige ka teisi testnumbreid  
    - kui saate veateate, siis kasutage veateadete teatmikku [eIDAS-Node Error Codes](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+-+Current+release)
        - küsimuste korral abistab teid help@ria.ee
    - kui olete sõnumiliikluse käima saanud, andke meile teada
6. Liituge toodangukeskkonnaga
    - eelduseks on liidese testimise edukus (vt eelmine punkt)
    - sõlmime teenuslepingu
    - toodangukeskkonnas on vaja juba SK serte
    - toodangukeskkonnas võivad rakenduda täiendavad turvameetmed; nendest otsesuhtluses.
