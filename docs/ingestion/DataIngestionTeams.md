# Data Ingestion Teams

Successfully bringing data into CEDP is a team event built on the interplay of various roles:

- **Architect:** Understands the end-to-end application and system requirements both at the data source and the target platform (i.e., CEDP). Fully understands the volume, type,  semantics and governance requirements of the data being ingested and as well as system specifics such as source architecture, maintenance windows, load tolerance and "freeze periods".

- **Data Source DBA:** The Database Administrator (DBA) at the data source is knowledgeable about all system and database aspects of the data source and is instrumental in defining technical parameters (e.g., connection details, preferred transfer method) as well as timing of the ingestion (e.g., during weekly maintenance window).

- **Data Engineer:** Performs the actual data ingestion from data source to DropZone as well as implementing the refresh strategy. Has a solid command of Database Administration (DBA), ETL tools such as DataStage,  and  standard database interfaces (DB2, JDBC, ODBC).

- **CEDP Platform Engineer:** Provides expertise on all aspects of the CEDP including interfaces, security, transfer rates, and ingestion approaches.

- **CDO Governance expert:** Reviews the data content, security and privacy objectives as well as the allowable purposes for using the data asset. Makes recommendations for authorization policy and all governance-related matters such as metadata management, data quality and curation. Manages access to the data asset's bluegroups.

- **Project management:** Setting up data ingestion for the first time involves timing and coordination of numerous factors. Dedicated project management is indispensable.


