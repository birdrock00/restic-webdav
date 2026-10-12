# restic + WebDAV daily builds

This repository is a fork of [restic](https://github.com/restic/restic), used
to publish a container image that supports WebDAV repositories.

restic accesses WebDAV servers through its built-in `rclone` backend, but the
official restic image does not ship the `rclone` binary, so WebDAV repositories
fail there with "invalid backend". This project publishes
`ghcr.io/<owner>/restic-webdav`: the **official upstream restic image** plus
the `rclone` binary (plus `curl`, which the official image also lacks and which
backup-verification jobs need to POST results to a dashboard). The restic side
is therefore always exactly the current
upstream build - nothing is recompiled.

## Usage

```sh
export RCLONE_CONFIG_MYBOX_TYPE=webdav
export RCLONE_CONFIG_MYBOX_VENDOR=hetzner        # or another webdav vendor
export RCLONE_CONFIG_MYBOX_URL=https://box.example.com
export RCLONE_CONFIG_MYBOX_USER=u123456
export RCLONE_CONFIG_MYBOX_PASS="$(rclone obscure 'secret')"

restic -r rclone:MYBOX:restic/myrepo backup ./data
```

## Daily workflow

`.github/workflows/daily-webdav-image.yml` runs every day:

1. merges upstream `master` into this fork (keeps docs/workflow current),
2. resolves the current upstream restic version,
3. builds and pushes a multi-arch (amd64/arm64) image with tags
   `latest`, `<restic-version>` and `<restic-version>-<YYYYMMDD>`,
4. publishes/publishes-daily a GitHub release describing the images.

## Manual trigger

```sh
gh workflow run "Daily webdav image"
```
