---
permalink: FAQ
---

**Arhiveeritud** 
{:.teade}

## Sagedasi küsimusi

**Küsimus: Mida tähendavad `NameIDFormat` väärtused `persistent`, `transient` ja `unspecified`? Miks neid nõutakse?**

SAML protokoll näeb ette, et autentimisvastuses tagastatakse tuvastatud isiku (_Subject_) nimi vm identifikaator (_NameID_).

````
    <saml2:Subject>
      <saml2:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified">
          Jüri Mets
      </saml2:NameID>
          ...
    </saml2:Subject>
````  

See "nimi-identifikaator" võib olla varjunimi (`persistent`), ühekordselt kasutatav (`transient`) vm omadustega ("nimeformaadiga").

Teenusepakkuja määratleb oma metateabes, elemendis `NameIDFormat` milliste omadustega NameID-sid ta suudab käsitleda.

eIDASe sõnumistandard (vt [Viited](Viited), eIDAS Message Format v1.1-2, jaotis 2.2 Name Identifiers) nõuab nimeformaatide `persistent`, `transient` ja `unspecified` toetust.

eIDAS-es aga sellel praktilist tähendust ei ole. eIDAS-es isiku identifikaator (rahvuslik ID-kood), ees- ja perekonnanimi tagastatakse autentimisvastuses atribuutidena, elemendis `<saml2:AttributeStatement>`.

SAML-i standard aga ei luba elementi `<saml2:Subject>` SAML-sõnumis ära jätta. Seetõttu, märkigem nõutud väärtused (`persistent`, `transient` ja `unspecified`) ja teadkem, et sisuliselt need meid ei mõjuta. 

Kirjandus<br>
- [SAML 2.0 Multiple md:NameIDFormat tag in service provider metadata](https://stackoverflow.com/questions/25620820/saml-2-0-multiple-mdnameidformat-tag-in-service-provider-metadata)
- [What is the difference between SAML 2.0 Persistent and SAML 2.0 Transient?](https://success.salesforce.com/answers?id=90630000000Cub2AAC)
