---
permalink: Valik
---

Piiriülene eIDAS-autentimine

NB! 18.04.2018<br><br>
Seoses muutustega CEF eIDAS tarkvaras (uus versioon 2.0) ei ole tagatud, et RIA eIDAS konnektorteenus säilib tulevikus muutumatul kujul. See tähendab riski, et liidestaja peab liidese ümber tegema. <br><br>
Standardne, soovitatud viis piiriülese eIDAS-autentimise kasutuselevõtmiseks on liidestada TARAga.
{:.teade} 

# Liidestamismeetodi valik

Välismaalase autentimiseks e-teenuses on vaja e-teenust pakkuv infosüsteem liidestada piiriülese autentimise e eIDAS-taristuga. Selleks on kaks võimalust:

- liidestada RIA eIDAS konnektorteenusega
- liidestada autentimisteenusega TARA.

|      |   ___eIDAS konnektorteenus___     |   __TARA__     |
|------|:---------------------------------:|:---------------:|
| sobib asutusele: | kellel on juba oma autentimislahendus ja kes soovib lisada eIDAS-võimekuse | kes soovib välise teenuse abil lahendada nii piiriülese kui ka siseriikliku autentimise.<br> Samas, TARA võib kasutada ka ainult piiriüleseks autentimiseks |
| piiriülene autentimine | jah | jah (alates kevad 2018) |
| siseriiklikud autentimismeetodid: | - | ID-kaart, m-ID |
| sessioonihaldus: | ei paku | ei paku |
| ühekordne sisselogimine (SSO) | ei paku | ei paku |
| liidestusprotokoll põhineb: | SAML 2 | OpenID Connect |
| adaptertarkvara: | jah, [eIDAS-Client](https://github.com/e-gov/eIDAS-Client) | - |   
