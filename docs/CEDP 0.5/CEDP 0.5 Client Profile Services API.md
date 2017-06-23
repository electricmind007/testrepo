# CEDP 0.5 Client Profile Services API

## Overview
The Client Profile Servcies (CPS) are a part of CEDP's Data-as-a-Service offering providing a service interface to various dimensions of data related to clients. These servicces enable applications to retrieve and integrate client data directly into their processing (e.g., a web user interface) with a web-oriented programming interface as opposed to first loading and integrating data into application-specific data stores. 

CPS has been designed according to the REST architectural style. The REST approach to data services is widely adopted and its underlying principles plentifully documented. A core tenant is the notion of _resource_ upon which operations are performed. Resources are addressed by URIs. These services are implemented over the HTTP protocol, as is the most common practice for RESTful services, and data representations are embodied in the ubiquitous JSON document format.

In this initial version of Client Profile Servcies, the core resources correspond to the following dimensions of an IBM client:

- Client summary
- Contract
- Revenue
- Financials
- Contacts
- Opportunities
- Marketing interactions
- Install base
 
For each resource, there is one or more URI-addressable service interface. The corresponding URI structure for these resources is outlined in the next section.

## URIs

### Relative URIs
All relative CPS URIs begin with:
```
/cps
```
Appended to the base URI is a version number to facilitate evolution:
```
 /cps/1.0
``` 
Next come the resources themselves, each used in its plural form:
```
 /cps/1.0/companies
 /cps/1.0/contracts
 /cps/1.0/revenue
 /cps/1.0/financials
 /cps/1.0/contacts
 /cps/1.0/opps
 /cps/1.0/mktg/interactions
 /cps/1.0/ibase/ibm
 /cps/1.0/ibase/comp
 ```
Each resource supports its own set of parameters for refining the information to be retrieved from the resource. Parameters are passed as either path parameters or URI query parameters. Certain parameters however are common to practically all services. Common parameters include : `clientid`, `offset`, `limit`, `sort_by`, `sort_order`.

In general, to retrieve a specific instance of a resource, a path parameter representing a unique ID is used. For example, assume a given Sales Connect Opportunity has a unique identifier, "abcde". The following syntax retrieves that Opportunity:
```
 /cps/1.0/opps/abcde
``` 
URI query parameters are used to apply filters or query options on the base resource. To retreive all the resources for a specific client, the `clientid` query parameter is passed. IBM client IDs are specified by its ID and its IBM legacy country code appended together, separated by a dot. To retrieve all Opportunities for a Domestic Client, DC637766, in Germany (IBM legacy country code 724), the URI is constructed  as such:
```
 /cps/1.0/opps?clientid=DC637766.724
```
To support pagination, the `offset` and `limit` parameters allow the API to be queried in blocks. By default, the API returns up to 100 records per request. To retrieve just the first 20 records, `offset` and `limit` are used as follows:
```
 /cps/1.0/opps?clientid=DC637766.724&offset=0&limit=20
``` 
The offset can be incremented in order to page to the next block of responses:
```
 /cps/1.0/opps?clientid=DC637766.724&offset=1&limit=20
```
There are also query parameters for specifying sort fields and sort order. Refer to the detailed documentation for more information

### Fully qualified URIs
The Client Profile Services currently are available through the following system:
```
cps.cogsys.zurich.ibm.com
```
Combining the system name, the http protocol, and the relative URI structure produces fully qualified URIs of the form:
```
https://cps.cogsys.zurich.ibm.com/cps/1.0/opps?clientid=DC637766.724&offset=1&limit=20
```

### Detailed API documentation
Each service is <a href="https://cps.cogsys.zurich.ibm.com/cpsdoc/" target="_blank">documented in detail using the popular Swagger API documentation framework</a>. This framework allows each API to be queried and tested with a web form. Note that an access token is required to issue requests from the Swagger documentation. See the section below on security for more details.

## Security Model
### OAuth 2 Tokens
Authentication to the APIs is accomplished with cryptographic tokens. Tokens are a more natural and efficient way to authenticate computer-to-computer communication. Tokens embed information about the user ID of the requester. The requester must be a member of a specific Bluegroup to be authorized to access the services.

