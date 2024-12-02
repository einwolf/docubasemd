# Yum Proxy

I don't think there is a command line option.

(RHEL 5) Yum does not use HTTP_PROXY and friends.

(RHEL 7+) Yum does use HTTP_PROXY.

I haven't checked if the user:pass@host format works. In general, that would be a problem if it was needed.

## Configuration File

Add proxy to /etc/yum.conf

```ini
[main]
proxy=http://proxy.example.com:80
```

## Repo Configuration

A proxy can be set per repo section file as in the yum.conf.

```ini
[reponame]
name=Repo Name
baseurl=http://something.com/
proxy=http://proxy.example.com:80
```

No proxy is expressed as `_none_`. RHEL 8+ can also use `none`. Very early RHEL 8 does not recognize `_none_`.

```ini
[reponame]
proxy=_none_
```

## Red Hat Satellite Override

Options can be added to repos managed through subscription-manager.

```bash
subscription-manager repo-override --repo=rhel-7-server-rpms --add=gpgcheck:1
subscription-manager repo-override --repo=rhel-7-server-rpms --add=proxy:_none_
```
