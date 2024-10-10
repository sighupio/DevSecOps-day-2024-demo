FROM docker.io/alpine:3 AS builder
RUN    apk update \
    && apk add bash \
    && apk fetch --output /tmp     alpine-base \
    && apk fetch --output /tmp     alpine-baselayout \
    && apk fetch --output /tmp     ca-certificates \
    && apk fetch --output /tmp     ca-certificates-bundle \
    && apk fetch --output /tmp     tzdata \
    && apk fetch --output /tmp     busybox-static \
    && apk fetch --output /tmp     apk-tools-static \
    && apk fetch --output /tmp     apk-tools \
    && mkdir /tmp/copy && mkdir --parents /tmp/copy/bin \
    && find /tmp -mindepth 1  -maxdepth 1  -type f  -name '*.apk' -exec tar --directory=/tmp/copy --exclude='.PKGINFO' --exclude='.SIGN*'  --extract  --file={} \; \
    && find /tmp -mindepth 1  -maxdepth 1  -type f  -name '*.apk' -exec tar --extract --file={} --to-stdout .PKGINFO  echo \; >> /tmp/copy/packages.txt \
    && ln -s /bin/busybox.static /tmp/copy/bin/busybox \
    && bash -c "for busybox_command in \$( /tmp/copy/bin/busybox.static --list | grep -v busybox); do ln -s /bin/busybox  /tmp/copy/bin/\${busybox_command}; done"


FROM  scratch
COPY  --from=builder /tmp/copy ./
ENV  SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt
USER  0:0
#RUN /sbin/apk.static -X http://dl-cdn.alpinelinux.org/alpine/latest-stable/main -U --allow-untrusted --initdb add apk-tools apk-tools-static alpine-base alpine-baselayout ca-certificates ca-certificates-bundle busybox-static 
#RUN apk.static update
WORKDIR  /
ENTRYPOINT ["/bin/sh"]