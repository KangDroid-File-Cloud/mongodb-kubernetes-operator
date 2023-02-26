# Initial Stage(Readiness, Dockerfile.builder)
ARG builder_image="golang:1.19"
ARG GOARCH_ARGS="arm64"
FROM ${builder_image} as builder

RUN mkdir /workspace
WORKDIR /workspace

# Clone Data
RUN git clone --depth=1 https://github.com/KangDroid-File-Cloud/mongodb-kubernetes-operator.git -b arm64-0.7.8 .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=$GOARCH_ARGS go build -a -o readinessprobe cmd/readiness/main.go

# Mid Stage
FROM busybox AS base
COPY --from=builder /workspace/readinessprobe /probes/

# Final Stage(Original: Dockerfile.readiness)
FROM busybox
COPY --from=base /probes/readinessprobe /probes/readinessprobe
