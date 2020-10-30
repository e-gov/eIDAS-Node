---
permalink: Vookirjeldus-1
---



# eIDAS päringuvoog - Demo SP - HTTP Redirect
{: .no_toc}

- TOC
{:toc}

## Ülevaade

Käesolev dokument esitab samm-sammulise HTTP päringute liikumise eIDAS-autentimisel, Demo SP näitel, HTTP Redirect binding-u kasutamisel.

Kirjelduse eesmärk on:
- luua arusaamine, mis sõnumid ja kuidas liiguvad

Siin ei kirjeldata:
- laadilehtede ja skriptide laadimise päringuid
- sisuturbepoliitikaga seotud abistavaid päringuid
- serveripoolte omavahelisi päringuid SAML metaandmete otspunktide poole
- HTTP POST bindingu kasutamise juhtu - see väärib eraldi dokumenti.

## Suhtluse osapooled

- Demo SP (Demo Service Provider) - etendab e-teenust eIDASe mõistes (s.t süsteemi, mis kasutaja autentimist vajab)
    - Host: `eidastest.eesti.ee`
    - Otspunktid:
        - `https://eidastest.eesti.ee/SP/populateIndexPage`
        - `https://eidastest.eesti.ee/SP/changeProtocolBinding.action`
        - `https://eidastest.eesti.ee/SP/ReturnPage` 
        - `https://eidastest.eesti.ee/SP/populateReturnPage` 

- RIA eIDAS konnektorteenus (testpaigaldus); kasutame ka nimetust RIA eIDAS test-Node - etendab saatva riigi eIDAS Node-i. Saatev riik - riik, mille e-teenusest kasutaja autentimisele saadetakse
    - Host: `eidastest.eesti.ee`
    - Otspunktid:
        - `https://eidastest.eesti.ee/EidasNode/ServiceProvider`
        - `https://eidastest.eesti.ee/EidasNode/ColleagueResponse`

- CEF eIDAS test-Node - etendab vastuvõtva riigi eIDAS Node-i. Vastuvõttev riik - riik, mille autentimisteenuses kasutaja autenditakse; ühtlasi etendab ka vastuvõtva riigi autentimisteenust; neid funktsionaalsusi pakutakse nime CEF eIDAS Validation Service all.
    - Host: `ec.europa.eu`
    - Otspunktid:
        - `https://ec.europa.eu/eid-integration-test/EidasNode/ColleagueRequest`
        - `https://ec.europa.eu/eid-integration-test/IdP/AuthenticateCitizen`
        - `https://ec.europa.eu/eid-integration-test/EidasNode/SpecificIdPResponse`
        - `https://ec.europa.eu/eid-integration-test/AP/ConsentValue`
        - `https://ec.europa.eu/eid-integration-test/EidasNode/APSelector`

- kasutaja

- sirvik.

## Voo sisu

Siinkirjeldatu on demo- ja test-, mitte tootmisvoog. Validation Service etendab, et tuvastatakse isik nimega `Javier Garcia`. Demovoog lõpeb veaolukorra tekkimisega RIA eIDAS test-Node-s (selle põhjus vajab selgitamist - v-o on viga seadistuses?).

Edasi kirjeldame päringuid ja nende vastuseid detailselt.

## 1. Pöördumine Demo SP avalehe poole.

Kasutaja sisestab sirvikus Demo SP avalehe URL-i.

````
GET https://eidastest.eesti.ee/SP/populateIndexPage
````

Vastuses saadab Demo SP serveripool sirvikusse Demo SP avalehe. Samuti seatakse seansiküpsis.

## 2. Kasutaja valikute edastamine serverisse (1)

Seejärel kasutaja valib:

- e-teenuse riigi - `SP Country` -> `EE`
- oma koduriigi - `Citizen Country` -> `CD`
- nõutavad kasutajat kirjeldavad atribuudid - `Requested Core Attributes` -> `Mandatory`.

Rida valikuid on seatud vaikimisi.

Kasutaja vajutab `Submit`.

````
POST https://eidastest.eesti.ee/SP/IndexPage.action;jsessionid=seansitoken
````

Kasutaja poolt valitud parameetrid saadetakse päringu kehas, vormina.

Vastuses saadab Demo SP serveripool sirvikusse lehe, kus kuvatakse serveripooles moodustatud SAML autentimispäringusõnum (esialgsel kujul) ja palutakse valida edastusmeetod.

SAML autentimispäring:

