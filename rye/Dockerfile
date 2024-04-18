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

RUN --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    --mount=type=bind,source=requirements.lock,target=requirements.lock \
    --mount=type=bind,source=requirements-dev.lock,target=requirements-dev.lock \
    --mount=type=bind,source=.python-version,target=.python-version \
    --mount=type=bind,source=README.md,target=README.md \
    rye sync --no-lock

## Download dependencies as a separate step to take advantage of Docker's caching.
## Leverage a cache mount to /root/.cache/pip to speed up subsequent builds.
## Leverage a bind mount to requirements.txt to avoid having to copy them into
## into this layer.
#RUN --mount=type=cache,target=/root/.cache/pip \
#    --mount=type=bind,source=requirements.txt,target=requirements.txt \
#    python -m pip install -r requirements.txt
#
## Switch to the non-privileged user to run the application.
#USER appuser
#
## Copy the source code into the container.
#COPY . .
#
## Expose the port that the application listens on.
#EXPOSE 8000
#
## Run the application.
#CMD yes
