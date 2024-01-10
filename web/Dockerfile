FROM ubuntu:latest

RUN mkdir /web
WORKDIR /web

RUN apt update
RUN DEBIAN_FRONTEND=noninteractive apt install -y --no-install-recommends tzdata
RUN ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime

RUN echo "Asia/Taipei" > /etc/timezone
RUN dpkg-reconfigure --frontend noninteractive tzdata
RUN echo "Asia/Taipei" > /etc/timezone
# RUN apt install -y tzdata

RUN apt install -y curl net-tools vim


RUN apt install -y libpcre3 libpcre3-dev
RUN apt install -y python3 python3-pip
RUN apt install -y libpq-dev python3-dev

RUN pip install django
RUN pip install djangorestframework
RUN pip install django-cors-headers
RUN pip install requests psycopg2 ldap3
RUN pip install uwsgi --no-cache-dir