{% highlight xml %}
<saml2p:AuthnRequest
	xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol"
	xmlns:ds="http://www.w3.org/2000/09/xmldsig#"
	xmlns:eidas="http://eidas.europa.eu/saml-extensions"
	xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" Destination="https://eidastest.eesti.ee/EidasNode/ServiceProvider" ForceAuthn="true" ID="_WfZeh5TocZ-4gikOG._CVllEo8XUOqO06KfQn4Jy8q0233r0QZXGhsLzaIQKeVz" IsPassive="false" IssueInstant="2018-01-15T13:25:14.477Z" ProviderName="DEMO-SP-EE" Version="2.0">
	<saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">https://eidastest.eesti.ee/SP/metadata</saml2:Issuer>
	<ds:Signature
		xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
		<ds:SignedInfo>
			<ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
			<ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
			<ds:Reference URI="#_WfZeh5TocZ-4gikOG._CVllEo8XUOqO06KfQn4Jy8q0233r0QZXGhsLzaIQKeVz">
				<ds:Transforms>
					<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
					<ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
				<ds:DigestValue>YGVD5NG8Dj9SVUlE/4I2VvgIB7wKcjGGgraWHGepb2OS6L76yiTZs2MvAkeQiEhequcZEjgVh490IerbDLzfgw==</ds:DigestValue>
			</ds:Reference>
		</ds:SignedInfo>
		<ds:SignatureValue>RTLGTZg+Bc1dT5iuSWzC9Ac63PGr2msR4l3DsmcgSNFp2CE+Ds7pUi/4BXP22hZh6iSUfflXpspea5GELimFtgFbwQnl2wQsMleWa8Y7phd3aYfp0YQbp99h2EfAI0NGG4clw08HOZOjmMuPskxmpBkf2L78CKkNx80rtVnayiI5R0ulBAkME2oSqK9C3T4DJ9VmsUvaQ4wlTxydkVKakpvplvKo5xiU7Lgcc5INEwdcZY6aADJLnXjpuQ1KuWa0f0O4F8CKmc1ip1bPY4eSiSpc5U5pAbuuRgUCznoHcmDnR98Irc5JD6YntauJYDmZomz7Im7OdgVHPI7NNLZhCQ==</ds:SignatureValue>
		<ds:KeyInfo>
			<ds:X509Data>
				<ds:X509Certificate> ... </ds:X509Certificate>
			</ds:X509Data>
		</ds:KeyInfo>
	</ds:Signature>
	<saml2p:Extensions>
		<eidas:SPType>public</eidas:SPType>
		<eidas:RequestedAttributes>
			<eidas:RequestedAttribute FriendlyName="D-2012-17-EUIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/D-2012-17-EUIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="EORI" Name="http://eidas.europa.eu/attributes/legalperson/EORI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LEI" Name="http://eidas.europa.eu/attributes/legalperson/LEI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LegalAdditionalAttribute" Name="http://eidas.europa.eu/attributes/legalperson/LegalAdditionalAttribute" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LegalName" Name="http://eidas.europa.eu/attributes/legalperson/LegalName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="LegalAddress" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LegalPersonIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="SEED" Name="http://eidas.europa.eu/attributes/legalperson/SEED" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="SIC" Name="http://eidas.europa.eu/attributes/legalperson/SIC" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="TaxReference" Name="http://eidas.europa.eu/attributes/legalperson/TaxReference" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="VATRegistration" Name="http://eidas.europa.eu/attributes/legalperson/VATRegistrationNumber" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="AdditionalAttribute" Name="http://eidas.europa.eu/attributes/naturalperson/AdditionalAttribute" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="BirthName" Name="http://eidas.europa.eu/attributes/naturalperson/BirthName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="CurrentAddress" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="FamilyName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentFamilyName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="FirstName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentGivenName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="DateOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/DateOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="Gender" Name="http://eidas.europa.eu/attributes/naturalperson/Gender" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="PersonIdentifier" Name="http://eidas.europa.eu/attributes/naturalperson/PersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="PlaceOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/PlaceOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
		</eidas:RequestedAttributes>
	</saml2p:Extensions>
	<saml2p:NameIDPolicy AllowCreate="true" Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified"/>
	<saml2p:RequestedAuthnContext Comparison="minimum">
		<saml2:AuthnContextClassRef>http://eidas.europa.eu/LoA/low</saml2:AuthnContextClassRef>
	</saml2p:RequestedAuthnContext>
</saml2p:AuthnRequest>
{% endhighlight %}

## 3. Kasutaja valikute edastamine serverisse (2)

Kasutaja saab nüüd valida `HTTP POST` või `HTTP Redirect` binding-u.

Kasutaja valib  `HTTP Redirect` ja vajutab `Submit`.

````
POST https://eidastest.eesti.ee/SP/changeProtocolBinding.action
````

Päringu kehas edastatakse vormina andmed:

````
samlRequestBinding	get
samlRequestLocation	https://eidastest.eesti.ee/EidasNode/ServiceProvider
citizenCountryCode	CD
samlRequestXML	...
````

