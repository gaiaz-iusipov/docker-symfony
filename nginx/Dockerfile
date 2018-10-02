FROM nginx:stable-alpine

LABEL maintainer="g.iusipov@gmail.com"

RUN set -ex && \
    addgroup -g 1000 docker && \
    adduser -u 1000 -G docker -h /home/docker -s /bin/sh -D docker && \
    sed -i 's|user\s*nginx;|user docker docker;|' /etc/nginx/nginx.conf
