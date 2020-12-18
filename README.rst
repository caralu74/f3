f3 - Combatir el fraude flash (Fight Flash Fraud)
======================

f3 es una herramienta sencilla que prueba la capacidad y el rendimiento 
de las tarjetas de memoria para ver si cumplen con las especificaciones reclamadas. Llena el 
dispositivo con datos seudoaleatorios y luego comprueba si son los mismos al ser leídos.

F3 es por las 3 f de su significado en inglés Fight Flash Fraud, or Fight Fake Flash. Combatir el fraude flash.

**Tabla de contenidos**

-  `Ejemplos <#Ejemplos>`__
-  `Instalación <#Instalación>`__
-  `Otros recursos <#Otros recursos>`__

.. _Ejemplos:

.. highlight:: bash

Ejemplos
========

Probando el rendimiento con f3read/f3write
---------------------------------------

Utilice estos dos programas en este orden. f3write escribirá archivos grandes 
en su disco montado y f3read comprobará si el dipositivo flash contiene exactamente 
los archivos escritos::

    $ ./f3write /media/michel/5EBD-5C80/
    $ ./f3read /media/michel/5EBD-5C80/

Por favor, cambien "/media/michel/5EBD-5C80/" por la ruta apropiada. Los 
dispositivos USB se montan en "/Volumes" en los Mac.

Si ya instalo f3read y f3write, puede eliminar el "./" que 
aparece antes de sus nombres.

Pruebas rápidas de capacidad con f3probe.
---------------------------------

f3probe es la prueba de unidad más rápida y adecuada para discos grandes, ya que 
solo escribe lo necesario para probar la unidad. Funciona directamente en el 
dispositivo de bloques (desmontado) y necesita ser ejecutado como (root) 
un usuario privilegiado::

    # ./f3probe --destructive --time-ops /dev/sdb

.. Advertencia:: ¡Esto destruirá cualquier dato almacenado previamente en su disco!

Corregir la capacidad al tamaño real con f3fix
---------------------------------------------

f3fix crea una partición que se ajusta al tamaño real de la unidad falsa. 
Usa la salida de f3probe para determinar los parámetros de f3fix::

    # ./f3fix --last-sec=16477878 /dev/sdb

Instalación
============

Descargar y compilar
--------------------

Los archivos de la versión estable de F3 están
`aquí <https://github.com/AltraMayor/f3/releases>`__. El 
siguiente comando descomprime los archivos::

    $ unzip f3-8.0.zip


Compilar software estable en Linux o FreeBSD
-------------------------------------------

Para construir::

    make

Si quiere instalar f3write y f3read, ejecute el siguiente comando::

    make install

Compilar la versión estable en Windows/Cygwin
-----------------------------------------

f3write y f3read pueden ser instalados en Windows, pero actualmente f3probe, f3fix 
y f3brew  `requieren Linux <#Aplicaciones-adicionales-para-Linux>`__.  Para usarlos en una máquina de Windows, 
use la instalación. `Docker Instalación <#docker>`__.  para f3write 
y f3read, sigue leyendo.

Si aún no lo ha hecho, instale los siguientes paquetes Cygwin y sus dependencias:

- `gcc-core`
- `make`
- `libargp-devel`

Para construir, necesitas banderas especiales::

    export LDFLAGS="$LDFLAGS -Wl,--stack,4000000 -largp"
    make

Si quiere instalar f3write y f3read, ejecute el siguiente comando::

    make install

Compile software estable en Apple Mac
------------------------------------

f3write y f3read se pueden instalar en Mac, pero actualmente f3probe, f3fix y 
f3brew `requieren Linux <#Aplicaciones-adicionales-para-Linux>`__.Para usarlos 
en Mac, use la `instalación de Docker. <#docker>`__.  Para f3write y f3read, siga 
leyendo.

Uso de HomeBrew
~~~~~~~~~~~~~~

Si ya tiene Homebrew instalado en su ordenador, el siguiente comando 
instalará F3::

    brew install f3

Uso de MacPorts
~~~~~~~~~~~~~~

Si utiliza MacPorts en su lugar, utilice el siguiente comando::

    port install f3

Compilar la última versión de desarrollo a partir del código fuente
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

La mayoría del código fuente de f3 se construye bien usando XCode, la única 
dependencia que falta es la biblioteca C de GNU "argp". Puede construir argp 
desde cero, o usar la versión proporcionada por HomeBrew y MacPorts como "argp-standalone"

