# Arg Rewrite with Proxy Redirect POC

This is meant to illustrate a way to use a transparent nginx proxy to rewrite the `redirect_uri` that is incorporated into an OAuth2.0 handshake.

Recreate:

```
docker-compose up --timeout 1 --no-build -d
docker-compose run curl "http://pass?redirect_uri=some.domain.com" -v
```

Expected output should be the following:

```
* Rebuilt URL to: http://pass/?redirect_uri=some.domain.com
*   Trying 172.21.0.3...
* TCP_NODELAY set
* Connected to pass (172.21.0.3) port 80 (#0)
> GET /?redirect_uri=some.domain.com HTTP/1.1
> Host: pass
> User-Agent: curl/7.61.0
> Accept: */*
> 
< HTTP/1.1 302 Moved Temporarily
< Server: nginx
< Date: Wed, 13 Feb 2019 02:07:42 GMT
< Content-Type: text/html
< Content-Length: 138
< Connection: keep-alive
< Location: http://pass//?redirect_uri=other.domain.com
< 
<html>
<head><title>302 Found</title></head>
<body>
<center><h1>302 Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
* Connection #0 to host pass left intact
```

Note that `some.domain.com` has been changed to `other.domain.com` in the `Location` of the redirect.

This is achieved with two blocks in `pass.conf`:
```
if ($args ~* "(.*)(some\.domain\.com)(.*)") {
    set $args "$1other.domain.com$3";
}
```
and
```
proxy_redirect "(.*)(some.domain.com)(.*)" "$1other.domain.com$3";
```