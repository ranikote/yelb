FROM postgres:10.21 AS builder

# Replaced deprecated MAINTAINER with LABEL
LABEL maintainer="massimo@it20.info"

WORKDIR /

# Use COPY instead of ADD (you're already using COPY, which is correct)
COPY init-yelb-db.sh /docker-entrypoint-initdb.d/init-yelb-db.sh

# Set environment variable
ENV POSTGRES_PASSWORD=postgres_password


