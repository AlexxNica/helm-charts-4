# Nexus Repository Manager OSS

This is a helm-chart for Nexus Repository Manager OSS. The starting
point was the [DevOpsKube package][], and we've added a simple reverse
proxy and a restore image so that it comes out of the box with Docker
registries pre-configured.

## Chart Details

This chart will create the following:

- a statefulset running the `sonatype/nexus3` image with a hosted
  docker repo running on 18444 and a group repo on 18443 that includes
  a proxy to the DockerHub registry.
- a deployment running a reverse proxy to route docker requests
  appropriately
- services for the above, and an ingress to the reverse proxy

## Configuration

The following tables lists the configurable parameters of the Nexus
chart and their default values.

| Parameter                      | Description                               | Default                             |
|--------------------------------+-------------------------------------------+-------------------------------------|
| `ingress.hostname`             | Host Name of this Instance                | `repo-test.devpoc1.jamconsultg.com` |
| `statefulset.javaOpts`         | Additional options for the JVM            | `"-Xms1200m -Xmx1200m"`             |
| `statefulset.persistence.path` | Path where all data on the host is stored | `/nexus-data`                       |

Specify each parameter using the `--set key=value[,key=value]`
argument to helm install.

Alternatively, a YAML file that specifies the values for the
parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-nexus -f values.private.yaml nexus-x.x.x.tgz
```

> **Tip**: You can use the default [values.yaml](values.yaml)

The initial Username/Pasword combination for the first login is:
`admin/admin123`.

## Persistence

To be able to keep stateful data in the nexus kubernetes container,
the following path is used:

```
/nexus-data
```

## Restoring Data

If desired, you can create a backup container with
[Sharecare/kubernetes-nexus-backup][backup] and use the container to
restore with the data in the container so that you can reinstall this
helm chart and preserve all your data. Make sure to update the values
in the `restore` section in the `values.yaml` appropriately.

## Accessing

The UI can be accessed by pointing a browser to [here][].

## Using the Docker Registries

You'll either need to set up an SSL cert that matches the hostname you
specify in `ingress.hostname`, or you'll need to configure your Docker
daemon for using an the [insecure registry][]. Once you've done either
of these steps, the following should work:

```
$ docker login $INGRESS_HOSTNAME -u admin -p admin123

# in a directory with a Dockerfile
$ docker build -t $INGRESS_HOSTNAME/hello-world:1.0.0 .
$ docker push $INGRESS_HOSTNAME/hello-world:1.0.0
```

If you're running this in minikube, make sure to include the port
number in your insecure registry specification for Docker, and as part
of the `docker login` command.

Note that the docker registries configured inside of Nexus are
deliberately running on HTTP ports because our setup is that the SSL
cert configuration is for the ingress hostname (which points at the
reverse proxy). The alternative of letting Nexus manage the SSL cert
and run the docker registries on HTTPS ports doesn't apply to our case
since we need a reverse proxy in front of Nexus (and even if
Kubernetes figures out how to route by port in Nexus, the ingress is
still in front of Nexus, so).

## create manifests

From the root of this repository, you could

```
$ helm install ./nexus --dry-run --debug --values ./nexus/values-sc.yaml | \
  sed -e '1,/MANIFEST/d' > ./nexus/manifests.yaml
```

[nexus3]: https://hub.docker.com/r/sonatype/nexus3/
[DevOpsKube package]: https://github.com/devopskube/devopskube/tree/master/charts/nexus
[here]: http://repo.devpoc1.jamconsultg.com/nexus
[backup]: https://github.com/Sharecare/kubernetes-nexus-backup
[insecure registry]: https://docs.docker.com/registry/insecure/
