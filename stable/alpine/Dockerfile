FROM nginx:stable-alpine AS builder

ENV RTMP_VERSION 1.2.1

WORKDIR /usr/src

COPY nginx-rtmp-module.patch .

RUN set -x \
    \
    && apk add --no-cache alpine-sdk bash git mercurial \
    && git clone --depth 1 --branch "v${RTMP_VERSION}" https://github.com/arut/nginx-rtmp-module.git \
    && (cd nginx-rtmp-module && git apply /usr/src/nginx-rtmp-module.patch) \
    && addgroup -S builder \
    && adduser -S -D -H -s /sbin/nologin -G builder -g builder builder \
    && addgroup builder abuild \
    && mkdir builder-workspace \
    && chown builder:builder builder-workspace \
    && (cd builder-workspace && su builder -s /bin/bash -c "set -x \
        \
        && hg clone -u ${NGINX_VERSION}-${PKG_RELEASE} https://hg.nginx.org/pkg-oss \
        && cd pkg-oss/alpine && make abuild-base \
        && cd abuild-base && abuild deps && abuild checksum && abuild unpack \
        && cd src/nginx-${NGINX_VERSION} && ./configure --with-compat --add-dynamic-module=/usr/src/nginx-rtmp-module && make modules") \
    && mkdir dist \
    && cp "builder-workspace/pkg-oss/alpine/abuild-base/src/nginx-${NGINX_VERSION}/objs/ngx_rtmp_module.so" dist

FROM nginx:stable-alpine

LABEL maintainer="Valentin Lahaye <valentin.lahaye@gmail.com>"

ENV RTMP_VERSION 1.2.1

COPY --from=builder --chown=root:root /usr/src/dist/ngx_rtmp_module.so /usr/lib/nginx/modules
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 1935