Users will be able to request their intranet IDs (personal or functional) be added to the bluegroup by means of an approval process managed by [OneTEAM](https://w3-03.ibm.com/finance/access/initAccessAppRequest.wss?appCode=CEDP). This process will be available shortly. In the meantime, contact [Grant Miller](mailto:millerg@us.ibm.com) to request access.

Note that tokens expire every 24 hours. Programs  use a very simple protocol to refresh their tokens. This protocol is described below in the section, "Obtaining tokens programmatically".

Manually generating tokens is useful for people using browsers such as developers, testers and evaluators. To manually request a token, login into the [CEDP OAuth application](https://cps.cogsys.zurich.ibm.com/cedp-oauth/), register your intranet ID for OAuth access,  and press the "ACCESS TOKENS" command in the upper right of the screen. This token can be copied and pasted as needed.

### Using tokens to access Client Profile Services
The client passes the access token as a parameter to all service requests. Access tokens can be passed in the URL with the `access_token` query parameter or in the HTTP `Authorization` header. The latter is much more secure as query parameters are visible to proxies and are stored in logs.

Below is an example request passing the token as a query parameter:

```
https://cps.cogsys.zurich.ibm.com/cps/1.0/opps?clientid=DC637766.724&access_token=wyoSNiCjnH20mNnnLWf5JiOL4LXfawxLlWleKb7D
```

Alternatively, tokens can be passed in the `Authorization` HTTP request header as described in [RFC 6750 The OAuth 2.0 Authorization Framework: Bearer Token Usage](https://tools.ietf.org/html/rfc6750):

    Authorization: Bearer  Access_token

where:

- `Bearer`     (Mandatory) Is the required string for the token type, as defined in the OAuth 2.0 specification.
- `Access_token`    (Mandatory) is the access token.

Example:
```
Authorization: Bearer  wyoSNiCjnH20mNnnLWf5JiOL4LXfawxLlWleKb7D
```

### Using tokens with Swagger documentation
To use an OAuth token when making service requests from the Swagger API documentation, perform the following steps:
- Log into the CEDP OAuth application, generate a token and mark/copy it.
- In Swagger, click on the "Authorize" button at the top
- Paste "Bearer " plus the token into provided input form. For example:
```
  Bearer wyoSNiCjnH20mNnnLWf5JiOL4LXfawxLlWleKb7D
```
All Swagger requests will now automatically include your token.

### Obtaining tokens programmatically
####OAuth Endpoints

OAuth requires designated URIs for issuing authorization codes and access tokens.
- Authorization endpoint: None. Flows involving OAuth authorization codes currently not used for CPS.
- Token endpoint: `https://cps.cogsys.zurich.ibm.com/oauth2/endpoint/CedpOAuthProvider/token`

 
The next section outlines the basic flow involved in obtaining an access token and using it to access services.

#### Requesting a new token
Clients access services with access tokens. Access tokens are temporary and have expirations.

To obtain a fresh access token, the client issues a POST request to the CEDP CPS OAuth Token Endpoint (above). The form parameters of the POST request include the intranet ID and OAuth client secret (displayed in the CEDP CPS Token Management application). A successful response is a formatted message containing the new access token.

Below, curl is used to illustrate the request syntax:
```
$ curl -k -H "Content-Type: application/x-www-form-urlencoded;charset=UTF-8" -d "grant_type=client_credentials&client_id=cgi@zurich.ibm.com&client_secret=546c1a32-d1aa-47c1-bfdf-7baee96371d1" https://cps.cogsys.zurich.ibm.com/oauth2/endpoint/CedpOAuthProvider/token
```
 
The corresponding response is a JSON document with the `access_token` field containing the new access token:

 ``
{"access_token":"yCqgSt9PJH5cdINSlWzkzqlWHaMDM2m5PQXSxtjF","token_type":"Bearer","expires_in":3599,"scope":""}
``

This access token can be used in service requests.
