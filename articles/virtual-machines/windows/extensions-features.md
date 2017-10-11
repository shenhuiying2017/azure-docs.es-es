---
title: "Características y extensiones de máquinas virtuales para Windows en Azure | Microsoft Docs"
description: "Obtenga información acerca de qué extensiones están disponibles para máquinas virtuales de Azure, agrupadas por lo que proporcionan o mejoran."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ce0eebd2585c9457d7f922898d7f2fa3e7ffad7
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Características y extensiones de las máquinas virtuales para Windows

Las extensiones de máquina virtual de Azure son pequeñas aplicaciones que realizan tareas de automatización y configuración posterior a la implementación en máquinas virtuales de Azure. Por ejemplo, si una máquina virtual necesita que se instale software, protección antivirus o configuración de Docker, se puede usar una extensión de máquina virtual para completar estas tareas. Las extensiones de máquina virtual de Azure se pueden ejecutar con la CLI de Azure, PowerShell, plantillas de Azure Resource Manager y Azure Portal. Las extensiones pueden empaquetar con una nueva implementación de máquina virtual o se pueden ejecutar en cualquier sistema existente.

Este documento proporciona información general sobre las extensiones de máquina virtual, requisitos previos para usar las extensiones de máquina virtual e instrucciones sobre cómo detectar, administrar y quitar extensiones de máquina virtual. Este documento proporciona información generalizada porque muchas extensiones de máquina virtual están disponibles, cada una con una configuración potencialmente única. En cada documento específico de la extensión individual pueden encontrarse detalles específicos de extensión.

## <a name="use-cases-and-samples"></a>Casos de uso y ejemplos

Hay muchas extensiones de máquina virtual de Azure diferentes disponibles, cada una con un caso de uso específico. Algunos casos de uso de ejemplo son:

