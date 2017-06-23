# Automated Deployment Guide

**For Dev setup [README.md](../../ansible/playbooks/caas_deploy/README.md)**

This document serves as a guide for those wishing to deploy the CaaS services to a Kubernetes cluster. The automated deployment will copy new and updated files to the Kubernetes cluster, delete any existing CaaS pods and services, create updated CaaS pods and services, register new copies of any core CaaS cognitive module, and then start the training of the NLQA with the information in those newly registered modules.

## Prerequisites

There are a number of things that need to be done before the automated deployment can be performed. Ensure that all of the following requirements are met, otherwise the automated deployment will fail. Most of them will already have been done simply because you already have a build environment setup, but they are listed here to provide a complete description of the automated deployment dependencies.

#### _Kubernetes Cluster_

The automated deployment script is meant to deploy the CaaS services to an existing Kubernetes cluster that has the `caas_nlqa` namespace created within it. This will typically be done by following the [installation instructions for the CEDP platform](../CEDP%201.0%20Alpha/README.md). This cluster needs to expose the ports that the CaaS services will be using.

For more information on which ports to open and steps on how, refer to [Deployment Guide](./Deployment%20Guide.md)

#### _Nexus Repository_

The Kubernetes pods that get created all pull their images from a docker registry. While this could be any docker registry, we assume that it is a Nexus repository that was setup as part of our [platform install](../CEDP%201.0%20Alpha/README.md). This Nexus repository is assumed to be listening on port 8083 which is how that installation sets it up and what our build process expects.

#### _Shared Data Mount Point_

All of the machines in the Kubernetes cluster need to have a shared data mount point set up on `/data`. The automated deployment copies all of the deployment artifacts to `/data/caas` and module artifacts to module specific folder `/data/dataexplorer` on only the Kubernetes master, but they need to be available no matter what system in the cluster the CaaS pods are brought up on. Check out our [Zurich Compute Cloud Setup](../CEDP%201.0%20Alpha/ZC2%20Infrastructure%20Setup.md) for an example of how this shared data mount point is created.

#### _Passwordless SSH Login_

The automated deployment is performed by an Ansible playbook, and as such, Ansible requires passwordless SSH access to the systems that it manages. The automated deployment playbook needs access to the Kubernetes master node and the system that the Nexus repository resides on. Basically, this requires that you create a private/public key pair on the system you will be doing the deployment from, and ensuring that the public key is present in the `~/.ssh/authorized_keys` files on the target systems for the user Ansible will be connecting as. See [Workstation Setup](../Workstation%20Setup.md) and [Zurich Compute Cloud Setup](../CEDP%201.0%20Alpha/ZC2%20Infrastructure%20Setup.md) for an example of how this is done in one environment. Once that is done, you can add something similar to the following to the `~/.ssh/config` file on the system you will be doing the deployment from. This indicates what private key you want to use when accessing the systems in the given cluster (assuming you added the corresponding public key to the `authorized_keys` files on the systems).

```
Host *.test-cluster.zc2.ibm.com
IdentityFile ~/.ssh/privateKeyForCluster
```

#### _Ansible Inventory File_

The automated deployment playbook expects the inventory file that is using to have two groups defined within it. The first group is `kubernetes-master`, which contains one system that is the master Kubernetes node. The second group is `nexus`, which contains one system that is the Nexus repository. If following the [installation instructions for the CEDP platform](../CEDP%201.0%20Alpha/README.md), you will have already created a compatible inventory file at the default location of `/etc/ansible/hosts`. If you wish to create an inventory file for use with the automated deployment, create a file with the following contents (updated with your host names) and specify it with the `-i` parameter when executing the playbook.
```ini
[nexus]
<fully qualified hostname of Nexus repository>

[kubernetes-master]
<fully qualified hostname of master Kubernetes node>
```

#### _Kubernetes Configuration_

The automated deployment playbook expects to find the Kubernetes configuration to use to access the cluster in the file `~/.kube/config.{IP address of master}`, where `{IP address of master}` is the IP address of the Kubernetes master system identified in the Ansible inventory file. If following the [installation instructions for the CEDP platform](../CEDP%201.0%20Alpha/README.md), this file will have been created. If you find yourself missing that file (and you are not using a Rancher install), you can execute the [fetchKubeConfig](/ansible/playbooks/caas_deploy/fetchKubeConfig.yml) playbook to create that file.
```bash
# All commands are run from this directory
cd ansible/playbooks/caas_deploy

# Assuming /etc/ansible/hosts holds the inventory and we connect as the user ubuntu
ansible-playbook -u ubuntu fetchKubeConfig.yml

# Specify the host to get the configuration from
ansible-playbook -u ubuntu -i "kubeMaster.testCluster.zc2.ibm.com," fetchKubeConfig.yml

# Use the inventory file created at ~/deploy/inventory
ansible-playbook -u ubuntu -i ~/deploy/inventory fetchKubeConfig.yml
```

