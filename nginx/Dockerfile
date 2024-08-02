FROM nginx:stable-alpine

ENV NGINX_DIR=/etc/nginx

# Update dan install openssl
# RUN apk update --no-cache && \
#     apk upgrade --no-cache && \
#     apk add --no-cache openssl

RUN sed -i 's/dl-cdn.alpinelinux.org/dl-5.alpinelinux.org/g' /etc/apk/repositories && \
    apk update --no-cache && \
    apk upgrade --no-cache && \
    apk add --no-cache openssl

# Copy file konfigurasi
COPY setup/nginx.conf ${NGINX_DIR}/nginx.conf
COPY setup/index.html /usr/share/nginx/html/index.html
COPY setup/default.conf ${NGINX_DIR}/conf.d/

# Membuat direktori untuk sertifikat
RUN mkdir -p ${NGINX_DIR}/certs

# Generate sertifikat SSL
# RUN openssl req \
#     -subj '/C=DE/ST=Berlin/L=Berlin/O=None/CN=localhost' \
#     -x509 -newkey rsa:4096 \
#     -nodes -keyout ${NGINX_DIR}/certs/ckan-local.key \
#     -out ${NGINX_DIR}/certs/ckan-local.crt \
#     -days 365

# Menentukan default command
CMD ["nginx", "-g", "daemon off;"]
