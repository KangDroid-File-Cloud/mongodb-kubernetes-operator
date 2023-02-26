# Initial Stage(Versionhook, Dockerfile.builder)
ARG builder_image="golang:1.19"
ARG GOARCH_ARGS="arm64"
FROM ${builder_image} as builder

RUN mkdir /workspace
WORKDIR /workspace

# Clone Data
RUN git clone --depth=1 https://github.com/KangDroid-File-Cloud/mongodb-kubernetes-operator.git -b arm64-0.7.8 .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=$GOARCH_ARGS go build -a -o version-upgrade-hook cmd/versionhook/main.go

# Final Stage
FROM busybox
COPY --from=builder /workspace/version-upgrade-hook /version-upgrade-hook