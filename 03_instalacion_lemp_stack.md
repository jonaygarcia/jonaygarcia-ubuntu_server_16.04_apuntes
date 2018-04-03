# Instalación de LEMP Stack

__LEMP stack__ es un conjunto de software que se usa para servir páginas web dinámicas y aplicaciones web desarrolladas con PHP. Es un acrónimo que describe el uso de:

* Linux como sistema operativo.
* Nginx como servicio web.
* MySQL como gestor de base de datos.
* PHP como lenguaje de programación.

> __Nota__: existe otro conjunto de software llamado __LAMP stack__ que la única diferencia con respeco a _LEMP stack_ es que usa __Apache__ como servidor web en vez de _Nginx_.

## Instalación de Nginx

__Nginx__ es un servidor web que se utiliza para servir páginas web. Es más moderno y eficiente que _Apache_.

> __Nota__: Todo el software que usaremos se puede descargar directamente de los repositorios oficiales de Ubuntu utilizando el gestor de paquetes __apt__.

Para instalar _Nginx_:

```bash
$ sudo apt-get update
$ sudo apt-get install nginx
```

Una vez instalado, comprobamos que el servicio está ejecutándose en la VM:

```bash
$ systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since lun 2018-04-02 14:46:42 WEST; 12s ago
 Main PID: 1808 (nginx)
   CGroup: /system.slice/nginx.service
           ├─1808 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           └─1809 nginx: worker process

abr 02 14:46:42 ubuntu systemd[1]: Starting A high performance web server and a reverse proxy server...
abr 02 14:46:42 ubuntu systemd[1]: nginx.service: Failed to read PID from file /run/nginx.pid: Invalid argument
abr 02 14:46:42 ubuntu systemd[1]: Started A high performance web server and a reverse proxy server.
```

Por defecto, el servidor escucha en el puerto 80, pero como vamos a acceder desde fuera de la VM, debemos crear un _port forwarding_ (redireccionamiento de puertos), para que, cuando intentemos acceder al puerto 8080 en nuestro equipo, redirija al puerto 80 de la VM, que es donde está ejecutándose el servidor web, para ello abrimos la configuración de nuestra VM, en concreto, la sección de _Network_ y pulsamos sobre _Port Forwarding_:

![img_01][img_01]

Creamos una nueva regla con los siguientes datos:

* Name: HTTP.
* Protocol: TCP.
* Host Port: 8080.
* Guest Port: 80.

![img_02][img_02]

Ahora, para comprobar que funciona, en el navegador de nuestro equipo, abrimos un navegador web e intentamos acceder a la URL http://localhost:8080. Deberíamos ver la siguiente pantalla:

![img_03][img_03]

## Instalación del procesador PHP

Ya tenemos instalado el servidor web, que es capaz de mostrar contenido estatico (HTML5 y CSS3). Para generar contenido dinámico debemos configurar el Nginx para que sea capaz de procesar páginas web creadas en PHP.

Como Nginx no contiene procesamiento PHP nativo como otros servidores web, necesitaremos instalar php-fpm, que significa "administrador de procesos fastCGI". Le diremos a Nginx que pase las solicitudes de PHP a este software para su procesamiento.

Para instalar PHP:

```bash
$ sudo apt-get install php-fpm php-mysql
```

### Configurar el procesador PHP

Una vez instalado PHP, debemos realizar algunas configuraciones.

La primera es abrir el fichero _/etc/php/7.0/fpm/php.ini_ y descomentar la línea _cgi.fix_pathinfo_ y configurarlo con el valor _1_ (Se encuentra en la línea 760 del fichero):

```bash
$ sudo nano /etc/php/7.0/fpm/php.ini
...
cgi.fix_pathinfo=1
```

Salvamos los cambios en el fichero (CTRL+o) y salimos del editor nano (CTRL+x).

Para que los cambios tengan efecto hay que reiniciar el procesador de PHP:

