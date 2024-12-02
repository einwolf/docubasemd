# Python web proxy

The distribution from http://www.python.org/

Cygwin python also follows this.

## Pip 19

Pip 19.2 is working ok without having proxy configured in pip.ini. Maybe it picks up the system proxy. The proxy (firewall) messes with the SSL certificates and needs the trustedhosts configured.

## Environment Variables

pip uses HTTP_PROXY and such even on Windows.

## Command Line

```bash
pip install -–proxy=user:pass@localhost:3128 package
pip install --proxy https://user:password@mydomain:port package
```

## Pip Configuration Location

Also see pip config command.

```ini
%APPDATA%\pip\pip.ini
```

## Sample pip.ini

Use URL syntax.

```ini
[global]
proxy = [user:password@]proxy.server:port
```

```ini
[global]
proxy = localhost:3128
trusted-host = pypi.python.org
               pypi.org
               files.pythonhosted.org
```
