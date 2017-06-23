# TLS

TLS is a widely-used crytpographic protocol used to secure communications. All connections to the CEDP infrastructure utilize TLS.

Activating TLS requires additional configuration steps when connecting any client to the CEDP. These steps include:

- Verifying local software and/or driver versions (e.g., DB2 [JDBC driver](JDBC.md))
- Optionally creating a truststore containing the IBM CA certificates
- Configuring TLS with a truststore containing the IBM CA certificates

### JDBC Drivers
If using JDBC to connect to CEDP databases, be sure to use the correct DB2 JDBC drivers as described in [the section on JDBC](JDBC.md).

### Trusted Certificates
To support the TLS protocol with both DZ and BigSQL databases, the following internal certificates must be configured on the client side:

- [IBM internal CA root certificate](https://daymvs1.pok.ibm.com/ibmca-wireless/view/downloadCarootCert.do?file=carootcert.der)
- [IBM intenal intermediate CA certificate](https://daymvs1.pok.ibm.com/ibmca/downloadCarootCert.do?file=caintermediatecert.der)

To use the IBM CA certificates with Java-based tools (i.e., over JDBC), a truststore must be created and passed in the respective JDBC configuration.

The following commands create a Java truststore and import the above certifacates into the new created store. 
```
keytool -importcert  -alias IBMCA -trustcacerts -file carootcert.der -keystore ibm-truststore.jks
keytool -importcert  -alias IBMCAintermediate -trustcacerts -file caintermediatecert.der -keystore ibm-truststore.jks
```
If you do not have the keytool comand-line utlity on your system or otherwise are experiencing difficulties, [raise a github issue](../ingestion/Community.md) and request assistance in creating a truststore.


