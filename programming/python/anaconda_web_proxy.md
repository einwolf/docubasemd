# Conda web proxy (untested)

Use dotcondarc in your user directory. This is a yaml format file.

```powershell
%userprofile%/.condarc
```

```yaml
proxy_servers:
    http: http://[username:password@]contractorproxyeast.northgrum.com:80
    https: https://[username:password@]contractorproxyeast.northgrum.com:80
```

Probably need to set ssl_verify when proxy messes with certificates.

```yaml
channels:
  - admin

show_channel_urls: true
allow_other_channels: true

proxy_servers:
  http: http://domainname\username:password@proxyserver:port
  https: http://domainname\username:password@proxyserver:port

ssl_verify: true
```