Los siguientes pasos han sido probados en OS X El Capitán 10.11.

1) Instalar las herramientas de línea de comandos de Apple::

       xcode-select --install

vea http://osxdaily.com/2014/02/12/install-command-line-tools-mac-os-x/
para más detalles.

2) Instalar Homebrew o MacPorts

   HomeBrew::

     /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   Vea https://brew.sh/ para más detalles.

   MacPorts: https://www.macports.org/install.php

3) Instalar la biblioteca argp::

       brew install argp-standalone

   Vea http://brewformulas.org/ArgpStandalone y
   https://www.freshports.org/devel/argp-standalone/ para más
   informaciónn.

   O, para MacPorts::

     port install argp-standalone

   Vea https://trac.macports.org/browser/trunk/dports/sysutils/f3/Portfile
   para más información.

4) Construir F3::

   Al usar Homebrew, puedes simplemente correr::

       make

   Al utilizar MacPorts, deberá pasar la ubicación donde MacPorts 
   instaló el argp-standalone::

       make ARGP=/opt/local

Docker
------

Inicio rápido
~~~~~~~~~~~

Una imagen precompilada `imágen <https://cloud.docker.com/repository/docker/peron/f3>`__
está disponible en Docker Hub, listo para usarse. 
Con docker iniciado, simplemente ejecute::

    docker run -it --rm --device <device> peron/f3 <f3-command> [<f3-options>] <dispositivo>

Por ejemplo, para probar una unidad montada en /dev/sdb::

    docker run -it --rm --device /dev/sdb peron/f3 f3probe --destructive --time-ops /dev/sdb

Opcionalmente, también puede crear su propio contenedor  *si* no desea usar 
la imagen precompilado. Desde este directorio, ejecute::

    docker build -t f3:latest .
    docker run -it --rm --device <device> f3:latest <f3-command> [<f3-options>] <dispositivo>

Permisos de unidad / Passthrough
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Conseguir que el dispositivo de la unidad se mapee en el contenedor del Docker es difícil para Mac y
Windows.  Pasar a través de los dispositivos en Mac y Windows es un asunto bien documentado 
(`[github]
<https://github.com/docker/for-mac/issues/3110#issuecomment-456853036>`__
`[stackexchange]
<https://devops.stackexchange.com/questions/4572/how-to-pass-a-dev-disk-device-on-macos-into-linux-docker/6076#6076>`__
`[tty]
<https://christopherjmcclellan.wordpress.com/2019/04/21/using-usb-with-docker-for-mac/#tldr>`__)
En Linux debería funcionar, pero en Mac o Windows, Docker tiende a mapear 
la unidad como un directorio normal en lugar de una unidad montada y obtendrá 
un error como :code:`f3probe: Can't open device '/opt/usb': Is a directory`, eso 
si es que consigue mapearlo.

Para resolver esto, podemos usar docker-machine para crear una máquina virtual en VirtualBox 
(boot2docker), en la que ejecutar el contenedor de Docker. Dado que VirtualBox *puede* 
controlar el paso a través del dispositivo, podemos pasar el dispositivo a través de la máquina virtual VirtualBox 
que, a continuación, puede pasar el dispositivo al contenedor de Docker. Milad Alizadeh 
escribió algunas buenas instrucciones `Aquí
<https://mil.ad/docker/2018/05/06/access-usb-devices-in-container-in-mac.html>`__
que están orientadas a dispositivos USB, pero no debería ser demasiado difícil adaptarse a 
otros tipos de unidades. Esto es lo que he escrito en mi terminal Mac (probablemente 
similar para Windows, pero sin probar)::

    docker-machine create -d virtualbox default
    docker-machine stop
    vboxmanage modifyvm default --usb on
    docker-machine start
    vboxmanage usbfilter add 0 --target default --name flashdrive --vendorid 0x0123 --productid 0x4567
    eval $(docker-machine env default)


Para el comando add de usbfilter, tenga en cuenta que el argumento "name" es el nuevo nombre 
que le da al filtro así que puede nombrarlo como quiera.
:code:`--vendorid` y :code:`--productid` se pueden encontrar en Mac en  "System
Information" bajo "USB". También puedes intentar buscar el dispositivo correcto en 
:code:`vboxmanage list usbhost`.

