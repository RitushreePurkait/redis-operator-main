---
title: Redis Operator Tutorial to create an instance of standalone Redis 
description: This tutorial explains how to create an instance of etcd cluster
---

### Create Instance for Standalone Redis

The configuration of Redis setup should be described in Redis Custom Resource Definition(CRD). The redis-Operator Instance can be created using this CRD YAML files. 

**step 1:** For the standalone Redis setup, we will first need to create a Persistent Volume (PV) so that the data stored in the redis database persists. Create the yaml file for PV. 

```execute
cat <<'EOF' >redis-pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: redis-persistent-volume
spec:
  storageClassName: standard
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
EOF
```

**Step 2:** create PV.

```execute
kubectl create -f redis-pv.yaml -n my-redis-operator
```

Sample output:

```output
persistentvolume/redis-persistent-volume created
```

**step 3:** Create a custom resource YAML file for Standalone redis.

```execute
cat <<'EOF' >standalone-redis.yaml
---
apiVersion: redis.opstreelabs.in/v1alpha1
kind: Redis
metadata:
  name: opstree-redis
spec:
  mode: standalone
  global:
    image: opstree/redis:v2.0
    imagePullPolicy: IfNotPresent
    password: "Opstree@1234"
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 100m
        memory: 128Mi
  service:
    type: LoadBalancer
  redisExporter:
    enabled: true
    image: quay.io/opstree/redis-exporter:1.0
    imagePullPolicy: Always
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 100m
        memory: 128Mi
  storage:
    volumeClaimTemplate:
      spec:
        storageClassName: standard
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
      selector: {}
  # nodeSelector:
  #   #   kubernetes.io/hostname: minikube
EOF
```

**Step 4:** create standalone redis in the same namespace of operator.

```execute
kubectl create -f standalone-redis.yaml -n my-redis-operator
```

Sample output:

```output
redis.redis.opstreelabs.in/opstree-redis created
```

**Step 5:** Check redis pods.

```execute
kubectl get pods -n my-redis-operator
```

You will see output similar like below.

It may take up to a few minutes until all the resources are created and the cluster is ready for use.

```output
NAME                              READY   STATUS    RESTARTS   AGE
opstree-redis-standalone-0        2/2     Running   0          39s
redis-operator-7794d64c48-2p7vq   1/1     Running   0          3m1s
```

**Note - Please wait till `Status` will be `Running` and `READY` should be 2/2 for `opstree-redis-standalone-0` , and then proceed further.**

**Step 6:** Check the PV created earlier got `Bound` by the standalone redis.

```execute
kubectl get pv
```

You will see output similar like below.

```output
NAME                      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                                                   STORAGECLASS   REASON   AGE
redis-persistent-volume   10Gi       RWO            Retain           Bound    my-redis-operator/opstree-redis-standalone-opstree-redis-standalone-0   standard                5m21s
```

### Accessing Standalone Redis from within a pod/container

**Step 1:** Validate the Standalone Redis is working fine or not.

```execute
kubectl -n my-redis-operator exec -it opstree-redis-standalone-0 bash
```

You will see output similar below:

```output
Defaulting container name to opstree-redis-standalone.
Use 'kubectl describe pod/opstree-redis-standalone-0 -n my-redis-operator' to see all of the containers in this pod.
bash-4.4#
```

Now to connect to the redis database execute the following:

```execute
redis-cli -c -a Opstree@1234
```

You will now be able to access the redis database.

```output
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6379>
```

In the above output, **127.0.0.1** is your machine's IP address and **6379** is the port on which Redis server is running.

Now type the following **PING** command and press `Enter`

```copycommand
ping 
```

Sample output:

```output
PONG
```

This shows that Redis is working properly.

**Step 3:** Put sample key-value pair into redis database.

Copy below command to put key-value pair and press `Enter`.

```copycommand
set foo bar
```

Sample output:

```output
OK
```

**Step 4:** Retrieving the value by key.

Copy below command and press `Enter` to retrieve value of key.

```copycommand
get foo
```

Sample Output:

```output
"bar"
```

Copy below command and press `Enter` to exit out of the database:

```copycommand
exit
```

Execute below command to exit out of terminal:

```execute
exit
```
