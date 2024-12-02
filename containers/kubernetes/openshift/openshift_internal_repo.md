# OpenShift internal repo

OpenShift has an internal container repo for each project.

See https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html

## Codeready Containers

Access from outside crc.

```bash
# Get the host name for internal repo
oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}'

# Get certificate for internal repo
oc extract secret/router-ca --keys=tls.crt -n openshift-ingress-operator

# Put certificate in certs.d for podman to recognize it. Also looks in /etc/docker/certs.d
cp tls.crt /etc/containers/certs.d

# Login using token auth. Password does not work.
podman login -u kubeadmin -p $(oc whoami -t) default-route-openshift-image-registry.apps-crc.testing

# Tag image using project name
podman tag centos:7 default-route-openshift-image-registry.apps-crc.testing/project1/centos:7
podman image push centos:7 default-route-openshift-image-registry.apps-crc.testing/project1/centos:7
```

Access from inside crc.

```bash
oc get nodes
oc debug nodes/crc-ctj2r-master-0

# Debug shell
chroot /host
oc login -u kubeadmin -p x https://api.crc.testing:6443
podman login -u kubeadmin -p $(oc whoami -t) image-registry.openshift-image-registry.svc:5000
podman image ls
```
