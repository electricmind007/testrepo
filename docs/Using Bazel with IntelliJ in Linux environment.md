Building Thrift from source seems to give certain errors when tried on both Mac and Linux. On Linux, we can install the thrift compiler using the following command, which installs 0.9.1 version of thrift compiler on Mac.

> sudo apt-get install thrift-compiler

Also, we can setup the environment (which includes the thrift installation) by running the install_workstation.sh script on our local linux machine.

Once the environment has been setup, we can use IntelliJ IDE for our development and unit testing on our local machine.

Steps:-

1. Download the community version of IntelliJ.
2. Install the bazel plugin from within the IDE (**Settings > Plugins > Install > Browse Repositories, and search for 'Bazel’**).
3. **Import Basel project > Select the existing Basel project > Select project view (.bazelproject file)** - Select the **Create from scratch option**.
4. The content of the `.bazelproject` file would be as below:

```
directories:
  catalog-service/src/main/java/com/ibm/cdo/cedp/caas/catalog
  catalog-service/src/main/resources
  catalog-service/src/main/thrift
  catalog-service/src/test/java/com/ibm/cdo/cedp/caas/catalog
  issue-maker-service/src/main/java/com/ibm/cdo/cedp/caas/issuemaker
  issue-maker-service/src/main/resources
  issue-maker-service/src/main/thrift
  issue-maker-service/src/test/java/com/ibm/cdo/cedp/caas/issuemaker
  java/com/ibm/cedp/access
  java/com/ibm/cedp/pipeline
  java/com/ibm/cedp/pipeline/testing
  java/com/ibm/cedp/spss/solutionpublisher
  java/com/ibm/db2/jcc
  java/lib
  javatests/com/ibm/cedp/pipeline
  javatests/com/ibm/tools/bzl
  service-util/src/main/java/com/ibm/cdo/cedp/service/util
  thrift/catalog
  thrift/intent-router
  thrift/issue-maker
  thrift/platform
  thrift/spss
  tools/bzl
targets:
  catalog-service/src/main/java/com/ibm/cdo/cedp/caas/catalog:catalog  
  catalog-service/src/main/java/com/ibm/cdo/cedp/caas/catalog:CatalogServiceServer
  catalog-service/src/main/thrift:config
  catalog-service/src/test/java/com/ibm/cdo/cedp/caas/catalog:tests
  catalog-service/src/test/java/com/ibm/cdo/cedp/caas/catalog:GeneratedTestRules
  issue-maker-service/src/main/java/com/ibm/cdo/cedp/caas/issuemaker:issuemaker
  issue-maker-service/src/main/java/com/ibm/cdo/cedp/caas/issuemaker:IssueMakerServiceServer
  issue-maker-service/src/main/thrift:config
  issue-maker-service/src/test/java/com/ibm/cdo/cedp/caas/issuemaker:tests
  issue-maker-service/src/test/java/com/ibm/cdo/cedp/caas/issuemaker:issuemaker
  issue-maker-service/src/test/java/com/ibm/cdo/cedp/caas/issuemaker:IssueMakerServiceServer
  java/com/ibm/cedp/access:credentials
  java/com/ibm/cedp/pipeline:flink
  java/com/ibm/cedp/pipeline:pipeline
  java/com/ibm/cedp/pipeline:flink_runtime_import
  java/com/ibm/cedp/pipeline:flink_runtime
  java/com/ibm/cedp/pipeline:akka_actor_import
  java/com/ibm/cedp/pipeline:akka_actor
  java/com/ibm/cedp/pipeline:scala_import
  java/com/ibm/cedp/pipeline:scala
  java/com/ibm/cedp/pipeline/testing:testing
  java/com/ibm/cedp/spss/solutionpublisher:SpssSolutionPublisher
  java/com/ibm/db2/jcc:db2jcc4
  java/lib:cspj-jar
  javatests/com/ibm/cedp/pipeline:CedpStreamsTest
  javatests/com/ibm/tools/bzl:ExampleServiceTest
  javatests/com/ibm/tools/bzl:ExampleStructTest
  service-util/src/main/java/com/ibm/cdo/cedp/service/util:proxy_builder
  thrift/catalog:catalog
  thrift/intent-router:intent-router
  thrift/issue-maker:issue-maker
  thrift/platform:platform
  thrift/spss:spssservice
  tools/bzl:example_struct
  tools/bzl:example_service
additional_languages:
  # Uncomment any additional languages you want supported
  # android
  # dart
  # python
  # scala
```

5. Finish.
