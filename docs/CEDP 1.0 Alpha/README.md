# CEDP 1.0 Alpha Deployment

#### Pre-requisites

Setup the Zurich environment by following these instructions. <br>
[ZC2 Infrastructure Setup.md](ZC2%20Infrastructure%20Setup.md) 


#### Setup the Cognitive Enterprise Data Platform. 

Choose to either install the full platform from nothing (this will install Docker, Kubernetes and all other required software) or install to a rancher environments. 

**Note:** Install one of the following options **not** both.


- ###### (Option 1) Install full platform to a blank system.

    Run the instructions located here. <br>
[Full Platform Install](../../ansible/playbooks/cedp_stack/README.md)


- ###### (Option 2) Install platform to a Rancher environment (instructions includes optional Rancher setup)

    Run the instructions located here. <br>
[Rancher Install](Rancher%20Install.md)
