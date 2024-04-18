# syntax=docker/dockerfile:labs

ARG PYTHON_VERSION=3.12.2
FROM python:${PYTHON_VERSION}-slim as base

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# See https://docs.docker.com/go/dockerfile-user-best-practices/
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

# Install uv
ARG UV_VERSION=0.1.33
ENV PATH="/home/${USER}/.local/bin:${PATH}"
RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip install uv==${UV_VERSION}
RUN uv venv

# Install dependencies
RUN --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
    uv pip install -r pyproject.toml
