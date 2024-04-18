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

# Install pipenv
# FIXME:
#   * DockerfileにARGで指定した値はbuild中は環境変数として扱われる
#   * pipenv側でPIPENV_VERSIONを環境変数がある場合にそれを使う作りになっている
#     * そのせいでpipenv syncが失敗するため回避が必要
#     * See: https://github.com/pypa/pipenv/issues/3633#issuecomment-478250721
ARG PIPENV__VERSION=2023.12.1
ENV PATH="/home/${USER}/.local/bin:${PATH}"
RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip install pipenv==${PIPENV__VERSION}

# Install dependencies
RUN --mount=type=bind,source=Pipfile,target=Pipfile \
    --mount=type=bind,source=Pipfile.lock,target=Pipfile.lock \
    pipenv sync
