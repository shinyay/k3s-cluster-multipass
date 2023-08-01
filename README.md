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

## Demo

## Features

- feature:1
- feature:2

## Requirement

## Usage

## Installation

## References

## Licence

Released under the [MIT license](https://gist.githubusercontent.com/shinyay/56e54ee4c0e22db8211e05e70a63247e/raw/34c6fdd50d54aa8e23560c296424aeb61599aa71/LICENSE)

## Author

- github: <https://github.com/shinyay>
- twitter: <https://twitter.com/yanashin18618>
- mastodon: <https://mastodon.social/@yanashin>
