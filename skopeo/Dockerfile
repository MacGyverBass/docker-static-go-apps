# Base image for building static binary
FROM	golang:alpine3.13 AS compile

# Install packages needed to compile binary
# Also intall ca-certificates & upx packages
RUN	apk --no-cache add	\
		make	\
		g++	\
		linux-headers	\
		ca-certificates	\
		upx

# Download source code
WORKDIR	/skopeo/
RUN	wget "https://github.com/containers/skopeo/tarball/master" -O- |tar zxv --strip-components=1

# Compile static binary
RUN	make	\
		CFLAGS='-static -pthread'	\
		LDFLAGS='-s -w -static-libgcc -static'	\
		EXTRA_LDFLAGS='-s -w -linkmode external -extldflags "-static -lm"'	\
		BUILDTAGS='static netgo osusergo exclude_graphdriver_btrfs exclude_graphdriver_devicemapper'	\
		DISABLE_CGO=1	\
		bin/skopeo

# Compress the binary
RUN	upx -9 /skopeo/bin/skopeo


# Build from scratch for smallest possible secure build
FROM	scratch

# Copy static binary into image
COPY	--from=compile /skopeo/bin/skopeo /skopeo

# Copy ca-certificates.crt into image
COPY	--from=compile /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Define our entrypoint
ENTRYPOINT	["/skopeo"]

