# Deployment Guide

This document covers the process of building a new module and injecting it into the platform.

### Creating Docker Images and Deploying Kubernetes Pod

The catalog service, intent router and CaaS UI are deployed in the same Kubernetes pod and any other module is deployed to separate pods.

Hereon:

* The 'build machine' refers to the machine with the code base from Git where the docker images are built and pushed to Nexus.
* The 'kubernetes machine' refers to the machine(s) where the Kubernetes pod is run.


#### Added Port Rules (Zurich cluster only):
Instructions on how to open ports on Zurich are located here [ZC2 Infrastructure Setup.md](/documentation/CEDP%201.0%20Alpha/ZC2 Infrastructure Setup.md) in the `Open Ports` section at the bottom.

- Add a port range rule, enter `30096` into field `Port from` and `30099` into field `Port to`, leave everything else as default and click "Add".

  | Ports | Used For |
  | --- | --- |
  | 30096 | This port will be used to handle CaaS UI HTTPS requests |
  | 30097 | This port will be used to handle CaaS UI HTTP requests |
  | 30098 | This port is used by CaaS intent router |
  | 30099 | This port is used by CaaS catalog service |

#### Setup passwordless SSH login

1. Setup ssh passwordless login from the build machine to the nexus host if it is not setup already
2. Setup ssh passwordless login from the build machine to all the Kubernetes machines both master and minions


#### Setup shared Kubernetes cluster access and create required folders

1. copy file `/etc/kubernetes/admin.conf` file from Kubernetes master node to `~/.kube/config` on build machine
  - Confirm directory `~/.kube` exists on build machine
  - On Kubernetes master node run `$ sudo scp /etc/kubernetes/admin.conf <user>@<BuildMachine>:~/.kube/config`
2. On Kubernetes master node run `$ sudo mkdir -p /data/caasPod`
3. On build machine try running command `kubectl get pods`. This should return a list of all pods currently deployed to Kubernetes cluster


#### Build and upload the Docker Images
On the build machine:

1. Make sure all software required for building the images are installed. This can be done by running the `ansible/playbooks/workstation/workstation.yml` script.
2. Make sure _bluemixUsername_, _bluemixPassword_, _bluemixWorkspace_ in `config/intentrouter.properties` are set to appropriate values.
3. Define variables for the nexus url variable and the deploy path on the Kubernetes machines (For ZC2 ubuntu cluster the [user] is ubuntu).

  ```bash
  export NEXUS_URL=[Nexus IP]:8083
  export DEPLOY_USER=ubuntu
  export DEPLOY_PATH=/data/caasPod
  ```
4. Create or edit file `~/.gradle/gradle.properties` and set _nexusUrl_, _release_, _nexusUser_ and _nexusPassword_ properties.

  ```ini
  nexusUrl=[Nexus IP]:8083
  release=[release tag eg. CEDP_Alpha1.0]
  nexusUser=[replace with nexus user]
  nexusPassword=[replace with nexus password]
  ```
5. Build the docker images and push them to Nexus by running from the git root directory:

  ```bash
  ./gradlew dockerPush
  ```
6. Set the Nexus URL in the pod configuration files.

  ```bash
  sed -i "s/localhost:8083/${NEXUS_URL}/g" caaspod.yml
  sed -i "s/localhost:8083/${NEXUS_URL}/g" dataexplorerpod.yml
  ```
7. Set the config mount point for the pod

  ```bash
  sed -i "s/\/home\/vagrant\/cognitive-data-platform\/intent-router-service\/config/${DEPLOY_PATH//\//\\/}/g" caaspod.yml
  ```
8. Push file system resources to Kubernetes machines. (For ZC2 ubuntu cluster the [user] is ubuntu).

  ```bash
  cd <path-to-git-repo>/cognitive-data-platform
  scp intent-router-service/config/intentrouter.properties ${DEPLOY_USER}@KubeMasterIPaddress:$DEPLOY_PATH
  ```

#### Config Nexus repo login on Kubernetes cluster:
On the kubernetes machine, both master and minion:

1. Login as a non-root user
2. Define deployment variables

  ```bash
  export NEXUS_URL=[Nexus IP]:8083
  export DEPLOY_USER=ubuntu
  ```
3. Log into Nexus

  ```bash
  docker login ${NEXUS_URL}
  ```
4. Copy ./docker/config.json file to /root folder

  ```bash
  sudo cp $HOME/.docker/config.json /root/.docker
  ```

#### Run the Kubernetes Pod:
On the build machine

1. Create kubernetes secret

  ```bash
  kubectl create secret docker-registry caasregistrykey --docker-server=${NEXUS_URL} --docker-username=[Nexus user] --docker-password=[Nexus password] --docker-email=admin@example.org
  ```
2. Run the Pod on kubernetes master

  ```bash
  kubectl create -f caaspod.yml
  kubectl create -f dataexplorerpod.yml
  ```
