FROM python:3-alpine3.6

MAINTAINER Kacper Czarczyński <kacper.czarczynski@gmail.com>

ENV PGADMIN_VERSION 3.6
ENV UID             999
ENV GID             999

ENV SERVER_MODE   false
ENV MAIL_SERVER   mail.example.tld
ENV MAIL_PORT     465
ENV MAIL_USE_SSL  false
ENV MAIL_USE_TLS  false
ENV MAIL_USERNAME username
ENV MAIL_PASSWORD password
ENV MAIL_DEBUG    false

# Metadata
LABEL   org.label-schema.name="pgAdmin4" \
        org.label-schema.description="Docker image pgAdmin4" \
        org.label-schema.url="https://www.pgadmin.org" \
        org.label-schema.license="PostgreSQL" \
        org.label-schema.version=${PGADMIN_VERSION} \
        org.label-schema.vcs-url="https://github.com/Chorss/docker-pgAdmin4"

RUN apk add --no-cache --virtual .run-deps postgresql postgresql-libs libffi-dev openssl shadow sudo su-exec bash
RUN apk add --no-cache --virtual .build-deps make gcc musl-dev openssl postgresql-dev linux-headers \
 && pip install --upgrade pip \
 && pip3 --no-cache-dir install https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v${PGADMIN_VERSION}/pip/pgadmin4-${PGADMIN_VERSION}-py2.py3-none-any.whl \
 && apk del .build-deps \
 && rm -rf /root/.cache

RUN addgroup -g ${GID} -S pgadmin \
 && adduser -D -S -h /pgadmin -s /sbin/nologin -u ${UID} -G pgadmin pgadmin \
 && mkdir -p /data/config /data/logs /data/storage /data/sessions /data/misc \
 && chown -R ${UID}:${GID} /data \
 && rm -rf /root/.cache

COPY config_local.py /usr/local/lib/python3.6/site-packages/pgadmin4/
COPY entrypoint /
RUN chmod 0775 /entrypoint

VOLUME /data
ENTRYPOINT ["/entrypoint"]

CMD ["su-exec", "pgadmin", "python", "/usr/local/lib/python3.6/site-packages/pgadmin4/pgAdmin4.py"]