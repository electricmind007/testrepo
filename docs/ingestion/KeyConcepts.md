# Key Concepts

Data Ingestion is the means of adding new or refreshing existing data assets in the Cognitive Enterprise Data Platform (CEDP).

The key concepts and terms relating to Data Ingestion in the CEDP are:

- **Data Asset:** Data in any form, be it a simple file, a stream of documents, or an entire database.


- **Data Asset Owner, aka Publisher:** A person or organization which has ownership of a data asset to be published in the CEDP


- **Drop Zone (DZ) :** The staging area of the CEDP where data is initially deposited or "dropped".


- **Landing Zone (LZ) :** The area of the CEDP where data is served to authorized users.


- **Catalog:** once data has been moved into the LZ, it is registered in a metadata repository. This repository is implemented with the IBM Governance Catalog (IGC) and is often referred to simply as "the catalog". Users of the CEDP use the catalog to browse and understand the available data assets.


- **Consumer :** any user who is authorized to access or consume an data asset in the LZ.


- **Bluegroups :** Access to the DZ and LZ are determined by membership in asset-specific bluegroups.


These concepts are elaborated upon in the following sections.
