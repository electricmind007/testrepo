# VM Setup

## Request VMs
[Request VMs here](https://csdc.canlab.ibm.com/citm/vm/new.wss).

> **NOTE:** If the drop down lists of OS's are disabled, you need to request access to the Canada Lab Hybris Cloud Main pool, see the [Request Access to Canada Lab Hybrid Cloud Main](#request-access-to-canada-lab-hybrid-cloud-main) section for how to do this.

> **NOTE:** You will also need an ftp3.linux.ibm.com account to be able to install updates and new packages. See the [Request Access to ftp3.linux.ibm.com](#request-access-to-ftp3linuxibmcom) section for how to do this.

Options:

- OS: RHEL 7.2 64.
- Pool: Canada Lab Hybrid Cloud Main
- Flavour: Small
- Lease Duration: 90 days
- Software Stack: Docker

Some time after the request has been submitted you will receive an email with the new VMs host name and root password

## VM Setup with Ansible
* Ensure you have control system with Ansible installed
* Add your ssh public key for root access to all systems you want to control
* On the ansible control system, edit `/etc/ansible/hosts` and add the following block - replacing the hosts in the `[kubernetes]` group with systems you want to install Kubernetes on, and add any other systems you want users set up on under the `[other]` group.

  ```
  [all:children]
  kubernetes
  other
  
  [kubernetes]
  csx00111.canlab.ibm.com
  csx00222.canlab.ibm.com

  [other]
  csx00333.canlab.ibm.com
  ```
* To run ansible to set up users, run `ansible-playbook cognitive-data-platform/ansible/playbooks/kubernetes/vm_setup.yml`
* To install kubernetes, run `ansible-playbook cognitive-data-platform/ansible/playbooks/kubernetes/kubernetes.yml`
  
## Manual VM Setup

* Set up ssh keys
* Change root password
* Add user (e.g. `useradd matthewh` - this will create a primary group of the same name and create a home directory)
* Set user's password: (e.g. `passwd matthewh`)
* Add user to wheel and docker groups (e.g. `usermod -a -G wheel,docker matthewh`)
    * Users in the `wheel` group can use sudo to run commands as root
    * Users in the docker group can use docker commands. If a user is not in the docker group, docker commands can only be run with sudo
* Set up ssh keys for the new user account
* Install package updates
  * `sudo /root/ibm-yum.sh update`
  * You will be prompted for your ftp3 account credentials. See section below for info on how to [Request Access to ftp3.linux.ibm.com](#request-access-to-ftp3linuxibmcom).
* Enable the docker service (this will automatically start the service when booting)
  * `sudo systemctl enable docker`
* Start the docker service (This starts the service now, will not be necessary on reboots since the service is now enabled)
  * `sudo systemctl start docker`

### Install Kubernetes

> **NOTE:** Red Hat does have a golang package, but the version available is missing a library that is required to build kubernetes

Run the following commands as root

* Install pre-requisites
  * `/root/ibm-yum.sh install git mercurial`
* Install golang
  * Download the linux tarball [from here](https://golang.org/dl) (or use this [direct link for v1.7.4](https://storage.googleapis.com/golang/go1.7.4.linux-amd64.tar.gz))
    * `wget https://storage.googleapis.com/golang/go1.7.4.linux-amd64.tar.gz`
  * Extract to /usr/local/
    * `tar -C /usr/local -xzf go1.7.4.linux-amd64.tar.gz`
  * Add links to binaries
    * `ln -s /usr/local/go/bin/go /usr/local/bin/`
    * `ln -s /usr/local/go/bin/godoc /usr/local/bin/`
    * `ln -s /usr/local/go/bin/gofmt /usr/local/bin/`
  * Install etcd

      ```bash
      cd /opt
      git clone https://github.com/coreos/etcd
      cd /opt/etcd/
      ./build
      ln -s /opt/etcd/bin/etcd /usr/local/bin
      ```
  * Download godep

      ```bash
      cd /opt
      GOPATH=/opt/godep go get github.com/tools/godep
      ln -s /opt/godep/bin/godep /usr/local/bin/
      ```
    * Download Kubernetes

        ```bash
        cd /opt
        git clone https://github.com/GoogleCloudPlatform/kubernetes.git
        ```
    * Set up env vars

        ```bash
        export GOPATH=/opt/godep
        export PATH=$PATH:$GOPATH/bin
        ```
    * Install cfssl tools:
      * `go get -u github.com/cloudflare/cfssl/cmd/...`

### Run Kubernetes  

As root:

  * Set up env vars (add this to root's .bashrc for next time)

    ```bash
    export GOPATH=/opt/godep
    export PATH=$PATH:$GOPATH/bin
    ```
  * Start Kubernetes
    * `/opt/kubernetes/hack/local-up-cluster.sh`

The command for starting Kubernetes will show commands to start using the cluster:

As regular user in another terminal:

```bash
export KUBERNETES_PROVIDER=local

cd /opt/kubernetes
cluster/kubectl.sh config set-cluster local --server=https://localhost:6443 --certificate-authority=/var/run/kubernetes/apiserver.crt
cluster/kubectl.sh config set-credentials myself --username=admin --password=admin
cluster/kubectl.sh config set-context local --cluster=local --user=myself
cluster/kubectl.sh config use-context local
cluster/kubectl.sh
```

## Request Access to Canada Lab Hybrid Cloud Main

In order to request VMs you need access to Canada Lab Hybrid Cloud. You can [request access here](https://idman.torolab.ibm.com/access/createSystemUseridDisplayForm.wss?sysId=100049).

For more info see [Request access to a CSDC Pool](https://apps.na.collabserv.com/wikis/home?lang=en-us#!/wiki/W5e7c71b03fa9_4a62_bb1d_df5c86e8e4f9/page/Request%20access%20to%20a%20CSDC%20Pool).

## Request Access to ftp3.linux.ibm.com

In order to install updates and packages you need an FTP3 account. [Request access here](https://ftp3.linux.ibm.com/myaccount/).

An email will be sent to your manager for approval. After your manager approves the request, you will receive an email allowing you to set a password.

