FROM nginx

ARG DEBIAN_FRONTEND=noninteractive

# Update and install openssl
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils && \
    apt-get -y --no-install-recommends install --fix-missing openssl && \ 
    rm -rf /var/lib/apt/lists/*
RUN cd /etc/nginx && echo -e "\n\n\n\n\n\n\n" | openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/cert.key -out /etc/nginx/cert.crt

COPY proxy_params /etc/nginx/proxy_params
RUN rm /etc/nginx/conf.d/default.conf
COPY default /etc/nginx/conf.d/default.conf
RUN rm /etc/nginx/nginx.conf
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80 443
