### Overview

Some external services and internal resources (such as DBs) require password to be passed to the pods.
Adding passwords into pod configuration results in them being stored in plain text in source control,
builds and in the file resources deployed to the cluster.

---

### Solution
Kubernetes supports secrets. Kubernetes secrets are currently limited to base64 encoding and a few
specialized auth tokens with plans to add more in the future.

---

### Creation
This first step is to create the base64 encoded credentials which can be done on most linux distributions
without any extra package installation.
```bash
echo -n "username" | base64
echo -n "password" | base64
```

Use the output from the above to create a secret
```yaml
apiVersion: v1
    kind: Secret
    metadata:
        name: <any name>
    type: Opaque
    data:
        username: <output from the above username encoding>
        password: <output from the above password encoding>
```

Next create the secret in Kubernetes
```bash
kubectl create -f <file name containing the above yaml>
```

Configure pods to use this secret as parameters.
In the env section of the pod definition the value becomes a lookup to the secret
```yaml
    ...
        - name: <env var name>
          valueFrom:
            secretKeyRef:
                name: zabbix-db-secret
                key: username
    ...
```
