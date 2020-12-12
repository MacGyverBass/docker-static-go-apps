# Base image for building static binary
FROM	golang:alpine3.12 AS compile

# Intall packages needed to compile binary
RUN	apk --no-cache add	\
		git	\
		make	\
		gcc	\
		linux-headers	\
		musl-dev	\
		gpgme-dev	\
	&& apk --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing add	\
		nix

# Download source code
RUN	git clone https://github.com/containers/skopeo/ $GOPATH/src/github.com/containers/skopeo/

# Compile binary and move it to bin/
RUN	make -C $GOPATH/src/github.com/containers/skopeo/ static DISABLE_CGO=1	\
	&& mv -v $GOPATH/src/github.com/containers/skopeo/bin/* $GOPATH/bin/


# Use alpine to download ca-certificates.crt file, which is needed for this binary
FROM	alpine:3.12 AS cacerts

# Install ca-certificates
RUN	apk --no-cache add ca-certificates


# Build from scratch for smallest possible secure build
FROM	scratch

# Copy static binary into image
COPY	--from=compile /go/bin/skopeo /skopeo

# Copy ca-certificates.crt into image
COPY	--from=cacerts /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Define our entrypoint
ENTRYPOINT	["/skopeo"]

