# Client 360 Services API

## Overview
The Client 360 Servcies API (C360 API) are a part of CEDP's Data-as-a-Service offering providing a service interface to various dimensions of data related to clients and companies. These services enable applications to retrieve and integrate client data directly into their processing (e.g., a web user interface) with a web-oriented programming interface as opposed to first loading and integrating data into application-specific data stores. 

The C360 API has been designed according to the REST architectural style. The REST approach to data services is widely adopted and its underlying principles plentifully documented. A core tenant is the notion of _resource_ upon which operations are performed. Resources are addressed by URIs. These services are implemented over the HTTP protocol, as is the most common practice for RESTful services, and data representations are embodied in the ubiquitous JSON document format.

In this initial version of Client Profile Servcies, the core resources correspond to the following dimensions of an IBM client:

- Client summary
- Revenue
- Company addresses
 
For each resource, there is one or more URI-addressable service interface. The corresponding URI structure for these resources is outlined in the next section.

## URIs

### Relative URIs
All relative CPS URIs begin with:
```
/client360
```
Appended to the base URI is a version number to facilitate evolution:
```
 /client360/1.0
``` 
Next come the resources themselves, each used in its plural form:
```
 /client360/1.0/companies
 /client360/1.0/addresses
 /client360/1.0/revenue
 
 ```
Each resource supports its own set of parameters for refining the information to be retrieved from the resource. Parameters are passed as either path parameters or URI query parameters. Certain parameters however are common to practically all services. Common parameters include : `cid`, `cid_type`, `offset`, `limit`, `sort_by`, `sort_order`.

As these services are client-centric, almost all service invocations require passing the company identifier (`cid`) of the company in question. For example, to retrieve revenue information for a specific company, the `cid` paramter is passed in the query portion of the corresponding URI, as in the following example:
```
 /client360/1.0/revenue?cid=87312974234&country=724&cid_type=sap
``` 
Note that two other paremeters are passed as well: `country` and `cid_type`. Because the API supports more than one way to identify a company (e.g., SAP number, DNB bumber, perhaps buying group id, etc.), the `cid_type` paremeter is required to inform the service as to the type of identifer. In the above example, the identiier is a SAP number. Some company identifier systems (e.g., SAP customer number, Domestic Client ID) require a country in order to uniquely identify the company. For such identifiers, the `country` is available. 

To support pagination, the `offset` and `limit` parameters allow the API to be queried in blocks. By default, the API returns up to 100 records per request. To retrieve just the first 20 records, `offset` and `limit` are used as follows:
```
 /client360/1.0/revenue?cid=DC637766.724&offset=0&limit=20
``` 
The offset can be incremented in order to page to the next block of responses:
```
 /client360/1.0/revenue?cid=DC637766.724&offset=1&limit=20
```
There are also query parameters for specifying sort fields and sort order. Refer to the detailed documentation for more information

### Fully qualified URIs
The Client Profile Services currently are available through the following system:
```
c360.data.zc2.ibm.com
```
Combining the system name, the http protocol, and the relative URI structure produces fully qualified URIs of the form:
```
https://c360.data.zc2.ibm.com/client360/1.0/revenue?cid=7798794&country=868&cid_type=sap&offset=1&limit=20
```
The following section describes each individual service and its respective parameters and oputput.

### Response data format
Data returned by all APIs is in JSON format. The backend data repository for these services is Elastic Search. The services pass the Elastic Search response document directly to the client, without changes. 

Below is an example response document to an address query. Note the response contains a header followed by an array of `hits` with each `hit` containing a `source` element. The content of the `source` element is the address object. Thus clients typically parse the response document, displaying the contents of each source object to the user.

