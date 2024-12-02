# Kubernetes demo apps

```text
docker.io/kicbase/echo-server:1.0

Only thing in image is /bin/echo-server
It prints out headers from request
podman run -p 8080:8080 kicbase/echo-server:1.0

HTTP/1.1 GET /

Host: localhost:8080
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.5
Connection: keep-alive
Dnt: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/116.0
```

```text
Other echoservers
gcr.io/google-containers/echoserver
```
