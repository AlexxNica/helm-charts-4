# Nexus Repository Manager OSS

This is a helm-chart for Nexus Repository Manager OSS.  It is based on the DevOpsKube package.

## Variables

To be able to use this package, the following variables should be set.

see values.yaml

## Chart Details

This chart uses the docker image from [sonatype](https://github.com/sonatype/nexus-oss).

## Installing the chart

Other installation-instructions can be found at [Using Helm](https://github.com/kubernetes/helm/blob/master/docs/using_helm.md) page.

## Configuration

The following tables lists the configurable parameters of the Nexus chart and their default values.

|Parameter|Description|Default|
|---------|-----------|-------|
|`ingress.hostname`|Host Name of this Instance|`repo.devpoc1.jamconsultg.com`|
|`deployment.containerPort`|Port of the container|`8081`|
|`deployment.maxMem`|Resource limit memory (-Xmx)|`1200M`|
|`deployment.minMem`|Minimum Memory (-Xms)|`1200M`|
|`deployment.javaOpts`|Additional options for the JVM|''|
|`deployment.persistence.path`|Path where all data on the host is stored|`/data/nexus`|

Specify each parameter using the `--set key=value[,key=value]` argument to helm install.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-nexus -f values.private.yaml nexus-x.x.x.tgz
```

> **Tip**: You can use the default [values.yaml](values.yaml)

The initial Username/Pasword combination for the first login is: `admin/admin123`.

## Persistence

To be able to keep stateful data in the nexus kubernetes container, the following path is used:

```
/data/nexus
```

## Accessing

The UI can be accessed by pointing a browser to [here](http://repo.devpoc1.jamconsultg.com/nexus).