```
{
    "took": 21,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 2,
        "max_score": 13.586275,
        "hits": [
            {
                "_index": "client360.addr-20170531224331",
                "_type": "company_addr",
                "_id": "AVxgQYvfqn5DOhkIYT_j",
                "_score": 13.586275,
                "_source": {
                    "street_address": "NAKANO",
                    "cust_name": "SOFTWARE CONSULTANT CORPORATION",
                    "issuing_country_no": "760",
                    "sap_customer_no": "0030639536",
                    "cust_name_3": "",
                    "cust_name_2": "",
                    "cust_state_prov": "13",
                    "cust_city": "NAKANO-KU",
                    "cust_postal_code": "1640001"
                }
            },
            {
                "_index": "client360.addr-20170531224331",
                "_type": "company_addr",
                "_id": "AVxgQmFYCh3ZG1y-wKda",
                "_score": 13.586275,
                "_source": {
                    "street_address": "NAKANO",
                    "cust_name": "SOFTWARE CONSULTANT CORPORATION",
                    "issuing_country_no": "760",
                    "sap_customer_no": "0040840274",
                    "cust_name_3": "",
                    "cust_name_2": "",
                    "cust_state_prov": "13",
                    "cust_city": "NAKANO-KU",
                    "cust_postal_code": "1640001"
                }
            }
           ]
      }
 }
 ```
 
## Detailed API documentation

In this section, each API is described in detail in terms of syntax, input parameters and output.
For space resaons, output examples do not include the full Elastic Search response heading and metadata, rather only the contents of the `source` field. See the previous section, _Response data format_, for a brief dscription of how response objects appear in an Elastic Search response document.

----

### `/client360/1.0/companies`
#### Description
Returns basic summary information for the specified company.

#### Data sources
RDc

#### HTTP Methods
GET

