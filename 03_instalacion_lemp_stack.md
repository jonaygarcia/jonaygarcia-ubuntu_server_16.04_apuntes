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

[img_01]: ./assets/img/02_instalacion_lemp_stack/01.png "HTTP Port Forwarding"
[img_02]: ./assets/img/02_instalacion_lemp_stack/02.png "HTTP Port Forwarding"
[img_03]: ./assets/img/02_instalacion_lemp_stack/03.png "Nginx OK"
[img_04]: ./assets/img/02_instalacion_lemp_stack/04.png "PHP OK"
















On Ubuntu 16.04, Nginx is configured to start running upon installation.

If you have the ufw firewall running, as outlined in our initial setup guide, you will need to allow connections to Nginx. Nginx registers itself with ufw upon installation, so the procedure is rather straight forward.

It is recommended that you enable the most restrictive profile that will still allow the traffic you want. Since we haven't configured SSL for our server yet, in this guide, we will only need to allow traffic on port 80.

You can enable this by typing:

    sudo ufw allow 'Nginx HTTP'

You can verify the change by typing:

    sudo ufw status

You should see HTTP traffic allowed in the displayed output:

Output
Status: active

To                         Action      From
--                         ------      ----
OpenSSH                    ALLOW       Anywhere                  
Nginx HTTP                 ALLOW       Anywhere                  
OpenSSH (v6)               ALLOW       Anywhere (v6)             
Nginx HTTP (v6)            ALLOW       Anywhere (v6)

With the new firewall rule added, you can test if the server is up and running by accessing your server's domain name or public IP address in your web browser.

If you do not have a domain name pointed at your server and you do not know your server's public IP address, you can find it by typing one of the following into your terminal:

    ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'

This will print out a few IP addresses. You can try each of them in turn in your web browser.

As an alternative, you can check which IP address is accessible as viewed from other locations on the internet:

    curl -4 icanhazip.com

Type one of the addresses that you receive in your web browser. It should take you to Nginx's default landing page: