FROM postgres:10.21 AS builder

LABEL org.opencontainers.image.authors="massimo@it20.info"

WORKDIR /

COPY init-yelb-db.sh /docker-entrypoint-initdb.d/init-yelb-db.sh

ENV POSTGRES_PASSWORD=postgres_password
