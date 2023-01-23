FROM alpine:3.12.1

LABEL maintainer="Sviatoslav Stetsiak"

RUN set -ex \
    && apk update \
    && apk upgrade \
    && apk add --no-cache \
    apache2-utils \
    bash \
    busybox-extras \
    curl \
    git \
    nmap \
    tcptraceroute \
    vim \
    openssh-client \
    mysql-client \
    tmux


CMD ["sh"]