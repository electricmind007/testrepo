# Connecting to CEDP Databases

This section provides tool-independent information on connecting to the CEDP databases. For tips on configuring specific tools, refer to [the section on Commonly Used Tools.](CommonTools.md)


### Drop Zone Database

| Parameter | Value |
|---   |  ---  |
|hostname |landing-egress-db2-1.datalake-infrastructure.zc2.ibm.com |
|port	    |50602 |
|database	|<data asset's Drop Zone DB Name> |
|user	    |Intranet ID (example: abc@us.ibm.com) |
|TLS	    | Required |

### Landing Zone (BigSQL) Database

| Parameter | Value |
|---   |  ---  |
|hostname |	bigsql.data.zc2.ibm.com|
|port	|52000|
|database	|bigsql|
|user	|Intranet ID with '@' and '.' replaced with '_' (example: abc_us_ibm_com)|
|TLS	|Required|
