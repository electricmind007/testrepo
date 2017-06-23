# Co-Creator Handbook
There are many examples of pods and services in the repository that can be used for examples.

The general process for creating a cognitive application is as follows:

1. Create a docker container with your app that can be built from source in the `cognitive-data-platform` repository
    - Create a directory for the code that will be built into docker containers in `cognitive-data-platform/co-creator/your-project-name/`
1. Request a namespace and state your resource requirements (memory/cpu/persistent storage etc) for a quota to be set
    - Put your Kubernetes resources such as pod and service yaml files in `cognitive-data-platform/ansible/playbooks/co-creator/<your namespace>`
1. Create Kubernetes pod/service yaml files referencing your docker image and namespace
1. Push your docker image to Nexus and then run the deploy scripts
    - See [Nexus Setup and Notes](./Nexus%20Setup%20and%20Notes.md) for information on how to push an image to Nexus
    - Note that this step will be automated as part of the build process post alpha:
        - After the alpha, the build will create the docker image of your app and push the image to the internal Nexus repository.
        - After the alpha, the build will deploy the pods/services defined in your yaml files, pulling docker images from the Nexus repo

## Where to Put Code
Paths are relative to the `cognitive-data-platform` project root directory.

- Java code should go in [/java/com/ibm/](/java/com/ibm)`<team>/<project>`
- Java tests should go in [/javatest/com/ibm/](/javatests/com/ibm)`<team>/<project>`
- Shared or common java modules can be put in [/java/com/ibm/](/java/com/ibm)`common`
- Other code such as for creating docker containers should be put in `/cedp/<team>/<project>`
- Kubernetes pod/service yaml files should be put in [/ansible/playbooks/co-creator/](/ansible/playbooks/co-creator)`<namespace>/{pod,service}/`
- Experimental code not directly related to a current project can be put in `/experimental/<user>`. Code in here can be merged to master after an informal peer code review to share ideas with the rest of the team.

## How to access the data lake
:warning: TBD

## Build Process
:warning: TBD

## Pods
### Create
Create a Kubenetes pod yaml file referencing your assigned namespace, resource limits, docker image name, and other settings such as environment variables, ports, file system mounts and more.

See the examples of pod creation here:

- [caaspod.yml](/caaspod.yml)
- [dataexplorerpod.yml](/dataexplorerpod.yml)

**Note:** For more information on creating pod yaml files see the [Pod Overview](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) page in the Kubernetes documentation.

**Note:** It is recommended to create a 'deployment' and/or replication set for your pods to ensure proper fail-over and recovery

#### Namespaces and Quotas
Namespaces have quotas associated with them that define limits on the amount of cpu/memory/disk that can be used. Your quota will be based off what you initially request and can be modified later if needed.

When creating pod yaml files you must define values for each of the resource limits. The combined values for each of the pods in your namespace cannot exceed your quota.

```yaml
resources:
  limits:
    cpu: 200m
    memory: 1Gi
```
- Restrictions
- Quotas
- Memory/cpu requests/limits

#### Best Practice Recommendations
##### Restart Policies
A restart policy defines how the pod is restarted on failure. For run-one jobs or cron jobs it is recommended to set the restartPolicy to "Never" or "OnFailure". For long running pods the recommended setting is "Always"

##### Readiness And Liveliness 
Readiness and Liveliness documentation is located [here](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/#defining-readiness-probes).

It is important to provide a way for kubernetes to know when your pods are up and running and periodically check to ensure the pod is till available and able to accept requests.

A _readiness probe_ will prevent kubernetes from forwarding requests to your pod before it is in a state where it can handle the request. 

A _liveliness probe_ defines how kubernetes will periodically check to ensure your pod is still able to accept requests. For example, a liveliness probe on a web server pod could be a check for a TCP connection on port 80. If the connection times out kubernetes will take down and restart the pod.


##### Logging, Storage, and Privacy
Your application should log directly to stdout and stderr to take advantage of persistent logging provided by Graylog. Logs are tagged appropriately to restrict access between namespaces so only your team will have access to you logs. Logging sensitive information like passwords or personal user data should still be avoided.

Every namespace will have a user created in the Graylog system that will allow that team and that team only to access the logs for the namespace. Currently the Graylog account creation is a manual process and may take a few business days to complete.

Currently the Graylog dashboards can be accessed from here http://prdbcdgcedp01.w3-969.ibm.com:30081/

Recommended information to log:
* Errors, and Exceptions
* Warnings (please try to avoid excessive warning that aren't important)
* New job/session/connection to your pod
* Session tacking from pod to pod for multi pod systems
* Timings (again please try to avoid excessive logging)

**Note:** Every line that is logged to stdout/stderror will flow through graylog. For example, every line in a stack trace will be logged as an individual message. It is recommended that you log to stdout/stderr high level information about what is happening with the system such as a summary of an exception but not the entire trace. For more thorough logging please use a filesystem mount (and rolling log files) to output finer grained information.


##### Persistent Storage
Persistent storage is managed by mapping a directory into a directory within the pods. The directory mapped in _and_ all subdirectories will be laid on top of the internal filesystem.

**Note:** If an internal directory /a/b/c/ has a file called 'foo' and an external directory /x/y/z/ is mounted to the internal location /a/b/c/ the file foo will no longer be available in the pod. The directory mappings are full replacements **not** augmentations of both locations.

The external mount location for each namespace is /data/&lt;namespace&gt;/. Multiple mount locations can be defined using this location and some can be static resources and some can be dynamic.

For example a pod that loads static resource, a logging directory and a database could map directory locations like this:

```
/data/*myNamespace*/static -> PodA /etc/myservive/cfg
/data/*myNamespace*/logs -> PodA /var/log/myservive/
/data/*myNamespace*/*anotherDir*/database -> PodB /mysql/data/
```

##### Recurring Jobs
- [Jobs - Run to Completion](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)
- [Cron Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

:exclamation: TODO - Add some actual helpful descriptions here

### Update
For the alpha CEDP timeframe updates and deletes will be handled manually. 

Documentation for rolling-updates can be found [here](https://kubernetes.io/docs/tasks/run-application/rolling-update-replication-controller/)

### Delete
For the alpha CEDP timeframe updates and deletes will be handled manually. 

Please be sure to include cleanup of resources that are no longer needed on both the file system and nexus repository.

## Services
Services allow communication to your pods. Services can be used to enable a pod to communicate directly to another pod or to open a port mapping that allows external communication into your pod such as handing an HTTP request.

Coordinate with the CEDP team to ensure no port conflicts and ensure the necessary ports are opened in the firewall.

**Note:** See the Kubernetes documentation page on [Services](https://kubernetes.io/docs/concepts/services-networking/service/) for more information on how to create services.

## How to Access Metrics
Metrics are gathered by Heapster and displayed by Grafana. The location of the Grafana dashboard is:

[http://prdbcdgcedp01.w3-969.ibm.com:30083](http://prdbcdgcedp01.w3-969.ibm.com:30083/alerting/notification/1/edit)

Anyone can sign up for a guest account or use the build in guest account with credentials guest/guest. The Guest account is a readonly view of a few dashboards. Over all cluster metrics and metrics by namespace.

Coordinate with the CEDP team to have a custom space created for your team where you can create you own dashboards. When creating your own dashboards it is important to write efficient queries. Please see the Grafana documentation [here](http://docs.grafana.org/features/datasources/influxdb/).

## Support
:warning: TBD

