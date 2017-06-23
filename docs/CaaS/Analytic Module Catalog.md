# Analytic Module Catalog

The Analytic Module Catalog (or Cognition Catalog, or Module Catalog, or just Catalog) is responsible for keeping track of the modules that have been registered with the CaaS platform.  The modules that are registered using the [Command Line Interface](./Command%20Line%20Interface.md) and surfaced through the [User Interface](./User%20Interface.md) after an utterance entered into the UI has been classified by the [Intent Router](./Intent%20Router.md).  For the most part, the Catalog is a backend component that a user typically does not interact with directly.

## Design

The Catalog is located in `catalog-service` and is a Thrift service writen in Java 8.  The Thrift interfaces for the service are defined outside of the implementation as the interfaces are defined in a manner that is language independent.  For this service the interfaces are in [`/thrift/catalog`](/thrift/catalog).  This service is bundled as a Docker container and is deployed to the CaaS platform.

### Persistence

The current method of persistence for the catalog is a simple Java serialization of the Thrift module structures that are registered with the service.  This method of persistence is acceptable for the near term but it has several drawbacks.  In particular, because there is no central and controlled storage location if there are multiple instances of the catalog service running they do not communicate with one another.  In principal this is a problem because it limits the number of instances of the service that can be running at any time *in a writable state*.  A workaround for this shortcoming is to simply start a single instance of the service, perform registration, then shutdown the service (which will cause the catalog to persist to common location), then start *n* copies of the service.  Upon startup these instances of the service will read the shared file.

The service has a storage interface called `DataStore` which has a single implementation at the moment `MemoryDataStore`.  In general, the `MemoryDataStore` uses a file called `catalog.bin` to read/store its data.  The file is expected to be in the current working directory.

For containerized deployments of the service the working directory must be a directory in the container that is a mount-point to the host machine which in turn should be a mount point across shared storage.  This mechanism ensures that the same `catalog.bin` file is read/written to by all instances of the service regardless of which node in a cluster it runs on.

In a future enhancement, the persistence mechanism will be replaced with a more durable storage mechanism.

### Analytic Events

When certain events occur within the catalog messages are generated and written to the standard application log.  These messages are intended to be in an easily machine readable form so that they can be collected and aggregated in the platform logging infrastructure.  Currently the following events are generated when the corresponding event occurs within the catalog.

  * module_registered
  * module_unregistered
  * intent_registered
  * intent_unregistered

A sample event in the log is as follows:

    2017-01-23 14:56:20 INFO  AnalyticsReporter:50 - event=module_registered; id=x:1.0;
    2017-01-23 14:56:20 INFO  AnalyticsReporter:50 - event=intent_registered; id=x:1.0:y;
    2017-01-23 14:56:20 INFO  AnalyticsReporter:50 - event=intent_registered; id=x:1.0:z;
    ....
    2017-01-23 14:56:20 INFO  AnalyticsReporter:50 - event=intent_unregistered; id=x:1.0:z;
    2017-01-23 14:56:20 INFO  AnalyticsReporter:50 - event=intent_unregistered; id=x:1.0:y;
    2017-01-23 14:56:20 INFO  AnalyticsReporter:50 - event=module_unregistered; id=x:1.0;

## Getting Started

    $ ./gradlew catalog-service:eclipse

## Building

Before running the catalog service it must be built.  The build tool currently in use for the catalog service is *gradle*.  Below is the command used to build the project, run unit tests, and building the docker image.  These commands must be run from the root of the cognitive-data-platform project.  Note that these should be run from within the vagrant virtual machine if using vagrant for development.

    $ ./gradlew catalog-service:assemble
    $ ./gradlew catalog-service:check
    $ ./gradlew catalog-service:dockerBuild


## Running

The catalog-service can be run in a few different ways: in eclipse for debugging, using gradle to test without docker, or as a docker container.  Below are the steps for running each of these.  There are various tradeoffs for using each of these different methods.  Running from within eclipse (or your IDE of choice) means that you can interact with the program and perform step-wise debugging.  This is useful when you want to work on just the catalog-service and not the other components of the CaaS platform.  Running from the command line using gradle is useful when you just want to quickly test without having to go through Docker.  Lastly, running through Docker is a good way of running the service as it would be run on Kubernetes.

### Running through Gradle

From the root of the cognitive-data-platform project:

    $ ./gradlew catalog-service:run


### Running from Eclipse

1. Open `com.ibm.cdo.cedp.caas.catalog.CatalogServiceServer`
2. Right-click on the editor and select *Run As* -> *Java Application*

### Running from Docker

    $ cd catalog-service
    $ docker run \
             -p 9999:9999 \
             -v ".:/store" \
             -w "/store" \
             --name "catalog-service" \
             catalog-service