Päringu vastuse kehas edastab server sirvikule eIDAS konnektorteenusele saatmiseks valmis sõnumi:

{% highlight xml %}
<saml2p:AuthnRequest
	xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol"
	xmlns:ds="http://www.w3.org/2000/09/xmldsig#"
	xmlns:eidas="http://eidas.europa.eu/saml-extensions"
	xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" Destination="https://eidastest.eesti.ee/EidasNode/ServiceProvider" ForceAuthn="true" ID="_UdCvtm1NPIWt4OAnhuEsmVVn3imhJULjC1djKrDGOtfVjE.B9zn31v3W_ggGcM3" IsPassive="false" IssueInstant="2018-01-12T11:08:39.355Z" ProviderName="DEMO-SP-EE" Version="2.0">
	<saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity"
		xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">https://eidastest.eesti.ee/SP/metadata
	</saml2:Issuer>
	<ds:Signature
		xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
		<ds:SignedInfo>
			<ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
			<ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
			<ds:Reference URI="#_UdCvtm1NPIWt4OAnhuEsmVVn3imhJULjC1djKrDGOtfVjE.B9zn31v3W_ggGcM3">
				<ds:Transforms>
					<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
					<ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
				</ds:Transforms>
				<ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
				<ds:DigestValue>DpOI61KXd4dQG4kLKkl6U8NYV2Y9nRhLZCeCl1VGchYfSpjTRhND2K82M5QSRjtXUfuNTHiKDHaP/gsCTxoFSw==</ds:DigestValue>
			</ds:Reference>
		</ds:SignedInfo>
		<ds:SignatureValue> ... </ds:SignatureValue>
		<ds:KeyInfo>
			<ds:X509Data>
				<ds:X509Certificate> ... </ds:X509Certificate>
			</ds:X509Data>
		</ds:KeyInfo>
	</ds:Signature>
	<saml2p:Extensions
		xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol">
		<eidas:SPType
			xmlns:eidas="http://eidas.europa.eu/saml-extensions">public
		</eidas:SPType>
		<eidas:RequestedAttributes
			xmlns:eidas="http://eidas.europa.eu/saml-extensions">
			<eidas:RequestedAttribute FriendlyName="D-2012-17-EUIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/D-2012-17-EUIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="EORI" Name="http://eidas.europa.eu/attributes/legalperson/EORI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LEI" Name="http://eidas.europa.eu/attributes/legalperson/LEI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LegalAdditionalAttribute" Name="http://eidas.europa.eu/attributes/legalperson/LegalAdditionalAttribute" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LegalName" Name="http://eidas.europa.eu/attributes/legalperson/LegalName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="LegalAddress" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="LegalPersonIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="SEED" Name="http://eidas.europa.eu/attributes/legalperson/SEED" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="SIC" Name="http://eidas.europa.eu/attributes/legalperson/SIC" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="TaxReference" Name="http://eidas.europa.eu/attributes/legalperson/TaxReference" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="VATRegistration" Name="http://eidas.europa.eu/attributes/legalperson/VATRegistrationNumber" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="AdditionalAttribute" Name="http://eidas.europa.eu/attributes/naturalperson/AdditionalAttribute" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="BirthName" Name="http://eidas.europa.eu/attributes/naturalperson/BirthName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="CurrentAddress" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="FamilyName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentFamilyName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="FirstName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentGivenName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="DateOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/DateOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="Gender" Name="http://eidas.europa.eu/attributes/naturalperson/Gender" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
			<eidas:RequestedAttribute FriendlyName="PersonIdentifier" Name="http://eidas.europa.eu/attributes/naturalperson/PersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="true"/>
			<eidas:RequestedAttribute FriendlyName="PlaceOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/PlaceOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri" isRequired="false"/>
		</eidas:RequestedAttributes>
	</saml2p:Extensions>
	<saml2p:NameIDPolicy AllowCreate="true" Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified"
		xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol"/>
		<saml2p:RequestedAuthnContext Comparison="minimum"
			xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol">
			<saml2:AuthnContextClassRef
				xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">http://eidas.europa.eu/LoA/low
			</saml2:AuthnContextClassRef>
		</saml2p:RequestedAuthnContext>
	</saml2p:AuthnRequest>
{% endhighlight %}

## 4. Pöördumine eIDAS konnektorteenuse poole

Eelmise päringu vastuses sai sirvik edastamiseks valmis SAML autentimispäringusõnumi. Nüüd tehakse Javascript redirect-ga pöördumine eIDAS konnektorteenuse poole.

````
GET https://eidastest.eesti.ee/EidasNode/ServiceProvider?
postLocationUrl=https://eidastest.eesti.ee/EidasNode/ServiceProvider&
redirectLocationUrl=https://eidastest.eesti.ee/EidasNode/ServiceProvider&
country=CD&
RelayState=MyRelayState&
SAMLRequest= ... &
sendmethods=GET
````

