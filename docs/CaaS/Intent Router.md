# Intent Router

The primary function of the Intent Router is to take an utterance from the user and determine the appropriate analytic to invoke.  In addition to classifying utterances the intent router is responsible for training a classifier and ensuring the overall quality and accuracy of the classifier.


## Design

The intent router is located in `intent-router` in the source repository with its various thrift IDL (interface definition language) in `/thrift/intent-router`.  The Thrift IDL is separated from the implementation as the Thrift IDL is meant to be public and not tied to any particular project.  The service is written in Java and uses the standard build tools for the CEDP along with Thrift for exposing its interface.

There are a variety of functions exposed from the intent router.  All of which are related to classifying an utterance as an intent that's been registered in the [Analytic Module Catalog](./Analytic Module Catalog.md) including:

   * Training a classifier with the intents in the catalog.
   * Testing the accuracy of the trained classifier.
   * Classifying an utterance to an intent in the catalog.

Documentation on each of these can be found in the [Thrift IDL](/thrift/intent-router) for the intent router.

The various functions of the intent router are further exposed using the [Command Line Interface](./Command Line Interface.md) for the CaaS platform.

## Implementation Details

The brains of the intent router is the Watson NLC Service.  This service is trained using data from the module `.yml` files that are registered with the Analytic Module Catalog.  Currently the training operation of the intent router is manual in that there is no automated re-training of the classifier when new modules are added.  See [Building a Module](./Building%20a%20Module.md) for information about the process of building a module which covers training.

Configuration for the NLC such as the endpoint, credentials, etc are stored in a `.properties` file outside of the intent router.  During startup the properties file is read in and used to configure the service for talking to the trained NLC.  When a new training run is complete, the .properties file is updated.

The configuration file for the intent router is in the `config` directory of the `intent-router` project.  The `.properties` file must be updated with appropriate credentials to access Bluemix and provision an NLC service instance.

Upon startup the intent router will listen for incoming connections on its default port `9998`.

### Docker Image

As with all of the CaaS services, the intent router is packaged and run on the platform as a Docker image.  The [Dockerfile](/intent-router-service/Dockerfile) contains the pertinent information for creating the Docker container.

### Connecting to the Catalog Service

As the intent router needs to determine what modules have been registered with the catalog, the catalog service must be running in order for the intent router to operate correctly.  

By default, so long as the catalog service is running on the same port and its default port, there is no additional configuration needed.  However, if these assumptions don't hold, the port that the intent router attempts to connect to can be changed specifying the correct port in the `CATALOG_SERVICE_PORT` environment variable.

### Training

A single NLC instance is trained with the all of the intents that are registered with the catalog.  When training a classifier after a new module is registered a new NLC instance is created and trained with the contents of the catalog.  After the NLC is trained the intent router swaps out the old classifier for the newly trained one and deletes the previous instance.  This switch-over means that once the intent router is trained the first time there should not be any time where there isn't a classifier in behind the service.

Note, however that if the intent router has never been trained, any attempt to classify an intent will result in a NotTrainedException. The time required for training depends on the amount of training data, starting at 6 minutes for a small training data set.

## Intent Router and Invoking Intents

A common misconception is that the intent router is responsible for not only classifying an utterance as a particular intent, but also invoking the intent and returning the response to the caller.  The intent router is not designed for this purpose for a variety of reasons.  First and foremost, as a microservice architecture each service should have as small a surface area as possible and not try to do too much -- similar to how a class should be designed.  More importantly than that, however, is that if the intent router were to invoke the intent in addition to classifying an utterance, the decision about which intent to invoke and how that interaction proceeds is hidden behind the intent router.  This becomes a problem if/when the system needs a different interface or there is a need for session-based interaction instead of question-and-answer interaction.  To give a concrete example, if a new user interface is needed there are no guarantees that the interface provided by the intent router would be applicable to the new user interface.

For the reasons stated above, the intent router is meant to do one thing: classify utterances to an intent or set of intents with confidence scores.  There are supporting pieces of functionality in the intent router as well, these are discussed in this documentation.

## Building

The intent router can be built using the standard build tools and targets from the root of the `cognitive-data-platform` repository.

    # Assemble the .jar (to run locally)
    $ ./gradlew intent-router-service:assemble

    # Build a docker image
    $ ./gradlew intent-router-service:dockerBuild


## Running

See the *Configuring the NLC and Bluemix Credentials* section below before running the intent router for the first time.

The intent router can be run in a variety of ways when doing development.  The easiest way is to run the `run` target for the build against the project.  To do this you will need to run the command from the root of the `cognitive-data-platform` source tree.

    $ ./gradlew --no-daemon intent-router-service:run

Alternatively, the docker container for the service can be run using the command below.  This command will ensure that the intent router is accessible through the docker container and that it attempts to connect to the catalog service on its default port (9999).  Additionally, the `.properties` file for the intent router configuration is specified below.

    $ sudo docker run -it -p 9998:9998 --rm -e CATALOG_SERVICE_PORT=9999 -e CONFIG_FILE=/config/intentrouter.properties -v <absolute path to intent router config directory on host>:/config intent-router

### Configuring the NLC and Bluemix Credentials

Before running the intent router it is important to configure your credentials for Bluemix in the `intentrouter.properties` file in `config`.  If this hasn't been done, then the intent router will not be able to provision an NLC service and therefore will fail to train and classify intents.  This configuration step only needs to happen once and comprises entirely of:

1. Update the following keys in `config/intentrouter.properties` and `config/intentrouter-development-docker.properties` with appropriate values:

    * _bluemixUsername_ - IBMid (not W3id) username that has an account with Bluemix.
    * _bluemixPassword_ - Password for the IBMid set for _bluemixUsername_
    * _bluemixWorkspace_ - Workspace name to provision the NLC service in (typically dev)
    * _catalogServiceServerHost_ - Hostname that the catalog service is running on (typically localhost)
