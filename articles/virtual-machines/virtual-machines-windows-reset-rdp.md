---
title: "Restablecimiento de la contraseña o la configuración de Escritorio remoto en una VM Windows | Microsoft Docs"
description: "Aprenda a restablecer la contraseña de una cuenta o los servicios de Escritorio remoto en una máquina virtual Windows mediante Azure Portal o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 8062106872bd50cd265fc4e64bc6f4c0fb2dbb1d
ms.openlocfilehash: b7458e13174dc03f2c4cab707c033d96a80628e6


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Restablecimiento del servicio Escritorio remoto o la contraseña de inicio de sesión en una VM con Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si no puede conectarse a una máquina virtual Windows, puede restablecer la contraseña de administrador local o la configuración de servicio de Escritorio remoto. Puede usar el Portal de Azure o la extensión de acceso de máquina virtual en Azure PowerShell para restablecer la contraseña. Si usa Azure PowerShell, procure tener instalado el módulo de PowerShell más reciente en el equipo de trabajo y haber iniciado sesión en su suscripción de Azure. Para ver los pasos detallados, lea [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!TIP]
> Puede comprobar qué versión de PowerShell tiene instalada usando el siguiente comando:
>
> `Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`


## <a name="ways-to-reset-configuration-or-credentials"></a>Métodos para restablecer la configuración o las credenciales
Puede restablecer los servicios y las credenciales de Escritorio remoto de varias maneras diferentes, dependiendo de sus necesidades. Para las máquinas virtuales creadas con el modelo de implementación de Resource Manager:

