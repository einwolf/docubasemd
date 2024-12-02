# Docker with web proxy

Reference https://docs.docker.com/network/proxy/

## No global config in daemon.json

`/etc/docker/daemon.json` does not have proxy configuration commands.

## Client environment variables

This sets the proxy environment variables into containers. It does not allow the docker command to use the proxy.

`~/.docker/config.json`

```json
{
    "proxies": {
        "default": {
                "httpProxy": "http://proxy.example.com:80",
                "httpsProxy": "http://proxy.example.com:80",
                "noProxy": "127.0.0.0/8,localhost"
        }
    }
}
```

## Docker environment variables

The docker command reads `HTTP_PROXY` `HTTPS_PROXY` `HTTPS_PROXY` `FTP_PROXY` and `NO_PROXY` (and lowercase variants).

### Configure the proxy in service file

Add environment variables to docker service file.

```bash
mkdir -p /etc/systemd/system/docker.service.d
nano /etc/systemd/system/docker.service.d/http-proxy.conf
```

```ini
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80"
Environment="HTTPS_PROXY=http://proxy.example.com:80"
Environment="127.0.0.0/8,localhost"
```

## Fedora 35

Docker through package moby-engine-20.10.12 is not passing through HTTP_PROXY and other environment variables. It needed config.json setup.
