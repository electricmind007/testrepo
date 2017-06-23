# Transfer Rates

Tranfer rates vary widely due to numerous factors such as:
- Transfer rate from the source database
- Network latency
- Connection parameters  (e.g., DataStage JDBC, ODBC, or native client connector)
- Table chararcteristics (character encoding, table, width)

As a result, it is not possible to provide precise and consistent transfer times. 

Some reference values from recent testing and practice to date include:

- Raw transfer time into the Drop Zone has been tested at 2 million rows in less than 30 seconds.
- A weekly ingestion using DataStage transfers 75 million rows from a Netezza/PDA in the UK to the Drop Zone in Zurich in just under 3 hours. 

So far, all cases of reported sluggish performance have been due to slow throughout from the source system. As described in the [section on Data Ingestion Teams](DataIngestionTeams.md), it is important to work closely with a DBA on the source system to identify the best connection methods and transfer times.

If experiencing slow transfer rates, please open a [github issue](Community.md) so that a contact between the CEDP specialists and the data source DBA can be established.

