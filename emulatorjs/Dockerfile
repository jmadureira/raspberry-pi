# Stage 1: download and extract a self-hosted EmulatorJS release (cores included),
# so games don't depend on the EmulatorJS CDN at runtime.
FROM alpine:3.20 AS emulatorjs

ARG EMULATORJS_VERSION=4.2.3

RUN apk add --no-cache curl 7zip \
    && curl -fsSL -o /tmp/emulatorjs.7z \
       "https://github.com/EmulatorJS/EmulatorJS/releases/download/v${EMULATORJS_VERSION}/${EMULATORJS_VERSION}.7z" \
    && mkdir -p /tmp/extract /emulatorjs \
    && 7z x -o/tmp/extract /tmp/emulatorjs.7z > /dev/null \
    # The archive layout has changed between releases; locate the data/ folder holding loader.js
    && DATA_DIR="$(dirname "$(find /tmp/extract -path '*/data/loader.js' | head -n 1)")" \
    && test -n "$DATA_DIR" && test "$DATA_DIR" != "." \
    && mv "$DATA_DIR" /emulatorjs/data \
    && rm -rf /tmp/extract /tmp/emulatorjs.7z

# Stage 2: serve the site with nginx
FROM nginx:1.27-alpine

COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=emulatorjs /emulatorjs /usr/share/nginx/html/emulatorjs
COPY site/ /usr/share/nginx/html/

EXPOSE 80
