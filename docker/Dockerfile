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

## Install miscellaneous apt packages
RUN apt-get install --yes --no-install-recommends git-all ca-certificates curl gnupg

## Setup `add-apt-launchpad-ppa`
COPY submodules/add-apt-source /usr/local/share/add-apt-source/

## Add deadsnakes PPA
RUN /usr/local/share/add-apt-source/src/add-apt-launchpad-ppa.sh deadsnakes ppa

## Update apt package lists
RUN apt-get update -qq

## Install Python3.13
RUN apt-get install --yes --no-install-recommends python3.13

## Create 'student' user and allow them to 'sudo'
## Ref: https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user
ARG USERNAME=student
RUN apt-get install --yes --no-install-recommends sudo \
    && usermod vscode --home "/home/$USERNAME" --login "$USERNAME" \
    && rm /etc/sudoers.d/vscode \
    && echo "$USERNAME ALL=\(root\) NOPASSWD:ALL" > "/etc/sudoers.d/$USERNAME" \
    && chmod 0440 "/etc/sudoers.d/$USERNAME"

## Configure apt & tidy up
RUN echo 'APT::Install-Recommends "false";' > /etc/apt/apt.conf.d/90local-no-recommends \
    && rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
    && rm -rf /var/lib/apt/lists/*

## Install uv
## Ref: https://docs.astral.sh/uv/getting-started/installation/#installation-methods
USER $USERNAME
RUN curl -LsSf https://astral.sh/uv/install.sh | sh

## Switch build user back to root
USER root
