FROM nginx:stable-alpine

ENV NGINX_DIR=/etc/nginx

# Function to retry a command up to 5 times
RUN retry() { \
  local n=1; \
  local max=5; \
  local delay=5; \
  while true; do \
    "$@" && break || { \
      if [[ $n -lt $max ]]; then \
        ((n++)); \
        echo "Command failed. Attempt $n/$max:"; \
        sleep $delay; \
      else \
        echo "The command has failed after $n attempts."; \
        return 1; \
      fi; \
    }; \
  done; \
}

RUN retry apk update --no-cache && \
    retry apk upgrade --no-cache && \
    retry apk add --no-cache openssl

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
