FROM golang:latest as builder

WORKDIR /go/app/api
COPY . .
ENV CGO_ENABLED=0
RUN go get -d -v ./...
RUN go build -o /tmp/api-server ./*.go

FROM alpine:latest

COPY --from=builder /tmp/api-server /usr/bin/api-server
COPY .env .
RUN apk --no-cache add curl
CMD ["api-server", "start"]