# Shark IQ (auth fix) — Home Assistant custom integration

Drop-in replacement for the built-in `sharkiq` integration that vendors
[`sharkiqlibs/sharkiq`](https://github.com/sharkiqlibs/sharkiq) `main`, which fixes
the Auth0 bot-detection failure:

```
sharkiq.exc.SharkIqAuthError: Auth0 password grant failed: 401
{"error":"requires_verification","error_description":"Suspicious request requires verification"}
```

## Why this exists

Home Assistant 2026.10.0 pins `sharkiq==1.5.0`. The upstream fix (PR
[sharkiqlibs/sharkiq#155](https://github.com/sharkiqlibs/sharkiq/pull/155), released as
`sharkiq` 1.5.1 on PyPI) replaces the browser form POST / password-grant login with
Auth0's cross-origin `/co/authenticate` endpoint plus PKCE — matching the request shape
of the real SharkClean mobile app, so Auth0 stops flagging it as suspicious.

Until HA bumps its pin, this custom component provides the fixed library.

## What it contains

- `custom_components/sharkiq/` — the Home Assistant integration files, verbatim from
  HA core `2026.10.0` (`homeassistant/components/sharkiq`), with the four
  `from sharkiq import ...` lines rewritten to `from .sharkiq import ...`.
- `custom_components/sharkiq/sharkiq/` — vendored `sharkiqlibs/sharkiq` `main`
  (includes the `/co/authenticate` fix).

No Python package requirements are declared: `aiohttp`, `requests` and `auth0-python`
are already present in the Home Assistant environment as dependencies of the
previously-installed `sharkiq` 1.5.0.

## Install

HACS → custom repository → this repo, category *Integration* → download → restart.
Existing `sharkiq` config entries are picked up automatically (same domain).

## Uninstall

Remove `custom_components/sharkiq/` and restart; the built-in integration takes over
again.
