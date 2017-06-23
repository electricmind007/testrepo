# Connecting to CEDP over JDBC

### DB2 JDBC Type 4 Driver version

Many tools utilize a DB2 JDBC driver. If this driver is not at a recent level, TLS problems will arise and the connection will fail, sometimes with cryptic error messages.

*It is essential that a recent version of the DB2 JDBC driver is used when connecting to the Drop Zone or BigSQL over TLS.*

The following links provide information on obtaining the latest JDBC drivers for DB2/BigSQL <sup>1)</sup> :

- http://www-01.ibm.com/support/docview.wss?uid=swg21363866
- https://www-01.ibm.com/marketing/iwm/iwm/web/reg/download.do?source=swg-idsdjs&S_PKG=dl&lang=en_US&cp=UTF-8&dlmethod=http


*Be sure to use the level 4 driver (db2jcc4.jar) and not the older driver (db2jcc.jar).*


1) For the above links, one must log in with an IBM ID. If you have already linked your IBM ID with your w3id, you can use the link for IBM employees to log in.
TLS

### TLS
Connecting to CEDP databases requires TLS support. As a Java interface, JDBC makes use of Java truststores in its TLS configuration. How truststores are specified is tool-specific. For general information about truststores, refer to [the section on TLS](TLS.md). For tool-specific configuration tips, see [the section on Common Tools](CommonTools.md).


