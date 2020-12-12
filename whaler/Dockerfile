# Base image for building static binary
FROM	golang:alpine3.12 AS builder

# Install git
RUN	apk --no-cache add git

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/P3GLEG/Whaler


# Build from scratch for smallest possible secure build
FROM	scratch

# Copy static binary into image
COPY	--from=builder /go/bin/Whaler /Whaler

# Define our entrypoint
ENTRYPOINT	["/Whaler"]

