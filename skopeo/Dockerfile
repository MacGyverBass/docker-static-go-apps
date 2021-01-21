# Base image for building static binary
# The alpine:edge base image is used here so we can download/install nix, which is used for the main build process
FROM	alpine:edge AS builder

# Install ca-certificates, upx, and nix packages
RUN	apk --no-cache add	\
		ca-certificates	\
		upx	\
	&& apk --no-cache add --repository https://dl-cdn.alpinelinux.org/alpine/edge/testing/	\
		nix

# Download source code
WORKDIR	/skopeo/
RUN	wget "https://github.com/containers/skopeo/tarball/master" -O- |tar zxv --strip-components=1

# Compile binary and modify permissions
# Note that the build process here takes a long time to download/compile everything
RUN	nix build -f nix/	\
	&& chmod 755 result/bin/skopeo

# Compress the binary
RUN	upx -9 result/bin/skopeo


# Build from scratch for smallest possible secure build
FROM	scratch

# Copy static binary into image
COPY	--from=builder /skopeo/result/bin/skopeo /skopeo

# Copy ca-certificates.crt into image
COPY	--from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Define our entrypoint
ENTRYPOINT	["/skopeo"]

