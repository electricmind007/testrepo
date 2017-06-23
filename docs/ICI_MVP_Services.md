# ICI Individual Services API

## Overview
The Individual Services API has been designed according to the REST architectural style. The REST approach to data services is widely adopted and its underlying principles plentifully documented. A core tenant is the notion of _resource_ upon which operations are performed. Resources are addressed by URIs. These services are implemented over the HTTP protocol, as is the most common practice for RESTful services, and data representations are embodied in the ubiquitous JSON document format.

In this initial version, the core resources correspond to the following dimensions of an individual:

- Individual business card
- Individual communication preferences
- Individual interests
- Individual's company profile
- Marketing interactions with an individual
- Opportunities associated with an individual
- SaaS Subscriptions and Usage (planned)

 
For each resource, there is one or more URI-addressable service interface. The corresponding URI structure for these resources is outlined in the next section.

## URIs

### Relative URIs
All relative URIs begin with:
```
/ici
```
Appended to the base URI is a version number to facilitate evolution:
```
 /ici/1.0
``` 
The base URI and version are followed by resource specifiers which together comprise a simple taxonomy of the information provided by the API:
```
 /ici/1.0/individuals
 /ici/1.0/individuals/mktg_interactions
 /ici/1.0/individuals/opts
 /ici/1.0/individuals/subscriptions
 ```
By convention, resource names are plural. 

Each resource supports a set of parameters for refining information to be retrieved. Parameters are passed as URI query parameters. Some parameters are common to practically all services. The common parameters include : `iid`, `clientid`, `offset`, `limit`, `sort_by`, `sort_order`. The common parameters, as well as URI-specific parameters, are described in the next section.

### Common URI Query Parameters
URI query parameters are used to apply filters or query options over a base resource. The following subsections describe parameters whihc are common to most of the API's services.

#### Individual IDs
As the URI space indicates, the ICI API is individual-centric and the ability to refer to a specific individual is fundamental. In general, individual specifiers are termed Individual IDs (`iid` for short) and can take various forms:

- Email address
- Login ID or IUI from web identity
- MPW Individual URN (URN_IDM_INDIV)
- Sales Connect ID (SC_CONT_ID)

To retrieve an instance of a resource pertaining to a specific with regards to an individual, the respective individual ID is passed. For example, assume a given individual has the unique email identifier, "name@company.com" :
```
 /ici/1.0/individuals?iid=name@company.com&iid_type=email
``` 
Note the required `iid_type` parameter indicates the identifer type. In the above example, the identifer type is 'email' to indicate the ID format is an email address.

#### Field selection
Each service returns one or more JSON documents as the response format where a JSON document is comprised of name-value pairs. With the optional `fields` parameter, applications can optionally indicate which fields should be returned. For example, in order that only email and last name fields are retrieved, the service is invoked as such:

```
 /ici/1.0/individuals?iid=76876876876&iid_type=idm&fields=business_card.email_adr,business_card.indiv_name_last
``` 
Note the field names must correspond exactly to the field names in the respective response document. Be aware that some services may place a field such as an email address in a different document location.

#### Pagination
To support pagination, the `offset` and `limit` parameters allow the API to be queried in blocks. By default, the API returns up to 100 records per request. To retrieve just the first 20 records, `offset` and `limit` are used as follows:
```
 /ici/1.0/individuals/mktg_interactions?iid=87238724&iid_type=idm&offset=0&limit=20
``` 
The offset can be incremented in order to page to the next block of responses:
```
 /ici/1.0/individuals/mktg_interactions?iid=87238724&iid_type=idm&offset=1&limit=20
```
#### Sorting and ordering
There are also query parameters for specifying sort fields and sort order. Refer to the detailed documentation for more information.

All requests require the userid in form of intranet id of the user on whose behalf the request is being made:
```
 /ici/1.0/individuals/mktg_interactions?iid=87238724&iid_type=idm&offset=1&limit=20&userid=abc@us.ibm.com
```

### Fully qualified URIs
The Individual Services currently will be available through the following system:
```
ici.data.zc2.ibm.com
```
Combining the system name, the http protocol, and the relative URI structure produces fully qualified URIs of the form:
```
https://ici.data.zc2.ibm.com/ici/1.0/individuals?iid=67367637673&iid_type=idm&offset=1&limit=20
```

## Service descriptions
This section provides a brief description of each service and its parameters.

### `/ici/1.0/individuals`
#### Description
Returns profile information about about an individual:
- business card
- communication preferences
- interests
- company profile

There are two ways to retrieve individual data: by individual identifier (see the 'iid' paremeter, below) or by company identifier ('cid' paremeter).

#### HTTP Methods
GET

#### Parameters
| Name | Required? | Description | Example |
|-----|-----|-----|-----|
| iid      |  Y/N  | A comma-separated list of Individual IDs, each ID identifying an invididual whose information will be returned. Required if the 'cid' parameter is not present. | user@company.com |
| iid_type |  Y/N  | Individual type specifier.  Supported values are: `email, idm, iui, sc, wipi` | email |
| cid      |  Y/N  | A comma-separated list of company identifiers. Required if iid is not present. Identifies the company or companies for which individuals are associated. Individual ID. Identifies the invididual whose information will be returned | 460171123173840 |
| cid_type |  Y/N  | Company identifier type specifier.  Supported values are: `idm, domain` | idm |
| offset       |  N  | Optional specification of the first returned data item relative to the result set arising from the service request. By default, offset is 0. Offset, together with limit, provides support for pagination. | 2 |
| limit        |  N  | Optional specification of the number of data items (i.e, records) to return. If not specified, up to 100 items will be returned. | 20 |
| sort_by      |  N  | Optional, case-insensitive string specifying the response field by which to sort results. If not specified, responses are sorted by the last_name field. Valid values for this parameter are any field returned for this service. | job_level_cd |
| sort_order   |  N  | Optional field specifying the sort order. Sort order can by ascending or descending. If not specified, the default order is ascending. Valid values for this fields are: asc,desc | desc |

