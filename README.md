# Pinned MinIO images

## Purpose

This repository builds reproducible, pinned container images for historical MinIO Community Edition versions whose upstream container images are no longer reliably available. It is not affiliated with or maintained by MinIO.

## Published images

- `ghcr.io/d-labs-dev/minio:RELEASE.2020-10-28T08-16-50Z-51-g7fdffa036`
- `ghcr.io/d-labs-dev/minio:RELEASE.2025-09-07T16-13-09Z`
- `ghcr.io/d-labs-dev/minio-mc:RELEASE.2025-08-13T08-35-41Z`

No `latest` tag is published.

## Provenance

- Legacy MinIO is compiled from exact source commit `7fdffa03638bbfc008631c404578b0d191f02792`.
- The 2025 server and mc images use exact official GitHub release artifacts. Before inclusion, the binaries are verified using pinned SHA256 hashes and Minisign.
- Exact versions, source revisions, artifact URLs, and verification details are recorded in the Dockerfiles.
- GitHub Actions builds these images and publishes them to GHCR.

## Architectures

| Image | Architectures |
| --- | --- |
| Legacy 2020 MinIO | `linux/amd64` |
| MinIO server 2025 | `linux/amd64`, `linux/arm64` |
| MinIO mc 2025 | `linux/amd64`, `linux/arm64` |

## Reproduction

The manually triggered GitHub Actions workflow rebuilds the pinned images from the documented sources. Run **Publish MinIO** from the Actions tab to publish them.
