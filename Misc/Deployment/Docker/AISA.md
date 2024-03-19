---
tags:
  - aisa
  - deployment
  - docker
origin:
  - https://gogs.net173.org/devsecops/ptai-cli.git
---
# Сборка Docker-образа
``` dockerfile
ARG SOURCE_TOOL_IMAGE=ubuntu:18.04
FROM ${SOURCE_TOOL_IMAGE}

ARG PTAI_VERSION=4.6.0.29976

COPY x509/*.crt /usr/local/share/ca-certificates/

# Install external packages
RUN set -ex && \
  apt-get update && \
  apt-get install -y --no-install-recommends ca-certificates gnupg curl apt-utils libssl1.1 libgssapi-krb5-2 libicu60 && \
  update-ca-certificates && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

# Install AISA
RUN set -ex && \
  mkdir -p /etc/apt/keyrings && \
  curl -fsSL https://update.ptsecurity.com/packages/PT-public.gpg | gpg --dearmor -o /etc/apt/keyrings/pt.gpg && \
  echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/pt.gpg] https://update.ptsecurity.com/packages/deb/AI.Shell/release all non-free" | \
    tee /etc/apt/sources.list.d/pt.list > /dev/null && \
  apt-get update && \
  DEBIAN_FRONTEND=noninteractive apt-get install -yq aisa=$PTAI_VERSION
```
``` bash
# Define variables
PTAI_VERSION=4.6.0.29976
DESTINATION_TOOL_TAG=20240319/ptai-cli:${PTAI_VERSION}
SOURCE_TOOL_IMAGE=ubuntu:18.04
# Copy trusted CA certificates into x509 subfolder
...
# Build image
docker build \
  --build-arg SOURCE_TOOL_IMAGE=${SOURCE_TOOL_IMAGE} \
  --build-arg PTAI_VERSION=${PTAI_VERSION} \
  --tag ${DESTINATION_TOOL_TAG} .
# Check image
docker run --rm -it ${DESTINATION_TOOL_TAG} aisa --version
```