#### Examples
By individual identifer:
```
GET https://ici.data.zc2.ibm.com/ici/1.0/individuals?iid=name@company.com&iid_type=email
```
By company identifier:
```
GET https://ici.data.zc2.ibm.com/ici/1.0/individuals?cid=460171123173840&cid_type=idm
GET https://ici.data.zc2.ibm.com/ici/1.0/individuals?cid=sybari.com&cid_type=domain
```
----

### `/ici/1.0/individuals/mktg_interactions`
#### Description
Returns marketing interactions for a specified indvidual. 

#### HTTP Methods
GET

#### Parameters
| Name | Required? | Description | Example |
|-----|-----|-----|-----|
| iid          |  Y  | Comma-separated list of Individual IDs, each ID identifying an individual whose marketing interactions will be returned.  | user@company.com |
| iid_type     |  Y  | Optional Indivdual ID type specifier.  Supported values are: `email, idm` | email |
| mktg_intrctn_type_key  |  N  | The marketing tactic code identifying a specific marketing interaction. The tactic code is typically obtained from the results returned when enumerating marketing interactions. | C24400DE |
| days_in_past |  N  | Optional number of days in the past to restrict the query to. For example, to retrieve marketing interactions for a specific client over the last 90 days, set this parameter to 90. If not specified, all interactions are retruned, regarldess of age. | 90 |
| offset       |  N  | Optional specification of the first returned data item relative to the result set arising from the service request. By default, offset is 0. Offset, together with limit, provides support for pagination. | 2 |
| limit        |  N  | Optional specification of the number of data items (i.e, records) to return. If not specified, up to 100 items will be returned. | 20 |
| sort_by      |  N  | Optional, case-insensitive string specifying the response field by which to sort results. If not specified, responses are sorted by the last_name field. Valid values for this parameter are any field returned for this service. | job_level_cd |
| sort_order   |  N  | Optional field specifying the sort order. Sort order can by ascending or descending. If not specified, the default order is ascending. Valid values for this fields are: asc,desc | desc |
| q       |  N  | Optional field specifying a valid Elastic Search query expression |   |

#### Example
```
GET https://ici.data.zc2.ibm.com/ici/1.0/individuals/mktg_interactions?iid=name@company.com&iid_type=email&days_in_past=90
```
----

### `/ici/1.0/individuals/optys`
#### Description
Returns all Sales Connect Opportunities associated with an individual.

#### HTTP Methods
GET

#### Parameters
| Name | Required? | Description | Example |
|-----|-----|-----|-----|
| iid      |  Y  | Individual ID. Identifies the invididual whose information will be returned | user@company.com |
| iid_type |  Y  | Optional iid type specifier.  Supported values are: `email, idm, iui, sc` | email |
| offset       |  N  | Optional specification of the first returned data item relative to the result set arising from the service request. By default, offset is 0. Offset, together with limit, provides support for pagination. | 2 |
| limit        |  N  | Optional specification of the number of data items (i.e, records) to return. If not specified, up to 100 items will be returned. | 20 |
| sort_by      |  N  | Optional, case-insensitive string specifying the response field by which to sort results. If not specified, responses are sorted by the last_name field. Valid values for this parameter are any field returned for this service. | job_level_cd |
| sort_order   |  N  | Optional field specifying the sort order. Sort order can by ascending or descending. If not specified, the default order is ascending. Valid values for this fields are: asc,desc | desc |
| q       |  N  | Optional field specifying a valid Elastic Search query expression |   |

----


## Security Model
### OAuth 2 Tokens
Authentication to the APIs is accomplished with cryptographic tokens. Tokens are a more natural and efficient way to authenticate computer-to-computer communication. Tokens embed information about the user ID of the requester. The requester must be a member of a specific Bluegroup to be authorized to access the services.

Users will be able to request their intranet IDs (personal or functional) be added to the bluegroup by means of an approval process managed by [OneTEAM](https://w3-03.ibm.com/finance/access/initAccessAppRequest.wss?appCode=CEDP). This process will be available shortly. In the meantime, contact [Grant Miller](mailto:millerg@us.ibm.com) to request access.

Note that tokens expire every 24 hours. Programs  use a very simple protocol to refresh their tokens. This protocol is described below in the section, "Obtaining tokens programmatically".

Manually generating tokens is useful for people using browsers such as developers, testers and evaluators. To manually request a token, login into the [CEDP OAuth application](https://cps.cogsys.zurich.ibm.com/cedp-oauth/), register your intranet ID for OAuth access,  and press the "ACCESS TOKENS" command in the upper right of the screen. This token can be copied and pasted as needed.

### Using tokens to access Individual Cognitive Insight (ICI) Services
The client passes the access token as a parameter to all service requests. Access tokens can be passed in the URL with the `access_token` query parameter or in the HTTP `Authorization` header. The latter is much more secure as query parameters are visible to proxies and are stored in logs.

Below is an example request passing the token as a query parameter:

```
https://ici.data.zc2.ibm.com/ici/1.0/optys?iid=64676764&iid_type=idm&access_token=wyoSNiCjnH20mNnnLWf5JiOL4LXfawxLlWleKb7D
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

