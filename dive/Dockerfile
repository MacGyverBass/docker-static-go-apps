# Base image for building static binary
FROM	macgyverbass/base-label:golang AS builder

# Install git & upx
RUN	apk --no-cache add git upx

# Build static binary
RUN	CGO_ENABLED="0" GOOS="linux" GOARCH="amd64" go get -v -ldflags="-w -s" github.com/wagoodman/dive

# Compress the binary
RUN	upx -9 /go/bin/dive


# Base image for downloading docker static binary
FROM	macgyverbass/base-label:alpine AS download

# Install binutils & upx
RUN	apk --no-cache add binutils upx

# Download docker static binary (automatically grabs the latest version if not provided)
ARG	ARCH="x86_64"
ARG	VERSION=""
RUN	if [ -z "${VERSION}" ];then VERSION="$(wget "https://download.docker.com/linux/static/stable/${ARCH}/" -O- |sed -n 's/^.*<a[^>]*href=\"docker-\([0-9\.]*\)\.tgz\"[^>]*>.*$/\1/p' |sort -V |tail -n1)";fi	\
	&& echo "Downloading Docker Static Binary v${VERSION}"	\
	&& wget "https://download.docker.com/linux/static/stable/${ARCH}/docker-${VERSION}.tgz" -O- |tar zxv docker/docker --strip-component=1

# Strip the binary to make it smaller
RUN	strip -s /docker

# Compress the binary
RUN	upx -9 /docker


# Build from scratch for smallest possible secure build
FROM	macgyverbass/base-label:scratch

# Copy static binaries into image
COPY	--from=builder /go/bin/dive /bin/dive
COPY	--from=download /docker /bin/docker

# Define our entrypoint
ENTRYPOINT	["/bin/dive"]