Vastuseks saadab konnektorteenus sirvikusse HTML-i, kus kasutajale kuvatakse (vilksamisi) `Redirecting to the citizen country...`. HTML-is on peidetud vorm ümbersuunamiskorraldusega. Tegelikult kaks vormi, üks Javascripti kasutamisega, teine ilma:

{% highlight html %}
<form name="redirectForm" method="GET" action="https://ec.europa.eu/eid-integration-test/EidasNode/ColleagueRequest">
    <input type="hidden" id="SAMLRequest" name="SAMLRequest" value="..."/>
    <input type="hidden" id="relayState" name="RelayState" value="MyRelayState"/>
                        
    <input type="hidden"  name="token"  value="1YbOdLyWSLz_VKQhyBwduOEaz7A$" />
</form>
<noscript>
    <form id="redirectFormNoJs" name="redirectFormNoJs" method="post" action="https://ec.europa.eu/eid-integration-test/EidasNode/ColleagueRequest">
        <input type="hidden" id="consentValue_SAMLRequest" name="SAMLRequest" value="..."/>
        <input type="hidden" id="consentValue_relayState" name="RelayState" value="MyRelayState"/>
        <p class="box-btn">
            <input type="submit" id="ConsentValue_button" class="btn btn-next" value="I accept"/>
        </p>
    </form>
</noscript>
{% endhighlight %}

Vormis olev ümbersuunamiskorraldus täidetakse automaatselt, skriptiga:

{% highlight javascript %}
// Automatic Redirect of the form
function submitRedirectFormAction() {
    document.getElementsByName('redirectForm')[0].submit();
}
window.addEventListener('load', submitRedirectFormAction());
{% endhighlight %}

## 5. Pöördumine kasutaja koduriigi eIDAS Node-i poole

Välisriigi eIDAS Node-ks on praegu CEF- i käitatav eIDAS test-Node (edaspidi "CEF test-Node"), aadressiga `https://ec.europa.eu/eid-integration-test/EidasNode`.

````
GET https://ec.europa.eu/eid-integration-test/EidasNode/ColleagueRequest?
SAMLRequest= ... &
RelayState=MyRelayState&
token= ... 
````

Vastuses saadab CEF test-Node sirvikusse HTML-i, kus kasutajale öeldakse, et ta peab nüüd koduriigi autentimisteenusesse suunamiseks nupule vajutama. Nupu taga on vorm ümbersuunamiskorraldusega. Vormis on ka SAML autentimispäring.

## 6. Pöördumine koduriigi autentimisteenusesse

````
POST https://ec.europa.eu/eid-integration-test/IdP/AuthenticateCitizen
````

Päringu kehas on vorm:

````
SAMLRequest	...
signAssertion	false
encryptAssertion	true
messageFormat	eidas
````

CEF test-Node, etendades koduriigi autentimisteenust, saadab vastuseks sirvikusse HTML-i, milles on peidetud vorm ümbersuunamiskorraldusega.

Kasutajale ei näidata siin midagi. Kredentsiaalide esitamise dialoogi kasutaja ei peeta. Isikutuvastus loetakse tehtuks ja viivitamata hakatakse autentimisvastust ahelat pidi tagasi saatma. Autentimisvastus on vormis juba olemas.

Märkus. CEF test-Node osas sisaldub sõnumivahetuses ka sisuturbepoliitikaga (CSP) seotud pöördumisi, nt
`https://wlpc0090.cc.cec.eu.int:1042/eid-integration-test/EidasNode/cspReportHandler`.

## 7. Autentimistulemuse edastamine koduriigi autentimisteenusest (mängult) CEF test-Node-le

Tehakse päring:

````
POST https://ec.europa.eu/eid-integration-test/EidasNode/SpecificIdPResponse
````

Päringu kehas on vorm autentimisvastusega (SAML Response sõnumiga):

````
SAMLResponse	...
username	xavi
````

Vastuseks saadab CEF test-sirvikusse Node HTML-i, kus kasutajal palutakse e-teenusesse tagasisuunamiseks nupule vajutada:

{% highlight html %}
<h1 class="title">
    <span>eIDAS Authentication Service</span>
</h1>
<h2>Please click on the next button to be redirected to the Service Provider.</h2>
<br/>

