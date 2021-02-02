# Base image for building static binary
FROM	macgyverbass/base-label:golang AS builder

# Install git & upx
RUN	apk --no-cache add git upx

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/starius/pasta/gopasta

# Compress the binary
RUN	upx -9 /go/bin/gopasta


# Build from scratch for smallest possible secure build
FROM	macgyverbass/base-label:scratch

# Copy static binary into image
COPY	--from=builder /go/bin/gopasta /pasta

# Define our entrypoint
ENTRYPOINT	["/pasta", "-dir", "/db/"]

# Volumes used by image
VOLUME	["/db/"]

# Define the ports used
EXPOSE	8042/tcp

