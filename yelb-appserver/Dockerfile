FROM bitnami/ruby:2.7.4 AS builder

LABEL maintainer="massimo@it20.info"

# Set working directory
WORKDIR /app

# Copy application files
COPY *.rb ./
COPY Gemfile Gemfile
COPY modules modules

# Environment configuration
ENV LANG=en_US.UTF-8 \
    LC_ALL=C.UTF-8 \
    RACK_ENV=production

# Install required gems
RUN gem install sinatra:2.0.5 --no-document && \
    gem install redis:4.2.5 --no-document

# Install required packages and clean cache
RUN apt-get update && \
    apt-get install -y --no-install-recommends libpq-dev curl && \
    rm -rf /var/lib/apt/lists/*

# Install additional gems
RUN gem install pg:1.2.3 --no-document && \
    gem install aws-sdk-dynamodb:1.72.0 --no-document && \
    gem install aws_lambda_ric:1.0.1 --no-document

# Return to root directory
WORKDIR /

# Copy startup script
COPY startup.sh startup.sh
RUN chmod +x startup.sh

# Lambda adapter
COPY --from=public.ecr.aws/awsguru/aws-lambda-adapter:0.5.1 /lambda-adapter /lambda-adapter

# Setup Lambda Runtime Interface Emulator (RIE)
RUN curl -sSL -o /usr/bin/lambda_rie https://github.com/aws/aws-lambda-runtime-interface-emulator/releases/download/v1.9/aws-lambda-rie-x86_64 && \
    chmod +x /usr/bin/lambda_rie

CMD ["./startup.sh"]