<form id="consentValue" name="redirectForm" method="post" action="https://ec.europa.eu/eid-integration-test/AP/ConsentValue">
    <input type="hidden" id="consentValue_callbackUrl" name="callbackUrl" value="https://ec.europa.eu/eid-integration-test/EidasNode/APSelector"/>
    <input type="hidden" id="consentValue_strAttrList" name="strAttrList" value="D-2012-17-EUIdentifier:false:[Directive 2012/17/EU Identifier,]:;EORI:false:[Economic Operator Registration and Identification (EORI),]:;LEI:false:[Legal Entity Identifier (LEI),]:;LegalName:false:[Current Legal Name,]:;LegalPersonAddress:false:[Legal Person address,]:;LegalPersonIdentifier:false:[LegalPersonUniqueId,]:;SEED:false:[System for Exchange of Excise Data (SEED),]:;SIC:false:[Standard Industrial Classification (SIC),]:;TaxReference:false:[Taxe,]:;VATRegistrationNumber:false:[VAT Registration Number,]:;BirthName:false:[Ωνάσης,Onases,]:;CurrentAddress:false:[Current Address,]:;CurrentFamilyName:false:[Garcia,]:;CurrentGivenName:false:[javier,]:;DateOfBirth:false:[1980-01-01,]:;Gender:false:[Male,]:;PersonIdentifier:false:[12345,]:;PlaceOfBirth:false:[Place of Birth,]:;"/>
    <input type="hidden" id="consentValue_username" name="username" value="xavi"/>
    <noscript>
        <p class="box-btn">
            <input type="submit" id="ConsentValue_button" class="btn btn-next" value="Submit"/>
        </p>
    </noscript>
{% endhighlight %}

## 8. Nõusoleku saatmise päring

Nupuvajutuse tähendus on kasutaja nõusoleku võtmine (_consent_) autentimisandmete edastamiseks e-teenusele.

Nupu taga on vorm, mille sisu edastataksegi järgmises päringus.

````
POST https://ec.europa.eu/eid-integration-test/AP/ConsentValue
````

Päringu kehas on:

````
callbackUrl	https://ec.europa.eu/eid-integration-test/EidasNode/APSelector
strAttrList	D-2012-17-EUIdentifier:false:[Directive+2012/17/EU+Identifier,]:;EORI:false:[Economic+Operator+Registration+and+Identification+(EORI),]:;LEI:false:[Legal+Entity+Identifier+(LEI),]:;LegalName:false:[Current+Legal+Name,]:;LegalPersonAddress:false:[Legal+Person+address,]:;LegalPersonIdentifier:false:[LegalPersonUniqueId,]:;SEED:false:[System+for+Exchange+of+Excise+Data+(SEED),]:;SIC:false:[Standard+Industrial+Classification+(SIC),]:;TaxReference:false:[Taxe,]:;VATRegistrationNumber:false:[VAT+Registration+Number,]:;BirthName:false:[Ωνάσης,Onases,]:;CurrentAddress:false:[Current+Address,]:;CurrentFamilyName:false:[Garcia,]:;CurrentGivenName:false:[javier,]:;DateOfBirth:false:[1980-01-01,]:;Gender:false:[Male,]:;PersonIdentifier:false:[12345,]:;PlaceOfBirth:false:[Place+of+Birth,]:;
username	xavi 
````

Vastuseks saadab CEF test-Node sirvikusse HTML-i, milles on peidetud vorm automaatse ümbersuunamiskorraldusega.

## 9. Eduka sisselogimise teade ja tagasisuunamine RIA eIDAS Node-i

````
POST https://ec.europa.eu/eid-integration-test/EidasNode/APSelector
````

Päringu kehas on vorm:

````
strAttrList	D-2012-17-EUIdentifier:false:[Directive+2012/17/EU+Identifier,]:;EORI:false:[Economic+Operator+Registration+and+Identification+(EORI),]:;LEI:false:[Legal+Entity+Identifier+(LEI),]:;LegalName:false:[Current+Legal+Name,]:;LegalPersonAddress:false:[Legal+Person+address,]:;LegalPersonIdentifier:false:[LegalPersonUniqueId,]:;SEED:false:[System+for+Exchange+of+Excise+Data+(SEED),]:;SIC:false:[Standard+Industrial+Classification+(SIC),]:;TaxReference:false:[Taxe,]:;VATRegistrationNumber:false:[VAT+Registration+Number,]:;BirthName:false:[Ωνάσης,Onases,]:;CurrentAddress:false:[Current+Address,]:;CurrentFamilyName:false:[Garcia,]:;CurrentGivenName:false:[javier,]:;DateOfBirth:false:[1980-01-01,]:;Gender:false:[Male,]:;PersonIdentifier:false:[12345,]:;PlaceOfBirth:false:[Place+of+Birth,]:;
````

Vastuseks saadab CEF test-Node sirvikusse HTML-i, kus kasutajale teatakse, et "LOGIN SUCCEEDED", näidatakse, mis atribuudid leiti (nt `CurrentGivenName` ja `CurrentFamilyName` väärtusteks `Javier Garcia`), milline oli SAML autentimispäringusõnum ja milline on krüpteeritud SAML autentimisvastusesõnum (vt fail [SAML-Response-Encrypted.xml](files/SAML-Response-Encrypted.xml)).

