# CEDP 0.5 Data Acquisition Process
## Overview
The Data Acquisition Process describes the steps required to bring a data asset into the CEDP.

Key Concepts and Terms:
- *Data Asset*: data in any form, be it a simple file, a stream of documents, or an entire database.
- *Data Asset Owner*: A person or organization which has ownership of a data asset.
- *Drop Zone (DZ)* : The staging area of the CEDP where data is initially deposited or "dropped". Once in the DZ, data is cataloged and moved into the CEDP where it potentially becomes available user access.
- *Landing Zone (LZ)* : The area of the CEDP where data is served to authorized users.

Viewed broadly, the acquisition process is as follows:
- Data Asset Owner makes initial clarifications with CDO.
- Data Asset Owner provides information detailed in the table below.
- The CEDP creates a Drop Zone database and provides administrative rigths to the DZ bluegroup governed by the Data Asset Owner
- The Data Asset Owner is notified of the creation of the DZ database and provided the necessary configuration details to the DZ.
- Data is ingested by the person(s) in the DZ bluegroup and stored in the DZ.
- A workflow is activated which copies the data from the DZ into the Landing Zone in the CEDP. 
- The workflow updates the Governance Catalog with the technical metadata for the data source.
- The workflow creates policies which authorize the user bluegroup for read access to the file system and BigSQL artifacts associated with the data in the LZ.

## Information required of Data Asset Owners
Data Asset Owners fill out a questionnaire  describing the data asset, governance requirements, contacts and other supporting information.

The table below enumerates the information collected from asset owners at the beginning of the acquisition process.

| Item            | Description                            | Example         |
| --------------------- | ---------------------------------| ------------------- |
| Data asset name  | The official name of the data asset| Reference Data Customer   |
| Data asset short-name  | The generally recognized short name of the data source (maximum of 8 charaters, no spaces)| RDC   |
| Asset Owner  | Email address of primary contact for the asset|  abc@ibm.com   |
| Subject Matter Expert   | Optional but recommended. The email address of an individual who can provide insight into the semantics of the data asset, if needed. For example, the lead data designer| def@ibm.com |
| Data category | General category of the data's domain such as Marketing, Sales, GTS, etc. | Customer Information |
| Type of data | Data can be structured or unstructured. | Structured |
|Bluegroup for drop zone | The name of a bluegroup containing the persons who have access rights to the Data Asset's drop zone. These users perform the data trasnfer from a data source to the Drop Zone. *The Data Asset Owner owns this bluegroup and is responsible for its governance.*   | data_source_dz_users |
|Bluegroup for CEDP user access  |The name of a bluegroup containing the persons (e.g., data scientist users) who have read access rights to the Data Asset inside the CEDP data lake *The Data Asset Owner owns this bluegroup and is responsible for its governance.*   | data_source_users  |
| Access request URL  | The URL of an entity providing information on how to acquire approval to access the Data Asset in the data lake. This can be a URL to a document or community page. It can also be a mailto: URL referring to a contact person.   |  http://w3.connections.ibm.com/ds/access_info|
| DOUs  | Reference to any pertinent DOUs  |  DOU 3646570|
| Approximate size of data asset | Describes the storage requirements for the Data Asset.    | 4 TB |
| Refresh interval |  How often will the data be refreshed in the data lake.  | Weekly |
| Refresh style |  Refresh style characterizes how data is refreshed: append-only, record-level updates, drop-replace (supported refresh styles to be determined). | drop-replace |
| Data movement strategy |  Describes the preferred technical means of copying data from its source to the Drop Zone. For example, a data asset can be pushed into the drop zone from the data source. Alternatively, the transfer can be initiated from an ETL component ("pulled").  | CEDP DataStage job|
| Personal information indicator|  Indicates if the Data Asset contains personal information(PI) | y/n |
| GPAT ID|  The GPA identifier, if available, pertaining to the data asset.  | 7687687264 |
| Sensitive Personal information indicator|  Indicates if the Data Asset contains sensitive personal information (SPI)  | y/n |

  