#### Parameters
| Name | Required? | Description | Example |
|-----|-----|-----|-----|
| cid      |  Y/N  | Company ID. Required if 'q' parameter is not specified. Identifies the company whose information will be returned | 87987987 |
| cid_type |  Y  | Company identifier type.  Supported values are: `sap, dc, gc, dbg, gbg, dnb` | dc |
| country |  N  | Optional country specifier. Can be a IBM legacy country code or ISO two letter code | 724 or de |
| q |  Y/N  | An [Elastic Search query string expression](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/query-dsl-query-string-query.html#query-string-syntax). Required if the 'cid' parameter is not specified. If this string does not embed field expressions, a full text search is made over all fields. This parameter is used for general purpose search.   | Citibank |
| offset      |  N  | Starting offset for pagination. Optional. Default value is 0. | 2 |
| limit       |  N  | Number of records to return. Optrional. Default value is 100. | 10 |
| sort_by      |  N  | Optional, case-insensitive string specifying the response field by which to sort results. Valid values for this parameter are any field returned for this service. | geo.iso_country_code |
| sort_order   |  N  | Optional field specifying the sort order. Sort order can by ascending or descending. If not specified, the default order is ascending. Valid values for this fields are: asc,desc | desc |


#### Example
Request:
```
GET https://c360.data.zc2.ibm.com/client360/1.0/companies?cid=690764618&cid_type=dnb
```
Response:
```
{
    "quad_tier_code": "B",
    "client_align_type": "GC",
    "enterprise_no": "100324",
    "cust_class_code": "43",
    "client_id": "GC295QZR",
    "cust_class_desc": "Solution Provider",
    "sap": {
        "cust_name": "SOFTWARE CONSULTANT CORPORATION",
        "issuing_country_no": "760",
        "sap_customer_no": "0035293106",
        "cust_name_3": "",
        "cust_name_2": ""
    },
    "geo": {
        "iot_id": "JN",
        "iot_name": "Japan",
        "iso_country_name": "Japan",
        "iso_country_code": "JP",
        "imt_name": "Japan",
        "imt_id": "2J"
    },
    "legal_hierarchy": {
        "gbl_client_id": "GC295QZR",
        "dom_client_id": "DC2B6K6Q",
        "dom_client_name": "SOFTWARE CONSULTANT CORPORATION",
        "dc_duns_no": "690764618",
        "gc_duns_no": "690764618",
        "gbl_ult_client_id": "GU295QZR"
    },
    "goe": {
        "goe_descr": "Not GOE",
        "goe_indc": "N",
        "privacy_cd_descr": "Worldwide",
        "privacy_cd": "W"
    },
    "buying_groups": {
        "buy_grp_name": null,
        "gbl_buy_grp_name": null,
        "gbl_buy_grp_id": null,
        "buy_grp_id": null,
        "bg_country_cd": null
    }
}
```

----

### `/client360/1.0/addresses`
#### Description
Returns address information for each SAP customer element associated with the specified company identifier.

#### Data sources
RDc

#### HTTP Methods
GET

#### Parameters
| Name | Required? | Description | Example |
|-----|-----|-----|-----|
| cid      |  Y  | Company ID. Identifies the company whose information will be returned | 87987987 |
| cid_type |  Y  | Company identifier type.  Supported values are: `sap, dc, gc, dbg, gbg, dnb` | dc |
| country |  N  | Optional country specifier. Can be a IBM legacy country code or ISO two letter code | 724 or de |
| offset      |  N  | Starting offset for pagination. Optional. Default value is 0. | 2 |
| limit       |  N  | Number of records to return. Optrional. Default value is 100. | 10 |
| sort_by      |  N  | Optional, case-insensitive string specifying the response field by which to sort results. Valid values for this parameter are any field returned for this service. | cust_city |
| sort_order   |  N  | Optional field specifying the sort order. Sort order can by ascending or descending. If not specified, the default order is ascending. Valid values for this fields are: asc,desc | desc |

#### Response

| Field | Description |
|-------|-------------|
|sap_customer_no| |
|cust_name| |
|street_address| |
|issuing_country_no| |
|sap_customer_no| |
|cust_name_3| |
|cust_name_2| |
|cust_state_prov| |
|cust_city| |
|cust_postal_code| |

#### Example
Request:
```
GET https://c360.data.zc2.ibm.com/client360/1.0/addresses?cid=690764618&cid_type=dn&boffset=0&limit=5
```
Response:
```
{
     "street_address": "NAKANO",
     "cust_name": "SOFTWARE CONSULTANT CORPORATION",
     "issuing_country_no": "760",
     "sap_customer_no": "0030639536",
     "cust_name_3": "",
     "cust_name_2": "",
     "cust_state_prov": "13",
     "cust_city": "NAKANO-KU",
     "cust_postal_code": "1640001"
}
...
```
----

### `/client360/1.0/revenue`
#### Description
Returns information about a specified company's IBM revenue.

#### Data sources
RDc, SDFDM

#### HTTP Methods
GET

#### Parameters
| Name | Required? | Description | Example |
|-----|-----|-----|-----|
| cid      |  Y  | Company ID. Identifies the company whose information will be returned | 87987987 |
| cid_type |  Y  | Company identifier type.  Supported values are: `cmr, dc, gc, dbg, gbg, dnb` | gc |
| country |  N  | Optional country specifier. Can be a IBM legacy country code or ISO two letter code | 724 or de |
| year | N | Optional revenue year. If not specified, data for the last 4 years is returned | 2015 |
| year | N | Optional revenue year. If not specified, data for the last 4 years is returned | 2015 |
| offset     |  N  | Starting offset for pagination. Optional. Default value is 0. | 2 |
| limit      |  N  | Number of records to return. Optrional. Default value is 100. | 10 |
| sort_by    |  N  | Optional, case-insensitive string specifying the response field by which to sort results.  Valid values for this parameter are any field returned for this service. | revenue |
| sort_order |  N  | Optional field specifying the sort order. Sort order can by ascending or descending. If not specified, the default order is ascending. Valid values for this fields are: asc,desc | desc |

```
GET https://c360.data.zc2.ibm.com/client360/1.0/revenue?iid=876876876&iid_type=sap&country=724&sort_by=revenue&sort_order=desc
```

----


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
https://c360.data.zc2.ibm.com/client360/1.0/revenue?cid=879798123&cid_type=724&access_token=wyoSNiCjnH20mNnnLWf5JiOL4LXfawxLlWleKb7D
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
