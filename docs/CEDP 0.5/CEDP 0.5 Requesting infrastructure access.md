# Purpose of this document
Follow this process if for your work you need to be able to spin up new virtual machines that are either hosted in close proximity (#1) or on the production infrastructure (#2) of the CEDP.

# Use-case 1: Development / testing environment on the Zurich Compute Cloud
If you just want to get access to the cloud please open a github ticket here to request access. After it has been processed by the infrastructure team you will be able to use the self sign up procedure on https://zc2.ibm.com to get access to the OpenStack dashboard.
You will get a default project name assigned with some quota that is usually enough for testing ideas.

# Use-case 2: Services related to the production Cognitive Enterprise Data Lake platform
If you need to run auxiliary applications that are integral parts of the production environment then please follow the procedure for use-case #1.
After you already have access to your personal project and can use the Zurich Compute Cloud, please follow the following additional steps:

* in the project creator tool (available at https://zc2.ibm.com) create a project. the project name should fit [a-zA-Z]{4,} as it will be part of the DNS name as well
* open a github ticket where you specify
  * the project name
  * the overall project quota (number of vCPUs, memory, disk space)
* after the ticket is processed you will be able to spin up virtual machines on the "datalake" zone and the project will be given access to the official RHEL6 and RHEL7 images
* once you spin up a RHEL based image please use the following command to register it and get the support/update entitlements:
  ```sudo subscription-manager register --activationkey=zc2_virtual_activation_key --org=6689782```
