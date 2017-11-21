---
title: "Características de PowerShell en Azure Cloud Shell (versión preliminar) | Microsoft Docs"
description: "Introducción a las características de PowerShell en Azure Cloud Shell"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: damaerte
ms.openlocfilehash: ffe5298678de6cc16af0ea29c7d887330f77589d
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell"></a>Características y herramientas para PowerShell en Azure Cloud Shell

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> También hay disponible un artículo sobre las características y herramientas para [Bash](features.md).

PowerShell en Cloud Shell se ejecuta en `Windows Server 2016`.

## <a name="features"></a>Características

### <a name="secure-automatic-authentication"></a>Protección de la autenticación automática

PowerShell en Cloud Shell autentica segura y automáticamente el acceso a la cuenta para Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Persistencia de archivos entre sesiones

Para conservar archivos entre sesiones, la primera vez que se inicia Cloud Shell se explica cómo conectar un recurso compartido de archivos de Azure.
Una vez finalizado, Cloud Shell conectará automáticamente su almacenamiento (montado como `$home\clouddrive`) para todas las sesiones futuras.
Dado que cada solicitud para Cloud Shell asigna una máquina temporal, los archivos fuera de `$home\clouddrive` y el estado de la máquina no persisten entre sesiones.

[Obtenga más información sobre cómo conectar recursos compartidos de archivos de Azure a Cloud Shell](persisting-shell-storage-powershell.md).

### <a name="azure-drive-azure"></a>Unidad de Azure (Azure):

PowerShell en Cloud Shell se inicia en la unidad de Azure (`Azure:`).
La unidad de Azure permite una fácil detección y navegación de los recursos de Azure, como proceso, red, almacenamiento, etc., de manera similar a la navegación del sistema de archivos.
Puede seguir usando los [cmdlets de Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure) que ya conoce para administrar estos recursos.
Cualquier cambio que se haga en los recursos de Azure, ya sea directamente en Azure Portal o mediante los cmdlets de Azure PowerShell, se reflejan de manera inmediata en la unidad de Azure.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Reconocimiento contextual

- **Ámbito del grupo de recursos**: cuando en el contexto de una ruta de acceso al grupo de recursos en la unidad de Azure (`Azure:`), el nombre del grupo de recursos se transfiere automáticamente a los cmdlets de Azure PowerShell.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: este cmdlet devuelve la lista de los comandos aplicables en el contexto de la ubicación en la unidad de Azure (`Azure:`). Por ejemplo, solo muestra los comandos relacionados con almacenamiento si el usuario está en `Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Enriquecimiento de la edición de scripts de PowerShell

Cuando usa VIM para editar los archivos de PowerShell (`.ps1,.psm1,.psd1`), recibe automáticamente la sintaxis resaltada y compatibilidad con IntelliSense.
La compatibilidad de IntelliSense se implementa con un complemento de vim que interactúa con una instancia local de [servicios del Editor de PowerShell](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Use `TAB` para completar (IntelliSense) los nombres de comandos, nombres de parámetros y valores de parámetros (cuando corresponda).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Modelo extensible

Con [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), puede instalar y actualizar fácilmente módulos y scripts personalizados desde la [Galería de PowerShell](https://www.powershellgallery.com).
Después de la instalación, los módulos persisten automáticamente entre distintas sesiones de Cloud Shell.

> [!TIP]
> Los módulos que los usuarios instalan se guardan en la carpeta `$Home\CloudDrive\.pscloudshell\WindowsPowerShell`. Se crea un vínculo simbólico para esta carpeta creada en la carpeta de documentos del usuario (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Administración de máquinas virtuales invitadas

Puede administrar remotamente las máquinas virtuales de Azure con dos comandos integrados `Enter-AzureRmVM` y `Invoke-AzureRmVMCommand`.
Estos comandos se basan en la supervisión de PowerShell y requieren la conectividad de PowerShell en las máquinas virtuales.

## <a name="tools"></a>Herramientas

|**Categoría**    |**Name**                                 |
|----------------|-----------------------------------------|
|Herramientas de Azure     |[Azure PowerShell (4.4.1)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.1)<br> [CLI de Azure (2.0.20)](https://docs.microsoft.com/en-us/cli/azure/overview)|
|Editores de texto    |vim<br> nano                             |
|Administrador de paquetes |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|Control de código fuente  |git                                      |
|Bases de datos       |[Módulo SqlServer](https://www.powershellgallery.com/packages/SqlServer)<br> [Utilidad sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Herramientas de prueba      |Pester                                   |

## <a name="language-support"></a>Compatibilidad con idiomas

|**Lenguaje**|**Versión**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 y [6.0 (versión beta)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Pasos siguientes

[Guía de inicio rápido de PowerShell en Cloud Shell](quickstart-powershell.md)

[Información acerca de Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
