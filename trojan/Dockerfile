#build stage
FROM golang:alpine AS builder
WORKDIR /src
RUN apk add --no-cache git \
    && git clone https://github.com/p4gefau1t/trojan-go.git /src/trojan-go
WORKDIR /src/trojan-go
RUN CGO_ENABLE=0 go build -ldflags="-s -w" -o /trojan-go

#final stage
FROM alpine:latest
RUN apk --no-cache add ca-certificates tzdata
COPY --from=builder /trojan-go /trojan-go
COPY ./startup /startup
WORKDIR /etc/config
ENTRYPOINT ["/startup", "/trojan-go"]
CMD ["-config", "config.json"]