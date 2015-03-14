<properties 
	pageTitle="Cómo usar VMAccess para las máquinas virtuales de Linux" 
	description="Cómo usar la extensión VMAccess para Linux para restablecer las contraseñas, claves SSH y configuraciones de SSH" 
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
	ms.date="10/30/2014" 
	ms.author="kathydav"/>

#Restablecimiento de una contraseña o de SSH para máquinas virtuales de Linux#

Si no puede conectarse a una VM de Linux debido a una contraseña olvidada, clave SSH o un problema con la configuración de SSH, utilice la extensión VMAccessforLinux para restablecer la contraseña, clave SSH o la configuración de SSH. 


##Requisitos

- Agente de Linux de Microsoft Azure versión 2.0.5 o posterior. La mayoría de imágenes de Linux en la Galería de máquinas virtuales incluyen la versión 2.0.5. Para averiguar qué versión está instalada, ejecute  `waagent -version`. Para actualizar el agente, siga las instrucciones de la [Guía de usuario del agente de Linux de Azure].

- El módulo de PowerShell de Azure. El módulo incluye el cmdlet **Set-AzureVMExtension**, con el que ejecutará comandos para usar la extensión **VMAccessForLinux**. Para obtener más información acerca de cómo configurar el módulo, consulte [Cómo instalar y configurar Azure PowerShell].

- Una nueva contraseña o claves SSH, si desea restablecer cualquiera de ellos. No es necesario aquellos si desea corregir la configuración de SSH. 

##No necesita la instalación

VMAccess no debe instalarse antes de su uso. Siempre que el agente de Linux y el módulo de Azure están instalados, la extensión se carga automáticamente cuando se ejecuta un comando que llama al cmdlet **Set-AzureVMExtension**. 

##Utilizar la extensión para restablecer una contraseña, la clave o la configuración de SSH, o agregar un usuario

El cmdlet **Set-AzureVMExtension** se usa para realizar cualquiera de los cambios que VMAccess permite realizar. En todos los casos, empiece usando el nombre de servicio de nube y el nombre de la máquina virtual para obtener el objeto de máquina virtual y almacenarlo en una variable.   

Abra Azure PowerShell y escriba lo siguiente en el símbolo del sistema. Asegúrese de reemplazar los marcadores de posición MyServiceName y MyVMName con los nombres reales:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

A continuación, puede realizar las tareas siguientes:

+ [Restablecer la contraseña](#password)
+ [Restablecer una clave SSH](#SSHkey)
+ [Restablecer la contraseña y la clave SSH](#both)
+ [Restablecer la configuración de SSH](#config)

### <a name="password"></a>Restablecer la contraseña
Escriba el nombre de usuario y la contraseña y almacénelos en variables; luego, cree una sola variable para almacenar los valores para que los comandos siguientes pueden utilizarlos.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

Almacene el nombre, el publicador y el número de versión en variables: 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

Con todos los valores necesarios almacenados en variables, ejecute el siguiente comando:

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Si desea restablecer la contraseña o clave SSH para una cuenta de usuario existente, asegúrese de escribir el nombre exacto del usuario. Si escribe un nombre diferente, la extensión VMAccess crea una nueva cuenta de usuario y asigna la contraseña a esa cuenta.

### <a name="SSHkey"></a>Restablecer una clave SSH

Escriba el nombre de usuario y la ruta de acceso de la nueva clave pública de SSH y almacénelos en variables:

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

Ejecute los comandos siguientes:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>Restablecer la contraseña y la clave SSH

Para el usuario actual, escriba una contraseña nueva y la ruta de acceso del nuevo certificado con la clave pública SSH y almacénelos en variables: 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

Ejecute los comandos siguientes:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>Restablecer la configuración de SSH

La existencia de errores en la configuración de SSH pueden impedir que tengan acceso a la máquina virtual. Puede solucionar este problema restableciendo la configuración a su valor predeterminado. Esto quita todos los parámetros de acceso nuevo en la configuración (nombre de usuario, contraseña o clave SSH). Esto no cambia la contraseña ni las claves SSH de la cuenta de usuario. La extensión reinicia el servidor SSH, abre el puerto SSH en su máquina virtual y restablece la configuración de SSH al valor predeterminado.  

Establezca la marca que indica que desea restablecer la configuración y almacénela en una variable: 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

Ejecute los comandos siguientes:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] El archivo de configuración de SSH se encuentra en /etc/ssh/sshd_config.

## Solución de problemas

Cuando se usa el cmdlet **Set-AzureVMExtension**, puede aparecer este error: "El Agente del invitado de aprovisionamiento debe estar habilitado en el objeto de la máquina virtual antes de establecer la extensión de acceso a máquinas virtuales IaaS". 

Esto puede suceder si ha utilizado el Portal de administración para crear la VM de Linux, porque no se puede establecer el valor de la propiedad de agente invitado en "True". Para solucionar este problema, ejecute los siguientes comandos:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#Recursos adicionales
[Características y extensiones de máquina virtual de Azure] []

[Conectarse a una máquina virtual de Azure con RDP o SSH] []


<!--Link references-->
[Guía de usuario del Agente de Linux de Azure]: ../virtual-machines-linux-agent-user-guide
[Cómo instalar y configurar Azure PowerShell]: ../install-configure-powershell
[Características y extensiones de máquina virtual de Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Conectarse a una máquina virtual de Azure con RDP o SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx



<!--HONumber=42-->
