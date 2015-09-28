<properties
	pageTitle="Cómo usar VMAccess para las máquinas virtuales de Linux"
	description="Cómo usar el Portal de vista previa de Azure o la extensión VMAccess de Linux para restablecer las contraseñas y claves de SSH, para volver a enviar las configuraciones de SSH y para eliminar los usuarios de Linux"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="cynthn"/>

# Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux #

Si no puede conectarse a una máquina virtual Linux debido a una contraseña olvidada, una clave incorrecta de Shell seguro (SSH) o un problema con la configuración de SSH, utilice el Portal de vista previa de Azure o la extensión VMAccessForLinux para restablecer la contraseña o la clave SSH, o para corregir la configuración de SSH. Tenga en cuenta que este artículo se aplica a las máquinas virtuales creadas con el modelo de implementación **Clásica**.

## Portal de vista previa de Azure

Para restablecer la configuración de SSH en el [Portal de vista previa de Azure](https://portal.azure.com), haga clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual Linux* > **Restablecer acceso remoto**. Aquí tiene un ejemplo.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-RDP-Reset-Linux.png)

Para restablecer el nombre y la contraseña de la cuenta de usuario con privilegios de sudo o la clave pública SSH en el [Portal de vista previa de Azure](https://portal.azure.com), haga clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual Linux* > **Toda la configuración** > **Restablecimiento de contraseña**. Aquí tiene un ejemplo.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-PW-Reset-Linux.png)


## CLI de Azure y PowerShell

Necesitará lo siguiente:

- Agente de Linux de Microsoft Azure versión 2.0.5 o posterior. La mayoría de imágenes de Linux en la Galería de máquinas virtuales incluyen la versión 2.0.5. Para averiguar qué versión está instalada, ejecute **waagent -version**. Para actualizar el agente, siga las instrucciones de la [Guía de usuario del agente de Linux de Azure].
- Interfaz de la línea de comandos (CLI) de Azure Para obtener más información acerca de cómo configurar la CLI de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../xplat-cli.md).
- Azure PowerShell. Utilizará comandos en el cmdlet Set-AzureVMExtension para cargar y configurar automáticamente la extensión VMAccessForLinux. Para obtener más información acerca de cómo configurar Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell].
- Una nueva contraseña o un conjunto de claves SSH, si desea restablecer cualquiera de ellos. No los necesita si desea restablecer la configuración de SSH.

### No necesita la instalación

No es necesario instalar la extensión VMAccess antes de utilizarla. Siempre que el agente de Linux esté instalado en la máquina virtual, la extensión se cargará automáticamente cuando se ejecute un comando de Azure PowerShell que use el cmdlet **Set-AzureVMExtension**.

## Uso de la CLI de Azure

Una vez instalada la CLI de Azure, podrá usar el comando **azure** desde su interfaz de la línea de comandos (Bash, Terminal, símbolo del sistema) para tener acceso a los comandos. Ejecute **azure vm extension set –help** para ver un uso detallado de la extensión.

Con la CLI de Azure, puede realizar las tareas siguientes:

