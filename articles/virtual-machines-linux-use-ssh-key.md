<properties 
	pageTitle="Uso de SSH para conectarse a máquinas virtuales de Linux en Azure" 
	description="Aprenda a generar y utilizar claves SSH con una máquina virtual de Linux en Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szark"/>

#Utilización de SSH con Linux en Azure

La versión actual del Portal de administración de Azure solo acepta claves públicas SSH que estén encapsuladas en un certificado X509. Siga los pasos a continuación para generar y utilizar claves SSH con Azure.

## Generación de claves compatibles con Azure en Linux ##

1. Instale la utilidad `openssl` si es necesario:

	**CentOS / Oracle Linux**

		# sudo yum install openssl

	**Ubuntu**

		# sudo apt-get install openssl

	**SLES y openSUSE**

		# sudo zypper install openssl


2. Use `openssl` para generar un certificado X509 con un par de claves RSA de 2048 bits. Responda a las preguntas que le solicite `openssl` (también puede dejarlas en blanco). La plataforma no utiliza el contenido de estos campos:

		# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Cambie los permisos en la clave privada para asegurarla.

		# chmod 600 myPrivateKey.key

4.	Cargue `myCert.pem` mientras se crea la máquina virtual Linux. El proceso de aprovisionamiento instalará automáticamente la clave pública del certificado en el archivo `authorized_keys` para el usuario especificado en la máquina virtual.

5.	Si va a utilizar la API directamente y no utiliza el Portal de administración, convierta `myCert.pem` en `myCert.cer` (certificado X509 con codificación DER) mediante el siguiente comando:

		# openssl  x509 -outform der -in myCert.pem -out myCert.cer


## Generación de una clave a partir de una clave compatible con OpenSSH existente
El ejemplo anterior describe la forma de crear una nueva clave para el uso con Azure. En algunos casos, es posible que ya tenga un par de claves pública y privada compatibles con OpenSSH y desee utilizar las mismas claves con Azure.

La utilidad `openssl` puede leer directamente las claves privadas OpenSSH. El siguiente comando empleará una clave privada SSH existente (id_rsa en el siguiente ejemplo) para crear la clave pública `.pem` que es necesaria para Azure:

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

El archivo **myCert.pem** es la clave pública que, a partir de entonces, se puede utilizar para aprovisionar Azure con una máquina virtual Linux. Durante el aprovisionamiento, el archivo `.pem` se convertirá en una clave pública compatible `openssh` y se colocará en `~/.ssh/authorized_keys`.


## Conexión a una maquina virtual Azure desde Linux

1. En algunos casos, el extremo SSH para una máquina virtual Linux puede configurarse para un puerto distinto al puerto predeterminado 22. Puede encontrar el número de puerto correcto en el Panel de la máquina virtual en el Portal de administración (en "Detalles de SSH").

2.	Conexión a la máquina virtual Linux mediante `ssh`. La primera vez que inicie sesión, se le solicitará que acepte la huella digital de la clave pública del host.

		# ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.	Opcionalmente, puede copiar `myPrivateKey.key` en `~/.ssh/id_rsa` para que su cliente OpenSSH pueda recuperarla automáticamente sin usar la opción `-i`.

## Obtención de OpenSSL para Windows ##

Existen varias utilidades que incluyen un `openssl` para Windows. A continuación, se enumeran algunos ejemplos:

### Uso de msysgit ###

1.	Descargue e instale msysgit desde la siguiente ubicación: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Ejecute `msys` desde el directorio instalado (ejemplo: c:\msysgit\msys.exe)
3.	Cambie al directorio `bin`. Para ello escriba `cd bin`


### Uso de GitHub para Windows ###

1.	Descargue e instale GitHub para Windows desde la siguiente ubicación: [http://windows.github.com/](http://windows.github.com/)
2.	Ejecute Git Shell desde la ruta: menú Inicio > Todos los programas > GitHub, Inc.

	**Nota:** puede encontrar el siguiente error al ejecutar los comandos `openssl` anteriores:

		Unable to load config info from /usr/local/ssl/openssl.cnf

	La manera más fácil de resolver este problema consiste en establecer la variable de entorno `OPENSSL_CONF`. El proceso para establecer esta variable variará según el shell que se ha configurado en Github:

	**Powershell:**

		$Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

	**CMD:**

		set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

	**GIT Bash:**

		export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf


###Uso de cygwin###

1.	Descargue e instale cygwin desde la siguiente ubicación: [http://cygwin.com/](http://cygwin.com/)
2.	Asegúrese de que el paquete OpenSSL y todas sus dependencias estén instalados.
3.	Ejecute `cygwin`


## Creación de una clave privada en Windows ##

1.	Siga uno de los conjuntos de instrucciones anteriores para poder ejecutar `openssl.exe`.
2.	Escriba el siguiente comando:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	La pantalla debería mostrar lo siguiente:

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	Responda las preguntas.
5.	Se habrán creado dos archivos: `myPrivateKey.key` y `myCert.pem`.
6.	Si va a utilizar la API directamente y no utiliza el Portal de administración, convierta `myCert.pem` en `myCert.cer` (certificado X509 con codificación DER) mediante el siguiente comando:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Creación de un PPK para Putty ##

1. Descargue e instale Puttygen desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Es posible que Puttygen no pueda leer la clave privada que se creó anteriormente (`myPrivateKey.key`). Ejecute el comando siguiente para convertirla en una clave privada RSA que Puttygen pueda entender:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	El comando anterior debería producir una nueva clave privada denominada myPrivateKey_rsa.

3. Ejecute `puttygen.exe`

4. Haga clic en el menú: File > Load a Private Key (Archivo > Cargar clave privada).

5. Busque su clave privada, a la que hemos denominado anteriormente `myPrivateKey_rsa`. Deberá cambiar el filtro de archivos para mostrar **Todos los archivos (*.*)**

6. Haga clic en **Abrir**. Recibirá una secuencia parecida a la siguiente:

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. Haga clic en **Aceptar**.

8. Haga clic en **Guardar clave privada**, que aparece resaltado en la siguiente captura de pantalla:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. Guarde el archivo como PPK.


## Uso de Putty para conectarse a una máquina virtual con Linux ##

1.	Descargue e instale Putty desde la siguiente ubicación: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Ejecute putty.exe.
3.	Rellene el nombre del host con la IP desde el Portal de administración:

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Antes de seleccionar **Abrir**, haga clic en la pestaña Connection > SSH > Auth (Conexión > SSH > Autenticación) para elegir la clave. Consulte la siguiente captura de pantalla con el campo que se va a rellenar:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Haga clic en **Abrir** para conectarse a su máquina virtual.

<!---HONumber=58-->