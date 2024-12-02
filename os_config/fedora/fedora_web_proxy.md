# Linux web proxy

## It's Really More of a Convention

A convention without any real standards. Programs may support more features (such as * in NO_PROXY). That works great until using a program that doesn't support that and it causes an error every time it's run.

Most programs search through all the variables. Lowercase variant is more popular. Curl uses only the lowercase names or something.

NO_PROXY matches ''DNS host name substrings''. Wildcard (ex 192.168.1.*) is nonstandard. Programs written in go parse CIDR network syntax. (ex 127.0.0.0/8)

There is also an `FTP_PROXY`.

## Example

Use URL syntax for authenticated proxies. `http://user:password@proxy.example.com`

```bash
export http_proxy=”http://proxy.example.com:80/”
export https_proxy=” http://proxy.example.com:80/”
export no_proxy=”127.0.0.0/8,localhost”

export HTTP_PROXY="${http_proxy}"
export HTTPS_PROXY="${https_proxy}"
export NO_PROXY="${no_proxy}"
```
