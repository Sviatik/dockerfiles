FROM nginx

RUN apt-get update && \
    apt-get install -y openssl && \
		openssl req -x509 -newkey rsa:4096 -keyout /etc/nginx/default.key -out /etc/nginx/default.crt \
		-days 10000 -nodes -subj "/C=US/ST=Test/L=Test/O=Test/CN=localhost"

COPY index.html /usr/share/nginx/html
COPY default.conf /etc/nginx/conf.d/

EXPOSE 80
EXPOSE 443