+ [Restablecer la contraseña](#pwresetcli)
+ [Restablecer la clave SSH](#sshkeyresetcli)
+ [Restablecer la contraseña y la clave SSH](#resetbothcli)
+ [Crear una nueva cuenta de usuario de sudo](#createnewsudocli)
+ [Restablecer la configuración de SSH](#sshconfigresetcli)
+ [Eliminar un usuario](#deletecli)
+ [Mostrar el estado de la extensión VMAccess](#statuscli)

### <a name="pwresetcli"></a>Restablecimiento de la contraseña

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo los valores de marcadores de posición.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01",
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Restablecimiento de la clave SSH

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo los valores de marcadores de posición.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Restablecimiento de la contraseña y la clave SSH

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo los valores de marcadores de posición.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword",
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

> [AZURE.NOTE]El archivo de configuración de SSH que obtiene el restablecimiento se encuentra en /etc/ssh/sshd\_config.

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido.

	{
	"reset_ssh":"True",
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Eliminación de un usuario

Si desea eliminar una cuenta de usuario sin iniciar sesión en la máquina virtual directamente, puede utilizar esta secuencia de comandos.

Paso 1: Creación de un archivo llamado PrivateConf.json con este contenido, sustituyendo el valor de marcador de posición.

	{
	"remove_user":"usernametoremove",
	}

Paso 2: Ejecución de este comando, sustituyendo "vmname" por el nombre de su máquina virtual.

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Visualización del estado de la extensión VMAccess

Para mostrar el estado de la extensión VMAccess, ejecute este comando.

	azure vm extension get


## Uso de Azure PowerShell

El cmdlet **Set-AzureVMExtension** se usa para realizar cualquiera de los cambios que VMAccess permite realizar. En todos los casos, empiece usando el nombre de servicio de nube y el nombre de la máquina virtual para obtener el objeto de máquina virtual y almacenarlo en una variable.

Introduzca los nombres del servicio en la nube y de la máquina virtual y ejecute los siguientes comandos en un símbolo de sistema de Azure PowerShell con nivel de administrador. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Si no conoce el nombre del servicio en la nube y de la máquina virtual, ejecute **Get-AzureVM** para que mostrar esa información para todas las máquinas virtuales de su suscripción actual.


> [AZURE.NOTE]Las líneas de comandos que comienzan con $ configuran las variables de PowerShell que más adelante se usan en los comandos de PowerShell.

Si ha creado la máquina virtual con el Portal de administración de Azure, ejecute el siguiente comando adicional:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá el error "El agente de invitado de aprovisionamiento debe estar habilitado en el objeto de máquina virtual antes de establecer la extensión de acceso de máquina virtual IaaS" cuando se ejecuta el comando Set-AzureVMExtension en las secciones siguientes.

A continuación, puede realizar las tareas siguientes:

+ [Restablecer la contraseña](#password)
+ [Restablecer una clave SSH](#SSHkey)
+ [Restablecer la contraseña y la clave SSH](#both)
+ [Restablecer la configuración de SSH](#config)
+ [Eliminar un usuario](#delete)
+ [Mostrar el estado de la extensión VMAccess](#status)

### <a name="password"></a>Restablecimiento de la contraseña

Introduzca el nombre de usuario actual de Linux y la nueva contraseña y, a continuación, ejecute estos comandos.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Si desea restablecer la contraseña o clave SSH para una cuenta de usuario existente, asegúrese de escribir el nombre exacto del usuario. Si escribe un nombre diferente, la extensión VMAccess crea una nueva cuenta de usuario y asigna la contraseña a esa cuenta.


### <a name="SSHKey"></a>Restablecimiento de una clave SSH

Introduzca el nombre de usuario actual de Linux y la ruta de acceso al certificado que contiene las claves SSH y, a continuación, ejecute estos comandos.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Restablecimiento de la contraseña y la clave SSH

Introduzca el nombre de usuario actual de Linux, la nueva contraseña y la ruta de acceso al certificado que contiene las claves SSH y, a continuación, ejecute estos comandos.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Restablecimiento de la configuración de SSH

La existencia de errores en la configuración de SSH puede impedir que tenga acceso a la máquina virtual. Para solucionar este problema, restablezca la configuración de SSH a su estado predeterminado. Esto quita todos los nuevos parámetros de acceso de la configuración, como el nombre de usuario, la contraseña y la clave SSH, pero no cambia la contraseña o las claves SSH de la cuenta de usuario. La extensión reinicia el servidor SSH, abre el puerto SSH en su máquina virtual y restablece la configuración de SSH al valor predeterminado.

Ejecute estos comandos.

	$PrivateConfig = '{"reset_ssh": "True"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]El archivo de configuración de SSH se encuentra en /etc/ssh/sshd\_config.

### <a name="delete"></a> Eliminación de un usuario

Introduzca el nombre de usuario de Linux que desea eliminar y, a continuación, ejecute estos comandos.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>Visualización del estado de la extensión VMAccess

Para mostrar el estado de la extensión VMAccess, ejecute este comando.

	$vm.GuestAgentStatus


## Recursos adicionales

[Características y extensiones de máquina virtual de Azure][]

[Conexión a una máquina virtual de Azure con RDP o SSH][]


<!--Link references-->
[Guía de usuario del agente de Linux de Azure]: virtual-machines-linux-agent-user-guide.md
[Instalación y configuración de Azure PowerShell]: ../install-configure-powershell.md
[Características y extensiones de máquina virtual de Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Conexión a una máquina virtual de Azure con RDP o SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=Sept15_HO3-->