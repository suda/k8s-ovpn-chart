## Configuration

The following table lists the configurable parameters of the `k8s-ovpn` chart and their default values.

| Parameter                | Description                                         | Default             |
| ------------------------ | --------------------------------------------------- | ------------------- |
| `image.repository`       | container image repository                          | `kylemanna/openvpn` |
| `image.tag`              | container image tag                                 | `2.3`               |
| `image.pullPolicy`       | container image pull policy                         | `IfNotPresent`      |
| `tolerations`            | node taints to tolerate (requires Kubernetes >=1.6) | `[]`                |
| `affinity`               | node/pod affinities (requires Kubernetes >=1.6)     | `{}`                |
| `nodeSelector`           | node labels for pod assignment                      | `{}`                |
| `resources`              | pod resource requests & limits                      | `{}`                |
| `limitTraficToNamespace` | limit network traffic just to OpenVPN namespace     | `true`              |
| `limitedCidr`            | CIDR to be blocked out                              | `10.0.0.0/8`        |
