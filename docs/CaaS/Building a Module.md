# Building a Module

## Prerequisite

You must ensure the following steps were already completed before registering your module.

1. You must have an active Bluemix account id.

To activate your id you can go to [Bluemix registration page](https://console.ng.bluemix.net/registration/) to register

2. Once registered, you will need to update the 2 properties files with your account information within your workstation image.

  Files:
  ```bash
  /intent-router-service/config/intentrouter-development-docker.properties
  /intent-router-service/config/intentrouter.properties
  ```

  Properties:
  ```bash
  bluemixUsername=
  bluemixPassword=
  ```

  > ***NOTE:*** More information on setting up your Workstation can be found [here](../Workstation Setup.md).

3. Ensure that all the required docker containers are started.
  Eg.
  ```bash
  CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                            NAMES
  3aa9e5eac596        caas-ui             "/usr/local/bin/np..."   17 hours ago        Up 17 hours         0.0.0.0:8080->8080/tcp, 0.0.0.0:8443->8443/tcp   caas-ui
  fe2b57356b1f        intent-router       "/app/intent-route..."   17 hours ago        Up 17 hours         0.0.0.0:9998->9998/tcp                           intent-router
  853dfaa76f06        catalog-service     "java -classpath /..."   17 hours ago        Up 17 hours         0.0.0.0:9999->9999/tcp                           catalog-service
  ```

  If not started you can use the `caas-start-all.sh` script to do so.

  If you find your docker containers do not exist, run `./gradlew dockerBuild` from the root project folder to build the CaaS containers.

## Module File formatting

Modules need to be in the following format for them to be properly parsed.

Example applicable to currently deployed modules.

```yaml
---
# <comments about the module>
id: <any string that validates with this regex: '[a-zA-Z0-9\\.-]+', eg. data-explorer>
version: <any decimal number, eg. 0.1>
owner: <any system user or can be set to `nobody`>
accessibleToGroups: <any system group or can be left empty>
summary: <a sentence>
description: <a sentence>
intents:
  - id: <any string that validates with this regex: '[a-zA-Z0-9\\.-]+', eg. general-questions>
    summary: <a sentence>
    training:
      positive:
        - <a question or sentence fragment>
        - What data is available to export?
        ...
    testing:
      positive:
        - <a question>
        - What online information do I have access to?
        ...
    endpoint:
      <an endpoint like thriftServiceEndpoint or stringResponseEndPoint, etc>
      thriftServiceEndpoint:
        host: localhost
        port: 9997
- id: <any string that validates with this regex: '[a-zA-Z0-9\\.-]+', eg. data-questions>
  ...
```
> ***Note:*** That the endpoint block can be defined at the intents level or at the module level where it would exist with the summary/description blocks.

For explicit detail on module design, review the javadoc comments in the thift code located in the `/thift/catalog` folder.

## Registering Modules

To register a module you must run the following command from within the root project folder.

```bash
cli/bin/cedp register <path to yaml file to be registered>
```

> ***NOTE***: If you get an error message stating your module is already registered, you can unregister it with the information in [Unregistering Modules](#Unregistering-modules).

## Unregistering Modules

To unregister a module you must run the following command from within the root project folder.

```bash
cli/bin/cedp unregister <module id:module version>
```

To get a list of currently registered modules you can run `cli/bin/cedp list` from within the root project folder.

## Training Modules

Once a module is registered, you can train it with the issuing the following command from within the root storage folder.

```bash
cli/bin/cedp train
```

### Training Monitoring

#### Using docker logs

To see the results of your training you can output the logs for the `intent-router` container so monitor its status and see the ouput of the testing results.

To monitor the log run the following command from within your root project folder.

```bash
docker logs -f intent-router
```

output:

```bash
2017-04-05 21:58:04 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:199 - Training classifier
2017-04-05 21:58:07 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:203 - Created classifier {
  "classifier_id": "90e7b4x199-nlc-35159",
  "language": "en",
  "name": "gcdo-intent-classifier",
  "status": "Training",
  "created": "2017-04-05T21:58:02.492",
  "status_description": "The classifier instance is in its training phase, not yet ready to accept classify requests",
  "url": "https://gateway.watsonplatform.net/natural-language-classifier/api/v1/classifiers/90e7b4x199-nlc-35159"
}
2017-04-05 22:03:08 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:222 - Classifier training completed.  Proceeding with accuracy testing.
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q15, old accuracy=1.000000, new accuracy=1.000000, old variance=0.011684, new variance=0.012192
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q16, old accuracy=1.000000, new accuracy=1.000000, old variance=0.000227, new variance=0.000223
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q13, old accuracy=0.875000, new accuracy=0.875000, old variance=0.072756, new variance=0.073857
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q14, old accuracy=0.928571, new accuracy=1.000000, old variance=0.055566, new variance=0.016437
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=data-explorer:0.1:general-information, old accuracy=1.000000, new accuracy=1.000000, old variance=0.053738, new variance=0.074503
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q17, old accuracy=1.000000, new accuracy=1.000000, old variance=0.002525, new variance=0.002709
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q18, old accuracy=1.000000, new accuracy=1.000000, old variance=0.002372, new variance=0.002235
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q1, old accuracy=1.000000, new accuracy=1.000000, old variance=0.040695, new variance=0.040189
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q11, old accuracy=1.000000, new accuracy=1.000000, old variance=0.015814, new variance=0.015209
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q5, old accuracy=1.000000, new accuracy=1.000000, old variance=0.001726, new variance=0.001910
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q12, old accuracy=0.900000, new accuracy=1.000000, old variance=0.117871, new variance=0.087093
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q4, old accuracy=1.000000, new accuracy=1.000000, old variance=0.017796, new variance=0.016677
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q3, old accuracy=1.000000, new accuracy=1.000000, old variance=0.000951, new variance=0.000956
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q10, old accuracy=0.500000, new accuracy=0.500000, old variance=0.455694, new variance=0.455493
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q2, old accuracy=1.000000, new accuracy=1.000000, old variance=0.023816, new variance=0.021359
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q9, old accuracy=1.000000, new accuracy=1.000000, old variance=0.000779, new variance=0.000716
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q8, old accuracy=1.000000, new accuracy=1.000000, old variance=0.004889, new variance=0.005044
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=data-explorer:0.1:data-links, old accuracy=0.928571, new accuracy=0.928571, old variance=0.045006, new variance=0.045519
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q7, old accuracy=1.000000, new accuracy=1.000000, old variance=0.007471, new variance=0.007243
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=CEDP.FAQ:0.1:q6, old accuracy=0.833333, new accuracy=1.000000, old variance=0.125629, new variance=0.006386
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceImpl:235 - New classifier candidate accuracy test for intent=data-explorer:0.1:filters-general-information, old accuracy=1.000000, new accuracy=1.000000, old variance=0.036458, new variance=0.036636
2017-04-05 22:04:41 - f1ee43de-1a4a-11e7-997c-080027e8ebcb INFO  IntentRouterServiceUtils:29 - Properties file is /config/intentrouter-development-docker.properties.
```

### Using Bluemix dashboard

Using the Bluemix dashboard you can seee various states and info on your currently running and previous run servies.

1. Goto to URL (Bluemix Homepage)[https://console.ng.bluemix.net/]
2. Click on `Log in` and user your IBMid login.
3. Scroll down until you see `All Services` table and check if you have the `gcdo-intent-classifer-service-instance`. If you don't, that just means you have not done any training yet.
4. Click `gcdo-intent-classifer-service-instance` and wait (page takes a few moment to refresh).
5. Look for an `Access the beta toolkit` button towards the bottom right of the page and click it.

Here you can see your classifer status.