NB! Vastus paistab olevat tühi (`Decrypted Assertion` = `no assertion found`).

## 10. Autentimisvastuse saatmine RIA eIDAS-Node-le

Kasutajal palutakse vajutada nupule `Submit`.

Nupu taga on vorm suunamisega RIA eIDAS-Node-i:

{% highlight html %}
<form action="https://eidastest.eesti.ee/EidasNode/ColleagueResponse" name="countrySelector" id="countrySelector" method="post">
{% endhighlight %}

Nupulevajutusega tehakse päring:

````
POST https://eidastest.eesti.ee/EidasNode/ColleagueResponse
````

Päringu kehas on vormielemendid:
- `SAMLResponse`
- `nodeType`
- `RelayState`
- `sAMLRequestTT`
- `samlResponseDecriptedXMLEdit`
- `eidasScenario`
- `eidasScenario2`
- `scenarioDesc`
- `theScenario`.

````
SAMLResponse	...
nodeType	Service
RelayState	MyRelayState
sAMLRequestTT	...
samlResponseDecriptedXMLEdit	<?xml+version="1.0"+encoding="UTF-8"+standalone="no"?><saml2p:Response+xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol"+xmlns:ds="http://www.w3.org/2000/09/xmldsig#"+xmlns:eidas="http://eidas.europa.eu/attributes/naturalperson"+xmlns:eidas-legal="http://eidas.europa.eu/attributes/naturalperson"+xmlns:eidas-natural="http://eidas.europa.eu/attributes/naturalperson"+xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion"+Consent="urn:oasis:names:tc:SAML:2.0:consent:obtained"+Destination="https://eidastest.eesti…e"+xsi:type="eidas-natural:PersonIdentifierType">CD/EE/12345</saml2:AttributeValue></saml2:Attribute><saml2:Attribute+FriendlyName="PlaceOfBirth"+Name="http://eidas.europa.eu/attributes/naturalperson/PlaceOfBirth"+NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"><saml2:AttributeValue+xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"+xsi:type="eidas-natural:PlaceOfBirthType">Place+of+Birth</saml2:AttributeValue></saml2:Attribute></saml2:AttributeStatement></saml2:Assertion></saml2p:Response>
eidasScenario	1
eidasScenario2	
scenarioDesc	eIDAS+-+The+Service+sends+the+original+eIDAS+SAML+Response+received +Expected+Result+:Success
theScenario	1
````

RIA eIDAS test-Node kontrollib (valideerib) teiselt Node-lt saadud SAML autentimisvastust. 

Kui valideerimisel leitud viga, saadab RIA eIDAS test-Node päringu vastuses sirvikusse HTML-i, milles teatab veast (vt jaotis 11). Kui valideerimisel vigu ei leia, siis saadab HTML-i sisselogimise õnnestumise kohta (vt jaotis 12).

## 11. Ebaõnnestunud autentimisest teatamine kasutajale

 RIA eIDAS test-Node HTML-i (vt fail [RIA-testNode-1.html](RIA-testNode-1.html)), et toimus ootamatu viga (`An unexpected error has occurred`). Kuvatakse täpsem veakood ja -teade: `203007 - The SAML message is not valid`

Sellega voog lõpeb.

## 12. SAML autentimisvastussõnumi esitamine kasutajale

````
POST https://eidastest.eesti.ee/SP/ReturnPage
````

Päringu vastuses saadab RIA eIDAS testNode sirvikusse HTML-i, kus kuvatakse:
- SAML autentimisvastusesõnum (Base64 kodeeritult)
- `RelayState väärtus`
- SAML autentimisvastusesõnum XML-kujul.

{% highlight xml %}
<saml2p:Response
    xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol"
    xmlns:ds="http://www.w3.org/2000/09/xmldsig#"
    xmlns:eidas="http://eidas.europa.eu/attributes/naturalperson"
    xmlns:eidas-legal="http://eidas.europa.eu/attributes/legalperson"
    xmlns:eidas-natural="http://eidas.europa.eu/attributes/naturalperson"
    xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" Consent="urn:oasis:names:tc:SAML:2.0:consent:obtained" Destination="https://eidastest.eesti.ee/SP/ReturnPage" ID="_iXNkQbFmfBZmnt3luxEAMGDibWxfNmPy8fWfuxv4aDg6Q-PDSJ5PWi77nVYrnyh" InResponseTo="_rv6umtnVG6AJFVWKz_v7smzfPmL6eW9HJgG4OYEIa8SV_JYjjxAgYzu8uCQ6HIT" IssueInstant="2018-01-15T14:05:31.067Z" Version="2.0">
<saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">
    https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata
