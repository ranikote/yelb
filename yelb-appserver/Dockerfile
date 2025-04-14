FROM bitnami/ruby:2.7.4 AS builder

LABEL maintainer="massimo@it20.info"

# Set environment variables
ENV LANG=en_US.UTF-8 \
    LC_ALL=C.UTF-8 \
    RACK_ENV=production

# Set the working directory to /app
WORKDIR /app

# Copy application code
COPY *.rb ./
COPY Gemfile Gemfile
COPY modules modules

# Install required Ruby gems
RUN gem install sinatra --no-document && \
    gem install redis --no-document

# Install required system packages with pinned versions
RUN apt-get update && \
    apt-get install -y --no-install-recommends libpq-dev=11.22-1.pgdg110+1 && \
    rm -rf /var/lib/apt/lists/*

# Install database and AWS-related gems
RUN gem install pg --no-document && \
    gem install aws-sdk-dynamodb --no-document && \
    gem install aws_lambda_ric --no-document

# Copy startup script
COPY startup.sh /startup.sh
RUN chmod +x /startup.sh

# Lambda adapter
COPY --from=public.ecr.aws/awsguru/aws-lambda-adapter:0.5.1 /lambda-adapter /lambda-adapter

# Setup Lambda RIE
RUN curl -L -o /usr/bin/lambda_rie https://github.com/aws/aws-lambda-runtime-interface-emulator/releases/download/v1.9/aws-lambda-rie-x86_64 && \
    chmod +x /usr/bin/lambda_rie

# Final working directory
WORKDIR /

# Command to run the app
CMD ["/startup.sh"]

