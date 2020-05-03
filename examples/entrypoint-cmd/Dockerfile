FROM ubuntu:18.04

LABEL maintainer="marta.arcones@gmail.com"

RUN apt update ; apt install -y nginx

RUN echo "Hello from ubuntu container" > /var/www/html/index.html

EXPOSE 80

# This command will run always
ENTRYPOINT ["/usr/sbin/nginx"]

# This is the default arg for ENTRYPOINT and can be easily overriden
CMD ["-h"]