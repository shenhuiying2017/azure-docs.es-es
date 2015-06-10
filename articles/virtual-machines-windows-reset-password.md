<properties 
	pageTitle="Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows" 
	description="Restablezca rápidamente una contraseña de administrador local o el servicio de Escritorio remoto para máquinas virtuales de Windows con el Portal de vista previa de Azure o comandos de PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="josephd"/>

# Cómo restablecer una contraseña o el servicio de Escritorio remoto para máquinas virtuales de Windows

Si no puede conectarse a una máquina virtual Windows debido a que se olvidó la contraseña o por un problema con la configuración de servicio del Escritorio remoto, use el Portal de vista previa de Azure o la extensión VMAccess para restablecer la contraseña de administrador local o restablezca la configuración de servicio de Escritorio remoto.

## Portal de vista previa de Azure

Para restablecer el servicio de Escritorio remoto en el [Portal de vista previa de Azure](https://portal.azure.com), haga clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual Windows* > **Restablecer acceso remoto**. Aquí tiene un ejemplo.


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

Para restablecer el nombre y la contraseña de la cuenta de administrador local en el [Portal de vista previa de Azure](https://portal.azure.com), haga clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual Windows* > **Toda la configuración** > **Restablecimiento de contraseña**. Aquí tiene un ejemplo.

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)

 
## Extensión VMAccess y PowerShell

Antes de comenzar, necesitará lo siguiente:

- El módulo de Azure PowerShell, versión 0.8.5 o posterior. Puede comprobar la versión de Azure PowerShell que ha instalado con el comando **Get-Module azure | format-table version**. Para obtener instrucciones y un vínculo a la versión más reciente, consulte [Instalación y configuración de Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409). 
- La nueva contraseña de cuenta de administrador local. No necesita esto si desea restablecer la configuración de servicio de Escritorio remoto. 
- El Agente de máquina virtual. 

No es necesario instalar la extensión VMAccess antes de utilizarla. Siempre que el agente de VM esté instalado en la máquina virtual, la extensión se cargará automáticamente cuando se ejecute un comando de Azure PowerShell que usa el cmdlet **Set-AzureVMExtension**.
 
En primer lugar, compruebe que el agente de máquina virtual ya está instalado. Introduzca el nombre de servicio de nube y el nombre de la máquina virtual y, a continuación, ejecute los siguientes comandos en un símbolo de sistema de Azure PowerShell con nivel de administrador. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Si no conoce el nombre del servicio en la nube y de la máquina virtual, ejecute **Get-AzureVM** para mostrar esa información para todas las máquinas virtuales de su suscripción actual.

Si el comando **write-host** muestra **True**, el agente de la máquina virtual está instalado. Si aparece **False**, consulte las instrucciones y un vínculo a la descarga en la publicación del blog de Azure [Agente de máquina virtual y extensiones, parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Si ha creado la máquina virtual con el Portal de administración de Azure, ejecute el siguiente comando adicional:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá el error "El agente de invitado de aprovisionamiento debe estar habilitado en el objeto de máquina virtual antes de establecer la extensión de acceso de máquina virtual IaaS" cuando se ejecuta el comando Set-AzureVMExtension en las secciones siguientes.

Ahora puede realizar estas tareas:

- Restablecer una contraseña de cuenta de administrador local
- Restablecer la configuración de servicio de Escritorio remoto

## Restablecer la contraseña de cuenta de administración local

Introduzca el nombre de la cuenta de administrador local actual y la contraseña nueva y, a continuación, ejecute estos comandos.

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- Si escribe un nombre distinto a la cuenta actual, la extensión VMAccess cambia el nombre del administrador local, asigna la contraseña a esa cuenta y emite un cierre de sesión con Escritorio remoto.
- Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.
 
Estos comandos también restablecen la configuración de servicio de Escritorio remoto.

## Restablecer la configuración de servicio de Escritorio remoto

Para restablecer la configuración de servicio de Escritorio remoto, ejecute este comando.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

La extensión VMAccess ejecuta estos dos comandos en la máquina virtual:

- **netsh advfirewall firewall set rule group="Escritorio remoto" new enable=Yes**

	Este comando habilita el grupo de firewall de Windows integrado que permite el tráfico de Escritorio remoto entrante, el que usa el puerto TCP 3389.

- **Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0**

	Este comando establece el valor de registro fDenyTSConnections en 0, lo que permite conexiones de Escritorio remoto.

Si esto no solucionó el problema de acceso a Escritorio remoto, ejecute el [paquete de diagnóstico de Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864).

1.	Haga clic en **Paquete de diagnóstico de Microsoft Azure IaaS (Windows)** que aparece en esta página para crear una sesión de diagnóstico nueva.
2.	En la página **¿Cuáles de los siguientes problemas experimenta con su máquina virtual de Azure?**, seleccione el problema **Conectividad de RDP a una máquina virtual de Azure (se requiere reinicio)** 

Para obtener más información, consulte el [artículo de Knowledge Base Paquete de diagnóstico de Microsoft Azure IaaS (Windows)](http://support.microsoft.com/kb/2976864).

Si no fue posible ejecutar el paquete de diagnóstico de Azure IaaS (Windows) o si lo ejecutó, pero no se solucionó el problema, consulte [Solución de problemas con conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-troubleshoot-remote-desktop-connections.md).


## Recursos adicionales

[Características y extensiones de máquina virtual de Azure](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[Conexión a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

<!---HONumber=58-->