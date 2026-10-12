# restic + webdav daily builds

This repository is a fork of [restic](https://github.com/restic/restic). The
upstream source is left untouched; it only adds daily builds that include the
optional `webdav` backend, which is **not** compiled into the official restic
binaries or container images (official builds use the `selfupdate` build tags
only).

## What gets added

* `Dockerfile.webdav` - Alpine-based image containing the restic binary built
  with the `webdav` build tag.
* `.github/workflows/daily-webdav-image.yml` - daily workflow that:
  1. merges `master` from the upstream restic repository,
  2. builds `linux/amd64` and `linux/arm64` binaries with
     `go run build.go -t webdav` (upstream default tags plus `webdav`),
  3. builds and pushes a multi-arch container image to the GitHub Container
     Registry (`:latest`, `:<restic-version>`, `:<restic-version>-<YYYYMMDD>`),
  4. publishes a daily GitHub release with the binaries.

## Usage

The image is a drop-in replacement for the official `restic/restic` image:

```sh
restic -r "webdav(host.example.com:443)/./myrepo" backup ./data
```

with `WEBDAV_USERNAME` and `WEBDAV_PASSWORD` set in the environment.

## Manual trigger

Run the workflow from the Actions tab (`Daily webdav image`, `Run workflow`)
or:

```sh
gh workflow run "Daily webdav image"
```
