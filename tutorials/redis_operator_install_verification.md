---
title: Redis Operator Installation Verification Tutorial
description: This tutorial explains how to verify that the Redis Operator got installed properly in the namespace
---

### Check the Redis Operator 

After installation, verify that your operator got successfully installed by executing the below command.

```execute
kubectl get csv -n my-redis-operator
```

You should see a similar output as below.

```output
NAME                    DISPLAY          VERSION   REPLACES                PHASE
redis-operator.v0.2.0   Redis Operator   0.2.0     redis-operator.v0.0.1   Succeeded
```

**Please wait till `PHASE` status will be `Succeeded` and then proceed further.**

After the installation is successful , you can check your operator pods by executing the below command.

```execute
kubectl get pods -n my-redis-operator
```

You should see a pod starting with 'redis-operator' with Ready value '1/1' and Status 'Running' like the output below.

```output
NAME                              READY   STATUS    RESTARTS   AGE
redis-operator-7794d64c48-nlbk7   1/1     Running   0          5h
```