</saml2:Issuer>
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
<ds:SignedInfo>
    <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
    <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
    <ds:Reference URI="#_iXNkQbFmfBZmnt3luxEAMGDibWxfNmPy8fWfuxv4aDg6Q-PDSJ5PWi77nVYrnyh">
        <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">
                <ec:InclusiveNamespaces
                    xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList="eidas-legal eidas-natural"/>
                </ds:Transform>
            </ds:Transforms>
            <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
            <ds:DigestValue>0TF17y8otwOuQZqnGkQoz+KxftN/GeZm9YzR9CP4YF9uFbd/eTKEYoY+c2jev2q5g0ZJq7/wdUkqOqQgISk01Q==</ds:DigestValue>
        </ds:Reference>
    </ds:SignedInfo>
    <ds:SignatureValue>NtVN5TMPtxCZ5liJdbaF7GHDiRiIJtM4/nSu3kzpwXwm/F4EvUrrwaweGnThFpYMXGhB8qxcvFfI7eam0/jhxQy98US+AF59Xn3NQLvBD7JQrmZjl9nK/MDlg6qMJloh+GZ+KUbOu29tLpdGeBIU8WrF9mvd5LJXc6jkdan3lZCzw4lhX6t0eoFDz0b+HVPgT2oiDWzGX1cdXg4FXSRx/WJ/nxE+5kcloq+D3il8xBfkPx+GBk2QNny0ptFxf/jAAZirPvf5EBTYhypGB5TsYdIJ9wRWvXfzpibGoKEdpG7GCGoOUGlvEBbZ28Oa+za39pZbyjYVVkMX3rnQWthxdg==</ds:SignatureValue>
    <ds:KeyInfo>
        <ds:X509Data>
            <ds:X509Certificate> ... </ds:X509Certificate>
        </ds:X509Data>
    </ds:KeyInfo>
</ds:Signature>
<saml2p:Status>
    <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
    <saml2p:StatusMessage>urn:oasis:names:tc:SAML:2.0:status:Success</saml2p:StatusMessage>