Alternativamente, puedes optar por añadir el dispositivo a través de la GUI 
de Virtualbox ::

    docker-machine create -d virtualbox default
    docker-machine stop
    # abra VirtualBox y agrege manualmente el dispositivo antes de ejecutar el siguiente comando
    docker-machine start
    eval $(docker-machine env default)

Una vez que haya ejecutado los comandos anteriores, desenchufe y vuelva a conectar la unidad flash y ejecute::

    docker-machine ssh default "lsblk"

para listar los dispositivos. Busque la unidad correcta: la columna "SIZE" puede ser 
útil para localizar el dispositivo de interés. Por ejemplo, :code:`sdb` es un punto de montaje 
común para una unidad USB. Ahora usted debe ser capaz de ejecutar el comando de 
inicio rápido::

    docker run --rm -it --device /dev/sdb peron/f3 f3probe --destructive --time-ops /dev/sdb

Puede resultar útil introducir un mensaje bash en el contenedor de Docker para 
buscar en el sistema de archivos::

    docker run --rm -it --device /dev/sdb peron/f3 bash

para que pueda ejecutar comandos como  :code:`ls /dev/*`.

Aplicaciones adicionales para Linux
--------------------------------

Instalar dependencias
~~~~~~~~~~~~~~~~~~~~

f3probe y f3brew requieren la versión 1 de la biblioteca libudev, y f3fix 
requiere la versión 0 de la biblioteca libparted para compilar. En Ubuntu, 
puede instalar estas bibliotecas con el siguiente comando::

    sudo apt-get install libudev1 libudev-dev libparted-dev

Si está ejecutando una versión de Ubuntu anterior a 20.04.1, reemplace el paquete `libparted-dev`
en la línea de comandos anterior con `libparted0-dev`.

En Fedora, puede instalar estas bibliotecas con el siguiente comando::

    sudo dnf install systemd-devel parted-devel

Compilar las aplicaciones adicionales
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

    make extra

.. nota::
   - Las aplicaciones adicionales solo se compilan y prueban en  
	 Linux.
   - Por favor, no me envíe un correo electrónico diciendo que desea que las 
	 aplicaciones adicionales se ejecuten en su plataforma; Ya lo sé.
   - Si desea que las aplicaciones adicionales se ejecuten en su plataforma, 
	 ayúdelas a portar o a encontrar a alguien que pueda portarlas por usted. 
	 Si portan alguna de ellas, por favor envíenme el parche para ayudar a los demás.
   - Las aplicaciones adicionales son f3probe, f3brew y f3fix.

Si quieres instalar las aplicaciones extra, ejecuta el siguiente 
comando::

    make install-extra

Otros recursos
===============

Interfaces gráficas
-------------------------

Gracias a nuestra creciente comunidad de luchadores contra el fraude flash, tenemos las siguientes 
interfaces gráficas (GUI) disponibles para F3:

`F3 QT <https://github.com/zwpwjwtz/f3-qt>`__ es una GUI para Linux que usa
QT. F3 QT soporta ``f3write``, ``f3read``, ``f3probe``, y ``f3fix``. Autor:
Tianze.

Por favor, apoye el proyecto anterior probándolo y dando retroalimentación 
a sus autores. Esto hará que su código mejore como ha mejorado el mío.

Archivos
-----

::

    changelog   - Registro de cambios para los mantenedores de paquetes
    f3read.1    - Página de manuales para f3read y f3write
                Para leer esta página del manual, ejecute  `man ./f3read.1`
                Para instalar la página, ejecute
                `install --owner=root --group=root --mode=644 f3read.1 /usr/share/man/man1`
    LICENSE     - Licencia (GPLv3)
    Makefile    - archivo make(1)
    README      - Este archivo
    *.h and *.c - C code of F3

Scripts de Bash
------------

Aunque los scripts simples enumerados en esta sección están listos para su uso, 
realmente están diseñados para ayudarle a escribir sus propios scripts. Así que 
puede personalizar F3 a sus necesidades específicas::

    f3write.h2w - Script para crear archivos exactamente como H2testw.
        Ejemplo:  `f3write.h2w /media/michel/5EBD-5C80/`

    log-f3wr    - Script que ejecuta f3write y f3read, y registra  
                  su salida en un archivo de registro.
        Ejemplo: `log-f3wr log-filename /media/michel/5EBD-5C80/`

Tenga en cuenta que todos los scripts y ejemplos de uso anteriores asumen 
que f3write, f3read y los scripts están en la misma carpeta.
