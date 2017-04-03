# sd-docker-assignment
**Cristian Gonzalez - Jaime Vélez**

1. Instalación de aptly:

Se adiciona el repositorio de aplty a archivo sources.list.
* echo deb http://repo.aptly.info/ squeeze main >> /etc/apt/sources.list

Se adiciona la key:
* sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 9E3E53F19C7DE460

Se realiza un apt-get update
* apt-get update

Se instala el paquete de aptly
* apt-get install aptly

2. Creación del mirror en aplty:

Se genera una key RSA para crear los repositorios de aplty
* gpg --gen-key

Generamos entropia
* cat /dev/urandom

Importamos la key creada
* gpg --no-default-keyring --keyring /usr/share/keyrings/ubuntu-archive-keyring.gpg --export | gpg--no-default-keyring --keyring trustedkeys.gpg --import

Creamos un repositorio llamado xenial-main-python3, con los siguientes paquetes mysql-server,python3, postgresql, postgresql-contrib.
* aptly mirror create -architectures=amd64 -filter='Priority (required) | Priority (important) | Priority(standard) | mysql-server | python3 | postgresql | postgresql-contrib' -filter-with-deps xenial-mainpython3 http://mirror.upb.edu.co/ubuntu/ xenial main

Actualizamos el mirror para hacer un cache de los paquetes a guardar.
* aptly mirror update xenial-main-python3

Creamos un snapshot del mirror para publicarlo.
* aptly snapshot create xenial-snapshot-python3 from mirror xenial-main-python3


Publicamos el mirror.
* aptly publish snapshot xenial-snapshot-python3

Exportamos la key para acceder a los repositorios del mirror.
* gpg --export --armor > my_key.pub

Corremos el servicio:
* aptly serve

3. Contenedor docker con postgres:

Docker:
``` 
FROM ubuntu:16.04
MAINTAINER jaime.velez2@correo.icesi.edu.co
ADD config/my_key.pub /tmp
RUN apt-key add /tmp/my_key.pub
RUN rm -f /tmp/my_key.pub
RUN echo "deb http://192.168.131.36:8080/ xenial main" > sources.list
RUN chmod 777 /tmp
RUN apt-get clean
RUN apt-get update -y
RUN apt-get install postgresql -y
RUN apt-get install postgresql-contrib -y
EXPOSE 5432
``` 
Creamos una subinterfaz para poder acceder al mirror.
* sudo ifconfig enp5s0:0 192.168.131.109
* ping 192.168.131.36

Copiamos la llave generada en /tmp en nuestro equipo host.

A partir del archivo dockerfile creamos una imagen
* docker build .

A partir de la imagen creamos un contenedor. Además mapeamos el puerto 5433 del contenedor al 5432 del host.
* sudo docker run -it -p 5433:5432 --rm $ID_IMAGEN$ /bin/bash

Finalmente obtenemos el contenedor con el servidor postgres instalado. Entramos por consola y verificamos que este instalado verificando la versión instalada.
* psql –version
