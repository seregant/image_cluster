############################
# STEP 1 build executable binary
############################
FROM golang:alpine AS builder
# Install git.
# Git is required for fetching the dependencies.
RUN apk update && apk add --no-cache git
RUN cd $GOPATH/src/
# Fetch dependencies.
#Install govendor
RUN go get github.com/kardianos/govendor
# Using go get.
RUN go get github.com/501army/golang-simple-api
#Run the api service binary
RUN cd  $GOPATH/src/github.com/501army/golang-simple-api && govendor sync 
RUN cd  $GOPATH/src/github.com/501army/golang-simple-api && CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -installsuffix cgo -ldflags="-w -s" -o /go/bin/golang-simple-api
############################
# STEP 2 build a small image
############################
FROM scratch
# Import the user and group files from the builder.
COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
# Copy our static executable.
COPY --from=builder /go/bin/golang-simple-api /go/bin/golang-simple-api

# Use an unprivileged user.
USER root

#Run app
ENTRYPOINT ["/go/bin/golang-simple-api"]
EXPOSE 2323/tcp