```bash
$ sudo systemctl restart php7.0-fpm
```

La segunda es configurar el servidor web _Nginx_ para que sea capaz de servir páginas web escritas en PHP. Para ello debemos abrir el fichero _/etc/nginx/sites-available/default_, borrar todo el contenido y añadir el siguiente:

```bash
$ sudo nano /etc/nginx/sites-available/default
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.php index.html index.htm index.nginx-debian.html;

    server_name server_domain_or_IP;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

Salvamos los cambios en el fichero (CTRL+o) y salimos del editor nano (CTRL+x).

Comprobamos la sintaxis del fichero de configuración por si hubiera errores:

```bash
$ sudo nginx -t
```

Si no hay ningún error, recargamos la configuración del servidor Nginx para que lea los cambios:

```bash
$ sudo systemctl reload nginx
```

Ahora creamos una página web en PHP. Por defecto, estos ficheros han de crearse en la ubicación _/var/www/html_. Creamos el fichero _info.php_ dentro de la carpeta anterior:

```bash
$ sudo nano /var/www/html/info.php
<?php
phpinfo();
```

Salvamos los cambios en el fichero (CTRL+o) y salimos del editor nano (CTRL+x).

Ahora, para comprobar que funciona, en el navegador de nuestro equipo, abrimos un navegador web e intentamos acceder a la URL http://localhost:8080/info.php. Deberíamos ver la siguiente pantalla:

![img_04][img_04]

## Instalación de MySQL

Ahora que ya tenemos el servidor web instalado y configurado para que genere contenido dinámico con PHP, vamos a instalar __MySQL__, que es un sistema gestor de base de datos que permite almacenar y gestionar datos para nuestro sitio web.

Para instalarlo ejecutar el siguiente comando:

```bash
$ sudo apt-get install mysql-server
```

Durante el proceso de instalación nos pedirá introducir el password del usuario root, en este caso, pondremos la misma que el usuario de la VM, es decir, _formacion_:

![img_05][img_05]

Nos pide de nuevo introducir el password del usuario root, que debe coincidir con el que introdujimos en la ventana anterior:

![img_06][img_06]

Una vez instalado, comprobamos que el servicio del MySQL se está ejecutando en la VM:

```bash
$ systemctl status mysql
● mysql.service - MySQL Community Server
   Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
   Active: active (running) since lun 2018-04-02 15:51:24 WEST; 2min 7s ago
 Main PID: 10791 (mysqld)
   CGroup: /system.slice/mysql.service
           └─10791 /usr/sbin/mysqld

abr 02 15:51:23 ubuntu systemd[1]: Starting MySQL Community Server...
abr 02 15:51:24 ubuntu systemd[1]: Started MySQL Community Server.
```

Ahora probamos a entrar a la consola de MySQL:

```bash
$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.21-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

