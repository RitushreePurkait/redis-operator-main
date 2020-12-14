<h1 align="center">Redis Operator</h1>

![Logo](_images/logo.PNG)

Redis operator is Golang based operator that will make/oversee Redis standalone/cluster mode setup on top of the Kubernetes. It can create a redis standalone setup with best practices on Cloud as well as the Bare metal environment. Also, it provides an in-built monitoring capability using redis-exporter.

For documentation, please refer to https://docs.opstreelabs.in/redis-operator/

## Architecture

![redis-arch](_images/redis-arch.PNG)

### Purpose

The purpose of creating this operator was to provide an easy and production grade setup of Redis on Kubernetes. It doesn't care if you have a plain on-prem Kubernetes or cloud-based.

### Supported Features

Here the features which are supported by this operator:-

- Redis cluster/standalone mode setup
- Inbuilt monitoring with prometheus exporter
- Dynamic storage provisioning with pvc template
- Resources restrictions with k8s requests and limits
- Password/Password-less setup
- Node selector and affinity
- Priority class to manage setup priority
- SecurityContext to manipulate kernel parameters

### Getting Started

If you want to deploy redis-operator from scratch to a local Minikube cluster, begin with the [Getting started](https://ot-container-kit.github.io/redis-operator/#/quickstart/quickstart) document. It will guide your through the setup step-by-step.

### Example

The configuration of Redis setup should be described in Redis CRD. You will find all the examples manifests in [example](https://github.com/OT-CONTAINER-KIT/redis-operator/blob/master/example) folder.

### Prerequisites

Redis operator requires a Kubernetes cluster of version `>=1.8.0`. If you have just started with Operators, its highly recommended to use latest version of Kubernetes.

### Quickstart

The setup can be done by using helm. If you want to see more example, please go through the [example](https://github.com/OT-CONTAINER-KIT/redis-operator/blob/master/example) folder.

But you can simply use the helm chart for installation.

```
# Deploy the redis-operator
helm upgrade redis-operator ./helm/redis-operator --install --namespace redis-operator
```

After deployment, verify the installation of operator

```
helm test redis-operator --namespace redis-operator
```

Creating redis cluster or standalone setup.

```
# Create redis cluster setup
helm upgrade redis-cluster ./helm/redis-setup -f ./helm/redis-setup/cluster-values.yaml \
  --set setupMode="cluster" --set cluster.size=3 \
  --install --namespace redis-operator
# Create redis standalone setup
helm upgrade redis ./helm/redis-setup -f ./helm/redis-setup/cluster-values.yaml \
  --set setupMode="standalone" \
  --install --namespace redis-operator
```

### Monitoring with Prometheus

To monitor redis performance we will be using prometheus. In any case, extra prometheus configuration will not be required because we will be using the Prometheus service discover pattern. For that we already have set these annotations:-

```
  annotations:
    redis.opstreelabs.in: "true"
    prometheus.io/scrape: "true"
    prometheus.io/port: "9121"
```