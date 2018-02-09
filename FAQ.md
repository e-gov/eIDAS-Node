__---
permalink: FAQ
---

## Sagedased küsimused

**Küsimus: Mida tähendavad `NameIDFormat` väärtused `persistent`, `transient` ja `unspecified`? Miks neid nõutakse?**

SAML protokoll näeb ette, et autentimisvastuses tagastatakse tuvastatud isiku (_Subject_) nimi (_Name_). Nime all mõeldakse igasugust isikut tähistavat nime või identifikaatorit.

````
    <saml2:Subject>
      <saml2:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified">
          Nimi
      </saml2:NameID>
          ...
    </saml2:Subject>
````  

Nimi võib olla varjunimi (`persistent`), ühekordselt kasutatav (`transient`) vm omadustega ("nimeformaatidega").

Teenusepakkuja määratleb oma metateabes, elemendis `NameIDFormat` milliste omadustega nimesid ta suudab käsitleda.

eIDASe sõnumistandard (vt [Viited](Viited), eIDAS Message Format v1.1-2, jaotis 2.2 Name Identifiers) nõuab nimeformaatide `persistent`, `transient` ja `unspecified` toetust.

Praktilist tähendust sellel aga ei ole, sest isiku identifikaator (rahvuslik ID-kood), ees- ja perekonnanimi tagastatakse autentimisvastuses atribuutidena, elemendis `<saml2:AttributeStatement>`.

Element `<saml2:Subject>` ei tohi aga SAML-sõnumis puududa. Seetõttu, märkigem nõutud väärtused (`persistent`, `transient` ja `unspecified`) ja teadkem, et sisuliselt need meid ei mõjuta. 

Kirjandus<br>
- [SAML 2.0 Multiple md:NameIDFormat tag in service provider metadata](https://stackoverflow.com/questions/25620820/saml-2-0-multiple-mdnameidformat-tag-in-service-provider-metadata)
- [What is the difference between SAML 2.0 Persistent and SAML 2.0 Transient?](https://success.salesforce.com/answers?id=90630000000Cub2AAC)