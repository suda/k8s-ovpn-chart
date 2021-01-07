# ⚠️ This chart has been deprecated. Please switch to the new one: https://artifacthub.io/packages/helm/suda/personal-ovpn

[![](https://img.shields.io/static/v1.svg?label=Deploy%20on&message=DigitalOcean&color=blue)](https://www.digitalocean.com/products/kubernetes/?refcode=fef9487dad1e&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=CopyPaste) [![Artifact HUB](https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/k8s-ovpn)](https://artifacthub.io/packages/search?repo=k8s-ovpn)

# Private Kubernetes OpenVPN Helm chart

TL;DR: This Chart is intended for deploying a private VPN server **without access to other Pods in the cluster**.
Think of it as roll-your-own Nord/Express VPN in your Kubernetes cluster.

## Usage

```bash
$ helm repo add k8s-ovpn https://raw.githubusercontent.com/suda/k8s-ovpn-chart/master/charts
$ helm repo update
$ helm install k8s-ovpn/k8s-ovpn-chart
```
### Skip manual steps: Automatic and Insecure way (not for production)

```bash
$ helm repo add k8s-ovpn https://raw.githubusercontent.com/suda/k8s-ovpn-chart/master/charts
$ helm repo update
$ helm install k8s-ovpn/k8s-ovpn-chart --set automatic.enabled=true
```

### Generate necessary secrets (manual way & secure CA)

```bash
$ git clone https://github.com/suda/k8s-ovpn-chart.git
$ cd k8s-ovpn-chart
$ export VPN_HOSTNAME=vpn.example.com
# Generate basic OpenVPN config
$ ./bin/generate-config
# Repeat this step for all the clients you need
$ CLIENT_NAME=my-client ./bin/add-client
# Set the Kubernetes secrets. Prepend with REPLACE=true to update existing ones
$ ./bin/set-secrets
```

After generating the secrets above, you'll have all the config, certificates **and the keys** on your machine, in the `ovpn0` directory. You need it to add more clients later but also isn't very secure to keep those keys just laying around on your machine.

### Allowing traffic inside of the cluster

If you want to make debugging the cluster easier and use VPN to access the pods, you might prefer using [`stable/openvpn` chart](https://github.com/helm/charts/tree/master/stable/openvpn).
But if you really want to, you can enable it by setting `limitTraficToNamespace` value to `false`.

## Configuration

The following table lists the configurable parameters of the `k8s-ovpn` chart and their default values.

| Parameter                           | Description                                                                                                               | Default             |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------- | ------------------- |
| `image.repository`                  | container image repository                                                                                                | `kylemanna/openvpn` |
| `image.tag`                         | container image tag                                                                                                       | `2.4`               |
| `image.pullPolicy`                  | container image pull policy                                                                                               | `IfNotPresent`      |
| `tolerations`                       | node taints to tolerate (requires Kubernetes >=1.6)                                                                       | `[]`                |
| `affinity`                          | node/pod affinities (requires Kubernetes >=1.6)                                                                           | `{}`                |
| `nodeSelector`                      | node labels for pod assignment                                                                                            | `{}`                |
| `resources`                         | pod resource requests & limits                                                                                            | `{}`                |
| `limitTraficToNamespace`            | limit network traffic just to OpenVPN namespace                                                                           | `true`              |
| `limitedCidr`                       | CIDR to be blocked out                                                                                                    | `10.0.0.0/8`        |
| `automatic.cipher`                  | Cipher used                                                                                                               | `"AES-256-CBC"`     |
| `automatic.digestAlgorythm`         | Authenticate  packets with HMAC using the given message digest algorithm (auth).                                          | `"SHA384"`          |
| `automatic.dnsServer`               | DNS Server IP                                                                                                             | `"8.8.8.8"`         |
| `automatic.enabled`                 | Skip manual steps and generate configuration & pki according to values config, Warning, PKI will be passwordless !        | `false`             |
| `automatic.externalHostname`        | Hostname OR Ip of cluster openvpn entrypoint, default to 'domain.tld' so you must define it                               | `""`                |
| `automatic.externalPort`            | Port cluster openvpn entrypoint, defaults to service.port (nodePort)                                                      | `""`                |
| `automatic.extraOptions`            | Additional options for openvpn configuration                                                                              | `[]`                |
| `automatic.persistence.accessModes` | PersistentVolumeClaim access modes                                                                                        | `["ReadWriteOnce"]` |
| `automatic.persistence.annotations` | PersistentVolumeClaim annotations                                                                                         | `{}`                |
| `automatic.persistence.mountPath`   | PersistentVolumeClaim mounting path                                                                                       | `"/etc/openvpn"`    |
| `automatic.persistence.size`        | PersistentVolumeClaim size request                                                                                        | `"8Gi"`             |


## Acknowledgements

This chart is based on [`chepurko/k8s-ovpn`](https://github.com/chepurko/k8s-ovpn) which is using the great [`kylemanna/docker-openvpn`](https://github.com/kylemanna/docker-openvpn) Docker image.
