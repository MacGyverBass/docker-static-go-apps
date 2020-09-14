FROM	golang:alpine3.12 AS builder

RUN	apk --no-cache add	\
		git

RUN	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go get -v -ldflags="-w -s" github.com/starius/pasta/gopasta


FROM	scratch

COPY	--from=builder /go/bin/gopasta /pasta

ENTRYPOINT	["/pasta", "-dir", "/db/"]

VOLUME	["/db/"]

EXPOSE	8042/tcp

