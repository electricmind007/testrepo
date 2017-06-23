# DataStage Job Design

## Sorting and Agregation of BigSQL Jobs
When creating DataStage jobs where input and output data objects are in BigSQL, avoid sort and aggregation stages in the DataStage job. These stages
result in the moving of data from BigSQL into DataStage. Instead of exploiting the parallelism of BigSQL to perform the sort,
the sort is performed on th esingle node DataStage instance which may not have enough disk or compute capacity to complete the sort in
reasonable time. Keep data as much as possible in BigSQL.
