# Nexus Documentation

## Nexus Setup

### Automatic Setup
> **NOTE:** In development and will be the deployment method for QA/Prod.

Ansible script path and commands to be posted here.

### Manual Setup

#### On host docker image do the following:
```bash
export USER_ID=rparhar
su - $USER_ID
mkdir -p ~/nexus-data
sudo rm -r ~/nexus-data/*
sudo chown -R 200 ~/nexus-data
export NEXUS_DOMAIN=$(hostname -d)
export NEXUS_IP_ADDRESS=$(hostname -i)
export NEXUS_HOST=$(hostname)
cd /etc/ssl
# Create self-signing cert in a keystore
#sudo keytool -genkeypair -keystore keystore.jks -storepass password -keypass password -alias jetty -keyalg RSA -keysize 2048 -validity 5000 -dname "CN=${NEXUS_HOST}.${NEXUS_DOMAIN}, OU=Example, O=Sonatype, L=Unspecified, ST=Unspecified, C=US" -ext "SAN=DNS:${NEXUS_HOST}.${NEXUS_DOMAIN},IP:${NEXUS_IP_ADDRESS}" -ext "BC=ca:true"
sudo keytool -genkeypair -keystore keystore.jks -storepass password -keypass password -alias jetty -keyalg RSA -keysize 2048 -validity 5000 -dname "CN=${NEXUS_HOST}, OU=Example, O=Sonatype, L=Unspecified, ST=Unspecified, C=US" -ext "SAN=DNS:${NEXUS_HOST},IP:${NEXUS_IP_ADDRESS}" -ext "BC=ca:true"
docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus -v /home/${USER}/nexus-data/:/nexus-data sonatype/nexus3

## Setup SSL with generated key, only required on 1st time nexus setup config
export NEXUS_ID=$(docker ps | grep 'sonatype/nexus3' | awk '{print $1}')
docker cp /etc/ssl/keystore.jks ${NEXUS_ID}:/opt/sonatype/nexus/etc/ssl

#### The below steps will login to the nexus docker image and configure https:
docker exec -u 0 -i -t ${NEXUS_ID} /bin/bash

echo 'application-port-ssl=8082' >> /nexus-data/etc/nexus.properties
echo 'nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-https.xml,${jetty.etc}/jetty-requestlog.xml' >> /nexus-data/etc/nexus.properties
cp -f /opt/sonatype/nexus/etc/jetty/jetty-https.xml /opt/sonatype/nexus/etc/jetty/jetty-https.xml.bak
sed 's/OBF:1v2j1uum1xtv1zej1zer1xtn1uvk1v1v/password/g' /opt/sonatype/nexus/etc/jetty/jetty-https.xml > /opt/sonatype/nexus/etc/jetty/jetty-https.xml.tmp
cp -f /opt/sonatype/nexus/etc/jetty/jetty-https.xml.tmp /opt/sonatype/nexus/etc/jetty/jetty-https.xml
exit
```

### Now that the docker image is configured for https, setup the cert in the docker os (found that on bluecloud these steps were not required but they were required on csdc):
```bash
docker restart nexus
keytool -printcert -sslserver ${NEXUS_HOST}.${NEXUS_DOMAIN}:8082 -rfc > /tmp/canlab.ibm.com.crt
#sudo cp /tmp/canlab.ibm.com.crt /etc/pki/ca-trust/source/anchors/${NEXUS_HOST}.${NEXUS_DOMAIN}.crt
mkdir -p /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082
chmod -R a+r+x /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082
sudo cp /tmp/canlab.ibm.com.crt /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt
sudo cp -R /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8083/ca.crt
sudo cp -R /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt /etc/docker/certs.d/${NEXUS_IP_ADDRESS}:8082/ca.crt
sudo cp -R /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt /etc/docker/certs.d/${NEXUS_IP_ADDRESS}:8083/ca.crt
sudo update-ca-trust
```

### Manual steps via the Nexus UI

1. login https://csx00435.canlab.ibm.com:8082
2. Click Repositories
3. Click create repository
4. Click docker-hosted
5. Give it a name 'docker-internal', set HTTP port to 8083, check of V1 API support. Click save.
6. docker search csx00435.canlab.ibm.com:8083/x
