---
permalink: eIDAS-Client
---

# eIDAS Client

- out-of-the-box solution to eIDAS integration problem
- connect to eIDAS cross-border authentication infrastructure
- greatly reduce integration costs
- minimal or no programming needed

eIDAS Client sits between eIDAS infrastructure and you e-service (see figure)
- talking SAML towards eIDAS Connector 
- and extremely simple HTTPS/JSON towards your e-service

### How does it work?

1  Your e-service sends the User to eIDAS Adapter

HTTPS Redirect 
- `country` - code of the country the User is from
- `loa` (optional) - requested level of assurance (`high`, `medium`, `loa`)
    - by default, `high` is assumed 
- `scope` (optional)
  - names of attributes of the User that your e-service requests
  - by default, eIDAS minimum set (PersonIdentifier, FirstName, FamilyName, DateOfBirth) is assumed
- `nonce` - random value, to protect against replay attacks

2  eIDAS Client sends authenticated User back to your e-service

HTTPS Redirect (using automatic POST)
- to pre-configured callback-URL
- `loa` - level of assurance
- `profile` - attribute set of the authenticated User
- `nonce` - reflected from the request

### Features

Session management | eIDAS Client is session- and stateless. There is no need to hold state, because returning Users are always forwarded to the same callback-URL of the e-service. eIDAS Client serves only one e-service.
Security | eIDAS Client and e-service share the trusted domain of the organisation. 
Clustering | Sessionless eIDAS Client can be installed in any number of instances

How eIDAS Client reduces integration costs?
- no need to implement SAML interface
- extremely simple JSON 
- sensible defaults for Level of Assurance and eIDAS attribute requests

<img src='img/CLIENT.png' style='width:700px'>



