---
title: Características y extensiones de las máquinas virtuales | Microsoft Docs
description: Obtenga información acerca de qué extensiones están disponibles para máquinas virtuales de Azure, agrupadas por lo que proporcionan o mejoran.
services: virtual-machines-windows
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/30/2016
ms.author: nepeters

---
# <a name="about-virtual-machine-extensions-and-features"></a>Acerca de las características y extensiones de las máquinas virtuales
## <a name="azure-vm-extensions"></a>Extensiones de máquina virtual de Azure
Las extensiones de máquina virtual de Azure son pequeñas aplicaciones que realizan tareas de automatización y configuración posterior a la implementación en máquinas virtuales de Azure. Por ejemplo, si una máquina virtual necesita que se instale software, protección antivirus o configuración de Docker, se puede usar una extensión de máquina virtual para completar estas tareas. Las extensiones de máquina virtual de Azure se pueden ejecutar con la CLI de Azure, PowerShell, plantillas de Resource Manager y Azure Portal. Las extensiones pueden empaquetar con una nueva implementación de máquina virtual o se pueden ejecutar en cualquier sistema existente.

En este documento se describen los requisitos previos de la extensión de máquina virtual de Azure y se indica cómo detectar las extensiones de máquina virtual disponibles. 

## <a name="azure-vm-agent"></a>Agente de máquina virtual de Azure
El agente de máquina virtual de Azure administra la interacción entre una máquina virtual de Azure y el controlador de tejido de Azure. El agente de máquina virtual es responsable de muchos aspectos funcionales de la implementación y administración de máquinas virtuales de Azure, incluida la ejecución de extensiones de máquina virtual. El agente de máquina virtual de Azure está preinstalado en imágenes de la galería de Azure y se puede instalar en sistemas operativos compatibles. 

Para más información sobre las instrucciones de instalación y los sistemas operativos compatibles, consulte [Agente de máquina virtual de Azure](virtual-machines-windows-classic-agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Detección de extensiones de máquina virtual
Hay muchas extensiones de máquina virtual diferentes disponibles para su uso con máquinas virtuales de Azure. Para ver una lista completa, ejecute el comando siguiente con la CLI de Azure, y reemplace la ubicación por la ubicación elegida.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Extensiones de máquina virtual comunes
| Nombre de la extensión | Description | Más información |
| --- | --- | --- |
| Extensión de la secuencia de comandos personalizada para Windows |Ejecución de scripts en una máquina virtual de Azure |[Extensión de la secuencia de comandos personalizada para Windows](virtual-machines-windows-extensions-customscript.md) |
| Extensión de DSC para Windows |Extensión DSC (configuración de estado deseado) de PowerShell |[Extensión de máquina virtual de Docker](virtual-machines-windows-extensions-dsc-overview.md) |
| Extensión de Diagnósticos de Azure |Administración de Diagnósticos de Azure |[Extensión de Diagnósticos de Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |

<!--HONumber=Oct16_HO2-->