- [Restablecimiento mediante Azure Portal](#azure-portal---resource-manager)
- [Restablecimiento mediante Azure PowerShell](#vmaccess-extension-and-powershell---resource-manager)

Para las máquinas virtuales creadas con el modelo de implementación clásica:

- [Restablecimiento mediante Azure Portal](#azure-portal---classic)
- [Restablecimiento mediante Azure PowerShell](#vmaccess-extension-and-powershell---classic)

## <a name="azure-portal---resource-manager"></a>Azure Portal: Resource Manager
Para expandir los menús del portal, haga clic en las tres barras en la esquina superior izquierda y, a continuación, en **Máquinas virtuales**:

![Busque la máquina virtual de Azure.](./media/virtual-machines-windows-reset-rdp/Portal-Select-VM.png)

Seleccione la máquina virtual Windows, haga clic en **Soporte y solución de problemas** > **Restablecer contraseña**. Se mostrará la hoja de restablecimiento de contraseña:

![Página Restablecimiento de contraseña](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Escriba el nombre de usuario y una nueva contraseña nueva; después, haga clic en **Guardar**. Trate de conectarse de nuevo a la máquina virtual.


## <a name="vmaccess-extension-and-powershell---resource-manager"></a>Extensión VMAccess y PowerShell: Resource Manager
Asegúrese de tener instalado Azure PowerShell 1.0 o una versión posterior, además de haber iniciado sesión en su cuenta con el cmdlet `Login-AzureRmAccount` .

### <a name="reset-the-local-administrator-account-password"></a>**Restablecer la contraseña de cuenta de administración local**
Puede restablecer la contraseña de administrador o el nombre de usuario con el comando de PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) .

Cree las credenciales de la cuenta de administrador local con el siguiente comando:

```powershell
$cred=Get-Credential
```

Si escribe un nombre distinto al de la cuenta actual, el siguiente comando de la extensión VMAccess cambia el nombre de la cuenta de administrador local, asigna la contraseña a esa cuenta y emite un evento de cierre de sesión con Escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.

Utilice la extensión VMAccess para establecer las credenciales nuevas como se muestra a continuación:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Reemplace `myResourceGroup`, `myVM`, `myVMAccess` y la ubicación por valores correspondientes a la instalación.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Restablecer la configuración de servicio de Escritorio remoto**
Puede restablecer el acceso remoto a su VM con los comandos [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) o [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), tal como se muestra aquí. Reemplace `myResourceGroup`, `myVM`, `myVMAccess` y la ubicación por sus propios valores.

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
    -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"
```

O bien:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0
```

> [!TIP]
> Ambos comandos agregan a la máquina virtual un nuevo agente de acceso de VM con nombre. En cualquier momento, una VM puede tener un solo agente de acceso a VM. Para establecer las propiedades del agente de acceso de máquina virtual correctamente, quite el agente de acceso establecido anteriormente; para ello, use `Remove-AzureRmVMAccessExtension` o `Remove-AzureRmVMExtension`. 
>
> Desde la versión 1.2.2 de Azure PowerShell, puede omitir este paso cuando use `Set-AzureRmVMExtension` con una opción `-ForceRerun`. Al usar `-ForceRerun`, procure usar el mismo nombre para el agente de acceso de máquina virtual establecido mediante el comando anterior.

Si sigue sin poder conectarse remotamente a la máquina virtual, consulte más pasos que puede tratar de realizar en [Solución de problemas de conexiones del Escritorio remoto a una máquina virtual de Azure con Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="azure-portal---classic"></a>Azure Portal: modelo de implementación clásica de Azure
En el caso de las máquinas virtuales creadas con el modelo de implementación clásica, puede usar el [Portal de Azure](https://portal.azure.com) para restablecer el servicio Escritorio remoto. Para expandir los menús del portal, haga clic en las tres barras en la esquina superior izquierda y, a continuación, en **Máquinas virtuales (clásico)**:

![Busque la máquina virtual de Azure.](./media/virtual-machines-windows-reset-rdp/Portal-Select-Classic-VM.png)

Seleccione la máquina virtual Windows y haga clic en **Restablecer acceso...** Aparece el siguiente cuadro de diálogo para restablecer la configuración de Escritorio remoto:

![Página Restablecer configuración de RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

También puede restablecer el nombre de usuario y la contraseña de la cuenta de administrador local. En la máquina virtual, haga clic en **Soporte y solución de problemas** > **Restablecer contraseña**. Se mostrará la hoja de restablecimiento de contraseña:

![Página Restablecimiento de contraseña](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Después de escribir el nuevo nombre de usuario y la contraseña, haga clic en **Guardar**.

## <a name="vmaccess-extension-and-powershell---classic"></a>Extensión VMAccess y PowerShell: modelo de implementación clásica de Azure
Asegúrese de que el agente de VM esté instalado en la máquina virtual. No es necesario instalar la extensión VMAccess para poder usarlo, siempre que el agente de VM esté disponible. Use el comando siguiente para comprobar que el agente de VM ya está instalado. (Reemplace los valores de "myCloudService" y "myVM" por los nombres de su servicio en la nube y su VM respectivamente. Para saber qué nombres son, ejecute `Get-AzureVM` sin ningún parámetro).

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Si el comando **write-host** muestra **True**, el agente de la máquina virtual está instalado. Si aparece **False**, consulte las instrucciones y un vínculo a la descarga en la publicación del blog de Azure [Agente de máquina virtual y extensiones, parte 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Si creó la máquina virtual con el Portal, compruebe si `$vm.GetInstance().ProvisionGuestAgent` devuelve un valor **True**. De lo contrario, puede configurarlo con este comando:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Este comando impedirá que surja el siguiente error cuando ejecute el comando **Set-AzureVMExtension** en los siguientes pasos: Provision Guest Agent must be enabled on the VM object before setting IaaS VM Access Extension (El Agente del invitado de aprovisionamiento debe estar habilitado en el objeto de la máquina virtual antes de establecer la extensión de acceso a máquinas virtuales IaaS).

### <a name="reset-the-local-administrator-account-password"></a>**Restablecer la contraseña de cuenta de administración local**
Cree una credencial de inicio de sesión con el nombre de cuenta de administrador local actual y una contraseña nueva y, luego, ejecute `Set-AzureVMAccessExtension` tal como se indica aquí.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Si escribe un nombre distinto a la cuenta actual, la extensión VMAccess cambia el nombre del administrador local, asigna la contraseña a esa cuenta y emite un cierre de sesión con Escritorio remoto. Si la cuenta de administrador local está deshabilitada, la extensión VMAccess la habilita.

Estos comandos también restablecen la configuración de servicio de Escritorio remoto.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Restablecer la configuración de servicio de Escritorio remoto**
Para restablecer la configuración del servicio Escritorio remoto, ejecute el siguiente comando:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

La extensión VMAccess ejecuta dos comandos en la máquina virtual:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Este comando habilita el grupo de firewall de Windows integrado que permite el tráfico de Escritorio remoto entrante, el que usa el puerto TCP 3389.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Este comando establece el valor de registro fDenyTSConnections en 0, lo que permite conexiones de Escritorio remoto.

## <a name="next-steps"></a>Pasos siguientes
Si la extensión de acceso de la máquina virtual de Azure no responde y no puede restablecer la contraseña, puede [restablecer la contraseña de Windows local sin conexión](virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este método es un proceso más avanzado y requiere que se conecte el disco duro virtual de la máquina virtual problemática a otra máquina virtual. Primero siga los pasos descritos en este artículo e intente solamente el método de restablecimiento de contraseña sin conexión como último recurso.

[Características y extensiones de las máquinas virtuales de Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Conexión a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO3-->


