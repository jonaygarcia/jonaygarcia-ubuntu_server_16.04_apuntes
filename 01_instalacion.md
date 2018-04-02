# Instalar Ubuntu Server 14.04 en VirtualBox

## Descargar imagen ISO

La imagen ISO de _Ubuntu Server 16.04 LTS_ puede descargarse de la siguiente URL:

[http://releases.ubuntu.com/16.04.3/ubuntu-16.04.3-server-amd64.iso](http://releases.ubuntu.com/16.04.3/ubuntu-16.04.3-server-amd64.iso)

## Crear la Máquina Virtual

En la ventana principal de VirtualBox, pulsamos en el botón _New_:

![](/assets/img/01_instalacion/01.png)

Configuramos el nombre y el tipo de la Máquina Virtual:

* **Name**: ub\_server\_16.04
* **Type**: Linux
* **Version**: Ubuntu \(64-bit\)

![](/assets/img/01_instalacion/02.png)

La versión de Ubuntu Server está pensada para ejecutar servicios y no trae ningún entorno gráfico instalado, con lo que la cantidad de _memoria RAM_ que le asignemos dependerá del servicio que vayamos a instalar \(MySQL, Tomcat, Nginx, ...\).

Le asignaremos un tamaño de 512MB.

![](/assets/img/01_instalacion/03.png)

Creamos un _disco duro virtual_ que simulará el disco duro de la Máquina Virtual:

![](/assets/img/01_instalacion/04.png)

Existen varios formatos para discos duros virtuales:

* **VDI**: es el formato nativo de VirtualBox y es el que se usa en la mayoría de los casos.
* **VMDK**: \(_VMware Disk Format_\) es un formato desarrollado por y para Vmware, pero VirtualBox también lo soporta. Este formato hace que exportar Máquinas Virtuales entre distintos entornos de virtualización como  \_Vmware Workstation\_ sea más fácil.
* **VHD**: es el formato nativo de Microsoft Virtual PC. Este formato es popular en Productos Microsoft.

En este caso usaremos _VDI_, que es la opción que viene configurada por defecto:

![](/assets/img/01_instalacion/05.png)

Ahora debemos elegir entre:

* **Asignación dinámica** \(D_inamically allocated_\): el tamaño del fichero del disco duro aumenta a medida que se usa el disco hasta alcanzar el tamaño máximo especificado durante la creación del mismo.

* **Tamaño fijo** \(_Fixed size_\): requiere espacio de almacenamiento físico equivalente al tamaño especificado para el disco duro en el momento de crearlo. El tamaño del fichero del disco duro será  del disco duro virtual y no se modificará.

> **Nota**: El formato _VMDK_ tiene la capacidad adicional de dividir el archivo de almacenamiento en archivos de menos de 2GB cada uno, lo cual resulta muy útil si nuestro sistema de archivos tiene algún límite en cuanto al tamaño de ficheros.

![](/assets/img/01_instalacion/06.png)

Asignamos un tamaño de 16GB a nuestro disco duro y pulsamos en el botón _Create_:

![](/assets/img/01_instalacion/07.png)

Vemos cómo se ha creado correctamente la máquina virtual llamada _ub\_server\_16.04_ en el panel de la izquierda:

![](/assets/img/01_instalacion/08.png)

## Configurar la Máquina Virtual

A continuación veremos cómo configurar distintos aspectos de la Máquina Virtual como el número de CPUs, el tipo de red, ...

Para acceder al panel de configuración, pulsar botón derecho sobre la Máquina Virtual y elegir la opción _Settings..._ del menú contextual:

![](/assets/img/01_instalacion/09.png)

En la pestaña _Motherboard_ del menú _System_ podemos ver la cantidad de memoria RAM que tiene asignada la Máquina Virtual:

![](/assets/img/01_instalacion/10.png)

> **Nota**: Sólo se puede ampliar o reducir la cantidad de memoria RAM con la Máquina Virtual apagada.

En la pestaña _Processor_ del menú _System_ podemos ver el número de CPUs que tiene asignada la Máquina Virtual:

![](/assets/img/01_instalacion/11.png)

> **Nota**: Sólo se puede ampliar o reducir el número de CPUs con la Máquina Virtual apagada.

Existen distintos tipos de configuración de las tarjetas de red:

* **NAT**: es la forma más sencilla que tiene una Máquina Virtual para acceder a una red externa. Por lo general, no se requiere ninguna configuración en la red, ni en el _equipo anfitrión_ \(en inglés _Host_\) ni en el _equipo invitado_ \(en inglés _Guest_\). Por este motivo, **es el modo de red por defecto que usa VirtualBox**.

> **Nota**: Entiéndase por _equipo anfitrión_ \(en inglés _Host_\) la máquina donde se encuentra instalado el VirtualBox y _equipo invitado_ \(en inglés Guest\) la Máquina Virtual.

El _modo NAT_ permite a la Máquina Virtual comunicarse con el exterior: navegar por internet, descargar ficheros, leer el correo electrónico, ...

En cambio, si desde el _equipo anfitrión_ quisiéramos comunicarnos con algún servicio del _equipo invitado_ \(p. ej. SSH, MySQL, ...\) necesitaríamos configurar _mapeo de puertos_ \(_Port Forwarding_\) \(esto lo veremos más adelante\).

En el menú _Network_ podemos ver la configuración de la tarjeta de red:

![](/assets/img/01_instalacion/12.png)

En el menú _Storage_ vamos a montar la ISO descargada previamente en la unidad DVD-ROM virtual:

![](/assets/img/01_instalacion/13.png)

Seleccionamos la imagen desde el sistema de ficheros:

![](/assets/img/01_instalacion/14.png)

Comprobamos que la imagen de _Ubuntu Server 16.04_ está montada en la unidad DVD-ROM virtual y pulsamos _OK_:

![](/assets/img/01_instalacion/15.png)

VirtualBox guarda por defecto las Máquinas Virtuales creadas en una ubicación que cambia en función del sistema operativo utilizado:

* **Windows**: "C:\Users\&lt;username&gt;\VirtualBox VMs"

En _Windows_, la Máquina Virtual creada se encontraría en la ruta "C:\Users\&lt;username&gt;\VirtualBox VMs\ub\_server\_16.04" y contiene los siguientes ficheros:

![](/assets/img/01_instalacion/16.png)

A continuación se detallan los ficheros más importantes:

* **ub\_server\_16.04.vbox**: fichero de configuración de la Máquina Virtual.

* **ub\_server\_16.04.vdi**: fichero de disco duro de la Máquina Virtual. Vemos que el tamaño de este fichero es de aproximadamente un poco más de 2,5GB, a pesar de haberlo configurado el tamaño del disco a 16GB. Esto es porque lo hemos configurado el disco en modo _asignación dinámica_, es decir, el tamaño del disco crecerá a medida que se vaya llenando \(descarga de archivos, instalación de software\) hasta un máximo de 16GB.

## Instalación de Ubuntu Server 16.04

Para arrancar la Máquina Virtual, pulsamos el botón _Start_:

![](/assets/img/01_instalacion/17.png)

Seleccionamos el _idioma_ \(nos movemos con los cursores\), en este caso E_spañol_ y pulsamos la tecla _Enter_ :

![](/assets/img/01_instalacion/18.png)

Seleccionamos _Instalar Ubuntu Server_, que es la opción que viene marcada por defecto, y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/19.png)

