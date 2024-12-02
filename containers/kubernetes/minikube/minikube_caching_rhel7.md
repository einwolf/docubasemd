# Testing minikube caching

This is tested with RHEL 7.

## Podman pulling images with tag and digest

Podman says pulling an image reference with tag and reference is not supported. Pulling by SHA256 gets the image but it is untagged.

```bash
podman pull kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6
Error: Invalid image name "kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6", unknown transport "kubernetesui/dashboard"

podman pull "docker://kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6"
Error: Docker references with both a tag and digest are currently not supported

podman pull "docker://kubernetesui/dashboard@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6"
Trying to pull docker.io/kubernetesui/dashboard@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6...
Getting image source signatures
Copying blob 035b5e7ced27 done
Copying blob a16055c5c364 done
Copying config 9a07b5b4bf done
Writing manifest to image destination
Storing signatures
9a07b5b4bfac07e5cfc27f76c34516a3ad2fdfa3f683f375141fe662ef2e72db

# Image is untagged
podman image ls
REPOSITORY                                                                        TAG                   IMAGE ID      CREATED        SIZE
docker.io/kubernetesui/dashboard                                                  <none>                9a07b5b4bfac  6 months ago   229 MB

# Check with podman inspect <image id>. Sha is listed as digest.
podman image inspect 9a07
[
    {
        "Id": "9a07b5b4bfac07e5cfc27f76c34516a3ad2fdfa3f683f375141fe662ef2e72db",
        "Digest": "sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6",
        "RepoTags": [],
        "RepoDigests": [
            "docker.io/kubernetesui/dashboard@sha256:3af248961c56916aeca8eb4000c15d6cf6a69641ea92f0540865bb37b495932f",
            "docker.io/kubernetesui/dashboard@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6"
        ],
        etc
    }
]
```

## minkube cache 1

This was run after the above podman pull.

```bash
minikube cache add kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6
❗  "minikube cache" will be deprecated in upcoming versions, please switch to "minikube image load"

minikube cache list
kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6

find ~/.minikube/cache
-> Shows images/kubernetesui/dashboard file name in results
./images
./images/kubernetesui
./images/kubernetesui/dashboard_v2.1.0@sha256_7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6
./images/kubernetesui/dashboard_v2.1.0@sha256_7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6.806566501.tmp
./images/kubernetesui/dashboard_v2.1.0@sha256_7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6.623203327.tmp
```

## minikube cache 2

(No podman pull run)

