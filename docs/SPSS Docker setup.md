# SPSS Modeler Docker Setup

SPSS Modeler is a predictive analytics platform that helps you build accurate predictive models quickly and deliver predictive intelligence to individuals, groups, systems and the enterprise. It provides a range of advanced algorithms and analysis techniques,including text analytics, entity analytics, decision management and optimization to deliver insights in near real-time. Use it to consistently make better decisions—from the desktop or within operational systems.

## Prerequisties
  * [Vagrant 1.9.0](https://releases.hashicorp.com/vagrant/1.9.0/)
  * [VirtualBox](https://www.virtualbox.org/wiki/Downloads) 
  * [IBM SPSS Modeler Server Professional 18.0 Linux x86-64](http://w3-03.ibm.com/software/spcn/swdownloads.html) 

## Steps
Collated steps provided here; explanations below
  1. Change to root of this [cloned git repo](https://github.ibm.com/cognitive-data-platform/cognitive-data-platform)  
    `cd ../../container/spss`
  2. Download SPSS Modeler server (`CN9C3ML`) & Batch (`CN9C4ML`) from [IBM software seller's workspace](http://w3-03.ibm.com/software/spcn/swdownloads.html). 
  3. Start Vagrant [/Vagrantfile](/Vagrantfile).   
    `vagrant up`
  4. Share the SPSS Modeler executable files (`spss_mod_btch_18.0_Lx86_ml.bin, spss_mod_svr_18.0_lx86_ml.bin`) to vagrant. 
  5. Login to the VM and build the docker image.     
    `vagrant ssh`  
    `docker build -t spssimagename .`(Run this cmd from the Dockerfile directory)  
    `docker images`  (lists the docker images)
  6. Create/Run the SPSS docker container.     
    `docker run  -it -d  -v /inputfiles:/usr/spss spssimagename`      
  7. Execute the clemb. 
    `docker exec  containerID ./usr/IBM/SPSS/ModelerBatch/18.0/clemb -username spss -password spss -stream stream.str   -Pinput.full_filename=input.csv -Presult.filename=output.txt  -log log.log -execute`       
     

