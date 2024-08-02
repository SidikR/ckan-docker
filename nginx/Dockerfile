FROM nginx:stable-alpine

ENV NGINX_DIR=/etc/nginx

# Define the retry function
RUN echo '#!/bin/sh\n\
n=1\n\
max=5\n\
delay=5\n\
while true; do\n\
  "$@" && break || {\n\
    if [ $n -lt $max ]; then\n\
      n=$((n+1))\n\
      echo "Command failed. Attempt $n/$max:"\n\
      sleep $delay\n\
    else\n\
      echo "The command has failed after $n attempts."\n\
      exit 1\n\
    fi\n\
  }\n\
done' > /usr/local/bin/retry && chmod +x /usr/local/bin/retry

# Use the retry function for apk commands
RUN /usr/local/bin/retry apk update --no-cache && \
    /usr/local/bin/retry apk upgrade --no-cache && \
    /usr/local/bin/retry apk add --no-cache openssl

COPY setup/nginx.conf ${NGINX_DIR}/nginx.conf
COPY setup/index.html /usr/share/nginx/html/index.html
COPY setup/default.conf ${NGINX_DIR}/conf.d/

RUN mkdir -p ${NGINX_DIR}/certs

ENTRYPOINT \
  openssl req \
    -subj '/C=DE/ST=Berlin/L=Berlin/O=None/CN=localhost' \
    -x509 -newkey rsa:4096 \
    -nodes -keyout /etc/nginx/ssl/default_key.pem \
    -keyout ${NGINX_DIR}/certs/ckan-local.key \
    -out ${NGINX_DIR}/certs/ckan-local.crt \
    -days 365 && \
  nginx -g 'daemon off;'
