# Initial Stage(Readiness, Dockerfile.builder)
ARG builder_image="golang:1.19"
ARG GOARCH_ARGS="arm64"
FROM ${builder_image} as builder

RUN mkdir /workspace
WORKDIR /workspace

# Clone Data
RUN git clone --depth=1 https://github.com/KangDroid-File-Cloud/mongodb-kubernetes-operator.git -b arm64-0.7.8 .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=$GOARCH_ARGS go build -a -o manager cmd/manager/main.go

# Mid Stage
FROM busybox AS outer_builder
RUN mkdir /workspace
COPY --from=builder /workspace/manager /workspace/
COPY --from=builder /workspace/build/bin /workspace/build/bin

# Final Stage
FROM registry.access.redhat.com/ubi8/ubi-minimal:latest

ENV OPERATOR=manager \
    USER_UID=2000 \
    USER_NAME=mongodb-kubernetes-operator

WORKDIR /
COPY --from=outer_builder /workspace/manager .
COPY --from=outer_builder /workspace/build/bin /usr/local/bin

RUN  /usr/local/bin/user_setup

USER ${USER_UID}

ENTRYPOINT ["/usr/local/bin/entrypoint"]