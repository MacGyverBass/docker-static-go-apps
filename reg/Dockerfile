# Base image for building static binary
FROM	golang:alpine3.12 AS builder

# Install git and ca-certificates
RUN	apk --no-cache add git ca-certificates

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/genuinetools/reg


# Build from scratch for smallest possible secure build
FROM	scratch

# Copy static binary and ca-certificates.crt into image
COPY	--from=builder /go/bin/reg /reg
COPY	--from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Define our entrypoint
ENTRYPOINT	["/reg"]
