# Drone.io

[Drone](http://readme.drone.io/) is a Continuous Integration platform
built on container technology. Every build is executed inside an
ephemeral Docker container, giving developers complete control over
their build environment with guaranteed isolation.

These files were initially found in a [fork][] as part of a
[PR to the main helm charts repo][pr], and have been modified for our
particular setup.

## Introduction

This chart stands up a Drone server. This includes:

- A [Drone Server][installation] Pod
- A [Drone Agent][installation] Pod with a sidecar DinD container

## Prerequisites

- Kubernetes 1.5+
- The ability to point a DNS entry or URL at your Drone installation

## Installing the Chart

The chart is currently set up to require the following information as
a Kubernetes secret:

```bash
$ kubectl create secret generic drone \
    --from-literal=github_client=<client> \
    --from-literal=github_secret=<secret> \
    --from-literal=github_orgs=<comma,separated,orgs> \
    --from-literal=github_admins=<comma,separated,admins> \
    --from-literal=db_driver=<postgres://user:pass@conn.str>
```

Note that these secret keys must match the `values.yaml` secretEnvKeys
section - it's unnecessarily confusing, but if you add the secret as
above, you don't have to worry about it.

Drone uses sqlite3 by default but we're using postgresql; drone has
additional [documentation for its backends][docs] available as well.

Once it's configured, you can then install drone. If you're doing it
for real, you need to set the `ingress.hostname`:

```bash
$ helm install ./drone --set ingress.hostname=your.dns.address
```

Or to upgrade an existing installation,

```bash
$ helm upgrade --set ingress.hostname=<dns> <existing-name> ./drone
```

[fork]: https://github.com/bacongobbler/kube-charts/tree/440e9d64298741253a06058c68dc871fd65aa32a
[pr]: https://github.com/kubernetes/charts/pull/821
[installation]: http://readme.drone.io/admin/installation-guide/
[drone]: http://readme.drone.io/admin/database-engines/
