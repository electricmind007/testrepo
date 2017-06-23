# Metrics Gathering

Metrics are gathered by cAdvisor which is integrated into Kubernetes and used by Kiberneted to handle resource alocation and restriction. Heapster communicates directly to cAdvisor and writes metrics to Influx DB. Graphna is used to display the metrics. The metrics gathering granualrity is 1 second for cAdvisor and 5 seconds for Heapster (lowest supported by Heapster).


### Ports
cAdvisor uses port 4194 by default and Heapster is configured to use port 31000.


### Rest Endpoints for cAdvisor

Documentation on the APIs can be found here: https://github.com/google/cadvisor/blob/master/docs/api.md

cAdvisor machine stats can be accessed by running the following on the Kubenetes master

`curl http://localhost:4194/api/v2.0/machine`

Get info on all docker containers

`curl http://localhost:4194/api/v1.2/docker`


### Rest Endpoints for Heapster

Documentation for the APIs can be found here: https://github.com/kubernetes/heapster/blob/master/docs/model.md

Get all metrics for a namespace (using 'default' below)

`curl http://localhost:31000/api/v1/model/namespaces/default/metrics`

List all pods in a namespace (using 'default' below)

`$curl http://localhost:31000/api/v1/model/namespaces/default/pods`

View all available metrics for a pod

`curl http://localhost:31000/api/v1/model/namespaces/default/pods/<pod name>/metrics`

