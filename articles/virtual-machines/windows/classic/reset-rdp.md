---
title: "Restablecimiento de la contraseña o la configuración de Escritorio remoto en una máquina virtual Windows de Azure | Microsoft Docs"
description: "Aprenda a restablecer la contraseña de una cuenta o los servicios de Escritorio remoto en una máquina virtual Windows mediante el modelo de implementación clásica con Azure Portal o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 43e5cf1ab3bc3121d7e3915ea0785998e0ee2fc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>Restablecimiento del servicio Escritorio remoto o la contraseña de inicio de sesión en una máquina virtual Windows con el modelo de implementación clásica
> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear y trabajar con recursos: [el Administrador de recursos y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. También puede realizar estos pasos en las máquinas virtuales creadas con el [modelo de implementación de Resource Manager](../reset-rdp.md).

Si no puede conectarse a una máquina virtual Windows, puede restablecer la contraseña de administrador local o la configuración de servicio de Escritorio remoto. Puede usar el Portal de Azure o la extensión de acceso de máquina virtual en Azure PowerShell para restablecer la contraseña.

## <a name="ways-to-reset-configuration-or-credentials"></a>Métodos para restablecer la configuración o las credenciales
Puede restablecer los servicios y las credenciales de Escritorio remoto de varias maneras diferentes, dependiendo de sus necesidades:

- [Restablecimiento mediante Azure Portal](#azure-portal)
- [Restablecimiento mediante Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portal de Azure
Restablezca el servicio de Escritorio remoto desde [Azure Portal](https://portal.azure.com). Para expandir los menús del portal, haga clic en las tres barras en la esquina superior izquierda y, a continuación, en **Máquinas virtuales (clásico)**:

![Busque la máquina virtual de Azure.](./media/reset-rdp/Portal-Select-Classic-VM.png)

Seleccione la máquina virtual Windows y haga clic en **Restablecer acceso...** Aparece el siguiente cuadro de diálogo para restablecer la configuración de Escritorio remoto:

![Página Restablecer configuración de RDP](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

También puede restablecer el nombre de usuario y la contraseña de la cuenta de administrador local. En la máquina virtual, haga clic en **Soporte y solución de problemas** > **Restablecer contraseña**. Se mostrará la hoja de restablecimiento de contraseña:

![Página Restablecimiento de contraseña](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Después de escribir el nuevo nombre de usuario y la contraseña, haga clic en **Guardar**.

## <a name="vmaccess-extension-and-powershell"></a>Extensión VMAccess y PowerShell
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
Si la extensión de acceso de la máquina virtual de Azure no responde y no puede restablecer la contraseña, puede [restablecer la contraseña de Windows local sin conexión](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este método es un proceso más avanzado y requiere que se conecte el disco duro virtual de la máquina virtual problemática a otra máquina virtual. Primero siga los pasos descritos en este artículo e intente solamente el método de restablecimiento de contraseña sin conexión como último recurso.

[Características y extensiones de las máquinas virtuales de Azure](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Conexión a una máquina virtual de Azure con RDP o SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Solucionar problemas de conexiones de Escritorio remoto a una máquina virtual de Azure basada en Windows](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

