# Subscription Manager use web proxy

## Set subscription-manager to use proxy

This is used on the satellite server so it can reach access.redhat.com to download repos.

See https://access.redhat.com/solutions/57669

Subscription-manager sets the proxy in each entry of /etc/yum.repos.d/redhat.conf

```bash
subscription-manager config --server.proxy_hostname=proxy.example.com --server.proxy_port=8080 --server.proxy_user=admin --server.proxy_password=secret
```

/etc/rhsm/rhsm.conf

```bash
# an http proxy server to use
proxy_hostname =

# port for http proxy server
proxy_port =

# user name for authenticating to an http proxy, if needed
proxy_user =

# password for basic http proxy auth, if needed
proxy_password =

```

## Override subscribed repo to not use proxy

This is useful on clients where there are internal repos (satellite server) and external internet repos (EPEL and everything else) used at the same time.

Subscription-manager can add arbitrary options to the repo configuration.

```bash
subscription-manager repo-override --list
subscription-manager repo-override --repo=rhel-7-server-rpms --add=proxy:_none_
```
