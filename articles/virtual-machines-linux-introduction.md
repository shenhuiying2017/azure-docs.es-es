<properties 
	pageTitle="Introducción a Linux en Azure - Tutorial de Azure" 
	description="Aprenda a utilizar máquinas virtuales de Linux en Azure." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/13/2014" 
	ms.author="szark"/>





#Introducción a Linux en Azure

En este tema se ofrece información general acerca de algunos aspectos relacionados con el uso de las máquinas virtuales con Linux en la nube de Azure. La implementación de una máquina virtual con Linux es un proceso sencillo cuando se usa una imagen de la galería. 

## Tabla de contenido ##

* [Autenticación: nombres de usuario, contraseñas y claves SSH](#authentication)
* [Generación y uso de claves SSH para iniciar sesión en máquinas virtuales con Linux.](#keygeneration)
* [Obtención de privilegios de superusuario con el uso de sudo](#superuserprivileges)
* [Configuración del firewall](#firewallconfiguration)
* [Cambios del nombre de host](#hostnamechanges)
* [Captura de imagen de máquina virtual](#virtualmachine)
* [Acoplamiento de discos](#attachingdisks)

## <a id="authentication"></a>Autenticación:  nombres de usuario, contraseñas y claves SSH

Al crear una máquina virtual con Linux con el Portal de administración de Azure, se le pedirá que facilite un nombre de usuario, una contraseña y (opcionalmente) una clave pública SSH. La elección de un nombre de usuario para implementar una máquina virtual Linux en Azure está sujeta a la siguiente limitación: no se admiten los nombres de cuentas del sistema (UID <100) ya existentes en la máquina virtual, como por ejemplo,  'root'.

 - Consulte [Utilización de SSH con Linux en Azure](linux-use-ssh-key.md)


### <a id="keygeneration"></a>Generación de claves SSH

La versión actual del Portal de administración solo acepta claves públicas SSH que estén encapsuladas en un certificado X509. Siga los pasos a continuación para generar y utilizar claves SSH con Azure.

1. Utilice openssl para generar un certificado X509 con un par de claves RSA de 2048 bits.
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	Responda a las preguntas que le solicite openssl (también puede dejarlas en blanco). La plataforma no utiliza el contenido de estos campos.

2. Cambie los permisos en la clave privada para asegurarla.

		chmod 600 myPrivateKey.key

3. Convierta  `myCert.pem` en  `myCert.cer` (certificado X509 con codificación DER)

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. Cargue  `myCert.cer` mientras se crea la máquina virtual con Linux. El proceso de aprovisionamiento instalará automáticamente la clave pública del certificado en el archivo `~/.ssh/authorized_keys`para el usuario especificado en la máquina virtual.

5. Conéctese a la máquina virtual con Linux mediante ssh.

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	La primera vez que inicie sesión, se le solicitará que acepte la huella digital de la clave pública del host.

6. Opcionalmente, puede copiar `myPrivateKey.key` en `~/.ssh/id_rsa` para que su cliente openssh pueda recuperarla automáticamente sin usar la opción -i.
   También puede modificar `~/.ssh/config` para incluir una sección para su máquina virtual:

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key


### Generación de una clave a partir de una clave compatible con OpenSSH existente
El ejemplo anterior describe la forma de crear una nueva clave para el uso con Azure. En algunos casos, es posible que los usuarios ya tengan un par de claves pública y privada compatibles con OpenSSH y deseen utilizar las mismas claves con Azure.

La utilidad  `openssl` puede leer directamente las claves privadas OpenSSH. El siguiente comando empleará una clave privada SSH existente (id_rsa en el siguiente ejemplo) para crear la clave pública  `.pem` que es necesaria para Azure:

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

El archivo **myCert.pem** es la clave pública que, a partir de entonces, se puede utilizar para aprovisionar Azure con una máquina virtual con Linux. Durante el aprovisionamiento, el archivo  `.pem` se convertirá en una clave pública compatible  `openssh` y se colocará en `~/.ssh/authorized_keys`.


## <a id="superuserprivileges"></a>Obtención de privilegios de superusuario con el uso de `sudo`

La cuenta de usuario especificada durante la implementación de la instancia de máquina virtual en Azure es una cuenta con privilegios. El Agente de Linux de Azure configura esta cuenta para elevar privilegios a root (cuenta de superusuario) con la utilidad  `sudo`. Después de iniciar sesión con esta cuenta de usuario, podrá ejecutar comandos como root con el uso del la sintaxis de comando

	# sudo <COMMAND>

También puede obtener un shell root con **sudo -s**.

- Consulte [Uso de privilegios raíz en máquinas virtuales con Linux en Azure](virtual-machines-linux-use-root-privileges.md)


## <a id="firewallconfiguration"></a>Configuración del firewall

Azure ofrece un filtro de paquetes de entrada que restringe la conectividad a los puertos especificados en el Portal de administración. De forma predeterminada, el único puerto permitido es SSH. Puede abrir el acceso a puertos adicionales de la máquina virtual con Linux con la configuración de extremos en el Portal de administración.

 - Consulte: [Configuración de extremos en una máquina virtual](virtual-machines-set-up-endpoints.md)

Las imágenes de Linux en la Galería de Azure no habilitan el *iptables* firewall de forma predeterminada. Si lo desea, el firewall puede configurarse para proporcionar filtrado adicional.


## <a id="hostnamechanges"></a>Cambios del nombre de host

Al implementar inicialmente una instancia de una imagen de Linux, se le pide que facilite un nombre de host para la máquina virtual. Cuando la máquina virtual está en ejecución, este nombre de host se publica en los servidores DNS de la plataforma, a fin de que varias máquinas virtuales conectadas entre sí puedan realizar búsquedas de direcciones IP con el uso de nombres de host.

Si desea realizar cambios en el nombre de host después de la implementación de una máquina virtual, use el comando

	# sudo hostname <newname>

El Agente de Linux de Azure incluye la funcionalidad para detectar automáticamente este cambio de nombre y configurar correctamente la máquina virtual para que mantenga este cambio y publica este cambio en los servidores DNS de la plataforma.

 - [Guía de usuario del Agente de Linux de Azure](virtual-machines-linux-agent-user-guide.md)

### Imágenes de Ubuntu
Las imágenes de Ubuntu usan cloud-init, que proporciona funcionalidades adicionales para arrancar una máquina virtual.

 - Consulte [Datos personalizados y cloud-init en Microsoft Azure](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)


## <a id="virtualmachine"></a>Captura de imagen de máquina virtual

Azure ofrece la capacidad de capturar el estado de una máquina virtual existente en una imagen que posteriormente puede usarse para implementar instancias adicionales de máquina virtual. El Agente de Linux de Azure puede usarse para la reversión de la personalización realizada durante el proceso de aprovisionamiento. Puede seguir los siguientes pasos para capturar una máquina virtual como una imagen:

1. Ejecute **waagent -deprovision** para deshacer la personalización del aprovisionamiento. O ejecute **waagent -deprovision+user** para eliminar opcionalmente la cuenta de usuario especificada durante el aprovisionamiento y todos los datos asociados.

2. Cierre o apague la máquina virtual.

3. Haga clic en  *Capture* en el Portal de administración o use Powershell o las herramientas de la CLI para capturar la máquina virtual como una imagen.

 - Consulte: [Captura de una máquina virtual de Linux para usar como plantilla](virtual-machines-linux-capture-image.md)


## <a id="attachingdisks"></a>Acoplamiento de discos

Cada máquina virtual tiene un disco de recursos ( *resource disk*) temporal y local adjunto. Debido a que los datos de un disco de recursos podrían no resistir los diversos reinicios, muchas veces los usan aplicaciones y procesos que se ejecutan en la máquina virtual para un almacenamiento de datos transitorio y**temporal**. También se usan para almacenar archivos de intercambio o de paginación para el sistema operativo.

En Linux, el disco de recursos se administra generalmente mediante el Agente de Linux de Azure y se monta automáticamente en **/mnt/resource** (o **/mnt** en las imágenes de Ubuntu).

	>[AZURE.NOTE] Tenga en cuenta que el disco de recursos es un disco **temporal**, que debe eliminarse y reformatearse cuando se reinicia la máquina virtual.

En Linux el kernel debe poner al disco de datos el nombre  `/dev/sdc` y los usuarios tendrán que particionar ese recurso, darle formato y montarlo. Esto se explica paso a paso en el tutorial: [Acoplamiento de un disco de datos a una máquina virtual](virtual-machines-linux-how-to-attach-disk.md).

 - Consulte también: [Configuración del software RAID en Linux](virtual-machines-linux-configure-raid.md)


<!--HONumber=45--> 
