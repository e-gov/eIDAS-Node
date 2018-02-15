---
permalink: Liitumisjuhend
---

# Liitumisjuhend (DRAFT)
{: .no_toc}

- TOC
{:toc}

Käesolev juhend kirjeldab teenust kasutava asutuse ja teenusepakkuja (RIA) tegevusi eIDAS konnektorteenuse kasutuselevõtul.

Vt ka: [Viited](Viited)

## 1 Selgitada välja liidetavav e-teenus (või -teenused)

Asutus selgitab välja, kas ja millistes oma e-teenustes soovib eIDAS konnektorteenust kasutada. Selleks palume:
- tutvuda [ärikirjeldusega](Arikirjeldus), teenustaseme leppega (SLA-ga), vajadusel ka [liidese spetsifikatsiooniga](Spetsifikatsioon)
- heita pilk ka teenuse [teekaardiga](https://e-gov.github.io/TARA-Doku/#teekaart)
- vajadusel pidada nõu RIA-ga, `help@ria.ee`.

## 2 Kavandada ja teostada arendus
Asutus kavandab ja teostab teenuse kasutamiseks vajalikud arendustööd.
  - Klientrakenduse täiendamine eIDAS SAML protokolli kohase klientkomponendiga vt. [Viited](https://e-gov.github.io/eIDAS-Connector/Viited)
  - Kindlasti tutvuda eIDAS konnektorteenuse [spetsifikatsiooniga](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon)
  - Lahenduse näidisena võib tutvuda [makettrakendusega](https://github.com/e-gov/eIDAS-Client)

Eriti tähele panna:<br>


## 3 Liituda test eIDAS konnektorteenusega
Asutus esitab taotluse testteenusega liitumiseks. Taotluse võib esitada juba enne arenduse algust. Taotluses palume teatada:

1) e-teenus või -teenused, mille kasutajaid soovitakse eIDAS konnektorteenuse abil autentida.

2) kohustumus kasutada teenust eesmärgipäraselt. Sh testteenust kasutada ainult testimiseks, mitte toodangus autentimiseks.

3) klientrakenduse haldaja kontaktandmed (e-post, telefon, isikukood).

4) ...

Kirja tuleb panna kuidas käib test sertifikaatide vahetus!

Taotlus esitatakse ja edasine suhtlus teenuse haldamisel käib läbi RIA kasutajatoe, `help@ria.ee`. Vt lähemalt [RIA autentimisteenuste lehel](https://www.ria.ee/ee/autentimisteenused.html).

RIA, rahuldades taotluse:
- avab asutuse klientrakenduse testversioonile juurdepääsu testteenusele.

Testeenuse otspunktid on järgnevad:
- Metateabe otspunkt: https://eidastest.eesti.ee/EidasNode/ServiceMetadata
- Autentimispäringu otspunkt: https://eidastest.eesti.ee/EidasNode/ServiceProvider

## 4 Testida liidestust
- Asutus testib liidestust
- RIA abistab võimalike probleemide lahendamisel.
- Asutus esitab testiraporti [RIA vormil]()<-Link näidisele mis on vaja luua (dokument vaja panna ka viidete alla)

Testida tuleb vähemalt järgnevaid aspekte:

| ID | Eesmärk | Viide |
|----|--------|--------|
| 1 | Metateave peab olema korrektselt publitseeritud ning sisaldama kohustuslikke elemente | [Nõuded metateabe otspunktile](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon#53-teenusepakkuja-metateave) |
| 2 | Autentimispäring peab olema korrektselt koostatud ning sisaldama kohustuslikke elemente| [Nõuded autentimispäringule](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon#6-autentimisp%C3%A4ring) |
| 3 | Autentimise voog peab olema tervenesti läbitav | [Kasutusvoog](https://e-gov.github.io/eIDAS-Connector/Spetsifikatsioon#lisa-6-eduka-autentimisvoo-jadadiagramm), [Juhend testkasutajatega autentimiseks](Testkasutajad) |

Testimise hõlbustamiseks on loodud [testijuhtude kogum](Testjuhud), mida tuleb (või on soovituslik?) järgida.

## 5 Liituda eIDAS konnektor toodanguteenusega
Eduka testimise järel asutus esitab taotluse toodanguteenuse avamiseks klientrakendusele. Taotluses näidata:
1) e-teenus või -teenused, mille kasutajaid soovitakse eIDAS konnektorteenuse abil autentida<br>
2) ...

RIA, rahuldades taotluse:
- avab asutuse klientrakenduse toodanguversioonile juurdepääsu toodanguteenusele.