```

## Instalar phpMyAdmin

__phpMyAdmin__ es una herramienta web escrita en PHP cuya finalidad es gestionar la administración de una base de datos _MySQL_ a través del navegador web. Está  disponible bajo la licencia GPL (General Public License y en más de 50 idiomas este proyecto se encuentra vigente desde el año 1998. 

Con esta herramienta podemos hacer cosas como:

* Crear y eliminar bases de datos.
* Crear y eliminar usuarios.
* Administrar privilegios.
* Crear, eliminar y modificar tablas (borrar, editar y añadir campos).
* Exportar e importar bases de datos.

Para instalar _phpMyAdmin_ debemos instalar el paquete _phpmyadmin_ utilizando el gestor de paquetes _apt_:

```bash
$ sudo apt-get install phpmyadmin
```

Durante la instalación nos pide elegir el servidor web en el que se va a ejecutar _phpMyAdmin_ y nos da dos opciones: apache2 y lighttpd. Como el servidor _Nginx_ no está en la lista, no marcamos ninguna opción y pulsamos _Aceptar_:

![img_07][img_07]

En la siguiente ventan nos pide si queremos configurar la conexión a base de datos con la herramienta _dbconfig-common_, decimos que sí:

![img_08][img_08]

Nos pide proporcionar el password del usuario _root_ de la base de datos MySQL, que anteriormente habíamos configurado como _formacion_:

![img_09][img_09]

En la siguiente pantalla nos piden confirmación de la contraseña, hay que poner la misma que la ventana anterior:

![img_10][img_10]

Una vez instalado, el _phpMyAdmin_ se instala en la ubicación _/usr/share/phpmyadmin_, como queremos acceder a ella a través de nuestro servidor _Nginx_, crearemos un enlace simbólico dentro de la carpeta _/var/www/html_:

```bash
$ sudo ln -s /usr/share/phpmyadmin /var/www/html/phpmyadmin
```

Vemos que se ha creado correctamente el enlace simbólico en la carpeta _/var/www/html_:

```bash
$ ls -l /var/www/html/
total 8
-rw-r--r-- 1 root root 612 abr  2 14:46 index.nginx-debian.html
-rw-r--r-- 1 root root  17 abr  2 15:27 info.php
lrwxrwxrwx 1 root root  21 abr  3 13:13 phpmyadmin -> /usr/share/phpmyadmin
```

Una vez creado el enlace simbólico, probamos a acceder al _phpMyAdmin_ a través de la URL http://localhost:8080/phpmyadmin.

En la ventana de inicio de sesión nos pide un usuario de base de datos, en este caso ponemos el usuario _root_ (el único usuario que hemos creado hasta el momento) y password el que configuramos durante el proceso de instalación, en este caso formación:

![img_11][img_11]

Si todo ha ido bien, debemos ver la ventana de gestión de la base de datos _MySQL_:

![img_12][img_12]

## Instalar WordPress

__WordPress__ es un sistema de gestión de contenidos o CMS (por sus siglas en inglés, Content Management System) enfocado a la creación de cualquier tipo de página web. Originalmente alcanzó una gran popularidad en la creación de blogs, para convertirse con el tiempo en una de las principales herramientas para la creación de páginas web comerciales. Está desarrollado en el lenguaje __PHP__ para entornos que ejecuten __MySQL__ como gestor de Base de datos  y __Apache__ o __Nginx__ como servidor web. Se distribuye bajo licencia GPL.

Para instalar _WordPress_ hay primero hay que descargarselo. Para ello podemos usar el comando _wget_ que nos permite descar directamente un fichero a nuestro equipo.

La última versión de _WordPress_ era la versión 4.9.4 cuando se hizo este tutorial y la podemos descargar desde la URL [https://wordpress.org/latest.tar.gz](https://wordpress.org/latest.tar.gz). Vamos a descargarla en nuestro equipo en la carpeta _/var/www/html_:

```bash
$ cd /var/www/html
$ sudo wget https://wordpress.org/latest.tar.gz 
```

> __Nota__: Si queremos instalar una otra versión de WordPress, podemos ver todas las versiones disponibles en la URL [https://wordpress.org/download/release-archive/](https://wordpress.org/download/release-archive/)

El fichero __latest.tar.gz__ es un fichero comprimido, para descomprimirlo debemos usar el comando __tar__:

```bash
$ sudo tar -xzvf latest.tar.gz 
```

Esto descomprime el contenido del fichero en la ubicación _/var/www/html_. En este caso crea una carpeta llamada __wordpress__:

```bash
formacion@ubuntu:/var/www/html$ ls -l /var/www/html/
total 8380
-rw-r--r-- 1 root   root        612 abr  2 14:46 index.nginx-debian.html
-rw-r--r-- 1 root   root         17 abr  2 15:27 info.php
-rw-r--r-- 1 root   root    8565525 feb  6 15:51 latest.tar.gz
lrwxrwxrwx 1 root   root         21 abr  3 13:13 phpmyadmin -> /usr/share/phpmyadmin
drwxr-xr-x 5 nobody nogroup    4096 feb  6 15:49 wordpress
```

Ahora debemos cambiar el propietario de la carpeta al usuario __www-data__, que es el usuario del sistema que ejecuta el proceso del _Nginx_:

```bash
$ sudo chown -R www-data:www-data /var/www/html/wordpress
```

> __Nota__: Esto es necesario hacerlo ya que la aplicación del _WordPress_ necesita permisos de escritura dentro del directorio _/var/www/html/wordpress_ para poder funcionar correctamente. El usuario que ejecuta el proceso del _Nginx_ es el usuario del sistema _www-data_, y es a este usuario al que hay que darle permisos de escritura en esta carpeta.

Vemos cómo el propietario de la carpeta wordpress ha pasado de _nobody:nogroup_ a _www-data:www_data_:

```bash
formacion@ubuntu:/var/www/html$ ls -l /var/www/html/
total 8380
-rw-r--r-- 1 root     root         612 abr  2 14:46 index.nginx-debian.html
-rw-r--r-- 1 root     root          17 abr  2 15:27 info.php
-rw-r--r-- 1 root     root     8565525 feb  6 15:51 latest.tar.gz
lrwxrwxrwx 1 root     root          21 abr  3 13:13 phpmyadmin -> /usr/share/phpmyadmin
drwxr-xr-x 5 www-data www-data    4096 feb  6 15:49 wordpress
```

## Crear una Base de Datos en MySQL

Para poder instalar correctamente _WordPress_ es necesario crear previamente una base de datos en MySQL y un usuario en base de datos que tenga todos los privilegios en esa base de datos.

Primero accedemos al gestor de base de datos _MySQL_ a través de línea de comandos:

```bash
formacion@ubuntu:~$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.21-0ubuntu0.16.04.1 (Ubuntu)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Luego creamos la base de datos llamada _wordpress_:

