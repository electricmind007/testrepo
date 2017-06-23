# Nexus Documentation

<!-- TOC depthFrom:1 depthTo:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Nexus Documentation](#nexus-documentation)
	- [Nexus Setup](#nexus-setup)
		- [Preliminary Notes](#preliminary-notes)
			- [Credentials](#credentials)
		- [Automatic Setup](#automatic-setup)
		- [Manual Setup](#manual-setup)
			- [Manual steps via the Nexus UI](#manual-steps-via-the-nexus-ui)
	- [Creating Nexus Config Data Package](#creating-nexus-config-data-package)
		- [Creating nexus-data.tar.bz2](#creating-nexus-datatarbz2)
	- [Nexus Usage Notes](#nexus-usage-notes)
		- [Docker Tagging Convention](#docker-tagging-convention)
		- [Pushing images to Nexus](#pushing-images-to-nexus)
			- [Pushing a Public Image to Nexus without a Dockerfile](#pushing-a-public-image-to-nexus-without-a-dockerfile)
			- [Importing a Dockerfile to Nexus](#importing-a-dockerfile-to-nexus)
			- [Nexus Image Searching Techniques](#nexus-image-searching-techniques)

<!-- /TOC -->

## Nexus Setup

### Preliminary Notes
The webpage describing the image is here: [NEXUS]
[nexus]: <https://hub.docker.com/r/sonatype/nexus/> "sonatype/docker-nexus"

#### Credentials
Nexus login:
```
  user: admin
  pass: admin123
```

### Automatic Setup
> **NOTE:** In development and will be the deployment method for QA/Prod.

Ansible script path and commands to be posted here.

### Manual Setup

On host docker image do the following:
```bash
export USER_ID=rparhar
su - $USER_ID
mkdir -p ~/nexus-data
sudo rm -r ~/nexus-data/*
sudo chown -R 200 ~/nexus-data
export NEXUS_DOMAIN=$(hostname -d)
export NEXUS_IP_ADDRESS=$(hostname -i)
export NEXUS_HOST=$(hostname)
cd /etc/ssl```  

Create self-signing cert in a keystore.  
```bash
sudo keytool -genkeypair -keystore keystore.jks -storepass password -keypass password -alias jetty -keyalg RSA -keysize 2048 -validity 5000 -dname "CN=${NEXUS_HOST}, OU=Example, O=Sonatype, L=Unspecified, ST=Unspecified, C=US" -ext "SAN=DNS:${NEXUS_HOST},IP:${NEXUS_IP_ADDRESS}" -ext "BC=ca:true"
docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus -v /home/${USER}/nexus-data/:/nexus-data sonatype/nexus3
```

Setup SSL with generated key, only required on 1st time nexus setup config.
```bash
export NEXUS_ID=$(docker ps | grep 'sonatype/nexus3' | awk '{print $1}')
docker cp /etc/ssl/keystore.jks ${NEXUS_ID}:/opt/sonatype/nexus/etc/ssl
```

The below steps will login to the nexus docker image and configure https.
```bash
docker exec -u 0 -i -t ${NEXUS_ID} /bin/bash

echo 'application-port-ssl=8082' >> /nexus-data/etc/nexus.properties
echo 'nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-https.xml,${jetty.etc}/jetty-requestlog.xml' >> /nexus-data/etc/nexus.properties
cp -f /opt/sonatype/nexus/etc/jetty/jetty-https.xml /opt/sonatype/nexus/etc/jetty/jetty-https.xml.bak
sed 's/OBF:1v2j1uum1xtv1zej1zer1xtn1uvk1v1v/password/g' /opt/sonatype/nexus/etc/jetty/jetty-https.xml > /opt/sonatype/nexus/etc/jetty/jetty-https.xml.tmp
cp -f /opt/sonatype/nexus/etc/jetty/jetty-https.xml.tmp /opt/sonatype/nexus/etc/jetty/jetty-https.xml
exit
```

Now that the docker image is configured for https, setup the cert in the docker os (found that on bluecloud these steps were not required but they were required on csdc).
```bash
docker restart nexus
keytool -printcert -sslserver ${NEXUS_HOST}.${NEXUS_DOMAIN}:8082 -rfc > /tmp/canlab.ibm.com.crt
mkdir -p /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082
chmod -R a+r+x /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082
sudo cp /tmp/canlab.ibm.com.crt /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt
sudo cp -R /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8083/ca.crt
sudo cp -R /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt /etc/docker/certs.d/${NEXUS_IP_ADDRESS}:8082/ca.crt
sudo cp -R /etc/docker/certs.d/${NEXUS_HOST}.${NEXUS_DOMAIN}:8082/ca.crt /etc/docker/certs.d/${NEXUS_IP_ADDRESS}:8083/ca.crt
sudo update-ca-trust
```

#### Manual steps via the Nexus UI
1. login https://csx00435.canlab.ibm.com:8082
2. Click Repositories
3. Click create repository
4. Click docker-hosted
5. Give it a name 'docker-internal', set HTTP port to 8083, check of V1 API support. Click save.
6. docker search csx00435.canlab.ibm.com:8083/x

## Creating Nexus Config Data Package
### Creating nexus-data.tar.bz2
To create the nexus data package you must launch nexus via the web console, configure it and then save the docker volume mount that results from the configuration for distribution.

This means you must first setup/deploy nexus on a server then proceed.

The easiest way to do this is to first run the ansible deploy scripts to setup nexus via https to a clean CSDC (or another server) then hit the admin URL as described in the README.md file and proceed to configure nexus. Then when done follow the below steps to archive your modifications.
1. Change to the docker user, `su - docker` (if not already done)
2. Stop nexus by issuing `docker stop nexus`
3. Compress the ``~/nexus-data` folder, `tar -cvSjf ~/nexus-data.tar.bz2 ~/nexus-data`

If looking to do a clean configuration you can go into the nexus.yml file and comment out the parts where the `nexus-data.tar.bz` is being transferred to the server and unarchived and then to the deploy.

This will maintain nexus original configuration but some manual steps will need to be performed before it can be connected to securely.


Follow the steps below to configure:

1) Configure nexus to respond via https.
```bash
echo 'application-port-ssl=8082' >> /nexus-data/etc/nexus.properties
echo 'nexus-args=${jetty.etc}/jetty.xml,${jetty.etc}/jetty-http.xml,${jetty.etc}/jetty-https.xml,${jetty.etc}/jetty-requestlog.xml' >> /nexus-data/etc/nexus.properties
```
2) Update Jetty with the keystore password that was set via the ansible setup.
```bash
sed -i 's/OBF:1v2j1uum1xtv1zej1zer1xtn1uvk1v1v/{{ nexus_data_keystore_password }}/g' /data/Alpha1.0/nexus/jetty/jetty-https.xml
```
>***NOTE:*** You can find the `nexus_data_keystore_password` in the `script_vars.yml` script in the cedp_stack folder.

3) Restart nexus `docker restart` nexus`

4) Using the instructions in the README.md file under title `Check if https responds on port 8082 via web` hit the nexus server on port 8082 after opening the tunnel to your server.

5) Click `Repositories` > `Create Repository`

6) Click `docker-hosted` and give it a name 'docker-internal', set HTTP port to 8083, check of V1 API support. Click save.


## Nexus Usage Notes
### Docker Tagging Convention

As a convention for pushing images to Nexus, we want to tag the images with a searchable "grouping" (aka tag) which allows one to easily identify a grouping of images for a release.

`<image name>_<official version>:<our tag>`

For our release our tag is "CEDP_Alpha1.0".

Eg. Image: `zabbix/zabbix-server-mysql:ubuntu-3.2-latest`

Tag for above image: `zabbix-server-mysql_ubuntu-3.2-latest:CEDP_Alpha1.0`

This tag will change between releases and will most probably be updated to include build numbers also.

> **NOTE:** We do not want to user a version number or the keyword '*latest*' for our tags as these are obsecure and difficult to identify groupings with.

### Pushing images to Nexus

There are 2 ways of pushing images to Nexus.
1. Where you want to push a dockerfile directly into Nexus.
2. Where you want to download an image from a public repository and push it to Nexus.

#### Pushing a Public Image to Nexus without a Dockerfile
1. Setup a tunnel from your host docker system that is connected to Docker Hub to the Nexus repository.  
```bash
ssh -N -L 8083:localhost:8083 docker@devpcbmrhedl02.w3-969.ibm.com  &
```

2. On your host docker system which is connected to another registry like Docker Hub, you first pull a docker image.  
```bash
docker pull ubuntu
```

3. Then you tag your image in docker.  
```bash
docker tag ubuntu:latest localhost:8083/ubuntu_latest:CEDP-Alpha1.0
```

4. Push your docker image to the Nexus registry.  
```bash
docker push localhost:8083/ubuntu_latest:CEDP-Alpha1.0
```
> **NOTE:** You can use the command `docker images` to see what docker images are installed in the current image repository (not the Nexus/Docker Hub registeries)

** *More verbose sample steps below.* **
```bash
[rparhar@csx00435 certs.d]$ ssh -N -L 8083:localhost:8083 rparhar@csx00435.canlab.ibm.com  &

[rparhar@csx00435 certs.d]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sonatype/nexus3     latest              2a55fb9c4767        5 weeks ago         486.1 MB
```
Pull Ubuntu from Docker Hub.
```bash
[rparhar@csx00435 certs.d]$ docker pull ubuntu
Using default tag: latest
latest: Pulling from library/ubuntu

8aec416115fd: Pull complete
695f074e24e3: Pull complete
946d6c48c2a7: Pull complete
bc7277e579f0: Pull complete
2508cbcde94b: Pull complete
Digest: sha256:71cd81252a3563a03ad8daee81047b62ab5d892ebbfbf71cf53415f29c130950
Status: Downloaded newer image for ubuntu:latest

[rparhar@csx00435 certs.d]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              f49eec89601e        4 days ago          129.5 MB
sonatype/nexus3     latest              2a55fb9c4767        5 weeks ago         486.1 MB

[rparhar@csx00435 certs.d]$ docker tag ubuntu:latest localhost/ubuntu_latest:CEDP-Alpha1.0
```
You can see the tagged image with the original image. Notice that the image ID's are the same.
```bash
[rparhar@csx00435 certs.d]$ docker images
REPOSITORY                            TAG                 IMAGE ID            CREATED             SIZE
localhost:8083/ubuntu_latest  CEDP-Alpha1.0   f49eec89601e        4 days ago          129.5 MB
ubuntu                                latest              f49eec89601e        4 days ago          129.5 MB
sonatype/nexus3                       latest              2a55fb9c4767        5 weeks ago         486.1 MB
```

Push tagged image to nexus repository
```bash
[rparhar@csx00435 certs.d]$ docker push localhost:8083/ubuntu_latest:CEDP-Alpha1.0
The push refers to a repository [localhost:8083/ubuntu_latest:CEDP-Alpha1.0]
5eb5bd4c5014: Pushed
d195a7a18c70: Pushed
af605e724c5a: Pushed
59f161c3069d: Pushed
4f03495a4d7d: Pushed
latest: digest: sha256:71cd81252a3563a03ad8daee81047b62ab5d892ebbfbf71cf53415f29c130950 size: 1357

[rparhar@csx00435 certs.d]$ docker images
REPOSITORY                            TAG                 IMAGE ID            CREATED             SIZE
localhost:8083/ubuntu_latest   CEDP-Alpha1.0  f49eec89601e        4 days ago          129.5 MB
ubuntu                                latest              f49eec89601e        4 days ago          129.5 MB
sonatype/nexus3                       latest              2a55fb9c4767        5 weeks ago         486.1 MB

[rparhar@csx00435 certs.d]$ docker search localhost:8083/ubuntu
NAME                                         DESCRIPTION   STARS     OFFICIAL   AUTOMATED
localhost:8083/ubuntu_latest:CEDP-Alpha1.0                 0
```

#### Importing a Dockerfile to Nexus
There is really a 2 part process for importing a dockerfile.

1. You either obtain a dockerfile from the Internet or you export it from your image repository with the following command:
```bash
docker save -o <save image to path> <image name>
```

Also it is a recommended to bzip2 compress the image. Detailed steps below.

```bash
[docker@csx00435 test]$ docker save -o ./nexus_3.dockerfile sonatype/nexus3
[docker@csx00435 test]$ ll
total 458416
-rw------- 1 docker users 469413888 Mar  1 18:53 nexus_3.dockerfile
```

bzip2 compress it so that is reduces the amount of time it takes to transfer this file around our systems.
``` bash
[docker@csx00435 test]$ bzip2 nexus.dockerfile
[docker@csx00435 test]$ ll
total 206264
-rw------- 1 docker users 211213399 Mar  1 18:53 nexus.dockerfile.bz2
```

2. You import the saved file into the Nexus registry.
```bash
docker load -i <path to image tar file>
```
> **NOTE:** This step implies you have copied your image file to the Nexus server via scp or some other method.




#### Nexus Image Searching Techniques
Searching Nexus images works slightly different that the typical search does with Docker Hub.

Any time you want to search any images you must use the following command.
```bash
docker search <repository url>:<port>/<search term>

docker search localhost:8083/CEDP-Alpha1.0
```  

If you do the following: `docker search localhost:8083/latest` you would see the following output.
```bash
[rparhar@csx00435 certs.d]$ docker search csx00435.canlab.ibm.com:8083/latest
Error response from daemon: invalid registry endpoint https://csx00435.canlab.ibm.com:8083/v1/: Get https://csx00435.canlab.ibm.com:8083/v1/_ping: dial tcp 9.28.167.179:8083: getsockopt: connection refused. If this private registry supports only HTTP or HTTPS with an unknown CA certificate, please add `--insecure-registry csx00435.canlab.ibm.com:8083` to the daemon's arguments. In the case of HTTPS, if you have access to the registry's CA certificate, no need for the flag; simply place the CA certificate at /etc/docker/certs.d/csx00435.canlab.ibm.com:8083/ca.crt
[rparhar@csx00435 certs.d]$ docker search localhost:8083/latest
NAME                                                                    DESCRIPTION   STARS     OFFICIAL   AUTOMATED
localhost:8083/zabbix-server-mysql_ubuntu-3.2-latest:CEDP-Alpha1.0                    0
localhost:8083/nexus:latest
```

Notice that the search term was '*latest*' and you will notice that it matched with images who had been tagged with '*latest*' and images whose name had the word '*latest*' in it as a token, meaning seperated by an _, -, or .

You can also use '*' in your search terms but remember it is based on word token matches in the name or the tags.

```bash
[rparhar@csx00435 certs.d]$ docker search localhost:8083/C*
NAME                                                                    DESCRIPTION   STARS     OFFICIAL   AUTOMATED
localhost:8083/kubernetes-dashboard-amd64_v1.5.1:CEDP-Alpha1.0                        0
localhost:8083/centos_7:CEDP-Alpha1.0                                                 0
localhost:8083/customimagetest:v1                                                     0
```

Although if you do not specifiy the '*', you do not get an wildcard matching.

```bash
[rparhar@csx00435 certs.d]$ docker search localhost:8083/C
NAME      DESCRIPTION   STARS     OFFICIAL   AUTOMATED
```

Also '*' needs to be prefaced with a character and must not be followed by any other characters. So unfortunately we have not found a way to list all images in the system in a single command. If the need exists, the Nexus UI can be used to accomplish this task for image management. Alternatively since we are tagging our releases with a standard naming scheme this allows us to get around this limitation.

```bash
[rparhar@csx00435 certs.d]$ docker search localhost:8083/*entos
NAME      DESCRIPTION   STARS     OFFICIAL   AUTOMATED

[rparhar@csx00435 certs.d]$ docker search localhost:8083/cen*os
NAME      DESCRIPTION   STARS     OFFICIAL   AUTOMATED

[rparhar@csx00435 certs.d]$ docker search localhost:8083/*
NAME      DESCRIPTION   STARS     OFFICIAL   AUTOMATED
```
