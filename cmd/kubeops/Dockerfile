# syntax = docker/dockerfile:1

FROM bitnami/golang:1.16.3 as builder
WORKDIR /go/src/github.com/kubeapps/kubeapps
COPY go.mod go.sum ./
COPY pkg pkg
COPY cmd cmd
ARG VERSION
# With the trick below, Go's build cache is kept between builds.
# https://github.com/golang/go/issues/27719#issuecomment-514747274
RUN --mount=type=cache,target=/go/pkg/mod \
  --mount=type=cache,target=/root/.cache/go-build \
  CGO_ENABLED=0 go build -installsuffix cgo -ldflags "-X main.version=$VERSION" ./cmd/kubeops

FROM scratch
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /usr/share/zoneinfo /usr/share/zoneinfo
COPY --from=builder /go/src/github.com/kubeapps/kubeapps/kubeops /kubeops
EXPOSE 8080
USER 1001
CMD ["/kubeops"]
