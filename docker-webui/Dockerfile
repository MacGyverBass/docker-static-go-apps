# Base image for building static binary
FROM	macgyverbass/base-label:golang AS builder

# Install git & upx
RUN	apk --no-cache add git upx

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/hajimeo/docker-webui

# Compress the binary
RUN	upx -9 /go/bin/docker-webui

# Copy repository app/assets and app/views folders
RUN	mkdir -p bin/app/	\
	&& cp -lr src/github.com/hajimeo/docker-webui/app/assets/ src/github.com/hajimeo/docker-webui/app/views/ bin/app/


# Build from scratch for smallest possible secure build
FROM	macgyverbass/base-label:scratch

# Copy static binary and app folder into image
COPY	--from=builder /go/bin/ /

# Define the folder where app/assets and app/views can be found
ENV	APP_STATIC_FILE_PATH="/app/"

# Define our entrypoint
ENTRYPOINT	["/docker-webui"]

# Volumes used by image
VOLUME	["/tmp/"]

# Define the ports used
EXPOSE	9000/tcp