#### _Nexus Certificate_

As part of the build process, we need to be able to push docker images to the Nexus repository. If the certificate that represents the Nexus repository is not present, you will get an error message that looks like
```
The push refers to a repository [9.4.143.166:8083/catalog-service]
Get https://9.4.143.166:8083/v1/_ping: x509: certificate signed by unknown authority
```
In order to fix this problem, the certificate needs to be obtained from the Nexus server (as outlined in [issue 1445](https://github.ibm.com/cognitive-data-platform/cognitive-data-platform/issues/1445)). Basically, on your build machine, you need to execute the following before you can push images to the docker repository.
```bash
export NEXUS_IP_ADDRESS=[replace with nexus host ip]

# Pull cert from nexus
keytool -printcert -sslserver ${NEXUS_IP_ADDRESS}:8082 -rfc > /tmp/nexus.crt
sudo mkdir -p /etc/docker/certs.d/${NEXUS_IP_ADDRESS}:8083
sudo chmod -R a+r+x /etc/docker/certs.d/${NEXUS_IP_ADDRESS}:8083
sudo cp -v /tmp/nexus.crt /etc/docker/certs.d/${NEXUS_IP_ADDRESS}:8083/ca.crt

sudo update-ca-certificates #Ubuntu
```

#### _Build Environment_

The system that you deploy from is assumed to be a build machine for the `cognitive-data-platform` repository. Thus, it has cloned the `cognitive-data-platform` repository, and the system has been set up as described in [Workstation Setup](../Workstation%20Setup.md). This ensures that all of the tools that the automated deployment needs are present.

#### _CaaS Services Built_

All of the CaaS services and the CLI need to have been built and docker images created and pushed to the Nexus repository before the automated deployment is run. Before building, you need to create or edit file `~/.gradle/gradle.properties` and set the _nexusUrl_, _release_, _nexusUser_ and _nexusPassword_ properties. This step is required so the build process knows how to tag the docker images and the Nexus repository to push the images to. The release tag must be set to `CEDP_Alpha1.0` as the automated deployment assumes that is version that should get pulled as part of the CaaS pods it creates.
```ini
nexusUrl=[Nexus IP]:8083
release=CEDP_Alpha1.0
nexusUser=[replace with nexus user]
nexusPassword=[replace with nexus password]
```
Once the `gradle.properties` file has been updated, you can build and push the images.
```bash
# Executed from the root of the repository

# Do a full build (which includes the CLI)
./gradlew build
# Or, simply build the CLI (and let the dockerPush build the services that get pushed)
./gradlew cli:build

./gradlew dockerPush
```

## Setting up Deployment Directory

Once all of the prerequisites have been met, the automated deployment can be performed. It, however, needs to have a deployment directory set up that contains all of the information and file artifacts that should be part of the deployment. This directory can be set up once and then used for multiple deployments as long as none of the information associated with the deployment has changed.

The deployment directory is organized with a subdirectory for each CaaS service, and a `deployVars.yml` file at the root of the directory tree that contains information that each service will need. This directory can easily be set up by copying the template for that directory to a new location and then updating the copy as needed.
```bash
cd ansible/playbooks/caas_deploy
cp -R deploy.template ~/deploy
```

Each CaaS service has different information that needs to be set up for the automated deploy. The following sections will detail the steps that need to be taken for each service.

#### _Catalog Service_

The catalog service has no additional information that needs to be configured for the automated deployment.

#### _Intent Router Service_

The intent router service requires that two entries be present in the `deployVars.yml` file.
- **bluemixApikey** - This is the value of a Bluemix API key that will be used by the intent router to sign into Bluemix via the `cf` command line utility. It is generated by logging into the [Bluemix console](https://www.ibm.com/cloud-computing/bluemix/) as the user you wish to own the NLC service (probably a functional ID for production deployments), and then clicking **Manage -> Security -> Bluemix API Keys**. On that page, click on **Create API key**, fill in the information, and then create the API key. You can choose to download or copy the key at this point. Either way, ensure that the value of that API key is saved as the value of this entry.
- **bluemixWorkspace** - The Bluemix workspace to create the NLC service in. Most likely, this will be left as the default value `dev`.

#### _Issue Maker Service_

The issue maker service requires that four entries be present in the `deployVars.yml` file. These entries identify the github user that will be used to create issues in github, and the github repository that the issues will be created in. This repository must be manually created through the github interface, and that user must have permission to create issues in this repository. Note that the default values in the template identify a repository that could be suitable for your purposes.
- **GIT_USER_ID** - The git hub userid that will be used to create the issues. For production deployments, this should be a functional ID that was created for this purpose.
- **GIT_OAUTH** - The github personal access token that will be used to authenticate the user. This is created by logging into github with the userid specified above, going to that user's settings page, select **Personal Access Tokens**, and then clicking on **Generate new token**. Fill in the description and select the appropriate permissions to allow for the creation of issues in the repository identified below. Click **Generate token** and copy the generated token as the value for this field.
- **GIT_REPO_ORG** - The organization for the github repository that issues are to be created in.
- **GIT_REPO_NAME** - The name for the github repository.

#### _User Interface_

The user interface requires that the `caas-ui` directory within the deployment directory is properly set up. It does not have any values that need to be set within `deployVars.yml`. This directory itself simply contains a `config` directory. Instructions for what needs to be present in the `config` directory are given in the [User Interface](../CaaS/User%20Interface.md#configuration) documentation. After following those directions, the `config` directory will contain a `config.json` file with all of the required fields within it defined, and all of the server keys and certificates required for the User Interface's HTTPS server and SSO authentication.

#### _Data Explorer_

The data explorer requires that two entries be present in the `deployVars.yml` file.
- **de_user** and **de_pass** - These give the username and password that the data explorer will use to authenticate with elastic search. These credentials need to be obtained from the administrators of the elastic search.

#### _Global Variables_

The `deployVars.yml` also has variables that need to be set that are not specific to any particular CaaS service.
- **nexusUser** and **nexusPassword** - These two fields give the username and password for the Nexus repository. This needs to be known so that a Kubernetes secret can be created that allows Kubernetes to pull the required images from the Nexus repository when a pod is created.

## Running the Automated Deployment

Once the deployment directory is set up, the automated deployment playbook can be run.
```bash
cd ansible/playbooks/caas_deploy

# All examples assume that ~/deploy is the deployment directory that was set up

# Execute the automated deployment as user ubuntu, using default ansible inventory
ansible-playbook -u ubuntu deploy.yml -e "srcDir=~/deploy"

# Execute the automated deployment as user ubuntu, using a specific ansible inventory file
ansible-playbook -u ubuntu -i ~/deploy/inventory deploy.yml -e "srcDir=~/deploy"
```

After the automated deployment has been run, all of the necessary files now exist on the Kubernetes cluster in the shared data mount point `/data`. The CaaS and data explorer pods have been created, all accessing the information on `/data`. The base CaaS cognitive modules have been registered with the catalog service, and the intent router has been told to train using the updated information in the catalog.

Note that the automated deployment playbook is simply a playbook that runs other playbooks in succession. If so desired, each of the deployment steps can be run independently.
```bash
cd ansible/playbooks/caas_deploy

# All examples assume that ~/deploy is the deployment directory that was set up

# Copy/generate the CaaS related files, and create the CaaS pod definition
ansible-playbook -u ubuntu copyCaasFiles.yml -e "srcDir=~/deploy"

# Copy/generate the data explorer related files, and create the data explorer pod definition
ansible-playbook -u ubuntu copyDataExplorerFiles.yml -e "srcDir=~/deploy"

# Create the CaaS pod - assumes that the copyCaasFiles playbook has been run
ansible-playbook -u ubuntu createCaasPod.yml -e "srcDir=~/deploy"

# Create the data explorer pod - assumes that the copyDataExplorerFiles playbook has been run
ansible-playbook -u ubuntu createDataExplorerPod.yml -e "srcDir=~/deploy"

# Register the base CaaS cognitive modules - assumes that the createCaasPod playbook has been run
ansible-playbook -u ubuntu registerCaasModules.yml

# Train the NLC classfier with whatever modules are registered with the catalog
ansible-playbook -u ubuntu trainCaas.yml
```



