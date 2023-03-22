FROM envimate/hugo:latest as build
WORKDIR /site
COPY . .
RUN hugo

FROM nginxinc/nginx-unprivileged:alpine
COPY --from=build /site/public /usr/share/nginx/html

WORKDIR /usr/share/nginx/html