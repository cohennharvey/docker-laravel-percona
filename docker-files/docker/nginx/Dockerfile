FROM nginx:1.19
LABEL maintainer="Cohen Harvey <cohen.n.harvey@gmail.com>"

RUN DEBIAN_FRONTEND="noninteractive" apt-get clean && apt-get update

COPY ./resources/config/default.conf /etc/nginx/conf.d/default.conf

EXPOSE 8080
EXPOSE 8443
