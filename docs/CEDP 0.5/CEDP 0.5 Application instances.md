# CEDP 0.5 Application instances

## DB2 Drop Zone

| Hostname             | IP Address  | DB2 version        | Type             | OS         |
| -------------------- | ----------- | ------------------ | ---------------- | ---------- |
| langing-egress-db2-1 | 9.4.135.201 | DB2 11.1 PureScale |	Member           | RedHat 7.3 |
| langing-egress-db2-2 | 9.4.135.200 | DB2 11.1 PureScale |	Member           | RedHat 7.3 |
| langing-egress-db2-3 | 9.4.135.205 | DB2 11.1 PureScale |	Member           | RedHat 7.3 |
| langing-egress-db2-4 | 9.4.135.204 | DB2 11.1 PureScale |	Caching Facility | RedHat 7.3 |
| langing-egress-db2-5 | 9.4.135.203 | DB2 11.1 PureScale |	Caching Facility | RedHat 7.3 |


## DB2 Integration Zone

| Hostname          | IP Address  | DB2 version        | Type             | OS         |
| ----------------- | ----------- | ------------------ | ---------------- | ---------- |
| integration-db2-1 | 9.4.135.196 | DB2 11.1 PureScale |	Member           | RedHat 7.3 |
| integration-db2-2 | 9.4.135.198 | DB2 11.1 PureScale |	Member           | RedHat 7.3 |
| integration-db2-3 | 9.4.135.38  | DB2 11.1 PureScale |	Member           | RedHat 7.3 |
| integration-db2-4 | 9.4.135.199 | DB2 11.1 PureScale |	Caching Facility | RedHat 7.3 |
| integration-db2-5 | 9.4.135.2   | DB2 11.1 PureScale |	Caching Facility | RedHat 7.3 |

## IGC/ISS

| Hostname                  | IP Address  | CPU | Mem   | Disk  | OS         |
| ------------------------- | ----------- | ---:| -----:| -----:| ---------- |
| igc-1                     | 9.4.135.41  | 8   | 64 GB | 50 GB | RedHat 7.3 |
| igc-2                     | 9.4.135.42  | 8   | 64 GB | 50 GB | RedHat 7.3 |
| igc-quorum                | 9.4.135.62  | 1   | 1 GB  | 15 GB | RedHat 7.3 |
| iis-igc-glusterfs         | 9.4.135.235 | 8   | 64 GB | 50 GB | RedHat 7.3 |
| iis-igc-nfs-1             | 9.4.135.230 | 1   | 1 GB  | 50 GB | RedHat 7.3 |

| Hostname           | IP Address | DB2 version | Type         | OS         |
| ------------------ | ---------- | ----------- | -------------| ---------- |
| catalog-db2-hadr-1 | 9.4.135.56 | DB2 10.5    | HADR Primary | RedHat 6.7 |
| catalog-db2-hadr-1 | 9.4.135.55 | DB2 10.5    | HADR Standby | RedHat 6.7 |

## BigInsights

| Service                         | Version    |
| ------------------------------- | ---------- |
| HDFS                            |	2.7.2      |
| MapReduce2                      | 2.7.2      |
| YARN                            | 2.7.2      |
| Hive                            | 1.2.1      |
| HBase                           | 1.2.0      |
| Pig                             | 0.15.0     |
| Sqoop                           | 1.4.6      |
| Oozie                           | 4.2.0      |
| ZooKeeper                       | 3.4.6      |
| Flume                           | 1.6.0      |
| BigInsights - BigSheets         | 5.21.0.288 |
| BigInsights - Big SQL           | 4.2.0.0    |
| BigInsights Data Server Manager | 2.1.0.0    |
| Kafka                           | 0.9.0.1    |
| Kerberos                        |	1.10.3     |
| Knox                            | 0.7.0      |
| Ranger                          | 0.5.2      |
| Slider                          |	0.90.2     |
| Spark                           |	1.6.1      |
| BigInsights Home                |	2.14       |

### Master components

| BigSheets Master | BigSQL Head | Data Server Manager | HBase Master | NameNode | Hive Metastore |
| ---------------- | ----------- | ------------------- | ------------ | -------- | -------------- |
| c13n5            | c04n1       | c13n5               | c13n3        | c13n1    | c13n3          |
|                  |             |                     | c13n6        | c13n2    | c13n6          |

| HiveServer2      | WebHCat Server | Kafka Broker | Knox Gateway | History Server | Oozie Server |
| ---------------- | -------------- | ------------ | ------------ | -------------- | ------------ |
| c13n3            | c13n3          | c13n1        | c13n5        | c13n1          | c13n6        |
| c13n6            | c13n6          |              |              |                |              |

| Ranger Admin     | Ranger Usersync | Spark History Server | Spark Thrift Server | BigInsights Home Server |
| ---------------- | --------------- | -------------------- | ------------------- | ----------------------- |
| c13n1            | c13n1           | c13n4                | c13n4               | c13n5                   |
| c13n5            |                 |                      |                     |                         |

| App Timeline Server | ResourceManager | ZooKeeper Server |
| ------------------- | --------------- | ---------------- |
| c13n1               | c13n1           | c13n1            |
|                     | c13n2           | c13n2            |
|                     |                 | c13n3            |

### Slave components

| HDFS DataNode | BigSQL Worker | Flume        | RegionServer | HBaseRestServer | JournalNode |
| ------------- | ------------- | ------------ | ------------ | --------------- | ----------- |
| bi-worker-1   | bi-worker-11  | bi-worker-1  | bi-worker-1  | bi-worker-1     | c13n1       |
| bi-worker-2   | bi-worker-12  | bi-worker-2  | bi-worker-2  | bi-worker-2     | c13n2       |
| bi-worker-3   | bi-worker-13  | bi-worker-3  | bi-worker-3  | bi-worker-3     | c13n3       |
| bi-worker-4   | bi-worker-14  | bi-worker-4  | bi-worker-4  | bi-worker-4     |             |
| bi-worker-5   | bi-worker-15  | bi-worker-5  | bi-worker-5  | bi-worker-5     |             |
| bi-worker-6   | bi-worker-16  | bi-worker-6  | bi-worker-6  | bi-worker-6     |             |
| bi-worker-7   | bi-worker-17  | bi-worker-7  | bi-worker-7  | bi-worker-7     |             |
| bi-worker-8   | bi-worker-18  | bi-worker-8  | bi-worker-8  | bi-worker-8     |             |
| bi-worker-9   | bi-worker-19  | bi-worker-9  | bi-worker-9  | bi-worker-9     |             |
| bi-worker-10  |               | bi-worker-10 | bi-worker-10 | bi-worker-10    |             |
| bi-worker-11  |               |              |              | c13n3           |             |
| bi-worker-12  |               |              |              | c13n6           |             |
| bi-worker-13  |               |              |              |                 |             |
| bi-worker-14  |               |              |              |                 |             |
| bi-worker-15  |               |              |              |                 |             |
| bi-worker-16  |               |              |              |                 |             |
| bi-worker-17  |               |              |              |                 |             |
| bi-worker-18  |               |              |              |                 |             |
| bi-worker-19  |               |              |              |                 |             |

| NodeManager   | ZKFailoverController |
| ------------- | -------------------- |
| bi-worker-1   | c13n1                |
| bi-worker-2   | c13n2                |
| bi-worker-3   |                      |
| bi-worker-4   |                      |
| bi-worker-5   |                      |
| bi-worker-6   |                      |
| bi-worker-7   |                      |
| bi-worker-8   |                      |
| bi-worker-9   |                      |
| bi-worker-10  |                      |

