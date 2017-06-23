# DataStage: Licensing Error using JDBC to connect to Z-Series DB2


1. Download the licence file from inter software download page, here is the name of that file: IBM DB2 Connect Application Server Advanced Edition - Quick Start and Activation V10.5.0.8 for Linux, UNIX and Windows Multilingual (CNED9ML )
2. On DS node: unzip the package and copy the license file db2consv_ee.lic to `/opt/ibm/db2/V10.5/license/` and copy the license jar file `db2jcc_license_cisuz.jar` to `/opt/ibm/db2/V10.5/java/`
3. Log in to DS node with the db2 client instance owner (e.g. su - db2inst1) and run the apply the new license file: `db2licm -a /opt/ibm/db2/V10.5/license/db2consv_ee.lic`
4. Change dsenv from
```
    if [ -f /home/db2home/db2inst1/sqllib/db2profile ]; then
    . /home/db2home/db2inst1/sqllib/db2profile
    fi
```
to
```
    if [ -f /home/db2home/db2inst1/sqllib/db2profile ]; then
    . /home/db2home/db2inst1/sqllib/db2profile
    fi
    CLASSPATH=/home/db2home/db2inst1//sqllib/java/db2jcc_license_cisuz.jar:$CLASSPATH; export CLASSPATH
```
5. Finally restart the DS to apply the new dsenv.

Some useful links about the issue:

- http://www-01.ibm.com/support/docview.wss?uid=swg21635635
- https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.qb.server.doc/doc/t0023608.html
- http://www-01.ibm.com/support/docview.wss?uid=swg21413734

Thanks to Attila (@KAL) and Viktor (@VKU) for resolving [this issue](https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/issues/1147).
