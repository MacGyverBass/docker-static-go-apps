FROM	golang:alpine3.12 AS compile

RUN	apk --no-cache add	\
		git	\
		make	\
		gcc	\
		linux-headers	\
		musl-dev	\
		gpgme-dev	\
	&& apk --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing add	\
		nix

RUN	git clone https://github.com/containers/skopeo/ $GOPATH/src/github.com/containers/skopeo/
RUN	make -C $GOPATH/src/github.com/containers/skopeo/ static DISABLE_CGO=1	\
	&& mv -v $GOPATH/src/github.com/containers/skopeo/bin/* $GOPATH/bin/


FROM	alpine:3.12 AS cacerts

RUN	apk --no-cache add	\
		ca-certificates


FROM	scratch

COPY	--from=compile /go/bin/skopeo /skopeo
COPY	--from=cacerts /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

ENTRYPOINT	["/skopeo"]

