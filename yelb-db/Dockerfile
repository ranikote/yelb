FROM postgres:10.21 AS builder
# MAINTAINER massimo@it20.info
LABEL maintainer massimo@it20.info

WORKDIR /

COPY init-yelb-db.sh /docker-entrypoint-initdb.d/init-yelb-db.sh

ENV POSTGRES_PASSWORD=postgres_password


