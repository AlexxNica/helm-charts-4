# Drone.io

[Drone](http://readme.drone.io/) is a Continuous Integration platform
built on container technology. Every build is executed inside an
ephemeral Docker container, giving developers complete control over
their build environment with guaranteed isolation.

These files were initially found
[here](https://github.com/bacongobbler/kube-charts/tree/440e9d64298741253a06058c68dc871fd65aa32a)
as part of a
[PR to the main helm charts repo](https://github.com/kubernetes/charts/pull/821).

## Introduction

This chart stands up a Drone server. This includes:

- A [Drone Server](http://readme.drone.io/admin/installation-guide/) Pod
- A [Drone Agent](http://readme.drone.io/admin/installation-guide/) Pod

## Prerequisites

- Kubernetes 1.5+
- The ability to point a DNS entry or URL at your Drone installation

## Installing the Chart

Add the github client and secret as a Kubernetes secret:

```bash
$ kubectl create secret generic github --from-literal=client=<client> --from-literal=secret=<secret>
```

Once it's configured, you can then install drone.

```
$ helm install ./drone
```

## Configuration

Please refer to [values.yaml](values.yaml) for the full run-down on
defaults. These are a mixture of Kubernetes and drone directives.

Specify each parameter using the `--set key=value[,key=value]`
argument to `helm install`.

Alternatively, a YAML file that specifies the values for the
parameters can be provided while installing the chart. For example,

```bash
$ helm inspect values incubator/drone > values.yaml
$ $EDITOR values.yaml # make some changes
$ helm install -f values.yaml incubator/drone
```
