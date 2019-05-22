FROM golang:1.12.2-stretch as builder
RUN apt update && apt -y install git-all
WORKDIR /go/src/github.com/popeye
COPY popeye    .
ENV GO111MODULE=on
RUN go build -o app

FROM ubuntu:18.04 
WORKDIR /root/
COPY --from=builder /go/src/github.com/popeye/app .
COPY run.sh    .
CMD ["./run.sh"]