FROM nginx:stable-alpine-perl AS build-env

WORKDIR /tmp

RUN set -x \
    \
    && apk add --no-cache alpine-sdk sudo wget \
    && wget 'https://hg.nginx.org/pkg-oss/raw-file/default/build_module.sh' \
    && chmod a+x build_module.sh \
    && adduser -S -D -s /sbin/nologin -G abuild -g builder builder \
    && echo 'builder ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
    && su builder -s /bin/sh -c "./build_module.sh -v '${NGINX_VERSION}' -V '1.2.2' -y 'https://github.com/arut/nginx-rtmp-module.git'"


FROM nginx:stable-alpine-perl

LABEL maintainer="Valentin Lahaye <valentin.lahaye@gmail.com>"

ENV RTMP_VERSION 1.2.2

COPY --from=build-env /tmp/build-module-artifacts /tmp/build-module-artifacts/

RUN set -x \
    \
    && find /tmp/build-module-artifacts -name '*.apk' -exec apk add --no-cache --allow-untrusted {} + \
    && rm -rf /tmp/build-module-artifacts

COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 1935
