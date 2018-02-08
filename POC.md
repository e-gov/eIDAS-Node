---
permalink: POC
---

# Javier Garcia

Kes on Javier Garcia?

Javier Garcia on eIDAS testisik.

Kõik võib-olla ei tea, kuid meil on aasta algusest käimas EL piiriülese autentimistaristu eIDAS Eesti otsa arendustööd.

Nortali arendajad said nüüd käima tervelt kuuest lülist koosneva ahela. Makettrakendus (mis etendab Eesti e-teenust) -> TARA -> eIDAS klient -> RIA eIDAS konnektorteenus -> CEF eIDAS Node (Brüsselis) -> CEF eIDAS Validator

Traageldatud, aga juba töötab. Välismaalase saadame meilt piki ahelat ja ta tuleb autenditult tagasi. Seni veel testina ja isiku nimeks on alati Javier Garcia.  

Tööd ja tehnilist keerukust on ees veel palju. Siiski on nüüd kindlus, et piiriülene teenus suudetakse tähtajaks valmis teha.

Siinkohal üks huvitav moment. Tehnilises plaanis on kõi keerelnud küsimuse ümber, kuidas siseriiklik TARA-teenus eIDAS konnektorteenusega ühendada. Erinevad protokollid, erinevad teegid,samade teekide erinevad versioonid jne - väga raske otsustada. Kas

a) eIDAS-kliendi paigutamine TARA sisse

<p style='text-align:center;'><img src='img/POC1.PNG' style='width:700px'></p>

või 

b) eIDAS-klient eraldi mikroteenusena?

<p style='text-align:center;'><img src='img/POC2.PNG' style='width:700px'></p>

Nortali mehed tegid küsimuse selgitamiseks kaks POC-i e katserakendust (Proof of Concept). Võib küsida, kas oli mõtet programmeerida kaht erinevat varianti? Oli küll, sest katserakendustest üks ei läinud üldse käima - teekide kokkusobimatus, probleemid rakenduse voo kohandamisel jm. Kui vaatate allolevalt plokkskeemilt, siis arhitektuurilist otsust ei olnudki enam vaja teha. Faktide loogikast koorus otsus ise välja. 

<p style='text-align:center;'><img src='img/Plokkskeem.PNG' style='width:450px'></p>

Teemaga haakub ettevalmistatav A/B testimise metoodika arenduse hange. Eesti.ee baasil tahame riigi IT arenduspraktikasse sisse tuua idufirmades populaarse tehnika, mille idee on, et otsustavad faktid, mitte disainerid-arhitektid.