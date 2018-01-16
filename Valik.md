---
permalink: Valik
---

Piiriülene eIDAS-autentimine

# Liidestamismeetodi valik

Välismaalase autentimiseks e-teenuses on vaja e-teenust pakkuv infosüsteem liidestada piiriülese autentimise e eIDAS-taristuga. Selleks on kaks võimalust:

- liidestada RIA eIDAS konnektorteenusega
- liidestada autentimisteenusega TARA.

|      |   ___eIDAS konnektorteenus___     |   __TARA__     |
|------|:---------------------------------:|:---------------:|
| sobib asutusele: | kellel on juba oma autentimislahendus ja kes soovib lisada eIDAS-võimekuse | kes soovib välise teenuse abil lahendada nii piiriülese kui ka siseriikliku autentimise |
| piiriülene autentimine | jah | jah (alates kevad 2018) |
| siseriiklikud autentimismeetodid: | - | ID-kaart, m-ID |
| sessioonihaldus: | ei paku | ei paku |
| ühekordne sisselogimine (SSO) | ei paku | ei paku |
| liidestusprotokoll põhineb: | SAML 2 | OpenID Connect |
| adaptertarkvara: | - | jah, [eIDAS-Client](https://github.com/e-gov/eIDAS-Client) |   

## Liidestamine RIA eIDAS konnektorteenusega

___RIA eIDAS konnektorteenus___ on pääsupunkt Euroopa Liidu piiriülesesse autentimistaristusse (nn eIDAS-taristusse).

<img src='img/Liides-eIDAS-adapteriga.PNG' style='width: 700px;'>

 Vt lähemalt [https://e-gov.github.io/eIDAS-Connector/](https://e-gov.github.io/eIDAS-Connector/).

## Liidestamine autentimisteenusega TARA

___TARA___ on teenus, millega asutus saab oma e-teenuses autentida ID-kaardi või mobiil-ID kasutaja, alates kevadest 2018 ka välisriigi kasutaja.

<img src='img/Liides-TARA-ga.PNG' style='width: 700px;'>

 Vt lähemalt [https://e-gov.github.io/TARA-Doku/](https://e-gov.github.io/TARA-Doku/).