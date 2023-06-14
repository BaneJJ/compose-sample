FROM golang:latest AS dependency-builder
WORKDIR /go/app/api
COPY go.* .
ENV CGO_ENABLED=0
RUN go get -d -v ./...

FROM dependency-builder AS application-builder
COPY . .
RUN go build -o /tmp/api-server ./*.go

FROM alpine:latest
COPY --from=application-builder /tmp/api-server /usr/bin/api-server
COPY .env .
RUN apk --no-cache add curl
CMD ["api-server", "start"]