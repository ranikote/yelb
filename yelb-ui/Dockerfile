# ---------- STAGE 1: Build Angular UI ----------
FROM node:14 AS ui-builder

LABEL maintainer="massimo@it20.info"

WORKDIR /app

# Install Angular CLI and node-sass globally
RUN npm install -g --unsafe-perm @angular/cli@6.0.0 node-sass@4.13.1

# Copy modified clarity-seed files
COPY clarity-seed-newfiles/ /app/clarity-seed-newfiles/

# Clone original clarity-seed and copy required files
RUN git clone https://github.com/vmware/clarity-seed.git /app/clarity-seed

WORKDIR /app/clarity-seed

RUN git checkout f3250ee26ceb847f61bb167a90dc957edf6e7f43 && \
    cp /app/clarity-seed-newfiles/src/index.html src/index.html && \
    cp /app/clarity-seed-newfiles/src/styles.css src/styles.css && \
    cp /app/clarity-seed-newfiles/src/env.js src/env.js && \
    cp /app/clarity-seed-newfiles/src/app/app* src/app && \
    cp /app/clarity-seed-newfiles/src/app/env* src/app && \
    cp /app/clarity-seed-newfiles/src/environments/env* src/environments && \
    cp /app/clarity-seed-newfiles/package.json package.json && \
    cp /app/clarity-seed-newfiles/angular-cli.json .angular-cli.json && \
    rm -rf src/app/home src/app/about

RUN npm install && \
    ng build --environment=prod --output-path=./prod/dist/ --aot --vendor-chunk --common-chunk --deploy-url=. --build-optimizer && \
    ng build --environment=test --output-path=./test/dist/ && \
    ng build --environment=dev --output-path=./dev/dist/

# ---------- STAGE 2: NGINX for serving ----------
FROM nginx:1.17.10 AS nginx-stage

LABEL maintainer="massimo@it20.info"

WORKDIR /

COPY startup.sh startup.sh
RUN chmod +x startup.sh

ENV UI_ENV=prod

COPY --from=ui-builder /app/clarity-seed/prod/dist /clarity-seed/prod/dist
COPY --from=ui-builder /app/clarity-seed/test/dist /clarity-seed/test/dist
COPY --from=ui-builder /app/clarity-seed/dev/dist /clarity-seed/dev/dist

CMD ["./startup.sh"]
