<properties 
	pageTitle="Cómo usar VMAccess para las máquinas virtuales de Linux" 
	description="Cómo usar la extensión VMAccess de Linux para restablecer las contraseñas y claves SSH, para volver a enviar las configuraciones de SSH y para eliminar los usuarios de Linux" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux #

Si no puede conectarse a una máquina virtual Linux debido a una contraseña olvidada, una clave incorrecta de Shell seguro (SSH) o un problema con la configuración de SSH, utilice la extensión VMAccessForLinux para restablecer la contraseña o la clave SSH, o para corregir la configuración de SSH. 

## Requisitos

- Agente de Linux de Microsoft Azure versión 2.0.5 o posterior. La mayoría de imágenes de Linux en la Galería de máquinas virtuales incluyen la versión 2.0.5. Para averiguar qué versión está instalada, ejecute  `waagent -version`. Para actualizar el agente, siga las instrucciones de la [Guía de usuario del agente de Linux de Azure].
- Azure PowerShell. Utilizará comandos en el cmdlet **Set-AzureVMExtension** para cargar y configurar automáticamente la extensión **VMAccessForLinux**. Para obtener más información acerca de cómo configurar Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell].
- Una nueva contraseña o un conjunto de claves SSH, si desea restablecer cualquiera de ellos. No los necesita si desea restablecer la configuración de SSH. 

## No necesita la instalación

No es necesario instalar la extensión VMAccess antes de utilizarla. Siempre que el agente de Linux esté instalado en la máquina virtual, la extensión se cargará automáticamente cuando se ejecute un comando de Azure PowerShell que use el cmdlet **Set-AzureVMExtension**. 

## Utilice la extensión para restablecer una contraseña, una clave SSH o la configuración de SSH, o para eliminar un usuario

El cmdlet **Set-AzureVMExtension** se usa para realizar cualquiera de los cambios que VMAccess permite realizar. En todos los casos, empiece usando el nombre de servicio de nube y el nombre de la máquina virtual para obtener el objeto de máquina virtual y almacenarlo en una variable. 

Introduzca los nombres del servicio en la nube y de la máquina virtual y ejecute los siguientes comandos en un símbolo de sistema de Azure PowerShell con nivel de administrador. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < y >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Si no conoce los nombres del servicio en la nube ni de la máquina virtual, ejecute **Get-AzureVM** para que se muestre esa información de todas las máquinas virtuales de su suscripción actual.


> [AZURE.NOTE] Las líneas de comandos que comienzan con $ configuran las variables de PowerShell que más adelante se usan en los comandos de PowerShell.

Si ha creado la máquina virtual con el Portal de administración de Azure, ejecute el siguiente comando adicional:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá el error "El agente de invitado de aprovisionamiento debe estar habilitado en el objeto de máquina virtual antes de establecer la extensión de acceso de máquina virtual IaaS" cuando se ejecuta el comando Set-AzureVMExtension en las secciones siguientes. 

A continuación, puede realizar las tareas siguientes:

+ [Restablecer la contraseña](#password)
+ [Restablecer una clave SSH](#SSHkey)
+ [Restablecer la contraseña y la clave SSH](#both)
+ [Restablecer la configuración de SSH](#config)
+ [Eliminar un usuario](#delete)

### <a name="password"></a>Restablecer la contraseña

Introduzca el nombre de usuario actual de Linux y la nueva contraseña y, a continuación, ejecute estos comandos.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Si desea restablecer la contraseña o clave SSH para una cuenta de usuario existente, asegúrese de escribir el nombre exacto del usuario. Si escribe un nombre diferente, la extensión VMAccess crea una nueva cuenta de usuario y asigna la contraseña a esa cuenta.


### <a name="SSHKey"></a>Restablecer una clave SSH

Introduzca el nombre de usuario actual de Linux y la ruta de acceso al certificado que contiene las claves SSH y, a continuación, ejecute estos comandos.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Restablecer la contraseña y la clave SSH

Introduzca el nombre de usuario actual de Linux, la nueva contraseña y la ruta de acceso al certificado que contiene las claves SSH y, a continuación, ejecute estos comandos.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Restablecer la configuración de SSH

La existencia de errores en la configuración de SSH puede impedir que tenga acceso a la máquina virtual. Para solucionar este problema, restablezca la configuración de SSH a su estado predeterminado. Esto quita todos los nuevos parámetros de acceso de la configuración, como el nombre de usuario, la contraseña y la clave SSH, pero no cambia la contraseña o las claves SSH de la cuenta de usuario. La extensión reinicia el servidor SSH, abre el puerto SSH en su máquina virtual y restablece la configuración de SSH al valor predeterminado. 

Ejecute estos comandos.

	$PrivateConfig = '{"reset_ssh": "True"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] El archivo de configuración de SSH se encuentra en /etc/ssh/sshd_config.

### <a name="delete"></a> Eliminar un usuario

Introduzca el nombre de usuario de Linux que desea eliminar y, a continuación, ejecute estos comandos.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## Recursos adicionales

[Características y extensiones de máquina virtual de Azure] []

[Conectarse a una máquina virtual de Azure con RDP o SSH][]


<!--Link references-->
[Guía de usuario del Agente de Linux de Azure]: ../virtual-machines-linux-agent-user-guide
[Instalación y configuración de Azure PowerShell]: ../install-configure-powershell
[Características y extensiones de máquina virtual de Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Conectarse a una máquina virtual de Azure con RDP o SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
