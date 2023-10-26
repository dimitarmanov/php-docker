FROM nginx:latest

ARG SERVICE
ARG CONFIG_FILE_NAME="default.conf"

COPY /docker/nginx/default.conf /etc/nginx/conf.d/default.conf
COPY ./public /home/dmanov/app/public

RUN sed -i "s/\$SERVICE/$SERVICE/g" /etc/nginx/conf.d/default.conf

EXPOSE 80
