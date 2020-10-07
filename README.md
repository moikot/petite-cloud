
# Petite Cloud 

A [GitOps Toolkit](https://toolkit.fluxcd.io/) setup for installing a set of services on a bare-metal [Kubernetes](https://kubernetes.io/) cluster of [Rock64s](https://www.pine64.org/devices/single-board-computers/rock64/).

**NOTE:** The following services need to be adjusted if you want to clone this repository and use it for your setup:

1. MetalLB is used as a layer 2 load balancer and manages IP addresses range 192.168.88.100-192.168.88.150 ([see MetalLB HelmRelease](https://github.com/moikot/fleet-infra/blob/master/helm-releases/metallb.yml)).
2. Domain cppcli.com with a wild card SSL certificate provided by Let's Encrypt (see [Traefik HelmRelease](https://github.com/moikot/fleet-infra/blob/master/helm-releases/traefik.yml)) is used within a local network for accessing the following services:
	* grafana.cppcli.com for Grafana (see [Ingress settings](https://github.com/moikot/fleet-infra/blob/master/helm-releases/grafana.yml)).
	* prometheus.cppcli.com for Prometheus (see [Ingress resource](https://github.com/moikot/fleet-infra/blob/master/kustomizations/lens-metrics/03-prometheus-ingress.yml)).
	* git.cppcli.com for the internal basic Git server (see [Ingress settings](https://github.com/moikot/fleet-infra/blob/master/helm-releases/basic-git-server.yml)).
3. NFS server is available at 192.168.88.246, and the path for the root directory, where NFS sub-directory provisioner is going to create sub-folder, is `/data/kubernetes` ([see NFS provisioner HelmRelease](https://github.com/moikot/fleet-infra/blob/master/helm-releases/nfs-client-provisioner.yml)).

## Installing 

You will need a bare-metal Kubernetes cluster accessible via your current `kubectl` context.

Install GitOps Toolkit CLI - `gotk` and set $GITHUB_USER and $GITHUB_TOKEN environment variables following [Get started with GitOps Toolkit](https://toolkit.fluxcd.io/get-started/).

Bootstrap the cluster by connecting to the GitOps repository.

```shell
gotk bootstrap github \
    --owner=$GITHUB_USER \
    --repository=petite-cloud \
    --branch=master \
    --personal \
    --arch arm64
```

## Components

The setup is powered by [GitOps Toolkit](https://toolkit.fluxcd.io/) and contains the following components:
* [GitOps Toolkit](https://toolkit.fluxcd.io/) for monitoring your repositories and apply changes to the cluster.
* [MetalLB](https://metallb.universe.tf/) so that you can have a layer 2 load balancer.
* [Traefik](https://doc.traefik.io/traefik/), so that you can access services using your domain and wildcard SSL certificates provided by [Let's Encrypt](https://letsencrypt.org/).
* [Grafana](https://grafana.com/) for displaying observability dashboards.
* Lens-metrics - a set of services including [Prometheus](https://prometheus.io/), [node-exporter](https://github.com/prometheus/node_exporter), and [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics), for gathering cluster metrics.
* [nfs-subdir-external-provisioner](https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner) for porvisining persisted volumes on your home NAS.
* [basic-git-server](https://github.com/moikot/basic-git-server) for accessing the private part of your cluster setup.