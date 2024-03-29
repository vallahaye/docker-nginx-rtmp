ARG RTMP_VERSION="1.2.2"

FROM nginx:stable-perl AS build-env
ARG RTMP_VERSION

WORKDIR /tmp

RUN set -x \
    \
    && apt-get update \
    && apt-get install --no-install-recommends --no-install-suggests -y wget \
    && wget 'https://hg.nginx.org/pkg-oss/raw-file/default/build_module.sh' \
    && chmod +x build_module.sh \
    && ./build_module.sh -v "${NGINX_VERSION}" -V "${RTMP_VERSION}" -y 'https://github.com/arut/nginx-rtmp-module.git'


FROM nginx:stable-perl
ARG RTMP_VERSION
ARG OCI_IMAGE_CREATED_AT

LABEL org.opencontainers.image.created="${OCI_IMAGE_CREATED_AT}" \
      org.opencontainers.image.authors="Valentin Lahaye <valentin.lahaye@gmail.com>" \
      org.opencontainers.image.url="https://hub.docker.com/r/vallahaye/nginx-rtmp" \
      org.opencontainers.image.source="https://github.com/vallahaye/nginx-rtmp" \
      org.opencontainers.image.version="${NGINX_VERSION}+${RTMP_VERSION}" \
      org.opencontainers.image.licenses="BSD-2-Clause"

ENV RTMP_VERSION="${RTMP_VERSION}"

COPY --from=build-env /tmp/build-module-artifacts /tmp/build-module-artifacts

RUN set -x \
    \
    && apt-get install --no-install-recommends --no-install-suggests -y /tmp/build-module-artifacts/nginx-module-rtmp_*.deb \
    && rm -rf /tmp/build-module-artifacts

COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 1935