- Aplique configuraciones de estado deseado de PowerShell a una máquina virtual mediante la extensión DSC para Windows. Para obtener más información, consulte la sección sobre la [extensión de configuración de estado deseado de Azure](extensions-dsc-overview.md).
- Configure la supervisión de máquina virtual mediante la extensión de máquina virtual de Microsoft Monitoring Agent. Para obtener más información, consulte [Conexión de máquinas virtuales de Azure a Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configure la supervisión de su infraestructura de Azure con la extensión de Datadog. Para obtener más información, consulte el [blog de Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configure una máquina virtual de Azure mediante Chef. Para obtener más información, consulte [Automatización de la implementación de la máquina virtual de Azure con Chef](chef-automation.md).

Además de las extensiones específicas de proceso, una extensión de script personalizado está disponible tanto para máquinas virtuales Windows como para máquinas virtuales Linux. La extensión de script personalizado para Windows permite que se ejecute cualquier script de PowerShell en una máquina virtual. Esto resulta útil al diseñar implementaciones de Azure que requieren una configuración más allá de lo que las herramientas de Azure nativas pueden proporcionar. Para obtener más información, consulte la sección sobre la [extensión de script personalizado de máquina virtual Windows](extensions-customscript.md).


## <a name="prerequisites"></a>Requisitos previos

Cada extensión de máquina virtual puede tener su propio conjunto de requisitos previos. Por ejemplo, la extensión de máquina virtual de Docker tiene un requisito previo de una distribución de Linux compatible. En la documentación específica de extensión se detallan los requisitos de extensiones individuales.

### <a name="azure-vm-agent"></a>Agente de máquina virtual de Azure
El agente de máquina virtual de Azure administra la interacción entre una máquina virtual de Azure y el controlador de tejido de Azure. El agente de máquina virtual es responsable de muchos aspectos funcionales de la implementación y administración de máquinas virtuales de Azure, incluida la ejecución de extensiones de máquina virtual. El agente de máquina virtual de Azure está preinstalado en imágenes de Azure Marketplace y se puede instalar en sistemas operativos compatibles.

Para obtener información sobre las instrucciones de instalación y los sistemas operativos compatibles, consulte [Agente de máquina virtual de Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Detección de extensiones de máquina virtual
Hay muchas extensiones de máquina virtual diferentes disponibles para su uso con máquinas virtuales de Azure. Para ver una lista completa, ejecute el siguiente comando con el módulo de PowerShell de Azure Resource Manager. Asegúrese de especificar la ubicación deseada al ejecutar este comando.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Ejecución de extensiones de máquina virtual

Las extensiones de máquina virtual de Azure pueden ejecutarse en máquinas virtuales existentes, lo que resulta útil si es preciso realizar cambios de configuración o recuperar la conectividad en una máquina virtual ya implementada. Las extensiones de máquina virtual también pueden agruparse con las implementaciones de plantilla de Azure Resource Manager. Al usar las extensiones con plantillas de Resource Manager, puede permitir la implementación y configuración de las máquinas virtuales de Azure sin necesidad de ninguna intervención después de la implementación.

Los siguientes métodos pueden usarse para ejecutar una extensión en una máquina virtual existente.

### <a name="powershell"></a>PowerShell

Existen varios comandos de PowerShell para ejecutar extensiones individuales. Para ver una lista, ejecute los siguientes comandos de PowerShell.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Esto proporciona una salida similar a la siguiente:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

En el ejemplo siguiente se usa la extensión de script personalizado para descargar un script desde un repositorio de GitHub en la máquina virtual de destino y, a continuación, ejecutar el script. Para obtener más información sobre la extensión de script personalizado, consulte [Introducción a la extensión de script personalizado](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

En este ejemplo, la extensión de acceso a la máquina virtual se usa para restablecer la contraseña administrativa de una máquina virtual Windows. Para obtener más información sobre la extensión de acceso a la máquina virtual, consulte la sección sobre cómo [restablecer el servicio Escritorio remoto en una máquina virtual Windows](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

El comando `Set-AzureRmVMExtension` se puede usar para iniciar cualquier extensión de máquina virtual. Para obtener más información, consulte la [referencia de Set-AzureRmVMExtension](https://msdn.microsoft.com/en-us/library/mt603745.aspx).


### <a name="azure-portal"></a>Portal de Azure

Una extensión de máquina virtual se puede aplicar a una máquina virtual existente a través de Azure Portal. Para ello, seleccione la máquina virtual que desea usar, elija **Extensiones** y haga clic en **Agregar**. Esto proporciona una lista de extensiones disponibles. Seleccione aquella que desee y siga los pasos del asistente.

En la siguiente imagen se muestra la instalación de la extensión de Microsoft Antimalware desde Azure Portal.

![Instalar la extensión de antimalware](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Plantillas de Azure Resource Manager

Las extensiones de máquina virtual se pueden agregar a una plantilla de Azure Resource Manager y ejecutar con la implementación de la plantilla. La implementación de extensiones con una plantilla es útil para crear implementaciones de Azure completamente configuradas. Por ejemplo, el siguiente JSON procede de una plantilla de Resource Manager que implementa un conjunto de máquinas virtuales de carga equilibrada y una base de datos Azure SQL Database y, a continuación, instala una aplicación .NET Core en cada máquina virtual. La extensión de máquina virtual se encarga de la instalación de software.

Para obtener más información, consulte la [plantilla completa de Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Para obtener más información, consulte [Creación de plantillas de Azure Resource Manager con extensiones de máquina virtual Windows](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Protección de datos de extensión de máquina virtual

Al ejecutar una extensión de máquina virtual, puede que sea necesario incluir información confidencial como credenciales, nombres de cuenta de almacenamiento y claves de acceso de cuenta de almacenamiento. Muchas extensiones de máquina virtual incluyen una configuración protegida que cifra datos y los descifra solo dentro de la máquina virtual de destino. Cada extensión tiene un esquema específico de configuración protegida que se detallará en la documentación específica de extensión.

En el ejemplo siguiente se muestra una instancia de la extensión de script personalizado para Windows. Tenga en cuenta que el comando que se ejecutará incluye un conjunto de credenciales. En este ejemplo, el comando que se ejecutará no se cifrará.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Proteja la cadena de ejecución moviendo la propiedad **command to execute** a la configuración **protegida**.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Solución de problemas de extensiones de máquina virtual

Cada extensión de máquina virtual puede tener unos pasos de solución de problemas específicos. Por ejemplo, al usar la extensión de script personalizado, se pueden encontrar detalles de ejecución de script localmente en la máquina virtual donde se ejecutó la extensión. Todos los pasos de solución de problemas específicos de extensión aparecen detallados en la documentación específica de extensión.

Los pasos de solución de problemas siguientes se aplican a todas las extensiones de máquina virtual.

### <a name="view-extension-status"></a>Consulta del estado de la extensión

Una vez que una extensión de máquina virtual se haya ejecutado en una máquina virtual, use el siguiente comando de PowerShell para volver al estado de la extensión. Reemplace los nombres de parámetros de ejemplo por los suyos propios. El parámetro `Name` recibe el nombre asignado a la extensión en el tiempo de ejecución.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

El resultado tendrá un aspecto similar al siguiente:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

El estado de ejecución de extensión también puede encontrarse en Azure Portal. Para ver el estado de una extensión, seleccione la máquina virtual, elija **Extensiones** y seleccione la extensión deseada.

### <a name="rerun-vm-extensions"></a>Repetición de ejecución de extensiones de máquina virtual

Puede haber casos en los que sea necesario volver a ejecutar una extensión de máquina virtual. Puede hacerlo quitando la extensión y, a continuación, volviendo a ejecutar la extensión con un método de ejecución de su elección. Para quitar una extensión, ejecute el siguiente comando con el módulo de Azure PowerShell. Reemplace los nombres de parámetros de ejemplo por los suyos propios.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

También se puede quitar una extensión mediante Azure Portal. Para ello:

1. Seleccione una máquina virtual.
2. Seleccione **Extensiones**.
3. Elija la extensión deseada.
4. Seleccione **Desinstalar**.

## <a name="common-vm-extensions-reference"></a>Referencia de extensiones de máquina virtual comunes
| Nombre de la extensión | Descripción | Más información |
| --- | --- | --- |
| Extensión de la secuencia de comandos personalizada para Windows |Ejecución de scripts en una máquina virtual de Azure |[Extensión de la secuencia de comandos personalizada para Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extensión de DSC para Windows |Extensión DSC (configuración de estado deseado) de PowerShell |[Extensión DSC para Windows](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extensión de Diagnósticos de Azure |Administración de Diagnósticos de Azure |[Extensión de Diagnósticos de Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensión de acceso a máquina virtual de Azure |Administración de usuarios y credenciales |[Extensión de acceso a máquina virtual para Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
