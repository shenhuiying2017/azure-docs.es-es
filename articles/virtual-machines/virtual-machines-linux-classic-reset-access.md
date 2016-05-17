<properties
	pageTitle="Restablecimiento de la contraseña de VM de Linux y clave SSH desde la CLI | Microsoft Azure"
	description="Uso de la extensión VMAccess desde la interfaz de la línea de comandos de Azure (CLI) para restablecer una contraseña de VM de Linux o clave SSH, corregir la configuración de SSH y comprobar la coherencia de disco"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/20/2016"
	ms.author="cynthn"/>

# Restablecimiento del acceso y administración de usuarios y discos de comprobación con la extensión VMAccess de Azure para Linux


Si no puede conectarse a una máquina virtual Linux porque olvidó la contraseña, la clave de Shell seguro (SSH) es incorrecta o hay un problema con la configuración de SSH, use la extensión VMAccessForLinux con la CLI de Azure para restablecer la contraseña o la clave SSH, corregir la configuración de SSH y comprobar la coherencia de los discos.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).




## Azure CLI

Necesitará lo siguiente:

- Interfaz de la línea de comandos (CLI) de Azure Necesitará [instalar la CLI de Azure](../xplat-cli-install.md) y [conectarse a la suscripción](../xplat-cli-connect.md) para usar recursos de Azure asociados a la cuenta.
- Una nueva contraseña o un conjunto de claves SSH, si desea restablecer cualquiera de ellos. No los necesita si desea restablecer la configuración de SSH.


## Utilice el comando del conjunto de extensión de VM de Azure

Con la CLI de Azure, podrá usar el comando **azure vm extension set** desde su interfaz de la línea de comandos (símbolo del sistema Command, Terminal y Bash) para obtener acceso a los comandos. Ejecute el comando **azure help vm extension set** para obtener información detallada sobre el uso de la extensión.

Con la CLI de Azure, puede realizar las tareas siguientes:

+ [Restablecer la contraseña](#pwresetcli)
+ [Restablecer la clave SSH](#sshkeyresetcli)
+ [Restablecer la contraseña y la clave SSH](#resetbothcli)
+ [Crear una nueva cuenta de usuario de sudo](#createnewsudocli)
+ [Restablecer la configuración de SSH](#sshconfigresetcli)
+ [Eliminar un usuario](#deletecli)
+ [Mostrar el estado de la extensión VMAccess](#statuscli)
+ [Comprobar la coherencia de los discos agregados](#checkdisk)
+ [Reparar los discos agregados a la VM de Linux](#repairdisk)

### <a name="pwresetcli"></a>Restablecimiento de la contraseña

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo los valores de marcadores de posición.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01"
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Restablecimiento de la clave SSH

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo los valores de marcadores de posición.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey"
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Restablecimiento de la contraseña y la clave SSH

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo los valores de marcadores de posición.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword"
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>Creación de una nueva cuenta de usuario de sudo

Si olvida su nombre de usuario, puede utilizar VMAccess para crear uno nuevo con la autoridad de sudo. En este caso, no se modificarán el nombre de usuario y la contraseña existentes.

Para crear un nuevo usuario de sudo con acceso de contraseña, utilice el script en [Restablecer la contraseña](#pwresetcli) y especifique el nuevo nombre de usuario.

Para crear un nuevo usuario de sudo con acceso de clave SSH, utilice el script de [Restablecer la contraseña](#sshkeyresetcli) y especifique el nuevo nombre de usuario.

También puede usar [Restablecer la contraseña y la clave SSH](#resetbothcli) para crear un nuevo usuario con acceso de contraseña y de clave SSH.

### <a name="sshconfigresetcli"></a>Restablecimiento de la configuración de SSH

Si la configuración de SSH se encuentra en un estado no deseado, podría perder el acceso a la máquina virtual. Puede usar la extensión VMAccess para restablecer la configuración a su estado predeterminado. Para ello, basta con establecer la clave "reset\_ssh" en "True". La extensión reinicia el servidor SSH, abre el puerto SSH en su máquina virtual y restablece la configuración de SSH al valor predeterminado. No se cambiará la cuenta de usuario (nombre, contraseña o claves SSH).

> [AZURE.NOTE] El archivo de configuración de SSH que obtiene el restablecimiento se encuentra en /etc/ssh/sshd\_config.

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido.

	{
	"reset_ssh":"True"
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Eliminación de un usuario

Si desea eliminar una cuenta de usuario sin iniciar sesión en la máquina virtual directamente, puede utilizar esta secuencia de comandos.

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo el valor de marcador de posición.

	{
	"remove_user":"usernametoremove"
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Visualización del estado de la extensión VMAccess

Para mostrar el estado de la extensión VMAccess, ejecute este comando.

	azure vm extension get

### <a name='checkdisk'<</a>Comprobación de la coherencia de los discos agregados

Para ejecutar fsck en todos los discos de la máquina virtual de Linux, debe hacer lo siguiente:

Paso 1: creación de un archivo llamado PublicConf.json con este contenido. Comprobar disco toma un valor booleano para comprobar los discos conectados a la máquina virtual o no.

    {   
    "check_disk": "true"
    }

Paso 2: ejecute este comando, que sustituye los valores de marcador de posición.

   la extensión de vm de Azure establece vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

### <a name='repairdisk'></a>Reparación de los discos agregados a la máquina virtual de Linux

Para reparar discos que no se están montando o que tienen errores de configuración de montaje, use la extensión VMAccess para restablecer la configuración de montaje en la máquina virtual de Linux.

Paso 1: creación de un archivo llamado PublicConf.json con este contenido.

    {
    "repair_disk":"true",
    "disk_name":"yourdisk"
    }

Paso 2: ejecute este comando, que sustituye los valores de marcador de posición.

    azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## Recursos adicionales

* Si desea usar los cmdlets de Azure PowerShell o plantillas de Azure Resource Manager para restablecer la contraseña o clave SSH, corregir la configuración de SSH y comprobar la coherencia de disco, consulte la [documentación sobre la extensión VMAccess en GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* También puede utilizar el [Portal de Azure](https://portal.azure.com) para restablecer la contraseña o clave SSH de una VM de Linux implementada en el modelo de implementación clásica. Actualmente, no puede usar el portal para realizar esta tarea en una máquina virtual de Linux implementada en el modelo de implementación de Resource Manager.

* Consulte [Acerca de las características y extensiones de las máquinas virtuales](virtual-machines-linux-extensions-features.md) para obtener más información sobre el uso de extensiones de máquinas virtuales para máquinas virtuales de Azure.

<!---HONumber=AcomDC_0504_2016-->