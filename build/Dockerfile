# Development image
FROM golang:1.12-alpine3.10 AS BUILD-ENV

ARG GOOS_VAL 
ARG GOARCH_VAL

# Add git
RUN apk update && apk add git

WORKDIR /app

# Download dependencies
COPY go.mod go.sum ./
RUN go mod download

# Copy source
COPY . .

# Build binary
RUN GOOS=${GOOS_VAL} GOARCH=${GOARCH_VAL} go build -o /go/bin/botkube ./cmd/botkube 

# Install kubectl binary
ENV KUBE_LATEST_VERSION="v1.13.0"

RUN apk add --no-cache ca-certificates bash git \
    && wget -q https://storage.googleapis.com/kubernetes-release/release/${KUBE_LATEST_VERSION}/bin/linux/amd64/kubectl -O /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl

# Production image
FROM alpine:3.10

COPY --from=BUILD-ENV /go/bin/botkube /go/bin/botkube
COPY --from=BUILD-ENV /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
COPY --from=BUILD-ENV /usr/local/bin/kubectl /usr/local/bin/kubectl

ENTRYPOINT /go/bin/botkube
