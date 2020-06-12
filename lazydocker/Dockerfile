FROM	golang:alpine3.12 AS builder

RUN	apk --no-cache add	\
		git

RUN	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go get -v -ldflags="-w -s" github.com/jesseduffield/lazydocker


FROM	scratch

COPY	--from=builder /go/bin/lazydocker /lazydocker

ENTRYPOINT	["/lazydocker"]

