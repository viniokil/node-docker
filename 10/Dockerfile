FROM node:10-alpine

ENV PS1='\[\033[1;32m\]📗  \[\033[1;36m\][\u@\h] \[\033[1;34m\]\w\[\033[0;35m\] \[\033[1;36m\]# \[\033[0m\]'

RUN set -xe \
    && apk add --no-cache \
                            bash \
                            curl \
                            git \
                            rsync \
                            openssh-client \
                            httpie \
                            jq \
                            netcat-openbsd \
                            autoconf \
                            automake \
                            libpng-dev \
                            lcms2-dev \
                            make \
                            gcc \
                            g++ \
                            python \
                            libtool \
    # Update npm to latest version
    && npm i -g npm

WORKDIR /app