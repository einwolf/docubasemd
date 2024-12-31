# Sonatype Nexus

## Community Download

https://www.sonatype.com/products/sonatype-nexus-oss-download#main-content

https://sonatype-download.global.ssl.fastly.net/repository/downloads-prod-group/3/nexus-3.73.0-12-unix.tar.gz
https://sonatype-download.global.ssl.fastly.net/repository/downloads-prod-group/3/nexus-3.73.0-12-win64.zip
https://sonatype-download.global.ssl.fastly.net/repository/downloads-prod-group/3/nexus-3.73.0-12-mac.tgz

## Install from tar

https://medium.com/@dev.gyinae/how-to-install-nexus-repository-manager-on-linux-44c38a827502

```bash
# As root

# Needs java 1.8 to 17
dnf install -y java-17-openjdk java-17-openjdk-headless java-17-openjdk-devel

# Create nexus user
adduser --no-create-home nexus

cd /opt
curl -O https://sonatype-download.global.ssl.fastly.net/repository/downloads-prod-group/3/nexus-3.73.0-12-unix.tar.gz
tar -xzvf nexus-3.73.0-12-unix.tar.gz
ln -s nexus-3.73.0-12 nexus

mkdir /opt/nexus-data
chown -Rv nexus:nexus /opt/nexus-3.73.0-12 /opt/nexus /opt/nexus-data/

# nexus.rc
vi /opt/nexus/bin/nexus.rc
# change run_as_user="nexus"

# Well that's the only setting in nexus.rc
mv -v /opt/nexus/bin/nexus.rc /opt/nexus/bin/nexus.rc.orig
cat > /opt/nexus/bin/nexus.rc << EOF
run_as_user="nexus"
EOF
chown -v nexus:nexus /opt/nexus/bin/nexus.rc

# nexus.vmoptions
cp -vp /opt/nexus/bin/nexus.vmoptions /opt/nexus/bin/nexus.vmoptions.orig
vi /opt/nexus/bin/nexus.vmoptions

# Nexus service pwd appears to be /opt/nexus.
# Data directory defaults to ../sonatype-work.
# WorkingDirectory in nexus.service doesn't change anything.

# Add/change
#-Xms1024m
#-Xmx1024m
#-XX:MaxDirectMemorySize=1024m
-XX:LogFile=/opt/nexus-data/sonatype-work/nexus3/log/jvm.log
#-XX:-OmitStackTraceInFastThrow
#-Djava.net.preferIPv4Stack=true
#-Dkaraf.home=.
#-Dkaraf.base=.
#-Dkaraf.etc=etc/karaf
#-Djava.util.logging.config.file=/etc/karaf/java.util.logging.properties
-Dkaraf.data=/opt/nexus-data/sonatype-work/nexus3
-Dkaraf.log=/opt/nexus-data/sonatype-work/nexus3/log
-Djava.io.tmpdir=/opt/nexus-data/sonatype-work/nexus3/tmp

# nexus.service
cat > /etc/systemd/system/nexus.service << EOF
# nexus.service
[Unit]
Description=Nexus Service
After=network.target

[Service]
Type=forking
WorkingDirectory=/opt/nexus-data/
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl start nexus
# Runs on http://localhost:8081
cat /opt/nexus-3.73.0-12/sonatype-work/nexus3/admin.password
cat /opt/nexus-data/sonatype-work/nexus3/admin.password
```

## Install from yum repo

https://github.com/sonatype-nexus-community/nexus-repository-installer

```bash
sudo wget -P /etc/yum.repos.d/ https://repo.sonatype.com/repository/community-hosted/rpm/sonatype-community.repo
dnf install -y nexus-repository-manager
# Errors saying gpg key in repo is not valid for nexus-repository-manager
```

## Nexus configuration

## HTTPS

Nexus doesn't make self signed certificates.
Need to go through java keystore to add TLS certificates.

Docs recommend using an HTTPS proxy.

## LDAP

Add the ldap certificate and ???

## Docker repo

Enable HTTPS connector on 58081 to allow pull/push to localhost:58081.

The Allow anonymous docker pull ( Docker Bearer Token Realm required ) means to go to
Security / Realms and add Docker Bearer Token Realm to active realms.

Setup a Cleanup Policy for docker format to delete images older than x days.
A cleanup unused docker image labels task is configured by default.

Nexus path is not usable for docker.
http://localhost:8081/repository/docker-repo1/

## registry.conf example

```bash
cat > /etc/containers/registries.conf.d/dockerhost1.conf << EOF
location = "dockerhost1"
insecure = true
blocked = false
EOF
```

```bash
# Test
# Authentication need for push
# What's with not honoring insecure = true
podman login --tls-verify=false dockerrepo1:58081
podman pull quay.io/podman/hello
podman tag quay.io/podman/hello:latest dockerrepo1:58081/docker-repo1/hello:latest
podman push --tls-verify=false dockerrepo1:58081/docker-repo1/hello:latest
```
