FROM golang:buster as build

WORKDIR /go/src/greeter-server

COPY ./grpc-server/main.go main.go
RUN  go mod init greeter-server && \
  go mod tidy && \
  go build -o /greeter-server main.go

FROM ubuntu:20.04

COPY --from=build /greeter-server /

EXPOSE 50051

CMD ["/greeter-server"]
