# nexus reverse proxy for kubernetes

To run a Docker registry in Nexus, you need to run it on a separate
port from 80, as the Docker API mandates that it has control over
`/`. So, you decide to run your private Docker registry on 18444 (and
a group registry that includes a proxies through to the main Docker
registry on 18443).

Unfortunately, Kubernetes ingress doesn't have any ability to route
requests based on port. Traefik _does_ have that functionality, but
since Kubernetes ingress is the way we're interfacing with Traefik,
our hands are tied - there's no way to specify in an Kubernetes ingress
definition what we want Traefik to do.

So, we run a Kube ingress that directs traffic to a reverse proxy that
routes Docker traffic to the appropriate ports, and the rest of the
traffic to Nexus proper on 80.

## usage

**You MUST update values.yaml**: change `service.nexus.host` to the
service where nexus is running. Otherwise, this definitely won't work.

    $ helm install ./nexus-reverse-proxy/ --set service.nexus.host=rude-pike-nexus

## kludges

Our current nexus set up in the dev cluster is a deployment (should be
a statefulset) that isn't managed by helm (charts were manually
applied). We've configured the nexus helm chart in this repo to use
the reverse proxy, but for the time being we need this separate chart
to only install the proxy, since the existing Nexus in the dev
cluster cannot simply be updated. Eventually, this can go away and
everything can be managed in the Nexus chart.
