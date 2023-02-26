FROM ubuntu:20.04 as base

ARG agent_version="12.0.15.7646-1"
ARG agent_distro="amzn2_aarch64"
ARG tools_distro="ubuntu1604-x86_64"
ARG tools_version="100.6.0"

RUN apt-get update && apt install -y curl
ADD https://downloads.mongodb.org/tools/db/mongodb-database-tools-${tools_distro}-${tools_version}.tgz /data/mongodb-tools.tgz
RUN if [ "$TARGETPLATFORM" = "amd64" ] ; then https://mciuploads.s3.amazonaws.com/mms-automation/mongodb-mms-build-agent/builds/automation-agent/prod/mongodb-mms-automation-agent-${agent_version}.linux_x86_64.tar.gz -o /data/mongodb-agent.tar.gz ; else curl https://mciuploads.s3.amazonaws.com/mms-automation/mongodb-mms-build-agent/builds/automation-agent/prod/mongodb-mms-automation-agent-${agent_version}.amzn2_aarch64.tar.gz -o /data/mongodb-agent.tar.gz ; fi 

FROM ubuntu:20.04

ARG agent_version="12.0.15.7646-1"

LABEL name="MongoDB Agent" \
      version="${agent_version}" \
      summary="MongoDB Agent" \
      description="MongoDB Agent" \
      vendor="MongoDB" \
      release="1" \
      maintainer="support@mongodb.com"

RUN apt-get -qq update \
        && apt-get -y -qq install \
        curl \
        libnss-wrapper \
        && apt-get upgrade -y -qq \
        && apt-get dist-upgrade -y -qq \
        && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /agent \
    && mkdir -p /var/lib/mongodb-mms-automation \
      && mkdir -p /var/log/mongodb-mms-automation/ \
      && chmod -R +wr /var/log/mongodb-mms-automation/ \
      # ensure that the agent user can write the logs in OpenShift
      && touch /var/log/mongodb-mms-automation/readiness.log \
      && chmod ugo+rw /var/log/mongodb-mms-automation/readiness.log


COPY --from=base /data/mongodb-agent.tar.gz /agent
COPY --from=base /data/mongodb-tools.tgz /agent
# COPY --from=base /data/LICENSE /licenses/LICENSE

RUN tar xfz /agent/mongodb-agent.tar.gz \
    && mv mongodb-mms-automation-agent-*/mongodb-mms-automation-agent /agent/mongodb-agent \
    && chmod +x /agent/mongodb-agent \
    && mkdir -p /var/lib/automation/config \
    && chmod -R +r /var/lib/automation/config \
    && rm /agent/mongodb-agent.tar.gz \
    && rm -r mongodb-mms-automation-agent-*

RUN tar xfz /agent/mongodb-tools.tgz --directory /var/lib/mongodb-mms-automation/ && rm /agent/mongodb-tools.tgz

USER 2000
CMD ["/agent/mongodb-agent", "-cluster=/var/lib/automation/config/automation-config.json"]
