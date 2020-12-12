# Base image for building static binary
FROM	golang:alpine3.12 AS builder

# Install git
RUN	apk --no-cache add git

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/jpillora/webproc


# Build from scratch for smallest possible secure build
FROM	scratch

# Copy static binary into image
COPY	--from=builder /go/bin/webproc /webproc

# Define our entrypoint
ENTRYPOINT ["/webproc"]

# Define the ports used
EXPOSE	8080/tcp

