# Drone.io

[Drone](http://readme.drone.io/) is a Continuous Integration platform
built on container technology. Every build is executed inside an
ephemeral Docker container, giving developers complete control over
their build environment with guaranteed isolation.

These files were initially found in a [fork][] as part of a
[PR to the main helm charts repo][pr], and have been modified for our
particular setup.

[fork]: https://github.com/bacongobbler/kube-charts/tree/440e9d64298741253a06058c68dc871fd65aa32a
[pr]: https://github.com/kubernetes/charts/pull/821

## Introduction

This chart stands up a Drone server. This includes:

- A [Drone Server](http://readme.drone.io/admin/installation-guide/) Pod
- A [Drone Agent](http://readme.drone.io/admin/installation-guide/) Pod

## Prerequisites

- Kubernetes 1.5+
- The ability to point a DNS entry or URL at your Drone installation

## Installing the Chart

Add the github information (OAuth app client and secret, approved orgs
and admins) as a Kubernetes secret:

```bash
$ kubectl create secret generic github \
    --from-literal=client=<client> \
    --from-literal=secret=<secret> \
    --from-literal=orgs=<comma,separated,orgs> \
    --from-literal=admins=<comma,separated,admins>
```

Note that these secret keys must match the values.yaml secretEnvKeys
section.

Once it's configured, you can then install drone.

```bash
$ helm install ./drone
```
