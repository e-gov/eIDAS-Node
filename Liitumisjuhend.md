---
permalink: Liitumisjuhend
---

# Liitumisjuhend
{: .no_toc}

- TOC
{:toc}

Juhend kirjeldab mida peate eIDAS konnektorteenuse kasutuselevõtmiseks tegema.

1. Selgitage välja millistes oma e-teenustes soovite RIA eIDAS konnektorteenust kasutada.
    - selleks tutvuge konnektorteenuse [ärikirjeldusega](Arikirjeldus)
    - ja [piiriülese eIDAS-autentimise liidestamismeetodi valiku abimaterjaliga](Valik)
        - lihtsa eIDAS-autentimise võite saada ka autentimisteenusest [TARA](https://e-gov.github.io/TARA-Doku/)
2. Kavandage ja teostage arendustöö
    - tehke arhitektuuriline otsus: kas ühendate iga oma e-teenuse eraldi - või teete ühe ühenduse, mis võtab suhtluse konnektorteenusega enda peale ja jagab autentimist e-teenustele
    - teil on vaja oma e-teenuses teha väike tarkvaraarendus:
        - metaandmete otspunkt
        - sihtriigi valik ("lipukesed")
        - SAML autentimispäringu koostamine 
        - ja saatmine konnektorteenusele
        - SAML autentimisvastuse vastuvõtmine konnektorteenuselt
        - ja lahtivõtmine
    - konnektorteenuse [spetsifikatsioonist](Spetsifikatsioon) leiate kõik vajalikud nõuded ja juhatused
        - kogenud arendajal kulub liidesele u 2 nädalat
        . nõutav SAML ja XML krüpto (allkirjastamine, dekrüpteerimine) tundmine
        - koodieeskujuks soovitame RIA tellitud [eIDAS-Client] (https://github.com/e-gov/eIDAS-Client) komponenti
3. Andke meile teada liitumissoovist
    - vabas vormis sooviavaldus aadressile `help@ria.ee`
        - märkige (kui on teada), mis e-teenuses soovite konnektorteenust kasutada
    - testkeskkonnaga liitumiseks sellest piisab
    - sooviavalduse võite saata juba enne arendustöö algust
    - toodangukeskkonnaga liitumiseks sõlmime liidese eduka testimise järel eraldi kokkuleppe
4. Saatke oma metateabe otspunkti allkirjasert
    - genereerige _self-signed_ X.509 sert
        - nt [onelogin](https://developers.onelogin.com/saml/online-tools/x509-certs/obtain-self-signed-certs) veebiteenuse abil
    - saatke sert e-postiga meile (`help@ria`)
    - meie admin paigaldab serdi konnektorteenuse võtmehoidlasse
    - sellega on juurdepääs konnektorteenuse testkeskkonnale avatud
5. Ajage liides testkeskkonnas käima
    - seadistage oma arendus konnektorteenuse otspunktide vastu:
        - metateave: `https://eidastest.eesti.ee/EidasNode/ServiceMetadata`
        - autentimispäring: `https://eidastest.eesti.ee/EidasNode/ServiceProvider`
    - teie e-teenus peab suutma:
        - publitseerida [nõuetekohase metateabe](Spetsifikatsioon#53-teenusepakkuja-metateave)
        - kuvada kasutajale "lipukesed"
        - lasta kasutajal valida sihtriigi
        - moodustadaa SAML autentimispäringu
        - saata autentimispäringu HTTP POST ümbersuunamisega konnektorteenusele
        - autentimispäringu vastu võtma, dešifreerima
        - ja moodustama autenditud kasutajale seansi
    - sihtriigiks valige esialgu Eesti (EE)
        - proovige, kas Eesti autentimisvahenditega töötab
            - Eesti autentimine võib olla seadistatud kasutama [mobiil-ID testnumbreid](https://www.id.ee/?id=36373).
                - proovige kõigepealt _happy path_ numbriga (´+37200000766´, isikukood ´11412090004´)
                - seejärel proovige ka teisi testnumbreid  
    - kui saate veateate, siis kasutage veateadete teatmikku [eIDAS-Node Error Codes](https://ec.europa.eu/cefdigital/wiki/display/CEFDIGITAL/eIDAS-Node+-+Current+release)
    - küsimuste korral abistab teid help@ria.ee
    - kui olete sõnumiliikluse käima saanud, andke meile teada
6. Liituge toodangukeskkonnaga
    - eelduseks on liidese testimise edukus (vt eelmine punkt)
    - sõlmime teiega teenuslepingu
    - toodangukeskkonnas on vaja juba SK serte
    - toodangukeskkonnas võivad rakenduda täiendavad turvameetmed (tulemüür vms)
        - nendest otsesuhtluses
