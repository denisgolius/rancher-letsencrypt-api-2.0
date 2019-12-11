FROM golang:1 as build

WORKDIR /go/src/rancher-letsencrypt

COPY . .

RUN go get ./... && make deps && make build

FROM alpine:3.5

RUN apk update \
  && apk add  --no-cache ca-certificates wget \
  && update-ca-certificates

ENV LETSENCRYPT_RELEASE v0.5.1
ENV SSL_SCRIPT_COMMIT 08278ace626ada71384fc949bd637f4c15b03b53

RUN wget -O /usr/bin/update-rancher-ssl https://raw.githubusercontent.com/rancher/rancher/${SSL_SCRIPT_COMMIT}/server/bin/update-rancher-ssl && \
    chmod +x /usr/bin/update-rancher-ssl

COPY ./package/rancher-entrypoint.sh /usr/bin/

COPY --from=build /go/src/rancher-letsencrypt/build/rancher-letsencrypt-linux-amd64 /usr/bin/rancher-letsencrypt

RUN chmod +x /usr/bin/rancher-letsencrypt

EXPOSE 80
ENTRYPOINT ["/usr/bin/rancher-entrypoint.sh"]
