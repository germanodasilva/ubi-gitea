#Build stage
FROM registry.access.redhat.com/ubi8/ubi AS build-env
LABEL maintainer="gedasilv@redhat.com"
LABEL description="SCM Gitea unsupported image"

EXPOSE 2222 3000

ARG GOPROXY
ENV GOPROXY ${GOPROXY:-direct}

ARG GITEA_VERSION
ARG TAGS="sqlite sqlite_unlock_notify"
ENV TAGS "bindata timetzdata $TAGS"
ARG CGO_EXTRA_CFLAGS


#Build deps
RUN yum install -y git nodejs npm make go wget
RUN yum update -y

WORKDIR /var/lib/gitea

COPY app.ini /etc/gitea/app.ini
#Clone repository
RUN wget -O gitea https://dl.gitea.io/gitea/1.18.0/gitea-1.18.0-linux-amd64
RUN chmod +x gitea

RUN groupadd  git
RUN adduser -d /var/lib/gitea -s /bin/bash -u 1000 -g git git

RUN mkdir -p /var/lib/gitea/{custom,data,log}
RUN chown -R git:git /var/lib/gitea/
RUN chmod -R 750 /var/lib/gitea/
RUN mkdir -p /etc/gitea
RUN chown git:git /etc/gitea
RUN chmod 775 /etc/gitea
RUN chmod 640 /etc/gitea/app.ini

RUN export GITEA_WORK_DIR=/var/lib/gitea/
RUN cp gitea /usr/local/bin/gitea

USER git
ENV GITEA_WORK_DIR /var/lib/gitea
ENV GITEA_CUSTOM /var/lib/gitea/custom
ENV GITEA_TEMP /tmp/gitea
ENV TMPDIR /tmp/gitea

ENV GITEA_APP_INI /etc/gitea/app.ini
ENV HOME "/var/lib/gitea/git"
VOLUME ["/var/lib/gitea", "/etc/gitea"]
WORKDIR /var/lib/gitea

ENTRYPOINT ["/usr/local/bin/gitea", "web", "-c", "/etc/gitea/app.ini"]
CMD []