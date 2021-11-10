FROM golang:1.15-alpine3.12@sha256:822b464369b2efa879368b7fa046b2f6ac8773d61157345da4d22c7bc374e339 as builder

WORKDIR $GOPATH/src/github.com/thanos-io/thanos
# Change in the docker context invalidates the cache so to leverage docker
# layer caching, moving update and installing apk packages above COPY cmd
# More info https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache

RUN apk update && apk upgrade && apk add --no-cache alpine-sdk
# Replaced ADD with COPY as add is generally to download content form link or tar files
# while COPY supports the basic copying of local files into the container.
# https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#add-or-copy
COPY . $GOPATH/src/github.com/thanos-io/thanos

RUN git update-index --refresh; make build

# -----------------------------------------------------------------------------

# By default we pin to amd64 sha. Use make docker to automatically adjust for arm64 versions.
# ARG SHA="5478a46f1eb37ebe414c399766f8088bc8353345602053485dd429b9a87097e5"
FROM quay.io/prometheus/busybox@sha256:5591971699f6cf8abf6776495385e9d62751111a8cba56bf4946cf1d0de425ed
LABEL maintainer="The Thanos Authors"

COPY --from=builder /go/bin/thanos /bin/thanos

ENTRYPOINT [ "/bin/thanos" ]