```bash
minikube cache add metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
❗  "minikube cache" will be deprecated in upcoming versions, please switch to "minikube image load"
❗  The image 'metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62' was not found; unable to add it to cache.

podman pull docker://metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
Trying to pull docker.io/metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62...
Error: Error initializing source docker://metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62: Error reading manifest sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62 in docker.io/metrics-server/metrics-server: errors:
denied: requested access to the resource is denied
unauthorized: authentication required

# I guess it depends on docker repository search order
podman pull docker://k8s.gcr.io/metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
Trying to pull k8s.gcr.io/metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62...
Getting image source signatures
Copying blob 9e4425256ce4 done
Copying blob 125b87d77e36 done
Copying config 17c225a562 done
Writing manifest to image destination
Storing signatures
17c225a562d97d7e4387b244c68853a3d36095122f414c027820d2f910513dd7

# Cache add fails
minikube cache add metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
❗  "minikube cache" will be deprecated in upcoming versions, please switch to "minikube image load"
❗  The image 'metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62' was not found; unable to add it to cache.

# Retag
podman tag dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62 metrics-server/metrics-server:v0.4.2
Error: dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62: image not known
podman tag k8s.gcr.io/metrics-server/metrics-server metrics-server/metrics-server:v0.4.2
Error: k8s.gcr.io/metrics-server/metrics-server: image not known
podman tag 17c225a562d97d7e4387b244c68853a3d36095122f414c027820d2f910513dd7 metrics-server/metrics-server:v0.4.2
-> ok
podman inspect metrics-server/metrics-server:v0.4.2
-> ok
[
    {
        "Id": "17c225a562d97d7e4387b244c68853a3d36095122f414c027820d2f910513dd7",
        "Digest": "sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62",
        "RepoTags": [
            "localhost/metrics-server/metrics-server:v0.4.2"
        ],
        "RepoDigests": [
            "k8s.gcr.io/metrics-server/metrics-server@sha256:9ea12bd44276106240f36137877fc2c56f27c62f3d866813535e494f53f4c8bc",
            "k8s.gcr.io/metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62",
            "localhost/metrics-server/metrics-server@sha256:9ea12bd44276106240f36137877fc2c56f27c62f3d866813535e494f53f4c8bc",
            "localhost/metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62"
        ],
		etc
	}
]

# Cache add still fails
minikube cache add metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
❗  "minikube cache" will be deprecated in upcoming versions, please switch to "minikube image load"
❗  The image 'metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62' was not found; unable to add it to cache.

# Try again
minikube cache add k8s.gcr.io/metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
❗  "minikube cache" will be deprecated in upcoming versions, please switch to "minikube image load"
-> ok

minikube cache list
k8s.gcr.io/metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6
metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
-> Added as k8s and no server name?

find ~/.minikube/cache
-> Don't know about this. Maybe can rename though.
./images/metrics-server
./images/k8s.gcr.io
./images/k8s.gcr.io/metrics-server
./images/k8s.gcr.io/metrics-server/metrics-server_v0.4.2@sha256_dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
```

## minikube cache 3

Deleted all cached images.

|addon|config|note|
|---|---|---|
|dashboard|Dashboard||
|kubevirt|Kubectl||
|metrics-server|MetricsServer, MetricsScraper||
|nvidia-driver-installer|NvidiaDriverInstaller, Pause||
|nvidia-gpu-device-plugin|NvidiaDevicePlugin|maybe Pause|
|registry|Registry|Wanted minikube running| 

Not sure on the NvidiaDriverInstaller reference.

Registry wants to interface with running minikube and does not enable.

```
        "CustomAddonImages": {
                "Dashboard": "kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6",
				"Kubectl": "bitnami/kubectl:1.17@sha256:de642e973d3d0ef60e4d0a1f92286a9fdae245535c5990d4762bbe86fcf95887",
                "MetricsScraper": "kubernetesui/metrics-scraper:v1.0.4@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf",
                "MetricsServer": "metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62"
				"NvidiaDriverInstaller": "minikube-nvidia-driver-installer:e2d9b43228decf5d6f7dce3f0a85d390f138fa01",
				"NvidiaDevicePlugin": "nvidia/k8s-device-plugin:1.0.0-beta4@sha256:94d46bf513cbc43c4d77a364e4bbd409d32d89c8e686e12551cc3eb27c259b90",
                "Pause": "pause:2.0@sha256:9ce5316f9752b8347484ab0f6778573af15524124d52b93230b9a0dcc987e73e",
				"Registry": "registry:2.7.1@sha256:d5459fcb27aecc752520df4b492b08358a1912fcdfa454f7d2101d4b09991daa"
        },
```

