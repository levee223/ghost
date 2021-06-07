## 1st stage

FROM node:14-alpine3.12 as build

COPY . /work/sources

RUN apk update \
    && apk upgrade \
    && apk add git \
    && cd /work/sources \
    && yarn config set network-timeout 1200000 \
    && yarn install --prod \
    && cp -rp node_modules /work/. \
    && yarn install \
    && yarn global add grunt-cli \
    && grunt release \
    && cp -rpT /work/sources/.docker/overlay /work/sources/.build/release/

## 2nd stage

FROM node:14-alpine3.12

ENV NODE_ENV production
ENV GHOST_INSTALL /var/lib/ghost
ENV GHOST_CONTENT /var/lib/ghost/content

EXPOSE 2368
VOLUME ["$GHOST_CONTENT/images", "$GHOST_CONTENT/settings"]
WORKDIR $GHOST_INSTALL
CMD ["node", "index.js"]

RUN apk upgrade --no-cache \
    && rm -rfv /tmp/*

COPY --from=build /work/node_modules $GHOST_INSTALL/node_modules
COPY --from=build /work/sources/.build/release $GHOST_INSTALL/
