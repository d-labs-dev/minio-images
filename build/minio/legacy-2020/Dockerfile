FROM --platform=$BUILDPLATFORM golang:1.15-alpine AS builder
ARG TARGETARCH

ENV GOPATH=/go \
    CGO_ENABLED=0 \
    GO111MODULE=on

RUN apk add --no-cache git
RUN git init /go/minio \
    && cd /go/minio \
    && git remote add origin https://github.com/minio/minio.git \
    && git fetch --depth=1 origin 7fdffa03638bbfc008631c404578b0d191f02792 \
    && git checkout --detach FETCH_HEAD \
    && test "$(git rev-parse HEAD)" = 7fdffa03638bbfc008631c404578b0d191f02792 \
    && test "$TARGETARCH" = amd64 \
    && SOURCE_DATE="$(git show -s --format=%cI HEAD)" \
    && GOARCH="$TARGETARCH" go install -v -ldflags "$(go run buildscripts/gen-ldflags.go "$SOURCE_DATE")" \
    && if [ ! -x /go/bin/minio ] && [ -x /go/bin/linux_amd64/minio ]; then mv /go/bin/linux_amd64/minio /go/bin/minio; fi

FROM alpine:3.12

ENV MINIO_ACCESS_KEY_FILE=access_key \
    MINIO_SECRET_KEY_FILE=secret_key \
    MINIO_KMS_MASTER_KEY_FILE=kms_master_key \
    MINIO_SSE_MASTER_KEY_FILE=sse_master_key \
    MINIO_UPDATE_MINISIGN_PUBKEY=RWTx5Zr1tiHQLwG9keckT0c45M3AGeHD6IvimQHpyRywVWGbP1aVSGav

EXPOSE 9000

COPY --from=builder /go/bin/minio /usr/bin/minio
COPY --from=builder /go/minio/CREDITS /third_party/
COPY --from=builder /go/minio/dockerscripts/docker-entrypoint.sh /usr/bin/docker-entrypoint.sh

RUN apk add --no-cache ca-certificates 'curl>7.61.0' 'su-exec>=0.2' \
    && echo 'hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4' >> /etc/nsswitch.conf

ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]
VOLUME ["/data"]
CMD ["minio"]