```bash
minikube cache add kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6
-> ok
minikube cache add kubernetesui/metrics-scraper:v1.0.4@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf
-> ok

# metrics-server problem
minikube cache add metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
❗  "minikube cache" will be deprecated in upcoming versions, please switch to "minikube image load"
❗  The image 'metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62' was not found; unable to add it to cache.

minikube cache add k8s.gcr.io/metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
-> ok
-> Trying to use the dashboard says it can't find the metrics-server/metrics-server image

minikube cache add bitnami/kubectl:1.17@sha256:de642e973d3d0ef60e4d0a1f92286a9fdae245535c5990d4762bbe86fcf95887
-> ok

minikube cache add "registry:2.7.1@sha256:d5459fcb27aecc752520df4b492b08358a1912fcdfa454f7d2101d4b09991daa"
-> ok

minikube cache list
metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
registry:2.7.1@sha256:d5459fcb27aecc752520df4b492b08358a1912fcdfa454f7d2101d4b09991daa
bitnami/kubectl:1.17@sha256:de642e973d3d0ef60e4d0a1f92286a9fdae245535c5990d4762bbe86fcf95887
k8s.gcr.io/metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
kubernetesui/dashboard:v2.1.0@sha256:7f80b5ba141bead69c4fee8661464857af300d7d7ed0274cf7beecedc00322e6
kubernetesui/metrics-scraper:v1.0.4@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf
```

```bash
# Not sure if this works as minikube dashbaord won't run

# metrics-server problem
minikube cache add metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
❗  "minikube cache" will be deprecated in upcoming versions, please switch to "minikube image load"
❗  The image 'metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62' was not found; unable to add it to cache.

podman pull docker://metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
Trying to pull docker.io/metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62...
Error: Error initializing source docker://metrics-server/metrics-server@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62: Error reading manifest sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62 in docker.io/metrics-server/metrics-server: errors:
denied: requested access to the resource is denied
unauthorized: authentication required

minikube cache add k8s.gcr.io/metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
Go to ~/.minikube/cache/images
rmdir metrics-server
mv k8s.gcr.io/metrics-server .
# Likely prints an error but deletes it anyway
minikube cache delete metrics-server/metrics-server:v0.4.2@sha256:dbc33d7d35d2a9cc5ab402005aa7a0d13be6192f3550c7d42cba8d2d5e3a5d62
# Should list metrics-server but not k8s.gcr.io/metrics-server
minikube cache list
```

## minikube image load 1

The docs imply minikube image load sends images to a running instance. Not sure this will work.

Checked this and it does require running minikube. It runs docker load image so use docker save to export.

```
# Using kubernetesui/metrics-scraper:v1.0.4@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf
podman pull docker://docker.io/kubernetesui/metrics-scraper@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf
Trying to pull docker.io/kubernetesui/metrics-scraper@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf...
Getting image source signatures
Copying blob 07008dc53a3e done
Copying blob 04d0e0aeff30 done
Copying blob 1f8ea7f93b39 done
Copying config 86262685d9 done
Writing manifest to image destination
Storing signatures
86262685d9abb35698a4e03ed13f9ded5b97c6c85b466285e4f367e5232eeee4

# When image has no tag it appears you need to use sha format
podman image save --format oci-archive -o metrics-scraper.tar docker.io/kubernetesui/metrics-scraper@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf

# Loading nothing happened
minikube image load metrics-scraper.tar
-> no output
minikube image ls
-> no output

# Also nothing
minikube image load --pull kubernetesui/metrics-scraper:v1.0.4@sha256:555981a24f184420f3be0c79d4efb6c948a85cfce84034f85a563f4151a81cbf

```

## minikube image load 2

Minikube uses docker and it can't read oci-archive format images. Image load and save scps a tar to minikube. (Does not network push.)

```
podman image save --format docker-archive -o echoserver_1.10.tar gcr.io/google-containers/echoserver:1.10
minikube image load echoserver_1.10.tar
minikube image ls
```

## Minikube dashboard 503 problem

This went away by itself so I don't know what fixed it.

```bash
minikube dashboard -v 10 --log_file minikube_log.txt
minikube -v 10 --alsologtostderr dashboard
# Repeated 503 errors

# Ref https://gist.github.com/F21/08bfc2e3592bed1e931ec40b8d2ab6f5
# Neither worked. Probably doesn't apply to v1.20.
minikube start --driver kvm2 --bootstraper kubeadm
kubectl create clusterrolebinding add-on-cluster-admin --clusterrole=cluster-admin --serviceaccount=kube-system:default
```
