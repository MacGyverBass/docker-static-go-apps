# Base image for building static binary
FROM	macgyverbass/base-label:golang AS builder

# Install git & upx
RUN	apk --no-cache add git upx

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/jpillora/webproc

# Compress the binary
RUN	upx -9 /go/bin/webproc


# Build from scratch for smallest possible secure build
FROM	macgyverbass/base-label:scratch

# Copy static binary into image
COPY	--from=builder /go/bin/webproc /webproc

# Define our entrypoint
ENTRYPOINT ["/webproc"]

# Define the ports used
EXPOSE	8080/tcp

