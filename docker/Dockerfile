FROM mcr.microsoft.com/devcontainers/base:ubuntu

## Tell apt not to ask for user interaction
ARG DEBIAN_FRONTEND=noninteractive

## Set locale
RUN apt-get update -qq \
    && apt-get -y install --no-install-recommends locales \
    && echo "en_GB.UTF-8 UTF-8" > /etc/locale.gen \
    && locale-gen en_GB.utf8 \
    && /usr/sbin/update-locale LANG=en_GB.UTF-8
ENV LC_ALL=en_GB.UTF-8
ENV LANG=en_GB.UTF-8

## Set timezone
ENV TZ="Europe/London"

## Install git and ca-certificates
RUN apt-get install --yes --no-install-recommends git-all ca-certificates

## Setup `add-apt-launchpad-ppa`
COPY ../submodules/add-apt-source /usr/local/share/
WORKDIR /usr/local/share/add-apt-source
RUN git clean -dfX \
    && rm -rf .git \
    && ln -s ../add-apt-source/add-apt-launchpad-ppa.sh /usr/local/bin/add-apt-launchpad-ppa
WORKDIR /

## Add deadsnakes PPA
RUN add-apt-launchpad-ppa deadsnakes ppa

## Update apt package lists
RUN apt-get update -qq

## Install Python3.13
RUN apt-get install --yes --no-install-recommends python3.13

## Create 'student' user and allow them to 'sudo'
## Ref: https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user
ARG USERNAME=student
ARG USER_UID=1000
ARG USER_GID=$USER_UID
RUN apt-get install --yes --no-install-recommends sudo \
    && groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USERNAME \
    && echo "$USERNAME ALL=\(root\) NOPASSWD:ALL" > "/etc/sudoers.d/$USERNAME" \
    && chmod 0440 "/etc/sudoers.d/$USERNAME"
## Set the default user
USER $USERNAME

## Configure apt & tidy up
RUN echo 'APT::Install-Recommends "false";' > /etc/apt/apt.conf.d/90local-no-recommends \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
    && rm -rf /var/lib/apt/lists/*
