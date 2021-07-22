FROM nginx:mainline-alpine AS build-env

WORKDIR /tmp

RUN set -x \
    \
    && apk add --no-cache alpine-sdk sudo wget \
    && wget 'https://hg.nginx.org/pkg-oss/raw-file/default/build_module.sh' \
    && chmod a+x build_module.sh \
    && adduser -S -D -s /sbin/nologin -G abuild -g builder builder \
    && echo 'builder ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
    && su builder -s /bin/sh -c "./build_module.sh -v '${NGINX_VERSION}' -V '1.2.2' -y 'https://github.com/arut/nginx-rtmp-module.git'"

FROM nginx:mainline-alpine

LABEL maintainer="Valentin Lahaye <valentin.lahaye@gmail.com>"

ENV RTMP_VERSION 1.2.2

COPY --from=build-env /tmp/build-module-artifacts/*.apk /tmp/build-module-artifacts/
COPY nginx.conf /etc/nginx/nginx.conf

RUN set -x \
    \
    && apk add --no-cache --allow-untrusted /tmp/build-module-artifacts/*.apk \
    && rm -rf /tmp/build-module-artifacts

EXPOSE 1935
