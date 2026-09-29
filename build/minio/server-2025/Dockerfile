FROM alpine:3.21 AS verified
ARG TARGETARCH
ARG MINIO_RELEASE=RELEASE.2025-09-07T16-13-09Z
ARG MINISIGN_PUBLIC_KEY=RWTx5Zr1tiHQLwG9keckT0c45M3AGeHD6IvimQHpyRywVWGbP1aVSGav
RUN apk add --no-cache curl minisign
WORKDIR /tmp/minio
RUN set -eu; \
    case "$TARGETARCH" in \
      amd64) expected=7c5bd8512c6e966455b1d198209358b2d191c77a83ab377c4073281065fb855f; arch=amd64 ;; \
      arm64) expected=5c83cd2cf151717ba0243f73e1c7802ff36e272b67144bdd7f1f7d684fd6f03d; arch=arm64 ;; \
      *) echo "Unsupported TARGETARCH: $TARGETARCH" >&2; exit 1 ;; \
    esac; \
    base="https://github.com/minio/minio/releases/download/${MINIO_RELEASE}/minio.linux-${arch}.${MINIO_RELEASE}"; \
    curl --fail --show-error --silent --location "$base" -o minio; \
    curl --fail --show-error --silent --location "$base.sha256sum" -o minio.sha256sum; \
    curl --fail --show-error --silent --location "$base.minisig" -o minio.minisig; \
    reported="$(awk 'NR == 1 { print $1 }' minio.sha256sum)"; \
    test "$reported" = "$expected"; \
    printf '%s  minio\n' "$expected" | sha256sum -c -; \
    minisign -Vm minio -x minio.minisig -P "$MINISIGN_PUBLIC_KEY"; \
    chmod 0755 minio

FROM alpine:3.21
ARG MINIO_RELEASE=RELEASE.2025-09-07T16-13-09Z
COPY --from=verified /tmp/minio/minio /usr/bin/minio
RUN /usr/bin/minio --version | grep -F "${MINIO_RELEASE}"
ENTRYPOINT ["/usr/bin/minio"]
CMD ["server", "/data"]
VOLUME ["/data"]
EXPOSE 9000
