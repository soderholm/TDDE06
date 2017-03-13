FROM ubuntu:latest

ENV RELEASE xenial
ENV KEY ABF5BD827BD9BF62

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $KEY


RUN echo "deb http://nginx.org/packages/ubuntu/ $RELEASE nginx" >> /etc/apt/sources.list \
	&& echo "deb-src http://nginx.org/packages/ubuntu/ $RELEASE nginx" >> /etc/apt/sources.list \
	&& apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $KEY \
	&& apt-get update \
	&& apt-get install --no-install-recommends --no-install-suggests -y nginx

# forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
	&& ln -sf /dev/stderr /var/log/nginx/error.log

COPY nginx.conf /

EXPOSE 80 443

# Testing the config file
RUN ["nginx", "-t", "-c", "/nginx.conf"] 

CMD ["nginx","-c","/nginx.conf", "-g", "daemon off;"]