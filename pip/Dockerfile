# syntax=docker/dockerfile:labs
ARG PYTHON_VERSION=3.12.2
FROM python:${PYTHON_VERSION}-slim as base

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Create a non-privileged user that the app will run under.
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

# Install dependencies
RUN --mount=type=cache,target=/root/.cache/pip \
    --mount=type=bind,source=requirements.lock,target=requirements.lock \
    python -m pip install -r requirements.lock
