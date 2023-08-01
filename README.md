# Local K3s Cluster With Multipass

**Multipass** is a tool that allows you to spin up Ubuntu VMs in a matter of seconds on a Mac, Linux, or Windows workstation.

Depending upon your OS, Multipass uses Hyper-V, HyperKit, KVM, or VirtualBox natively for the fastest startup time.

I’ll set up a K3s Kubernetes cluster on virtual machines created with Multipass.

## Description

### Creation of the VMs

```shell
multipass launch --name node1
multipass launch --name node2
multipass launch --name node3
```

```shell
multipass list
```

```shell
Name                    State             IPv4             Image
node1                   Running           192.168.64.9     Ubuntu 22.04 LTS
node2                   Running           192.168.64.10    Ubuntu 22.04 LTS
node3                   Running           192.168.64.11    Ubuntu 22.04 LTS
```

### Initialize K3s on node1

By using the exec subcommand of Multipass, I install [K3s](https://k3s.io/)

```shell
multipass exec node1 -- \
  bash -c "curl -sfL https://get.k3s.io | sh -"
```

After installation complete, I'll gather the elements to add additional Kubernetes distribution.

- The token to join the cluster

```shell
set TOKEN $(multipass exec node1 sudo cat /var/lib/rancher/k3s/server/node-token)
```

- The IP of the API server running on node1

```shell
set IP $(multipass info node1 | grep IPv4 | awk '{print $2}')
```

### Join node2 and node3 to the Cluster

```shell
multipass exec node2 -- \
bash -c "curl -sfL https://get.k3s.io | K3S_URL=\"https://$IP:6443\" K3S_TOKEN=\"$TOKEN\" sh -"
```

```shell
multipass exec node3 -- \
bash -c "curl -sfL https://get.k3s.io | K3S_URL=\"https://$IP:6443\" K3S_TOKEN=\"$TOKEN\" sh -"
```

### Get the Cluster Configuration

Take a look at the all the nodes of the cluster.

```shell
multipass exec node1 -- sudo kubectl get nodes
```

```shell
NAME    STATUS   ROLES                  AGE     VERSION
node1   Ready    control-plane,master   30m     v1.27.4+k3s1
node2   Ready    <none>                 5m13s   v1.27.4+k3s1
node3   Ready    <none>                 3m22s   v1.27.4+k3s1
```

We need to get the `kubeconfig` file on `node1` to access the cluster's API Server from the local PC.

```shell
multipass exec node1 sudo cat /etc/rancher/k3s/k3s.yaml > k3s.yaml
```

Next, the server key must be changed to refer to the remote IP address of node1 instead of the local host.

```shell
sed -i '' "s/127.0.0.1/$IP/" k3s.yaml
```

Finally, configure the local `kubectl` to use the `kubeconfig` file (k3s.yaml) that you obtained. The easy way is to set the `KUBECONFIG` environment variable to point to the configuration file.

```shell
set -x KUBECONFIG $PWD/k3s.yaml
```

## Demo

## Features

- feature:1
- feature:2

## Requirement

## Usage

### Deploy Kubernets Dashboard

Apply the declaration of admin user.

```shell
kubectl apply -f sa-admin-user.yml
```

The following is the `sa-admin-user.yml`

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

## Installation

## References

## Licence

Released under the [MIT license](https://gist.githubusercontent.com/shinyay/56e54ee4c0e22db8211e05e70a63247e/raw/34c6fdd50d54aa8e23560c296424aeb61599aa71/LICENSE)

## Author

- github: <https://github.com/shinyay>
- twitter: <https://twitter.com/yanashin18618>
- mastodon: <https://mastodon.social/@yanashin>
