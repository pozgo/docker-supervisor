FROM alpine:3.18.3

RUN \
  apk add --update bash supervisor inotify-tools && \
  rm -rf /var/cache/apk/*

ADD container-files /

VOLUME ["/data"]

ENTRYPOINT ["/config/bootstrap.sh"]
