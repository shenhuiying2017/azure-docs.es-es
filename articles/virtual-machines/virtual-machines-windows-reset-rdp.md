<properties
	pageTitle="Restablecimiento de la contraseña o el escritorio remoto en una máquina virtual Windows | Microsoft Azure"
	description="Restablezca la contraseña de administrador o los servicios de escritorio remoto en una máquina virtual Windows creada con el modelo de implementación del Administrador de recursos."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="dkshir"/>

# Establecimiento del servicio de Escritorio remoto o la contraseña de inicio de sesión en una VM de Azure basada en Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


Si no puede conectarse a una máquina virtual de Windows debido a que se le olvidó la contraseña o por un problema con la configuración del servicio de Escritorio remoto, en este artículo se describe cómo restablecer la contraseña de administrador local o restablecer la configuración del servicio de Escritorio remoto.

Según el modelo de implementación de la máquina virtual, puede usar el portal o la extensión VMAccess en Azure PowerShell. Si usa Azure PowerShell, asegúrese de tener instalado el módulo de Azure PowerShell más reciente en el equipo de trabajo y de que inició sesión en su suscripción a Azure. Para obtener los pasos detallados, lea [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).


> [AZURE.TIP] Puede comprobar la versión de Azure PowerShell que tiene instalada con el comando `Get-Module azure | format-table version`.


## Máquinas virtuales de Windows en el modelo de implementación clásica

### Portal de Azure

En el caso de las máquinas virtuales creadas con el modelo de implementación clásica, puede usar el [Portal de Azure](https://portal.azure.com) para restablecer el servicio de Escritorio remoto. Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual de Windows* > **Restablecer acceso remoto...**. Aparece la siguiente página.


![](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

También puede intentar restablecer el nombre y la contraseña de la cuenta de administrador local. Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual de Windows* > **Toda la configuración** > **Restablecer contraseña**. Aparece la siguiente página.

![](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Haga clic en **Guardar** después de escribir el nuevo nombre de usuario y contraseña.

### Extensión VMAccess y PowerShell

Asegúrese de que el agente de VM esté instalado en la máquina virtual. No es necesario instalar la extensión VMAccess para poder usarlo, siempre que el agente de VM esté disponible. Use el comando siguiente para comprobar que el agente de VM ya está instalado. Reemplace los valores de "myCloudService" y "myVM" por los nombres de su servicio en la nube y su VM, respectivamente. Para saber cuáles son, ejecute `Get-AzureVM` sin ningún parámetro.

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

Si el comando **write-host** muestra **True**, el agente de la máquina virtual está instalado. Si aparece **False**, consulte las instrucciones y un vínculo a la descarga en la publicación del blog de Azure [Agente de máquina virtual y extensiones, parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409).

Si creó la máquina virtual con el portal, revise si `$vm.GetInstance().ProvisionGuestAgent` devuelve un valor **True**. Si no es así, establézcalo con este comando:

	$vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá el error "El agente de invitado de aprovisionamiento debe estar habilitado en el objeto de máquina virtual antes de establecer la extensión de acceso de máquina virtual IaaS" cuando se ejecuta el comando **Set-AzureVMExtension** en las secciones siguientes.

#### **Restablecer la contraseña de cuenta de administración local**

Cree una credencial de inicio de sesión con el nombre de cuenta de administrador local actual y una contraseña nueva y, luego, ejecute `Set-AzureVMAccessExtension` tal como se muestra a continuación.

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Si escribe un nombre distinto a la cuenta actual, la extensión VMAccess cambia el nombre del administrador local, asigna la contraseña a esa cuenta y emite un cierre de sesión con Escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.

Estos comandos también restablecen la configuración de servicio de Escritorio remoto.

#### **Restablecer la configuración de servicio de Escritorio remoto**

Para restablecer la configuración de servicio de Escritorio remoto, ejecute el siguiente comando.

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

La extensión VMAccess ejecuta estos dos comandos en la máquina virtual:

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Este comando habilita el grupo de firewall de Windows integrado que permite el tráfico de Escritorio remoto entrante, el que usa el puerto TCP 3389.

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Este comando establece el valor de registro fDenyTSConnections en 0, lo que permite conexiones de Escritorio remoto.


## Máquinas virtuales de Windows en el modelo de implementación de Resource Manager

Actualmente, el Portal de Azure no es compatible con el restablecimiento del acceso remoto o las credenciales de inicio de sesión de las máquinas virtuales creadas con Resource Manager.


### Extensión VMAccess y PowerShell

Asegúrese de tener instalado Azure PowerShell 1.0 o una versión superior, además de haber iniciado sesión en su cuenta con el cmdlet `Login-AzureRmAccount`.

#### **Restablecer la contraseña de cuenta de administración local**

Puede restablecer la contraseña de administrador y el nombre de usuario con el comando [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) de PowerShell.

Cree las credenciales de la cuenta de administrador local con el comando siguiente:

	$cred=Get-Credential

Si escribe un nombre distinto de la cuenta actual, el comando de extensión VMAccess que aparece a continuación cambiará el nombre de la cuenta de administrador local, asignará la contraseña a esa cuenta y emitirá un cierre de sesión con Escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilitará.
	
Utilice la extensión VMAccess para establecer las credenciales nuevas como se muestra a continuación:

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password


Reemplace `myRG`, `myVM`, `myVMAccess` y la ubicación por valores correspondientes a la instalación.


#### **Restablecer la configuración de servicio de Escritorio remoto**

Puede restablecer el acceso remoto a su VM con los comandos [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) o Set-AzureRmVMAccessExtension, tal como se muestra a continuación. Reemplace `myRG`, `myVM`, `myVMAccess` y la ubicación por sus propios valores.

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus

O<br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus

	
> [AZURE.TIP] Tanto el comando `Set-AzureRmVMAccessExtension` como el comando `Set-AzureRmVMExtension` agregan un nuevo agente de acceso de VM denominado a la máquina virtual. En cualquier momento, una VM solo puede tener un agente de acceso a VM. Para establecer las propiedades del agente de acceso a VM de manera sucesiva, quite el agente de acceso establecido anteriormente; para ello, use `Remove-AzureRmVMAccessExtension` o `Remove-AzureRmVMExtension`. A partir de la versión 1.2.2 de Azure PowerShell, puede evitar este paso cuando use `Set-AzureRmVMExtension` con una opción `-ForceRerun`. Asegúrese de usar el mismo nombre para el agente de acceso de VM según lo establecido por el comando anterior cuando use la opción `-ForceRerun`.


Si todavía no se puede conectar remotamente a la máquina virtual, consulte más pasos que puede intentar en [Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).


## Recursos adicionales

[Características y extensiones de las máquinas virtuales de Azure](virtual-machines-windows-extensions-features.md)

[Conexión a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0323_2016-->