Seleccionamos la **ubicación**, que servirá para fijar la zona horaria donde nos encontramos. En este caso, seleccionamos _España_, que es la opción que viene marcada por defecto y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/20.png)

No hace falta realizar una detección de nuestro teclado, marcamos la opción _No_, que es la opción que viene marcada por defecto y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/21.png)

Seleccionamos la **distribución de teclado**, en este caso S_panish_, que es la opción que viene marcada por defecto, y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/22.png)

![](/assets/img/01_instalacion/23.png)

> **Nota**: Si la distribucion de teclado que viene marcada por defecto no es  _Spanish_, es que **no hemos elegido el idioma español en uno de los pasos anteriores**. Por lo tanto, debemos movernos con el cursor hasta seleccionar como distribución de teclado  _spanish_. Si no lo hacemos, tendremos una distribución de teclado diferente a la disposición del teclado de nuestro ordenador: por ejemplo, si seleccionamos la distribución de teclado Enligsh, cuando pulsemos , por ejemplo, la tecla '_ñ'_ de nuestro teclado, estaremos insertando en realidad un '_;'_.

Configuramos el **nombre del equipo**, en esta caso escribimos _ubuntu_, que es la opción que viene escrita por defecto, nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/24.png)

Durante el proceso de instalación de Ubuntu debemos crear un usuario. Este usuario podrá realizar cualquier tarea que requiera privilegios de administrador a través del comando **sudo**.

Vamos a configurar un usuario con los siguientes datos:

* **Nombre real del usuario**: formacion.

* **Nombre de usuario para la cuenta**: formacion.​

* **Password del usuario**: formacion.

Configuramos el **nombre real del usuario**. En este campo debemos poner el nombre completo del usuario, por ejemplo, _Jhon Allen Smith_. Es este caso vamos configurarlo a  _cfgs_, nos movemos con la tecla T_abulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/25.png)

Configuramos el **nombre de usuario para la cuenta**. Es este caso vamos configurarlo a _cfgs_, nos movemos con la tecla T_abulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/26.png)

Configuramos el **password del usuario**. Es este caso vamos configurar el password a _cfgs_, nos movemos con la tecla T_abulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/27.png)

Repetimos la misma constraseña que introducimos en el apartado anterior, nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/28.png)

Nos sale un mensaje de aviso indicando que la constraseña elegida tiene menos de 8 caracteres y es considerada una contraseña débil. Nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Sí_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/29.png)

Configuramos la **zona horaria**, en este caso **Atlantic/Canary**. Nos pregunta si esta configuración es correcta. Nos movemos con la tecla T_abulador_ hasta situarnos en la opción _Sí_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/30.png)

