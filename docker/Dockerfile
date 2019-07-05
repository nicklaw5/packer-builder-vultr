FROM golang:alpine as builder

ENV GO111MODULE=on
ARG PACKER_VERSION=v1.4.2

RUN apk add --no-cache git
RUN go get -d github.com/hashicorp/packer@${PACKER_VERSION}
RUN go get -d github.com/nicklaw5/packer-builder-vultr@${PACKER_VERSION}
WORKDIR /go/pkg/mod/github.com/hashicorp/packer@${PACKER_VERSION}
RUN cp -r /go/pkg/mod/github.com/nicklaw5/packer-builder-vultr@${PACKER_VERSION}/vultr /go/pkg/mod/github.com/hashicorp/packer@${PACKER_VERSION}/builder/
COPY plugin.go /go/pkg/mod/github.com/hashicorp/packer@${PACKER_VERSION}/command/
RUN GOARCH=amd64 CGO_ENABLED=0 GOOS=linux go build -o /tmp/packer .

FROM alpine:latest
RUN apk add --no-cache ca-certificates
COPY --from=builder /tmp/packer /packer/
RUN chmod +x /packer/packer
ENTRYPOINT [ "/packer/packer" ]
