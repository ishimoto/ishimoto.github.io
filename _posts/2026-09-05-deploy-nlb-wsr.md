---
published: true
layout: post
title:  "Legacy Deploy — NLB + WSR behind Apache"
author: ishimoto
date:   2026-09-05
categories: Deploy
tags: Deploy
---
---

If you came from WebObjects, this deployment model needs no introduction: a `.woa`
bundle on an application server, its static files on a web server, and Apache in
front joining the two. TreasureBoat still builds exactly that, and a good number of
production boxes still run it.

This post writes it down properly — what the two builds contain, why their
timestamps must match, and the one property that decides whether your CSS loads.

Newer options exist and later posts will cover them. This one is the old road, and
it is still paved.

---
---

## Two builds, one timestamp

The legacy model splits an application in half.

| | **Legacy Build (NLB)** | **WebServerResources Build (WSR)** |
|---|---|---|
| Goes to | the application server | the web server |
| Contains | JARs, Resources, `.wo` components, run scripts | static files only — css, js, images, fonts |
| Frameworks at | `Contents/Frameworks/` | `Frameworks/` (root level) |
| Archive | `App-1.0.0-20260905-1030.woa.tar.gz` | `App-WebServerResources-1.0.0-20260905-1030.woa.tar.gz` |

Both extract to a folder with **the same name**. That is the whole trick, and it is
why the order matters:

1. **Create Legacy Build** first. It establishes the version and the timestamp.
2. **Create WebServerResources Build** second. It finds the legacy build and reuses
   that timestamp.

Rebuild the application and you must rebuild the resources too. If the timestamps
drift apart, the application emits URLs containing one folder name while Apache is
serving another, and every stylesheet 404s while the application itself works
perfectly. Nothing in the logs will mention a timestamp.

> The IDE will tell you if you get the order wrong — the WSR action refuses to run
> without a legacy build to take the timestamp from.

---
---

## What is actually in the WSR bundle

Not just your application's files. TreasureBoat frameworks carry their own static
assets inside their JARs, and the WSR build extracts them:

```
App-1.0.0-20260905-1030.woa/
├── Contents/
│   └── WebServerResources/        # your app: css, images, javascript, favicon
└── Frameworks/                     # note: root level, not Contents/
    ├── tb-core-skin-keen.framework/
    │   └── WebServerResources/
    ├── tb-archive-jquery.framework/
    │   └── WebServerResources/
    └── tb-core-foundation.framework/   # empty if the framework ships no assets
```

The `Frameworks/` folder sits at the **root** here, while the legacy build keeps its
frameworks under `Contents/`. That difference is deliberate and it matches the URLs
the application generates.

---
---

## The deploy configuration

A project's `.treasureboat` file describes both halves, and the Deploy editor edits
it:

```
deploy.server.app01.host=159.XXX.XXX.XXX
deploy.server.app01.user=centos
deploy.server.app01.base=/opt/TreasureBoat
deploy.server.app01.build=LEGACY
deploy.server.apache.host=159.XXX.XXX.XXX
deploy.server.apache.build=LEGACY
deploy.target.live.servers=app01\:app, apache\:wsr
```

Two servers, two roles. `app01:app` takes the application, `apache:wsr` takes the
static resources. They can be the same physical machine — often are — but they stay
separate roles, because the files land in different places and are served by
different processes.

By hand it is two copies and two extractions:

```bash
scp target/deploy/App-*-Application.tar.gz         user@app01:/opt/TreasureBoat/Applications/
scp target/deploy/App-*-WebServerResources.tar.gz  user@apache:/var/www/webobjects/

ssh user@app01 'cd /opt/TreasureBoat/Applications && tar -xzf App-*-Application.tar.gz'
ssh user@apache 'cd /var/www/webobjects   && tar -xzf App-*-WebServerResources.tar.gz'
```

---
---

## Apache: serve the files, proxy the rest

Two jobs. Serve the static bundle from disk, and pass everything else to the running
application.

```apache
# 1. The static half — served straight from disk, never touching Java
Alias /WebObjects/App-1.0.0-20260905-1030.woa /var/www/webobjects/App-1.0.0-20260905-1030.woa

<Directory /var/www/webobjects/App-1.0.0-20260905-1030.woa>
    Options FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

# 2. The application half — reverse proxy to the instance
ProxyPreserveHost On
ProxyPass        /TB/App http://127.0.0.1:8081/TB/App
ProxyPassReverse /TB/App http://127.0.0.1:8081/TB/App
```

Three things worth knowing about that:

* **`ProxyPreserveHost On`** — without it the application sees Apache's hostname
  instead of the one the browser asked for, and every absolute URL it generates
  points at the wrong host.
* **`ProxyPassReverse`** — rewrites redirects coming back out. Skip it and a login
  redirect sends the browser to `127.0.0.1:8081`.
* **No blanket `ProxyPass /`.** Proxy only the application's path prefix. A catch-all
  swallows `/.well-known/`, which is how Let's Encrypt renews your certificate — the
  failure arrives ninety days later, which is a long time to wait to learn something.

The `Alias` path contains the version and timestamp, so it changes with every
release. That is not an oversight: it is a cache-busting scheme that predates
cache-busting having a name. Old builds keep working while the new one goes up, and
you can point Apache back at yesterday's folder in one line.

---
---

## The property that decides whether your CSS loads

This is the part that catches people, and it has nothing to do with Apache being
misconfigured.

TreasureBoat decides *at runtime* what kind of URL to emit for a resource:

* **Web-server URLs** (`/WebObjects/App-….woa/…`) — when the request looks like it
  came through a web server adaptor. Apache serves these from the WSR bundle. This is
  what you want in the setup above.
* **App-served URLs** (`/wr?wodata=…`) — the application serves its own resources out
  of the JARs. Slower, but it needs no web server at all.

The switch is:

```properties
org.treasureboat.resources.selfServe=false   # web server serves them (NLB + WSR)
org.treasureboat.resources.selfServe=true    # the app serves them (plain reverse proxy)
```

So:

* **Deploying NLB + WSR as above?** Leave it `false`. The application emits
  `/WebObjects/…` URLs and Apache serves them from disk.
* **Plain `mod_proxy` with no WSR bundle?** Set it `true`. Otherwise the application
  emits web-server URLs for files that were never copied to the web server, and you
  get a working application with no styling whatsoever.

That second case is the one that wastes an afternoon, because the application is fine,
Apache is fine, and the only symptom is an unstyled page.

> A detail if you deploy behind a cloud load balancer: the check for "did this come
> through a web server" is header-based, and AWS's ALB adds `x-amzn-trace-id`, which
> is enough to make a direct request look proxied. On such a box, `selfServe=true` is
> the reliable answer.

---
---

## Is this still the right choice?

Sometimes, honestly.

It is the right choice when the web server is already there and already serving other
things, when you want static files off the Java process entirely, and when the
operations team knows this shape and would rather not learn another one.

It is the wrong choice when you are standing up something new. The newer build types
carry their dependencies with them and need far less from the server — no Apache
required, no two-halves-must-match discipline, no timestamp to keep in step. Those
are the next posts.

But if you have run WebObjects, you already know this model, and TreasureBoat still
builds it. Nothing here has been deprecated out from under you.
