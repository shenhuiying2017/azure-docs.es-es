<properties
	pageTitle="Implementación de una aplicación de Linux mediante la extensión CustomScript de Azure"
	description="Aprenda a usar la extensión CustomScript de Azure para implementar aplicaciones en máquinas virtuales de Linux."
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	services=""
	authors="gbowerman"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="2/23/2015"
	ms.author="guybo"/>

# Implementación de una aplicación LAMP con la extensión CustomScript para Linux#

La extensión CustomScript de Azure para Linux proporciona una manera de personalizar sus máquinas virtuales (VM) mediante la ejecución de código arbitrario escrito en cualquier lenguaje de scripting compatible con la máquina virtual (por ejemplo, Python, Bash, etc.). Esto proporciona una forma muy flexible de automatizar la implementación de aplicaciones en varios equipos.

Puede implementar la extensión CustomScript mediante el Portal de Azure, PowerShell o la interfaz de la línea de comandos entre plataformas de Azure (xplat-cli).

En este ejemplo, analizaremos los pasos para implementar una simple aplicación LAMP para Ubuntu usando la xplat-cli.

## Requisitos previos

Para este tutorial, cree dos máquinas virtuales de Azure con Ubuntu 14.04. Las llamaré  *script-vm* y  *lamp-vm* aquí. Use nombres únicos cuando pruebe esto. Una será para ejecutar los comandos de CLI y la otra es donde se implementará la aplicación LAMP.

También necesita una cuenta de almacenamiento de Azure y una clave para tener acceso a ella (se puede obtener desde el portal de Azure).

Si necesita ayuda para crear máquinas virtuales de Linux en Azure, consulte [Creación de una máquina virtual con Linux](virtual-machines-linux-tutorial.md).

Si bien los comandos de instalación específicos darán por sentado Ubuntu, puede adaptar los pasos generales para cualquier distribución admitida.

La máquina virtual  *script-vm* debe tener instalado xplat-cli, con una conexión activa a Azure. Para obtener ayuda con este procedimiento, consulte [Instalación y configuración de la interfaz de la línea de comandos entre plataformas de Azure](xplat-cli.md).

## Cargar una secuencia de comandos

En este ejemplo, la extensión CustomScript ejecutará una secuencia de comandos en una máquina virtual remota para instalar la pila LAMP y crear una página PHP. Para tener acceso a la secuencia de comandos desde cualquier lugar, la cargaremos como un blob de Azure.

**La secuencia de comandos**

Esta secuencia de comandos instala una pila LAMP en Ubuntu (incluida la configuración de una instalación silenciosa de MySQL), escribe un simple archivo PHP e inicia Apache:

	#!/bin/bash
	# set up a silent install of MySQL
	dbpass="mySQLPassw0rd"

	export DEBIAN_FRONTEND=noninteractive
	echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
	echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

	# install the LAMP stack
	apt-get -y install apache2 mysql-server php5 php5-mysql  

	# write some PHP
	echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
	echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

	# restart Apache
	apachectl restart

**Cargar**

Guarde la secuencia de comandos como un archivo de texto, por ejemplo  *lamp_install.sh* y, a continuación, cárguelo en el almacenamiento de Azure. Puede hacerlo fácilmente con xplat-cli. En el ejemplo siguiente se carga el archivo en un contenedor de almacenamiento denominado "scripts". Nota: Si el contenedor no existe, deberá crearlo primero.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

También debe crear un archivo JSON que describa cómo descargar la secuencia de comandos del almacenamiento de Azure. Guárdelo como  *public_config.json* (reemplazando "mystorage" con el nombre de la cuenta de almacenamiento):

    {fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## Implementación de la extensión

Ahora, ya estamos listos para implementar la extensión CustomScript de Linux a la máquina virtual remota usando la xplat-cli:

    azure vm extension set -c "./public_config.json" lamp-vm CustomScriptForLinux Microsoft.OSTCExtensions 1.*

Esto descargará y ejecutará la secuencia de comandos  *lamp_install.sh* en la máquina virtual denominada  *lamp-vm*.

Dado que la aplicación incluye un servidor web, no olvide abrir un puerto de escucha HTTP en la máquina virtual remota:

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## Supervisión y solución de problemas

Para comprobar el progreso de la ejecución de la secuencia de comandos, puede examinar el archivo de registro en la máquina virtual remota. SSH a  *lamp-vm* y el archivo de registro en la cola:

    cd /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.3.0.0/
    tail -f extension.log

Una vez que la extensión CustomScript haya terminado de ejecutarse, puede examinar la página PHP que creó, que en este ejemplo sería:  *http://lamp-vm.cloudapp.net/phpinfo.php*.

## Recursos adicionales

Puede usar los mismos pasos básicos para implementar aplicaciones más complejas. En este ejemplo, la secuencia de comandos de instalación se guardó como un blob público en el almacenamiento de Azure. Una opción más segura sería almacenar la secuencia de comandos de instalación como un blob seguro con una [firma de acceso seguro](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS).

A continuación, se ofrecen algunos recursos adicionales para xplat-cli, Linux y la extensión CustomScript:

[Automatización de las tareas de personalización de VM de Linux con la extensión CustomScript](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensiones de Linux de Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Informática de código abierto y Linux en Azure](virtual-machines-linux-opensource.md)

<!--HONumber=47-->
