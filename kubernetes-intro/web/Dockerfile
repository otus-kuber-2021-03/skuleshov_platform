FROM alpine:latest
RUN apk update && apk add nginx

RUN addgroup -g 1001 app && \
    adduser -u 1001 -G app -D app

RUN mkdir /app && \
    mkdir /run/nginx && \
    touch /run/nginx/nginx.pid && \
    chown -R app:app /run/nginx && \
    chown -R app:app /var/log/nginx && \
    chown -R app:app /var/lib/nginx && \
    chown -R app:app /app

COPY app/* /app/
COPY nginx.conf /etc/nginx/nginx.conf
USER 1001
EXPOSE 8000
CMD [ "nginx" ]
