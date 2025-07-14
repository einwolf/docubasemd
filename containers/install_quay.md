# Project Quay Install

Github has source code.

https://github.com/quay/quay/releases

Container images

https://quay.io/repository/projectquay/

https://quay.io/repository/projectquay/quay

https://quay.io/repository/projectquay/clair

## Development Local Install

Local install requires building quay plus configuring redis and postgresql.

https://github.com/quay/quay/blob/master/docs/quick-local-deployment.md

Development install

https://github.com/quay/quay/blob/master/docs/getting-started.md#running-quay-for-development

The local-dev system uses a docker compose file.

https://github.com/quay/quay/blob/master/docker-compose.yaml

## Container Install

The quay docs "proof of concept" install uses a multi container podman setup.

https://docs.projectquay.io/deploy_quay.html

https://docs.redhat.com/en/documentation/red_hat_quay/3.15/html/proof_of_concept_-_deploying_red_hat_quay/index

## Podman

```bash
# Project Quay guide uses sudo, so trying as root.
# $QUAY is /opt/quay

cd /opt
mkdir quay-3.15.0
ln -s quay-3.15.0 quay
mkdir -p quay/postgres-quay
mkdir -p quay/config
mkdir -p quay/storage

setfacl -m u:26:-wx /opt/quay/postgres-quay
setfacl -m u:1001:-wx /opt/quay/storage

# $QUAY = /opt/quay
podman run -d --rm --name postgresql-quay \
  -e POSTGRESQL_USER=quayuser \
  -e POSTGRESQL_PASSWORD=quaypass \
  -e POSTGRESQL_DATABASE=quay \
  -e POSTGRESQL_ADMIN_PASSWORD=adminpass \
  -p 5432:5432 \
  -v /opt/quay/postgres-quay:/var/lib/pgsql/data:Z \
  docker.io/centos/postgresql-10-centos7@sha256:de1560cb35e5ec643e7b3a772ebaac8e3a7a2a8e8271d9e91ff023539b4dfb33

podman exec -it postgresql-quay /bin/bash -c 'echo "CREATE EXTENSION IF NOT EXISTS pg_trgm" | psql -d quay -U postgres'

podman run -d --rm --name redis \
  -p 6379:6379 \
  -e REDIS_PASSWORD=strongpassword \
  docker.io/centos/redis-32-centos7@sha256:06dbb609484330ec6be6090109f1fa16e936afcf975d1cbc5fff3e6c7cae7542

# Doing config.yaml

# Quay must use 8080,8443 internally
podman run -d --rm -p 8082:8080 -p 8482:8443  \
   --name=quay \
   -v /opt/quay/config:/conf/stack:Z \
   -v /opt/quay/storage:/datastorage:Z \
   quay.io/projectquay/quay:v3.15.0

```

## Podman-compose

Use the local-dev docker-compose but use images from quay.io instead of compiling.
Also going to use podman kube play instead of docker compose.

https://github.com/quay/quay/blob/master/docker-compose.yaml

```bash
mkdir quay-compose
cd quay-compose

curl -O https://raw.githubusercontent.com/quay/quay/refs/heads/master/docker-compose.yaml
cp docker-compose.yaml compose.yaml
mv docker-compose.yaml docker-compose.yaml.orig

# Edit compose.yaml and replace 

podman-compose up
```

## Quay config.yaml

```yaml
BUILDLOGS_REDIS:
    host: {{ quayhost }}
    password: strongpassword
    port: 6379
CREATE_NAMESPACE_ON_PUSH: true
DATABASE_SECRET_KEY: a8c2744b-7004-4af2-bcee-e417e7bdd235
DB_URI: postgresql://quayuser:quaypass@{{ quayhost }}:5432/quay
DISTRIBUTED_STORAGE_CONFIG:
    default:
        - LocalStorage
        - storage_path: /datastorage/registry
DISTRIBUTED_STORAGE_DEFAULT_LOCATIONS: []
DISTRIBUTED_STORAGE_PREFERENCE:
    - default
FEATURE_MAILING: false
SECRET_KEY: e9bd34f4-900c-436a-979e-7530e5d74ac8
SERVER_HOSTNAME: {{ quayhost }}
SETUP_COMPLETE: true
USER_EVENTS_REDIS:
    host: {{ quayhost }}
    password: strongpassword
    port: 6379
SUPER_USERS:
  - quayadmin
```

## Testing

Self create a user using the create user button on the quay page.
http://{{ quayhost }}:8082/

TLS certificates need to be created for port 443 to work.
http://{{ quayhost }}:8482/

```bash
podman login --tls-verify=false {{ quayhost }}:8082
podman pull quay.io/podman/hello
podman tag quay.io/podman/hello:latest {{ quayhost }}:8082/quayadmin/hello:latest
podman push --tls-verify=false {{ quayhost }}:8082/quayadmin/hello:latest
```
