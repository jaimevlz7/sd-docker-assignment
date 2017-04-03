FROM ubuntu:16.04
MAINTAINER jaime.velez2@correo.icesi.edu.co

ADD config/my_key.pub /tmp

#Configure repository
RUN apt-key add /tmp/my_key.pub 
RUN rm -f /tmp/my_key.pub 
RUN echo "deb http://192.168.131.36:8080/ xenial main" > sources.list 
RUN chmod 777 /tmp

#Install packages
RUN apt-get clean
RUN apt-get update -y 
RUN apt-get install postgresql -y
RUN apt-get install postgresql-contrib -y

#Giving permisions
EXPOSE 5432


