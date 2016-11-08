---
title: Restablecimiento de la contraseña o la configuración de Escritorio remoto en una máquina virtual Windows | Microsoft Docs
description: Aprenda a restablecer la contraseña de una cuenta o los servicios de Escritorio remoto en una máquina virtual Windows mediante Azure Portal o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: iainfou

---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Restablecimiento del servicio Escritorio remoto o la contraseña de inicio de sesión en una VM con Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si no puede conectarse a una máquina virtual Windows, puede restablecer la contraseña de administrador local o la configuración de servicio de Escritorio remoto. Puede usar el Portal de Azure o la extensión de acceso de máquina virtual en Azure PowerShell para restablecer la contraseña. Si usa Azure PowerShell, procure tener instalado el módulo de PowerShell más reciente en el equipo de trabajo y haber iniciado sesión en su suscripción de Azure. Para ver los pasos detallados, lea [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

> [!TIP]
> Puede comprobar qué versión de PowerShell tiene instalada con `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`.
> 
> 

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Máquinas virtuales de Windows en el modelo de implementación de Resource Manager
### <a name="azure-portal"></a>Portal de Azure
Seleccione la máquina virtual haciendo clic en **Examinar** > **Máquinas virtuales** > *su máquina virtual con Windows* > **Toda la configuración** > **Restablecer contraseña**. Se mostrará la hoja de restablecimiento de contraseña:

![Página Restablecimiento de contraseña](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Escriba el nombre de usuario y una nueva contraseña nueva; después, haga clic en **Guardar**. Trate de conectarse de nuevo a la máquina virtual.

### <a name="vmaccess-extension-and-powershell"></a>Extensión VMAccess y PowerShell
Asegúrese de tener instalado Azure PowerShell 1.0 o una versión posterior, además de haber iniciado sesión en su cuenta con el cmdlet `Login-AzureRmAccount` .

#### <a name="**reset-the-local-administrator-account-password**"></a>**Restablecer la contraseña de cuenta de administración local**
Puede restablecer la contraseña de administrador o el nombre de usuario con el comando de PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) .

Cree las credenciales de la cuenta de administrador local con el siguiente comando:

    $cred=Get-Credential

Si escribe un nombre distinto al de la cuenta actual, el siguiente comando de la extensión VMAccess cambia el nombre de la cuenta de administrador local, asigna la contraseña a esa cuenta y emite un evento de cierre de sesión con Escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.

Utilice la extensión VMAccess para establecer las credenciales nuevas como se muestra a continuación:

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Reemplace `myRG`, `myVM`, `myVMAccess` y la ubicación por valores correspondientes a la instalación.

#### <a name="**reset-the-remote-desktop-service-configuration**"></a>**Restablecer la configuración de servicio de Escritorio remoto**
Puede restablecer el acceso remoto a su VM con los comandos [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) o [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), tal como se muestra aquí. Reemplace `myRG`, `myVM`, `myVMAccess` y la ubicación por sus propios valores.

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

O bien:<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [!TIP]
> Ambos comandos agregan a la máquina virtual un nuevo agente de acceso de VM con nombre. En cualquier momento, una VM puede tener un solo agente de acceso a VM. Para establecer las propiedades del agente de acceso de máquina virtual correctamente, quite el agente de acceso establecido anteriormente; para ello, use `Remove-AzureRmVMAccessExtension` o `Remove-AzureRmVMExtension`. Desde la versión 1.2.2 de Azure PowerShell, puede omitir este paso cuando use `Set-AzureRmVMExtension` con una opción `-ForceRerun`. Al usar `-ForceRerun`, procure usar el mismo nombre para el agente de acceso de máquina virtual establecido mediante el comando anterior.
> 
> 

Si sigue sin poder conectarse remotamente a la máquina virtual, consulte más pasos que puede tratar de realizar en [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

## <a name="windows-vms-in-the-classic-deployment-model"></a>Máquinas virtuales de Windows en el modelo de implementación clásica
### <a name="azure-portal"></a>Portal de Azure
En el caso de las máquinas virtuales creadas con el modelo de implementación clásica, puede usar el [Portal de Azure](https://portal.azure.com) para restablecer el servicio Escritorio remoto. Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual Windows* > **Restablecer acceso remoto**. Aparece la siguiente página.

![Página Restablecer configuración de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

También puede intentar restablecer el nombre y la contraseña de la cuenta de administrador local. Haga clic en **Examinar** > **Máquinas virtuales (clásico)** > *su máquina virtual Windows* > **Toda la configuración** > **Restablecer contraseña**. Aparece la siguiente página.

![Página Restablecimiento de contraseña](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Después de escribir el nuevo nombre de usuario y la contraseña, haga clic en **Guardar**.

### <a name="vmaccess-extension-and-powershell"></a>Extensión VMAccess y PowerShell
Asegúrese de que el agente de VM esté instalado en la máquina virtual. No es necesario instalar la extensión VMAccess para poder usarlo, siempre que el agente de VM esté disponible. Use el comando siguiente para comprobar que el agente de VM ya está instalado. (Reemplace los valores de "myCloudService" y "myVM" por los nombres de su servicio en la nube y su VM respectivamente. Para saber qué nombres son, ejecute `Get-AzureVM` sin ningún parámetro).

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Si el comando **write-host** muestra **True**, el agente de la máquina virtual está instalado. Si aparece **False**, consulte las instrucciones y un vínculo a la descarga en la publicación del blog de Azure [Agente de máquina virtual y extensiones, parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Si creó la máquina virtual con el Portal, compruebe si `$vm.GetInstance().ProvisionGuestAgent` devuelve un valor **True**. De lo contrario, puede configurarlo con este comando:

    $vm.GetInstance().ProvisionGuestAgent = $true

Este comando impedirá que surja el siguiente error cuando ejecute el comando **Set-AzureVMExtension** en los siguientes pasos: Provision Guest Agent must be enabled on the VM object before setting IaaS VM Access Extension (El Agente del invitado de aprovisionamiento debe estar habilitado en el objeto de la máquina virtual antes de establecer la extensión de acceso a máquinas virtuales IaaS).

#### <a name="**reset-the-local-administrator-account-password**"></a>**Restablecer la contraseña de cuenta de administración local**
Cree una credencial de inicio de sesión con el nombre de cuenta de administrador local actual y una contraseña nueva y, luego, ejecute `Set-AzureVMAccessExtension` tal como se indica aquí.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Si escribe un nombre distinto a la cuenta actual, la extensión VMAccess cambia el nombre del administrador local, asigna la contraseña a esa cuenta y emite un cierre de sesión con Escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.

Estos comandos también restablecen la configuración de servicio de Escritorio remoto.

#### <a name="**reset-the-remote-desktop-service-configuration**"></a>**Restablecer la configuración de servicio de Escritorio remoto**
Para restablecer la configuración del servicio Escritorio remoto, ejecute el siguiente comando:

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

La extensión VMAccess ejecuta dos comandos en la máquina virtual:

* `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Este comando habilita el grupo de firewall de Windows integrado que permite el tráfico de Escritorio remoto entrante, el que usa el puerto TCP 3389.

* `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Este comando establece el valor de registro fDenyTSConnections en 0, lo que permite conexiones de Escritorio remoto.

## <a name="next-steps"></a>Pasos siguientes
Si la extensión de acceso de la máquina virtual de Azure no responde y no puede restablecer la contraseña, puede [restablecer la contraseña de Windows local sin conexión](virtual-machines-windows-reset-local-password-without-agent.md). Este método es un proceso más avanzado y requiere que se conecte el disco duro virtual de la máquina virtual problemática a otra máquina virtual. Primero siga los pasos descritos en este artículo e intente solamente el método de restablecimiento de contraseña sin conexión como último recurso.

[Características y extensiones de las máquinas virtuales de Azure](virtual-machines-windows-extensions-features.md)

[Conexión a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!--HONumber=Oct16_HO2-->


