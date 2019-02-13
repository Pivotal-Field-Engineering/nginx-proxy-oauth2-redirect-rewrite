# Arg Rewrite with Proxy Redirect POC

This is meant to illustrate a way to use a transparent nginx proxy to rewrite the `redirect_uri` that is incorporated into an OAuth2.0 handshake.

Recreate:

```
docker-compose up --timeout 1 --no-build -d
docker-compose run curl curl http://proxy?redirect_uri=some.domain.com
```