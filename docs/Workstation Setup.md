# Workstation Setup

Developer workstations should include all of the tools needed to develop and build the product.  One exclusion to this is an IDE, which tends to be a more personal choice.  However, build tools, code generators, command line tools, etc should all be installed at consistent version levels to ensure consistency and reproducibility of builds.  To this end, we use Ansible to set up our toolchain on a Virtual Machine.  In terms of virtual machines there are two approaches that one can use: Virtual Box running on a local host machine, or a virtual machine running in a cloud environment (CSDC, ZC2, etc).  Each approach has its pros and cons which are outlined below.

## Prerequisties
  * [Vagrant 1.9.0](https://releases.hashicorp.com/vagrant/1.9.0/)
  * [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 

## Steps
Collated steps provided here; explainations below
  1. Change to root of this [cloned git repo](https://github.ibm.com/cognitive-data-platform/cognitive-data-platform)  
    `cd %REPO_ROOT`
  1. Start Vagrant (Vagrant up executes [/install_dependencies.sh](/install_dependencies.sh) during install.)
    `vagrant up`
  1. Create pub/private ssh keys for use with deployment  
    `vagrant ssh`  
    `cd /home/vagrant/.ssh`  
    `ssh-keygen -t rsa -f zc2Cloud`  (hit <enter> for no passphrase)  
    `cp zc2Cloud.* /vagrant`  (these files are now available at %REPO_ROOT)  

## Explanations

### Operating System

We've standardized on using Ubuntu 14.04 as the build environment as it is the only Linux environment that Travis CI supports and it works well with Vagrant.  Further, because the platform is meant to run in a containerized manner the choice of build environment doesn't affect the runtime environment since the containers can still be running on Redhat base images.  The only area where this has an impact is the platform itself.

### Ansible

**Ansible** is used to ensure that tooling can be automatically installed on workstations without the need for manual intervention.  Dependencies, versions, and [installation steps](http://docs.ansible.com/ansible/intro_installation.html) are codified as Ansibile playbooks which can be run against developer workstations to ensure that all are consistent.  The playbooks for developer workstations are in the [/ansible/playbooks/workstation](/ansible/playbooks/workstation) directory of this repository.  The [/install_dependencies.sh](/install_dependencies.sh) script can, and is, used to provision a workstation as well as the Travis CI build environments.  This approach ensures that our environments are consistent.

### Vagrant

**Vagrant** is a tool that allows local virtual machines to be stood up and provisioned with ease.  The intended use for Vagrant is to allow builds and command line tools to be run from a consistent environment provisioned by Ansible.  Given that Vagrant wraps a virtual machine there are performance considerations and therefore other approaches may be considered in the future.  However, the fact that Vagrant provides a consistent environment regardless of the host operating system is a key benefit to it.  Additionally, Vagrant makes it easy to wipe and rebuild an environment without intervention. [Click here](https://www.vagrantup.com/downloads.html) to download vagrant.

### Host Tools

The intent is to have as few tools as possible required to bootstrap a development environment.  This limits the amount of software required to be installed before someone can be productive.  To this end, the following tools are required on the host operating system:

  * [Vagrant 1.9.0](https://releases.hashicorp.com/vagrant/1.9.0/)
  * [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 

Once these tools are installed, the developer workstation should have all that's needed.

### Provisioning a Vagrant machine

From the root directory of the repository, run the following command.  This will download a base box, start it, and install the required software.  This process will take some time as it means downloading large files and compiling several pieces of software.  

  `vagrant up`

Once Vagrant is running you can `ssh` into the vagrant box by running the following command.  When ssh'd into the vagrant box you will have complete control to do whatever you need to do: run builds, start processes, etc.  

  `vagrant ssh`

The repository is mounted inside the vagrant box under `/vagrant`.  This means that any changes made to this directory on the host operating system will be visible inside the vagrant box and vice versa.  This is also what allows IDEs and other tools to be run on the host operating system and use the vagrant image for as much or as little as desired.

### Building in Vagrant

Once the environment is set up and you've `ssh`'d into the VM you can run the build from the `/vagrant` directory just as you would on any other platform.


## Hosted Virtual Machine

Another approach to development is to provision a hosted Virtual Machine.  When using this approach the wipe-and-rebuild process is more involved as the entire machine needs to be destroyed and reprovisioned manually.  However, since most of the toolchain provisioning process is automated the only steps that need to be performed manually are those related to cloning the repository and installing any custom tools or IDEs.

One benefit of this approach is that your IDE and tools all run in the same environment.  Since you have a dedicated machine it can make some tasks more straight-forward.  The downside is that the IDE and tools are running on a remote machine meaning that you need a solid internet connection to work with your machine, and there may be lag related to running screen sharing programs.

### Provisioning using Hosted Virtual Machine

You will need to find a location to host your virtual machine.  There are several options for this, though `CSDC` and `ZC2` are excellent choices.  The steps for provisioning a machine differs per environment and it is expected that you are familiar with the process.

Once you have a machine that meets the operating system version stated above, and are able to connect to it you'll need to perform the following steps to install the tool chain.

1. SSH into the virtual machine.
2. Install git.
3. Clone the repository into the directory of your choosing.
4. Run the `install_dependencies.sh` script from your repository directory.  This will install the tool chain

Once the tools are installed you should be able to run a build on the hosted virtual machine.

### Building on the Hosted Virtual Machine

1. SSH into the virtual machine
2. Change to the repository directory
3. Run the build by running `./gradlew assemble`
 
### Additional Tools

Since your development environment will be remote you will likely want to install your development tools (in addition to the tool chain) on your Virtual Machine.  It is left up to the developer to decide which tools to use and how to work.  One suggestion is to install and set up `vncserver`, an X11 desktop environment (Gnome, Xfce, etc), and an IDE (Eclipse, IntelliJ, etc).  Again the process for setting up and installing these is outside the scope of this documentation.

### Eclipse Code Formatter Setup
If using Eclipse, be sure to install and use the Eclipse Code Formatter configuration for the CEDP.  The configuration is in [/documentation/Team Process Documentation and Artifacts/Code_Style.xml](/documentation/Team%20Process%20Documentation%20and%20Artifacts/Code_Style.xml).  To install the configuration:

1. Window > Preferences
2. Select Java > Code Style > Formatter
3. Select Import...
4. Browse to `/documentation/Team Process Documentation and Artifacts/Code_Style.xml`
5. Select Open
6. Select OK to save and close the Preferences window
