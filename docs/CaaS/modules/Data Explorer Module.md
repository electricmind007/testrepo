# CaaS Module for Data Explorer

## Architecture, Design and Overview
The Data Explorer Module will integrate a natural language query interface with the existing Data Explorer tool. The Data Explorer tool provides the ability for a user to create specified data sets from a provided set of curated RDC related data. The user has the ability to specify certain filters on the available data, and also to choose which data from the curated set will be included in the data export. The data export is provided via a CSV file, and is also made available to query via SQL through the BigSQL service available from the CEDP Portal.

### Filters

Filters can currently be specified by Account Levels, Countries, and Industries.

### Available Data

The available data tables that are available for export are (organized into related areas called packages):

#### Client Hierarchy
- IBM RDC Reference Set

#### Firmographics and Financials
- Company Name
- Address
- Industries by IBM RDC
- Company Financial KPIs

#### Client Install Base
- Client Competitive Install Base
- Company Install Base

#### Client Opportunities
- Opportunity Pipeline
- Marketing Interaction

#### IBM Revenue
- IBM Revenue by business unit
- IBM Revenue by product divisions
- IBM Revenue total yearly
- IBM Revenue total yearly transposed

#### Contacts
- Contacts
- Marketing Contacts

#### Client Contracts
- Contracts
- BMSIW Contracts

The IBM RDC Reference Set is the central data piece, with the rest of the tables relating to data in this table.

The Data Explorer tool allows for individual selection of any of the tables, but also allows for selection by "package", which allows selection of several tables that have been defined for the package. For example, a user can select the "Firmographics & Financials" package, and this will automatically add all of the tables for that category to the list of data to export (i.e. Company Name, Address, Industries by IBM RDC, Company Financial KPIs).

Additionally, the tool allows for selecting specific column data from each of the tables chosen for export.

Once filter(s) have been selected from the ones available, the tool allows for an export of the RDC list that matches the filter(s).

Based on the selected filter(s), the chosen packages / tables, and the specified columns, the tool can run a job that exports the selected data. This job is executed by clicking on an "Export" button.

### Intent

There is one main intent here, and that is to generate a subset of RDC related data from the curated data set available to the Data Explorer tool.

Based on the natural language query string input, the module will determine if the question is inquiring about data export from the Data Explorer. If it is, a parser will pull out any relevant data, such as which filters to apply, and which output tables are being asked for. Using this information, a link will be provided to the Data Explorer tool. The link will open the tool with the filters and tables preselected in the Data Explorer UI based on the information obtained from the initial question. The user can optionally make any modifications, and then export the data through the Data Explorer tool.

Note that this module will leave all columns selected from the chosen tables; that level of granularity will be left for the user to apply through the Data Explorer rather than attempt to provide it in a natural language query. The user has the ability to optionally deselect any columns from their selected tables from the Data Explorer UI, should the need arise.

### Integration

The main integration point for the module and the Data Explorer tool is the link provided which launches the tool's explorer.html page. That link will contain the relevant filter and output table information via query parameters.

The Data Explorer does not currently utilize this type of "deep-linking" functionality, it will require some changes, namely, the ability for it to accept the parameters that are passed to it. These parameters will then be inserted into the appropriate locations on the page (including executing any logic it would normally do) when presented to the user.

The interface between the Data Explorer Query Module and the Data Explorer tool will be a modification to the Data Explorer URL to add query parameters. The query parameters will indicate the filters to use, and the packages and tables to specify for the export. 

- Each parameter will be specified by a key=value pair, with the key being the name of a filter, or the keyword "package" to specify a package, or "table" to specify a table. The keywords currently in use are listed in the Filters section above.
- If a table, package, or filter is not specified, it will not appear in the query parameters.
- The key will always be in lower case.
- The values will be in mixed case.
- Multiple values for a given key will be separated by a comma.
- There is no specific order for the key/value pairs.

The URL generated from an utterance containing filters, packages, and tables will resemble this example:

[https://data-lake.zurich.ibm.com/explorer.html?table=Contracts&industries=Retail,Electronics&account+levels=Domestic+Client&countries=Japan&package=Firmographics+%26+Financials&company=Walmart]

### Appendix

#### Design Notes

##### Data Explorer

- Need to add the ability to specify filter / table parameters when calling the Data Explorer landing page, and have it populate the filters and tables based on those parameters. This will require logic in the explorer.html page.

- Need to create REST services to provide the list of Account Levels, Countries, and Industries. This information will be needed by the CaaS module.

- Need to create REST services to provide lists of packages and tables, and which tables are in which package. Note that this information should be available in Elastic Search, which provides json-based REST APIs. Should this be wrappered or accessed as-is? Utilizing a wrapper hides the implementation details, in case the back-end is ever changed.

- The list of columns for the tables, or other table metadata, is not needed so no need to integrate with the Mongo database or any of the REST APIs that sit on top of it.

##### Module

- A parser is required to pull out the relevant data from the natural language query. This parser will need to make use of the REST services to get some values that it should be looking for, i.e. such as the value domain for each of the filters, list of packages, list of tables, etc.

- A dictionary will need to be created with synonyms for some of the values. For example, "opportunity" could be specified as oppty, opptys, oppties, opportunity, opportunitiy, etc.

### Some sample natural language query questions:

**Question:** What data is available to export?<br/>
**Answer:** [List of packages / tables, link to the Data Explorer with no extra parameters]

**Question:** What filters can I use for the data?<br/>
**Answer:** [List of filters, link to the Data Explorer with no extra parameters]

**Question:** Can I get the retail client contracts for domestic clients in Canada?<br/>
**Answer:** [Link to the Data Explorer tool with filters: account level = Domestic Client, country = Canada, industry = Retail and tables = Contracts, BMSIW Contracts]

**Question:** What are the buying groups for the electronics industry?<br/>
**Answer:** [Link to the Data Explorer tool with filters: account level = Buying Group, country = All Countries, industry = Electronics]

**Question:** Can I get the information for Japanese automotive clients?<br/>
**Answer:** [Link to the Data Explorer tool with filters: account level = All Account Levels, country = Japan, industry = Automotive]

**Question:** What is the yearly revenue for the US by client?<br/>
**Answer:** [Link to the Data Explorer tool with filters: account level = All Account Levels, country = United States, industry = All Industries and tables = IBM Revenue total yearly]

**Question:** Can I export the financial info for American domestic clients for the banking industry?<br/>
**Answer:** [Link to the Data Explorer tool with filters: account level = Domestic Client, country = United States, industry = Banking and tables = Company Name, Address, Industries by IBM RDC, Company Financial KPIs]