En este paso se configura el **particionado del disco**, marcamos la opción _Guiado - utilizar el disco completo y configurar LVM_, que es la opción que viene marcada por defecto y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/31.png)

Elegimos qué discos queremos particionar. Como sólo configuramos un disco duro cuando creamos la Máquina Virtual, nos saldrá una opción, en este caso _SCSI3 \(0,0,0\) \(sda\) - 17.2 GB ATA VBOX HARDDISK_, que es la opción que viene marcada por defecto y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/32.png)

Nos indica que debe guardar el esquema de particionado actual en el disco antes de poder configura el Gestor de volúmenes Lógicos. Nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Sí_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/33.png)

Nos indica si queremos utilizar todo el espacio del disco a la hora de particionarlo. Es este caso viene configurado a _16,7 GB_ por defecto, que es el tamaño máximo del disco. Lo dejamos como está. Nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/34.png)

Nos indica las particiones que va a crear en el disco duro y pregunta si queremos escribir los cambios. Nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Sí_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/35.png)

Si todo va bien deberíamos ver la siguiente pantalla **instalando el sistema**:

![](/assets/img/01_instalacion/36.png)

Esta pantalla nos permite configurar un proxy HTTP, como no es el caso, lo dejamos en blanco. Nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/37.png)

En esta pantalla nos indica si queremos aplicar actualizaciones frecuentemente. Le indicamos _Sin actualizaciones automáticas_, que es la opción que viene por defecto y pusamos la tecla _Enter_:

![](/assets/img/01_instalacion/38.png)

En esta pantalla indicamos los programas que queremos instalar. En principio viene marcada la opción _standard system utilities_, vamos a marcar, además _OpenSSH server_, la cual nos permitirá establecer una conexión remota a la Máquina Virtual. Para ello, nos movemos con el cursor hasta posicionarnos en la opción que queremos marcar y pulsamos la _Barra espaciadora_ para marcarla. A continuación nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Continuar_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/39.png)

En esta pantalla configuramos el **cargador de arranque GRUB**, para ello nos movemos con la tecla T_abulador_ hasta situarnos en la opción _Sí_ y pulsamos la tecla _Enter_:

![](/assets/img/01_instalacion/40.png)

¡Listo! Ya hemos finalizado la instalación de Ubuntu Server 16.04 en nuestra Máquina Virtual. nos movemos con la tecla _Tabulador_ hasta situarnos en la opción _Sí_ y pulsamos la tecla E_nter_:

![](/assets/img/01_instalacion/41.png)

A continuación se reniciará la Máquina Virtual. Una vez reiniciada, veremos la siguiente pantalla, solicitando un usuario para acceder a ella:

![](/assets/img/01_instalacion/42.png)

Introducimos el usuario y password que configuramos durante el proceso de instalación. En este caso:

* **usuario**: cfgs
* **password**: cfgs

Si todo va bien deberíamos acceder al sistema y ver la siguiente pantalla:

![](/assets/img/01_instalacion/43.png)

A continuación escribimos el comando _exit_ para salir del sistema:

`cfgs@ubuntu:$ exit`

![](/assets/img/01_instalacion/42.png)

## Acceso a través de SSH

**SSH** o **Secure Shell**, es un protocolo de administración remota que permite a un usuario controlar una máquina sin estar físicamente delante de ella.

### Port Forwading

Abrimos la configuración de la Máquina Virtual y vamos al menú _Network-Advanded-Port Forwarding_:

![](/assets/img/01_instalacion/44.png)

Creamos una nueva regla que nos permita acceder por SSH a la Máquina Virtual desde nuestro equipo:

* **Name**: SSH
* **Protocol**: TCP
* **Host IP**: vacío
* **Host Port**: 2222
* **Guest IP**: vacío
* **Guest Port**: 22

Lo que estamos haciendo es configurar nuestro equipo para que, cada vez que intentemos conectarnos al puerto 2222, nos redirija el tráfico de red al puerto 22 de nuestra VM.

> **Nota**: Los campos _Host IP_ y _Guest IP_ se dejan vacíos ya que la tarjeta de red se ha configurado en modo _NAT_.

![](/assets/img/01_instalacion/45.png)

Descargamos un cliente SSH para Windows como [PuTTY ](http://www.putty.org)o [MobaXterm](https://mobaxterm.mobatek.net).

Si utilizamos el cliente _Putty_, para establecer una conexión SSH a la Máquina Virtual:

* **Host Name \(or IP address\)**: localhost
* **Port**: 2222

Luego pulsamos en el botón _Open_:

![](/assets/img/01_instalacion/46.png)

Si todo va bien se nos abrirá una pantalla donde se nos piden las credenciales de un usuario para poder acceder:

* **login as**: cfgs
* **cfgs@localhost's password**: cfgs

![](/assets/img/01_instalacion/47.png)

Con esto hemos configurado nuestra Máquina Virtual para poder acceder a ella usando un cliente SSH.

