# Planning and Testing

Correctly copying databases is a technical task requiring both expertise and time. Prior to an initial data ingestion, be sure to reserve enough time to test connections, transfers and to verify data is has been correctly converted during ingestion.  

Teams attempting a data ingestion for the first time the night before a deadline are often overly optimistic. Just one unexpected problem, no matter how small, can mean the difference between meeting a deadline or not.

The recommended approach is to test the intended data transfer one week in advance:

- Verify connection to data source.
- Verify connection to target (e.g., Drop Zone)
- Perform limited (e.g., small subset of a known table) end-to-end transfer from source to Drop Zone.
- Verify transfer of data from Drop Zone to Landing Zone
- Examine data in the Landing Zone, confirming the correct number of rows as well as the correct encoding of all data types.


