FROM nginx:stable-alpine

ENV NGINX_DIR=/etc/nginx

# Function to retry a command up to 5 times
RUN set -e; \
  max=5; \
  count=1; \
  until apk update --no-cache; do \
    if [ $count -eq $max ]; then \
      echo "Failed to update after $count attempts."; \
      exit 1; \
    fi; \
    echo "Retrying update ($count/$max)..."; \
    count=$((count + 1)); \
    sleep 5; \
  done

RUN set -e; \
  max=5; \
  count=1; \
  until apk upgrade --no-cache; do \
    if [ $count -eq $max ]; then \
      echo "Failed to upgrade after $count attempts."; \
      exit 1; \
    fi; \
    echo "Retrying upgrade ($count/$max)..."; \
    count=$((count + 1)); \
    sleep 5; \
  done

RUN set -e; \
  max=5; \
  count=1; \
  until apk add --no-cache openssl; do \
    if [ $count -eq $max ]; then \
      echo "Failed to add openssl after $count attempts."; \
      exit 1; \
    fi; \
    echo "Retrying add openssl ($count/$max)..."; \
    count=$((count + 1)); \
    sleep 5; \
  done

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
