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

You will need a postgresql database somewhere. You can create the
manifests for one Kubernetes like (add your own random password):

```
# name is optional. make sure to replace the password
$ helm install --dry-run --debug --name quaffing-ladybird-drone stable/postgresql \
  --set postgresUser=drone,postgresDatabase=drone,postgresPassword=<PASSWORD> | sed -e '1,/MANIFEST/d' > ./drone/pg-manifests.yaml
```

After either creating a pgsql database or finding a cloud provider,
you can construct the `db_driver` portion of the secret in the next
section as

```
postgres://drone:<PASSWORD>@quaffing-ladybird-drone-postgresql:5432/drone?sslmode=disable
```

Make sure not to commit the password.

## Installing the Chart

The chart is currently set up to require the following two Kubernetes
secrets. The names (`drone` and `drone-nexus`) cannot be changed
without changing the counterpart values in the manifest. The secret
values themselves are available in the credentials document in teams.

```bash
$ kubectl create secret generic drone \
    --from-literal=github_client=<client> \
    --from-literal=github_secret=<secret> \
    --from-literal=github_orgs=<comma,separated,orgs> \
    --from-literal=github_admins=<comma,separated,admins> \
    --from-literal=db_driver=<postgres://user:pass@conn.str>

$ kubectl create secret generic drone-nexus \
    --from-file=$HOME/.npmrc \
    --from-file=$HOME/.sbt/0.13/plugins/credentials.sbt \
    --from-file=$HOME/.m2/settings.xml \
    --from-file=$HOME/.gradle/gradle.properties \
    --from-literal=username=<REDACTED> \
    --from-literal=password=<REDACTED>
```

Note that the second secret assumes that you've configured those files
only for access to Nexus. Don't put your credentials for other
repositories in the secret.

Drone uses sqlite3 by default but we're using postgresql; drone has
additional [documentation for its backends][docs] available as well.

Once it's configured, you can then install drone.

```bash
$ helm install ./drone
```

Or to upgrade an existing installation,

```bash
$ helm upgrade <existing-name> ./drone
```

To see what helm is doing, or to install without helm tiller installed
remotely, you can see the generated manifests like

```bash
$ helm install ./drone --dry-run --debug  | sed -e '1,/MANIFEST/d' > ./drone/manifests.yaml
```

Note that you will need helm and tiller installed locally for this to
work.

[fork]: https://github.com/bacongobbler/kube-charts/tree/440e9d64298741253a06058c68dc871fd65aa32a
[pr]: https://github.com/kubernetes/charts/pull/821
[installation]: http://readme.drone.io/admin/installation-guide/
[drone]: http://readme.drone.io/admin/database-engines/
