# Cache headers for a static site on your own server: hashed assets versus HTML

When you host a static site yourself, cache headers are your responsibility. The rule is simple: cache files whose names change on every build forever, and do not cache the files whose names never change.

## Fingerprinted assets

Build tools output names like `app.3f9a2c.js`. A new build produces a new name, so the old file can be cached indefinitely:

```
Cache-Control: public, max-age=31536000, immutable
```

## HTML

`index.html` keeps the same name and references the new asset names. If browsers cache it, visitors keep loading old assets after a deploy:

```
Cache-Control: no-cache
```

`no-cache` still allows caching but forces revalidation with the server, which is cheap with ETags.

## Caddy example

```
example.com {
  root * /srv/site
  @assets path /assets/*
  header @assets Cache-Control "public, max-age=31536000, immutable"
  header /*.html Cache-Control "no-cache"
  encode zstd gzip
  file_server
}
```

## Nginx example

```
location /assets/ { add_header Cache-Control "public, max-age=31536000, immutable"; }
location / { add_header Cache-Control "no-cache"; try_files $uri $uri/ /index.html; }
```

## Unhashed files

Images in a public folder without fingerprints deserve a moderate lifetime, such as a day, so updates appear reasonably fast.

## Verify

`curl -I https://example.com/assets/app.3f9a2c.js` should show the long lifetime and the HTML should show `no-cache`.

The guide to [deploy a static site on your own server](https://peon.sh/blogs/deploy-static-site-own-server) covers TLS and deploy steps. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
