# Minikube commands

## Minikube arguments

Equals sign in options=arg format is required. Didn't test the quotes around options yet.

```bash
minikube start --cpus=2 --memory=8g --disk-size=20g --addons=[dashboard,metrics-server,registry] --insecure-registry=192.168.39.0/24
```

## Minikube start

```bash
minikube start
--interactive=true
--driver=kvm2
--download-only
--cpus='2'
--memory='6g'
--disk-size='20000mb'
--addons=[]
--insecure-registry=[]
--extra-disks=0
--kvm-gpu=true
--kvm-hidden=true
--kvm-network='default'
```

```bash
# more
--network=''          # Attach to bridge instead of kvm default nat?
--image-repository='' # Local image repo?
--iso-url=[]
```

## Minikube ssh

Information is in `~/.minikube/machines/minikube/config.json`.

```bash
minikube ssh
# or
ssh -i ~/.minikube/machines/minikube/id_rsa docker@$(minikube ip)
```

## kubectl in minikube

kubectl is stored in the cache or used through minikube command line.

```bash
~/.minikube/cache/linux/v1.23.1/kubectl version
minikube kubectl version
```

## Container Images

Minikube uses docker internally. The image cache comes from docker.

```bash
$ docker image ls
registry.access.redhat.com/ubi8/ubi            latest    b81e86a2cb9a   2 weeks ago     216MB
k8s.gcr.io/kube-apiserver                      v1.23.1   b6d7abedde39   8 weeks ago     135MB
k8s.gcr.io/kube-proxy                          v1.23.1   b46c42588d51   8 weeks ago     112MB
k8s.gcr.io/kube-scheduler                      v1.23.1   71d575efe628   8 weeks ago     53.5MB
k8s.gcr.io/kube-controller-manager             v1.23.1   f51846a4fd28   8 weeks ago     125MB
k8s.gcr.io/etcd                                3.5.1-0   25f8c7f3da61   3 months ago    293MB
k8s.gcr.io/coredns/coredns                     v1.8.6    a4ca41631cc7   4 months ago    46.8MB
k8s.gcr.io/pause                               3.6       6270bb605e12   5 months ago    683kB
kubernetesui/dashboard                         v2.3.1    e1482a24335a   8 months ago    220MB
kubernetesui/metrics-scraper                   v1.0.7    7801cfc6d5c0   8 months ago    34.4MB
gcr.io/k8s-minikube/storage-provisioner        v5        6e38f40d628d   10 months ago   31.5MB
k8s.gcr.io/metrics-server/metrics-server       <none>    17c225a562d9   12 months ago   60.5MB
registry                                       <none>    678dfa38fcfa   14 months ago   26.2MB
gcr.io/google_containers/kube-registry-proxy   <none>    60dc18151daf   5 years ago     188MB
```

```bash
# cri-o uses crictl
# Most of these don't take arguments
crictl images
crictl pods # list containers
# Doesn't seem to take container name
crictl exec -it 808b229f5beaa /bin/bash
```

## IP

Minikube attaches to KVM "default" NAT network.

Minikube uses an isolated 192.168.39.0/24 as its network. See minikube ip command for vm ip.

```bash
# Set insecure registry setting at start. Must use minikube delete to change.
minikube start --insecure-registry "192.168.39.0/24"
```

## Echoserver Test

Echoserver runs on port 8080.

podman run --rm -it -p 8080:8080 -p 8443:8443 gcr.io/google-containers/echoserver:1.10
