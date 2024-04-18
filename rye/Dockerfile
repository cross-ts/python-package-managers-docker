# syntax=docker/dockerfile:labs
FROM debian:bookworm-slim

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

ARG CURL_VERSION=7.88.1-10+deb12u5
ARG CA_CERTIFICATES_VERSION=20230311
RUN apt-get update \
 && apt-get install --no-install-recommends -y \
      ca-certificates=${CA_CERTIFICATES_VERSION} \
      curl=${CURL_VERSION} \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# See: https://docs.docker.com/go/dockerfile-user-best-practices/
ARG USER=portal
ARG UID=10001
RUN adduser \
    --disabled-password \
    --gecos "" \
    --shell "/sbin/nologin" \
    --uid "${UID}" \
    ${USER}
USER ${USER}
WORKDIR /var/task

# Install Rye
ARG RYE_VERSION=latest
ENV RYE_HOME=/home/${USER}/.rye
ENV PATH=${RYE_HOME}/shims:${PATH}
RUN curl -sSf https://rye-up.com/get | \
    RYE_INSTALL_OPTION="--yes" \
    RYE_VERSION="${RYE_VERSION}" \
        bash

# Install dependencies
RUN --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    --mount=type=bind,source=requirements.lock,target=requirements.lock \
    --mount=type=bind,source=requirements-dev.lock,target=requirements-dev.lock \
    --mount=type=bind,source=.python-version,target=.python-version \
    --mount=type=bind,source=README.md,target=README.md \
    rye sync --no-lock
