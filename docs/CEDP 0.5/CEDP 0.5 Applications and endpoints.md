# CEDP 0.5 Applications and endpoints

## DB2 Drop Zone (Landing Zone)

### Roles

| Owner           | Administrator          |
| --------------- | ---------------------- |
| Name here       | Admin #1               |
|                 | Admin #2               |

### Endpoints

| Endpoint address                                         | Port  | Secure   |
| -------------------------------------------------------- | ----- | -------- |
| dropzone-db2-1.data.zc2.ibm.com | 50602 | TLS v1.2 |
| dropzone-db2-2.data.zc2.ibm.com | 50602 | TLS v1.2 |
| dropzone-db2-3.data.zc2.ibm.com | 50602 | TLS v1.2 |

## DB2 Integration Zone

### Roles

| Owner           | Administrator          |
| --------------- | ---------------------- |
| Name here     | Admin #1               |
|                 | Admin #2               |

### Endpoints

| Endpoint address                                      | Port  | Secure   |
| ----------------------------------------------------- | ----- | -------- |
| integration-db2-1.datalake-infrastructure.zc2.ibm.com | 50602 | TLS v1.2 |
| integration-db2-2.datalake-infrastructure.zc2.ibm.com | 50602 | TLS v1.2 |
| integration-db2-3.datalake-infrastructure.zc2.ibm.com | 50602 | TLS v1.2 |

## IGC/ISS

### Roles

| Owner                            | Administrator                           |
| -------------------------------- | --------------------------------------- |
| Charles Hill (cwhill@us.ibm.com) | Sonia Mettezza (somezz@us.ibm.com)      |
|                                  | Stephen Cureton (cureton1@us.ibm.com)   |
|                                  | Carrie Fausel (cfausel@us.ibm.com)      |
|                                  | Rajesh Malwade (raj.malwade@us.ibm.com) |
|                                  | Syed Ahmed (ahmed@us.ibm.com)           |
|                                  | Ahmed Monsef (AMONSIF@eg.ibm.com)           |

### Endpoints

| Name                             | Short link | Endpoint address                                                          |
| -------------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------- |
| Launchpad                        |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/launchpad/   |
| WebSphere admin console          |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9043/ibm/console/         |
| Data Quality Exception Console   |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/dqec         |
| Operations Console               |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/ds/console   |
| Administration Console           |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/console      |
| Information Governance Catalog   | [ibm.biz/cedp-catalog](http://ibm.biz/cedp-catalog) | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/igc          |
| Information Governance Dashboard |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/igd          |
| Metadata Asset Manager           |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/imam/console |
| Subscription Manager             |                                                     | https://igc.datalake-infrastructure.zc2.ibm.com:9446/ibm/iis/events       |

## BigInsights

### Roles

| Owner           | Administrator          |
| --------------- | ---------------------- |
| Name here       | Admin #1               |
|                 | Admin #2               |

### Endpoints

In general, authenticate with your intranet username and password. For BigSQL JDBC, log in with your intranet password and username as follows: convert "." and "@" in your intranet username to "_".

| Name                             | Endpoint address                                                          |
| -------------------------------- | ------------------------------------------------------------------------- |
| Knox gateway                     | https://c13n5.i.zc2.ibm.com:8443/gateway/                                 |
| BigSQL JDBC                      | jdbc:db2://c04n1.i.zc2.ibm.com:52000/BIGSQL:sslConnection=true;sslTrustStoreLocation={trust-store-path};sslTrustStorePassword={trust-store-password} |

#### Admin/Dev

| Name                             | Short link                                              | Endpoint address                                                          |
| -------------------------------- | ------------------------------------------------------- | ------------------------------------------------------------------------- |
| Data Server Manager (BigSQL UI)  |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/default/BigInsightsWeb/index.html  |
| HDFS UI                          | [ibm.biz/cedp-sandboxes](http://ibm.biz/cedp-sandboxes) | https://c13n5.i.zc2.ibm.com:8443/gateway/default/hdfs/explorer.html       |
| WebHDFS REST API                 |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/default/webhdfs                  |
| WebHCat gateway                  |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/default/templeton                |
| Oozie gateway                    |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/default/oozie                    |
| HBase gateway                    |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/default/hbase                    |
| HBase UI                         |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/hbaseui/hbase/master-status      |
| Hive gateway                     |                                                         | jdbc:hive2://c13n5.i.zc2.ibm.com:8443/;ssl=true;sslTrustStore={gateway-trust-store-path};trustStorePassword={gateway-trust-store-password}?hive.server2.transport.mode=http;hive.server2.thrift.http.path=gateway/default/hive              |
| Yarn gateway                     |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/default/resourcemanager          |
| Yarn UI                          |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/yarnui/yarn                      |
| Spark History Server UI          |                                                         | https://c13n5.i.zc2.ibm.com:8443/gateway/sparkui/spark/                   |

#### Users

| Name                             | Endpoint address                                                          |
| -------------------------------- | ------------------------------------------------------------------------- |
| Data Server Manager (BigSQL UI)  | https://c13n5.i.zc2.ibm.com:8443/gateway/users/BigInsightsWeb/index.html  |
| HDFS UI                          | https://ambari-1.datalake-infrastructure.zc2.ibm.com:8443                 |
| WebHDFS REST API (SSO)           | https://c13n5.i.zc2.ibm.com:8443/gateway/users/webhdfs                    |
| WebHDFS REST API                 | https://c13n5.i.zc2.ibm.com:8443/gateway/webhdfs/webhdfs                  |
| Restricted Oozie gateway         | https://c13n5.i.zc2.ibm.com:8443/gateway/oozieexternal/oozie              |

Related documentation: [Knox URL mapping](http://www.ibm.com/support/knowledgecenter/SSPT3X_4.2.0/com.ibm.swg.im.infosphere.biginsights.admin.doc/doc/knox_url_mapping_hadoop_services.html)
