# Red Hat Satellite proxy

Add options during the install.

Also see https://access.redhat.com/solutions/1122203 for adding the proxy manually for pulp if the installer doesn't work correctly.

```bash
satellite-installer --help | grep katello-proxy
    --katello-proxy-password      Proxy password for authentication (current: UNDEF)
    --katello-proxy-port          Port the proxy is running on (current: UNDEF)
    --katello-proxy-url           URL of the proxy server (current: UNDEF)
    --katello-proxy-username      Proxy username for authentication (current: UNDEF)
```
