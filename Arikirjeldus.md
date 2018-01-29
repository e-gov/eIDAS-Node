---
title: eIDAS konnektorteenus. Ärikirjeldus
permalink: Arikirjeldus
---

# RIA eIDAS konnektorteenus. Ärikirjeldus
{:.no_toc}

versioon 1.2, 29.01.2018

* TOC
{:toc}

## 1 Ülevaade

_eIDAS konnektorteenus_, edaspidi ka _teenus_, on RIA poolt käitatav taristuteenus, mis võimaldab Eesti asutuse autentimisrakendusel autentida EL teise liikmesriigi eID kasutajat.

Tehniliselt teostab teenust RIA taristusse paigaldatud _eIDAS Connector servertarkvara_, mis liidestatakse ühelt poolt asutuse autentimisrakendusega ja teiselt poolt EL teiste riikide eIDAS taristutega.

Käesolev juhend on suunatud teenusega liituvale asutusele ja esitab teenuse kasutuselevõtmiseks vajaliku teabe ja tööd, eelkõige ärivaates.

Liidestamise tehnilised üksikasjad vt:
- konnektorteenuse [tehniline spetsifikatsioon](Spetsifikatsioon)
- [eIDAS siseriiklikud usaldus- ja krüptonõuded](Profiil).

## 2 Mõisted

_eIDAS konnektorteenus_ - RIA poolt käitatav taristuteenus, mis võimaldab Eesti asutuse autentimisteenusel teostada EL teise liikmesriigi eID kasutaja autentimist.<br>
_autentimisrakendus_ - asutuse poolt käitatav tarkvarakomponent, mis korraldab asutuse e-teenuse kasutaja (kes võib olla nii Eesti kui ka EL teise liikmesriigi eID kasutaja) autentimist (isikusamasuse tuvastamist), suheldes selleks eIDAS konnektorteenusega.<br>
_asutus_ - eIDAS konnektorteenust kasutava autentimisrakenduse omanikasutus.<br>
_teenusepakkuja_ - sama, mis _asutus_ - kasutajale teenust pakkuv asutus (_Service Provider_ eIDAS terminoloogias)

## 3 Teenusega liitumine

Teenuse kasutuselevõtmiseks esitab asutus RIA-le sooviavalduse eIDAS konnektorteenuse kasutamiseks.

Lepitakse kokku kontaktisikud ja tehakse kindlaks, et tehnilistest küsimustest saadakse õigesti aru. RIA nõustab liidestamise tehnilistes küsimustes.

Autentimisrakenduse arendusega paralleelselt valmistatakse ette testiplaan. Testiplaan kinnitatakse RIA ja asutuse poolt. 

Testide eduka täitmise järel võtakse teenus toodangukeskkonnas kasutusele. Teenuse kasutamise aluseks on teenuslepe.

## 4 Liidestamistööd

Liidestamiseks tuleb teostada järgmised tööd:

|            |  Asutus     | RIA               |
|---------|:------------:|:---------------:|
|  1  Autentimisrakenduse tarkvara loomine või täiendamine |  +  |   |
|  2  Autentimisrakenduse paigaldamine ja häälestamine ühendumiseks RIA-s asuva eIDAS Connector serveriga | + |   |
| 3 Liidese testimine | + | + |
| 4 Liidese käikulaskmine | + | + |

Teenusest arusaamist võib hõlbustada demorakendusega tutvumine. Demorakenduses saab läbi mängida kasutaja autentimisvoo. Demorakenduse lähtekoodi koos dokumentatsiooniga saab alla laadida CEF veebilehelt (demo SP) [eIDAS Node tarkvara].

Tutvuda saab ka RIAs ülespandud demorakendusega, selle URL on:

`https://eidastest.eesti.ee/SP/populateIndexPage`

Kasutada võib RIA poolt väljatöötatavat teeki [eIDAS Client](https://github.com/e-gov/eIDAS-Client).

## 5 Liidese testimine

Testimiseks kasutatakse eIDASe konnektorteenuse testinstantsi. Testimise autentimispäringuid vastuvõtva teenusepunkti URL on: 

`https://eidastest.eesti.ee/EidasNode/ServiceProvider`

## 6 Liidese käikulaskmine

Teenuse avamiseks toodangukeskkonnas peab teenus olema testitud. 

## Muutelugu

v 1.1 - 29.01.2018<br>
v 1.0 - 30.05.2017





