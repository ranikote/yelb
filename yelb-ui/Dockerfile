# ---------- STAGE 1: Build Angular UI ----------
FROM node:12.22 AS ui-builder

LABEL maintainer="massimo@it20.info"

WORKDIR /

# Install global dependencies and specific versions
RUN npm install -g @angular/cli@6.0.0 && \
    npm install node-sass@4.13.1

# Copy new files to inject into Clarity seed project
COPY clarity-seed-newfiles/ clarity-seed-newfiles/

# Clone the clarity-seed repo and checkout a specific commit
RUN git clone https://github.com/vmware/clarity-seed.git && \
    cd clarity-seed && \
    git checkout f3250ee26ceb847f61bb167a90dc957edf6e7f43

# Copy modified files into the seed project and remove unwanted components
RUN cp /clarity-seed-newfiles/src/index.html /clarity-seed/src/index.html && \
    cp /clarity-seed-newfiles/src/styles.css /clarity-seed/src/styles.css && \
    cp /clarity-seed-newfiles/src/env.js /clarity-seed/src/env.js && \
    cp /clarity-seed-newfiles/src/app/app* /clarity-seed/src/app && \
    cp /clarity-seed-newfiles/src/app/env* /clarity-seed/src/app && \
    cp /clarity-seed-newfiles/src/environments/env* /clarity-seed/src/environments && \
    cp /clarity-seed-newfiles/package.json /clarity-seed/package.json && \
    cp /clarity-seed-newfiles/angular-cli.json /clarity-seed/.angular-cli.json && \
    rm -r /clarity-seed/src/app/home /clarity-seed/src/app/about

# Move into project dir and install dependencies
WORKDIR /clarity-seed
RUN npm install

# Build Angular project for all environments
RUN ng build --environment=prod --output-path=./prod/dist/ -aot -vc -cc -dop --buildOptimizer && \
    ng build --environment=test --output-path=./test/dist/ && \
    ng build --environment=dev --output-path=./dev/dist/

# ---------- STAGE 2: NGINX for serving ----------
FROM nginx:1.17.10 AS nginx-stage

LABEL maintainer="massimo@it20.info"

WORKDIR /

# Copy startup script
COPY startup.sh startup.sh
RUN chmod +x startup.sh

ENV UI_ENV=prod

# Use a named stage (ui-builder) for copying files
COPY --from=ui-builder /clarity-seed/prod/dist /clarity-seed/prod/dist
COPY --from=ui-builder /clarity-seed/test/dist /clarity-seed/test/dist
COPY --from=ui-builder /clarity-seed/dev/dist /clarity-seed/dev/dist

CMD ["./startup.sh"]