```bash
mysql> CREATE DATABASE wordpress;
Query OK, 1 row affected (0,00 sec)
```

Posteriormente creamos el usuario llamado _wordpress_ que tendrá como password _wordpress_ también:

```bash
mysql> CREATE USER wordpress@'localhost' identified by 'wordpress';
Query OK, 0 rows affected (0,00 sec)
```

> __Nota__: El usuario wordpress@'localhost' sólo puede conectarse a la base de datos desde la misma máquina donde está instalado el gestor de base de datos.

Ahora asignamos todos los privilegios al usuario _wordpress@'localhost'_ dentro de la base de datos _wordpress_:

```bash
mysql> GRANT ALL PRIVILEGES ON wordpress.* to wordpress@'localhost';
Query OK, 0 rows affected (0,00 sec)
```

Para salir del gestor de base de datos:

```bash
mysql> exit
```

[img_01]: ./assets/img/02_instalacion_lemp_stack/01.png "HTTP Port Forwarding"
[img_02]: ./assets/img/02_instalacion_lemp_stack/02.png "HTTP Port Forwarding"
[img_03]: ./assets/img/02_instalacion_lemp_stack/03.png "Nginx OK"
[img_04]: ./assets/img/02_instalacion_lemp_stack/04.png "PHP OK"
[img_05]: ./assets/img/02_instalacion_lemp_stack/05.png "MySQL root password"
[img_06]: ./assets/img/02_instalacion_lemp_stack/06.png "MySQL root password"
[img_07]: ./assets/img/02_instalacion_lemp_stack/07.png "MySQL root password"
[img_08]: ./assets/img/02_instalacion_lemp_stack/08.png "MySQL root password"
[img_09]: ./assets/img/02_instalacion_lemp_stack/09.png "MySQL root password"
[img_10]: ./assets/img/02_instalacion_lemp_stack/10.png "MySQL root password"
[img_11]: ./assets/img/02_instalacion_lemp_stack/11.png "MySQL root password"
[img_12]: ./assets/img/02_instalacion_lemp_stack/12.png "MySQL root password"






