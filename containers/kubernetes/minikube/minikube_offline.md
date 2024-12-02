# Minikube offline

## Summary

* Copying download through ~/.minikube works
* Loading addons through tar and minikube image load does not work because images are referenced by SHA256
* Forwarding ports into minikube is fiddly because the 192.168.39 network is isolated

* Set port forwards on libvirt network
* run minikube service (maps to ports around 32000)

## Download

```bash
# Start cache
minikube start --driver=kvm2 --download-only
```

Minikube cache adds images to ~/.minikube that get added when it runs. Claims to be deprecated in v1.20 but still there in v1.25.

```bash
minikube cache add registry.access.redhat.com/ubi8/ubi
```

Minikube image load downloads a container image and sends it to the running minikube's cache. Minikube must be running.

```bash
minikube image load registry.access.redhat.com/ubi8/ubi
```

[[Minikube cache and image testing]]

## Addons
After enabling addons the image references is in the default config file at ~/.minikube/profiles/minikube/config.json
This may be all enable does. Images aren't pulled until a config requiring them is applied.

* https://minikube.sigs.k8s.io/docs/handbook/pushing/
* https://minikube.sigs.k8s.io/docs/handbook/registry/

```bash
minikube addons enable dashboard
minikube addons enable metrics-server
# Access dashboard with
minikube dashboard --url
```
