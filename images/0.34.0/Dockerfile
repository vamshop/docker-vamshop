FROM node:8
MAINTAINER VamShop <vamshop@gmail.com>

ENV NGINX_CODENAME jessie
ENV VAMSHOP_VERSION 0.34.0

# install requirements and NGINX
RUN echo "deb http://nginx.org/packages/debian/ ${NGINX_CODENAME} nginx" >> /etc/apt/sources.list \
	&& apt-get update && apt-get install --no-install-recommends --no-install-suggests -y --force-yes \
		bash \
		zip \
		unzip \
		wget \
		curl \
		nano \
		ca-certificates \
		nginx\
		nginx-module-image-filter

# install PM2
RUN npm install pm2 -g

# download project
RUN mkdir -p /var/www \
    && cd /var/www \
    && wget -O vamshop.zip https://github.com/vamshop/vamshop/archive/v$VAMSHOP_VERSION.zip \
    && unzip vamshop.zip \
    && rm vamshop.zip \
    && mv vamshop-$VAMSHOP_VERSION vamshop

# overwrite config
COPY config/* /var/www/vamshop/config/

# overwrite PM2 process file
COPY process.json /var/www/vamshop/

# Nginx config
COPY nginx/nginx.conf /etc/nginx/
COPY nginx/default.conf /etc/nginx/conf.d/

# script to run Nginx and PM2
COPY docker-entrypoint.sh /usr/local/bin/

# build project
RUN cd /var/www/vamshop \
    && npm install \
    && npm run build

WORKDIR /var/www/vamshop

EXPOSE 80

# start Nginx, PM2
ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
