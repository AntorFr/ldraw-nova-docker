# About this fork

This is a downstream fork of [anteloc/ldraw-nova-docker](https://github.com/anteloc/ldraw-nova-docker).
Its only purpose so far is to **publish a ready-to-run image** so the web app can run on a
Kubernetes cluster instead of being built locally with `docker compose`.

## What differs from upstream

- `main` follows upstream **releases**, not upstream `master`: it starts at upstream tag
  `v0.6.0`.
- `.github/workflows/docker-publish.yml` builds the image and publishes it to
  `ghcr.io/antorfr/ldraw-nova-docker`. The sibling toolkit
  ([anteloc/ldraw-nova](https://github.com/anteloc/ldraw-nova)) is checked out at
  `NOVA_REF` and passed as the `nova` build context, exactly as the upstream README does
  with `docker compose`.
- The image is **amd64 only**: LeoCAD publishes x86_64-only Linux AppImages.

Nothing else is changed: the Dockerfile, the web app and the runtime behaviour are upstream's.

## Versioning

A tag `vX.Y.Z` on this fork publishes `ghcr.io/antorfr/ldraw-nova-docker:X.Y.Z` (plus
`X.Y`, `X`, `sha-…`). Image `X.Y.Z` packages upstream release `vX.Y.Z` of both repos.

## Following an upstream release

```sh
git fetch upstream --tags
git merge vX.Y.Z                 # the new upstream release
# set NOVA_REF: vX.Y.Z in .github/workflows/docker-publish.yml
git tag vX.Y.Z && git push origin main vX.Y.Z
```

## Running it

The app has **no login**. On a cluster, keep it on a trusted network and mount two
persistent volumes: `/data` (models, chats, agents' work folders) and `/config` (LLM
settings, API keys and browser sessions).

The OpenAI (ChatGPT) browser sign-in expects its callback on the browser machine's
`localhost:1455`, relayed by the container on port 1456. On Kubernetes, forward it for the
time of the sign-in:

```sh
kubectl -n <namespace> port-forward deploy/<release> 1455:1456
```

## License

Same as upstream: AGPL-3.0 (see `LICENSE`), CC-BY-SA-4.0 for the content noted in
`CC-BY-SA-4.0`.
