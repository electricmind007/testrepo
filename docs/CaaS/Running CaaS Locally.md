# Running CaaS Locally

There are times when it makes sense to run the CaaS Platform locally (outside of Kubernetes).  This can be useful for local testing, or standing up all of the services locally for doing development on a particular service.

At the root of the cognitive-data-platform project there are 2 scripts: `caas-start-all.sh` and `caas-stop-all.sh`.  These scripts will start and stop all of the CaaS services respectively.

## Prerequisites

Before the start/stop scripts can be used for the platform, the platform must be built locally.  Additionally, it is expected that these commands are run from within the Vagrant virtual machine.

    # From the root of the cognitive-data-platform project
    $ ./gradlew dockerBuild

## Starting the CaaS Platform

    $ ./caas-start-all.sh

## Stopping the CaaS Platform

    $ ./caas-stop-all.sh

## Developing

Because the CaaS Platform is designed to be a set of cooperating micro-services there are various services that need to be running in order for the platform to operate.  The longer term vision is that it should be possible to work on a single component of the platform running locally while connecting to the services running on a production or development cluster elsewhere.  This would alleviate the need to run many services locally.  This approach does require some thought in the way one approaches their development.  In general, this means that changes should be made on a per-service basis, delivered, then work on the next set of changes.  However, currently this does not need to be done.

At present, developing against a service can be done by starting the platform, then selectively stopping the services that you are actively working on.  While working on the specific services, they can be stopped and started while under development.  When development is complete they should be started as a container using the `caas-start-all.sh` script to ensure that all of the services continue to work together.

1. Run the `./caas-start-all.sh` script.
2. Run `docker ps` to see which services are running.
3. Run `docker stop <container id>` for each service that you want to work on.
4. Develop on the service and start/stop those services while doing development.

## Troubleshooting

### Containers fail to start or stop
There are times when the CaaS Platform can get into a bad state.  This can happen when there are containers that fail to shutdown, or fail the start for some reason.  The best way to fix these issues is to reset the state of the docker services.  This can be done by running the `caas-stop-all.sh` and then inspecting the running images using `docker ps -a`.  Any CaaS-related containers that are running can be killed using `docker kill <container id>` and then further removed from the list of processing using `docker rm <container id>`.  Once this is done, the `caas-start-all.sh` script should work without error.

### Federated ID 
Newly federated ID's may cause issues with successfully running the `caas-start-all.sh` script. This is because automated Bluemix logins do not yet accept federated ID credentials. If you are experiencing trouble connecting to the intent router, try running `docker logs -f intent-router` after you have run `./caas-start-all.sh`. If you are given an error telling you that you are using a federated ID, you must manually create the Watson NLC service and bypass the automated login:


1. Login to the [Bluemix web interface](https://www.ibm.com/cloud-computing/bluemix/)
2. Select "Catalog" from the navigation bar, and search "Natural Language Classifier" in the catalog search bar.
3. Select Watson's Natural Language Classifier from the search results, and create the service.
4. Navigate to your Bluemix profile to retrieve the username and password for your NLC service.
5. Add the following lines to the `nlc_service_key.properties`, `intentrouter-development-docker.properties`, and `intentrouter.properties` files:
`serviceKeyUsername=<your service key username>`        
`serviceKeyPassword=<your service key password>`

