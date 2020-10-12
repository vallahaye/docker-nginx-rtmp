FROM nginx:mainline-perl AS builder

ENV RTMP_VERSION 1.2.1

WORKDIR /usr/src

COPY nginx-rtmp-module.patch .

RUN set -x \
    \
    && apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y dpkg-dev git wget gnupg1 ca-certificates lsb-release \
    && git clone --depth 1 --branch "v${RTMP_VERSION}" https://github.com/arut/nginx-rtmp-module.git \
    && (cd nginx-rtmp-module && git apply /usr/src/nginx-rtmp-module.patch) \
    && wget -O- https://nginx.org/keys/nginx_signing.key | apt-key add - \
    && echo "deb-src https://nginx.org/packages/`case mainline-perl in mainline*) echo mainline/ ;; *) ;; esac`debian/ `lsb_release -cs` nginx" > /etc/apt/sources.list.d/nginx.list \
    && apt-get update \
    && apt-get build-dep -y "nginx=${NGINX_VERSION}" \
    && apt-get source "nginx=${NGINX_VERSION}" \
    && (cd "nginx-${NGINX_VERSION}" && ./configure --with-compat --add-dynamic-module=/usr/src/nginx-rtmp-module && make modules) \
    && mkdir dist \
    && cp "nginx-${NGINX_VERSION}/objs/ngx_rtmp_module.so" dist

FROM nginx:mainline-perl

LABEL maintainer="Valentin Lahaye <valentin.lahaye@gmail.com>"

ENV RTMP_VERSION 1.2.1

COPY --from=builder --chown=root:root /usr/src/dist/ngx_rtmp_module.so /usr/lib/nginx/modules
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 1935
