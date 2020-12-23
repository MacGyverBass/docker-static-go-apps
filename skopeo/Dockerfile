# Base image for building static binary
FROM	golang:alpine3.12 AS compile

# Intall packages needed to compile binary
# Also install ca-certificates, so the ca-certificates.crt file can be copied to the final image
RUN	apk --no-cache add	\
		git	\
		make	\
		gcc	\
		linux-headers	\
		musl-dev	\
		gpgme-dev	\
		ca-certificates	\
		upx	\
	&& apk --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing add	\
		nix

# Download source code
RUN	git clone https://github.com/containers/skopeo/ $GOPATH/src/github.com/containers/skopeo/

# Compile binary and move it to bin/
RUN	make -C $GOPATH/src/github.com/containers/skopeo/ static DISABLE_CGO=1	\
	&& mv -v $GOPATH/src/github.com/containers/skopeo/bin/* $GOPATH/bin/	\
	&& chmod -cR 755 $GOPATH/bin/*

# Compress the binary
RUN	upx -9 /go/bin/skopeo


# Build from scratch for smallest possible secure build
FROM	scratch

# Copy static binary into image
COPY	--from=compile /go/bin/skopeo /skopeo

# Copy ca-certificates.crt into image
COPY	--from=compile /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

# Define our entrypoint
ENTRYPOINT	["/skopeo"]

