FROM alpine:3.21 AS verified
ARG TARGETARCH
ARG MC_RELEASE=RELEASE.2025-08-13T08-35-41Z
ARG MINISIGN_PUBLIC_KEY=RWTx5Zr1tiHQLwG9keckT0c45M3AGeHD6IvimQHpyRywVWGbP1aVSGav
RUN apk add --no-cache curl minisign
WORKDIR /tmp/mc
RUN set -eu; \
    case "$TARGETARCH" in \
      amd64) expected=01f866e9c5f9b87c2b09116fa5d7c06695b106242d829a8bb32990c00312e891; arch=amd64 ;; \
      arm64) expected=14c8c9616cfce4636add161304353244e8de383b2e2752c0e9dad01d4c27c12c; arch=arm64 ;; \
      *) echo "Unsupported TARGETARCH: $TARGETARCH" >&2; exit 1 ;; \
    esac; \
    base="https://github.com/minio/mc/releases/download/${MC_RELEASE}/mc.linux-${arch}.${MC_RELEASE}"; \
    curl --fail --show-error --silent --location "$base" -o mc; \
    curl --fail --show-error --silent --location "$base.sha256sum" -o mc.sha256sum; \
    curl --fail --show-error --silent --location "$base.minisig" -o mc.minisig; \
    reported="$(awk 'NR == 1 { print $1 }' mc.sha256sum)"; \
    test "$reported" = "$expected"; \
    printf '%s  mc\n' "$expected" | sha256sum -c -; \
    minisign -Vm mc -x mc.minisig -P "$MINISIGN_PUBLIC_KEY"; \
    chmod 0755 mc

FROM alpine:3.21
ARG MC_RELEASE=RELEASE.2025-08-13T08-35-41Z
COPY --from=verified /tmp/mc/mc /usr/bin/mc
RUN /usr/bin/mc --version | grep -F "${MC_RELEASE}"
ENTRYPOINT ["/usr/bin/mc"]
