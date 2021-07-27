FROM nginx:stable-perl AS build-env

WORKDIR /tmp

RUN set -x \
    \
    && apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y wget \
    && wget 'https://hg.nginx.org/pkg-oss/raw-file/default/build_module.sh' \
    && chmod a+x build_module.sh \
    && ./build_module.sh -v "${NGINX_VERSION}" -V '1.2.2' -y 'https://github.com/arut/nginx-rtmp-module.git'


FROM nginx:stable-perl

LABEL maintainer="Valentin Lahaye <valentin.lahaye@gmail.com>"

ENV RTMP_VERSION 1.2.2

COPY --from=build-env /tmp/build-module-artifacts /tmp/build-module-artifacts/

RUN set -x \
    \
    && dpkg -i -R /tmp/build-module-artifacts \
    && rm -rf /tmp/build-module-artifacts

COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 1935
