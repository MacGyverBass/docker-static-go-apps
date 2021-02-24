# Base image for building static binary
FROM	macgyverbass/base-label:golang AS builder

# Install git & upx
RUN	apk --no-cache add git upx

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/hajimeo/docker-webui

# Compress the binary
RUN	upx -9 /go/bin/docker-webui


# Base image for downloading the repository files
FROM	macgyverbass/base-label:busybox AS download

# Download/Extract files from the repository
WORKDIR	/webui/
RUN	wget "https://github.com/hajimeo/docker-webui/archive/master.tar.gz" -O- |tar zxv --strip-components=1 "docker-webui-master/app/"


# Build from scratch for smallest possible secure build
FROM	macgyverbass/base-label:scratch

# Copy static binary into image
COPY	--from=builder /go/bin/docker-webui /docker-webui

# Copy /app/ folder into image
COPY	--from=download /webui/app/ /app/

# Define the folder where app/assets and app/views can be found
ENV	APP_STATIC_FILE_PATH="/app/"

# Define our entrypoint
ENTRYPOINT	["/docker-webui"]

# Volumes used by image
VOLUME	["/tmp/"]

# Define the ports used
EXPOSE	9000/tcp