</saml2p:Status>
<saml2:Assertion ID="_zjP.FVWZy59ua1liRNXjqzK9Wca1FYjh_ix6M6gwQKQAy2d.kPaw4esi4qB6Tlc" IssueInstant="2018-01-15T14:05:31.067Z"
Version="2.0"
    xmlns:eidas-legal="http://eidas.europa.eu/attributes/legalperson"
    xmlns:eidas-natural="http://eidas.europa.eu/attributes/naturalperson"
    xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">
    <saml2:Issuer Format="urn:oasis:names:tc:SAML:2.0:nameid-format:entity">https://eidastest.eesti.ee/EidasNode/ConnectorResponderMetadata</saml2:Issuer>
    <ds:Signature
        xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>
            <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha512"/>
            <ds:Reference URI="#_zjP.FVWZy59ua1liRNXjqzK9Wca1FYjh_ix6M6gwQKQAy2d.kPaw4esi4qB6Tlc">
                <ds:Transforms>
                    <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
                    <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">
                        <ec:InclusiveNamespaces
                            xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList="eidas-legal eidas-natural"/>
                        </ds:Transform>
                    </ds:Transforms>
                    <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha512"/>
                    <ds:DigestValue>RTMjFpjpc2QrMXwHqswrETxZ/fdGuMWHF/2Nw6p2mwwiV25PgsBIQfgpuPyBU7NNpMhSBNqV5ZzlsSC/kWJXRg==</ds:DigestValue>
                </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>O1sg3U5aZnJUlnDPKUtob6DwG6zziH2hSv44Z1azv1vsM+W3C+AC28W4+VK++6UvaHoHwZ7tmYzcH63KtLAd/IRmmxmEMInzMeCZLfPMkOAxlHJKOip/SN//hmh+KycKrzZJ8g6ETTze/w5clyaOiTHyyBwZ22wfdmOsBYzDZu19khUutIUwOskJbZb6QMcc1Z6p8QIWOdqe90Tty5zsODscx5B/A9QClKfHGeCLfYN2f5dhIT8qmC0bsLi0uFvwQCT/Z4A4E5dUaIxDe27DtxjpsLMHyH2Ps+05EVazmkzp36+1ubNseAKpNuGmQbgoI8SRYt5R9Wq18JgMw3fZLg==</ds:SignatureValue>
            <ds:KeyInfo>
                <ds:X509Data>
                    <ds:X509Certificate> ... </ds:X509Certificate>
                </ds:X509Data>
            </ds:KeyInfo>
        </ds:Signature>
        <saml2:Subject>
            <saml2:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified" NameQualifier="http://C-PEPS.gov.xx">CD/EE/LegalPersonUniqueId</saml2:NameID>
            <saml2:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
                <saml2:SubjectConfirmationData Address="192.168.2.191" InResponseTo="_rv6umtnVG6AJFVWKz_v7smzfPmL6eW9HJgG4OYEIa8SV_JYjjxAgYzu8uCQ6HIT" NotOnOrAfter="2018-01-15T14:10:31.067Z" Recipient="https://eidastest.eesti.ee/SP/ReturnPage"/>
            </saml2:SubjectConfirmation>
        </saml2:Subject>
        <saml2:Conditions NotBefore="2018-01-15T14:05:31.067Z" NotOnOrAfter="2018-01-15T14:10:31.067Z">
            <saml2:AudienceRestriction>
                <saml2:Audience>https://eidastest.eesti.ee/SP/metadata</saml2:Audience>
            </saml2:AudienceRestriction>
        </saml2:Conditions>
        <saml2:AuthnStatement AuthnInstant="2018-01-15T14:05:31.067Z">
            <saml2:AuthnContext>
                <saml2:AuthnContextClassRef>http://eidas.europa.eu/LoA/high</saml2:AuthnContextClassRef>
                <saml2:AuthnContextDecl/>
            </saml2:AuthnContext>
        </saml2:AuthnStatement>
        <saml2:AttributeStatement>
            <saml2:Attribute FriendlyName="D-2012-17-EUIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/D-2012-17-EUIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:D-2012-17-EUIdentifierType">Directive 2012/17/EU Identifier
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="EORI" Name="http://eidas.europa.eu/attributes/legalperson/EORI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:EORIType">Economic Operator Registration and Identification (EORI)
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="LEI" Name="http://eidas.europa.eu/attributes/legalperson/LEI" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:LEIType">Legal Entity Identifier (LEI)
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="LegalName" Name="http://eidas.europa.eu/attributes/legalperson/LegalName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:LegalNameType">Current Legal Name
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="LegalAddress" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"/>
            <saml2:Attribute FriendlyName="LegalPersonIdentifier" Name="http://eidas.europa.eu/attributes/legalperson/LegalPersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:LegalPersonIdentifierType">CD/EE/LegalPersonUniqueId
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="SEED" Name="http://eidas.europa.eu/attributes/legalperson/SEED" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:SEEDType">System for Exchange of Excise Data (SEED)
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="SIC" Name="http://eidas.europa.eu/attributes/legalperson/SIC" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:SICType">Standard Industrial Classification (SIC)
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="TaxReference" Name="http://eidas.europa.eu/attributes/legalperson/TaxReference" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:TaxReferenceType">Taxe
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="VATRegistration" Name="http://eidas.europa.eu/attributes/legalperson/VATRegistrationNumber" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-legal:VATRegistrationNumberType">VAT Registration Number
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="BirthName" Name="http://eidas.europa.eu/attributes/naturalperson/BirthName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" eidas-natural:LatinScript="false" xsi:type="eidas-natural:BirthNameType">Ωνάσης
                </saml2:AttributeValue>
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-natural:BirthNameType">Onases
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="CurrentAddress" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentAddress" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri"/>
            <saml2:Attribute FriendlyName="FamilyName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentFamilyName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-natural:CurrentFamilyNameType">Garcia
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="FirstName" Name="http://eidas.europa.eu/attributes/naturalperson/CurrentGivenName" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-natural:CurrentGivenNameType">javier
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="DateOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/DateOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-natural:DateOfBirthType">1980-01-01
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="Gender" Name="http://eidas.europa.eu/attributes/naturalperson/Gender" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-natural:GenderType">Male
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="PersonIdentifier" Name="http://eidas.europa.eu/attributes/naturalperson/PersonIdentifier" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-natural:PersonIdentifierType">CD/EE/12345
                </saml2:AttributeValue>
            </saml2:Attribute>
            <saml2:Attribute FriendlyName="PlaceOfBirth" Name="http://eidas.europa.eu/attributes/naturalperson/PlaceOfBirth" NameFormat="urn:oasis:names:tc:SAML:2.0:attrname-format:uri">
                <saml2:AttributeValue
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="eidas-natural:PlaceOfBirthType">Place of Birth
                </saml2:AttributeValue>
            </saml2:Attribute>
        </saml2:AttributeStatement>
    </saml2:Assertion>
</saml2p:Response>
{% endhighlight %}

Kasutajal on võimalus vajutusega `Submit` liikuda edasi.

## 13. Eduka sisselogimise teate esitamine Demo SP-s

````
POST https://eidastest.eesti.ee/SP/populateReturnPage
````

Päringu vastuses saadab RIA eIDAS testNode sirvikusse HTML-i, kus:
- kasutajale teatatakse, et sisselogimine on edukalt tehtud ja
- kuvatakse autentimisvastusega saadud atribuudid.

Sellega voog lõpeb.
