---
title: Diferencias y consideraciones para las máquinas virtuales en Azure Stack | Microsoft Docs
description: Aprenda sobre las diferencias y consideraciones al trabajar con máquinas virtuales en Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.openlocfilehash: 83a0b8ff040425ac30cff96936f2f639fd1b5643
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076169"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Consideraciones sobre el uso de máquinas virtuales en Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Las máquinas virtuales de Azure Stack proporcionan recursos informáticos escalables y a petición. Para poder implementar máquinas virtuales (VM), debe entender las diferencias entre las características de máquina virtual disponibles en la Azure Stack y en Microsoft Azure. Este artículo describe estas diferencias e identifica las principales consideraciones para planificar implementaciones de máquina virtual. Para obtener información acerca de las diferencias de alto nivel entre Azure y Azure Stack, consulte el artículo [Key considerations](azure-stack-considerations.md) (Consideraciones clave).

## <a name="cheat-sheet-virtual-machine-differences"></a>Hoja de referencia rápida: Diferencias entre máquinas virtuales

| Característica | Azure (global) | Azure Stack |
| --- | --- | --- |
| Imágenes de máquinas virtuales | Azure Marketplace contiene imágenes que puede usar para crear una máquina virtual. Consulte la página de [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) para ver la lista de imágenes que están disponibles en Azure Marketplace. | De forma predeterminada, no hay ninguna imagen disponible en el Marketplace de Azure Stack. El administrador de la nube de Azure Stack debe publicar o descargar imágenes en el Marketplace de Azure Stack antes de que los usuarios puedan usarlas. |
| Tamaños de máquina virtual | Azure admite una amplia variedad de tamaños para máquinas virtuales. Para aprender más acerca de las opciones y los tamaños disponibles, consulte los temas [Tamaños de máquinas virtuales Windows](../../virtual-machines/virtual-machines-windows-sizes.md) y [Tamaños de máquina virtual Linux](../../virtual-machines/linux/sizes.md). | Azure Stack admite un subconjunto de tamaños de máquinas virtuales que están disponibles en Azure. Para ver la lista de tamaños admitidos, consulte la sección [Tamaños de máquina virtual](#virtual-machine-sizes) de este artículo. |
| Cuotas de máquinas virtuales | [Límites de cuota](../../azure-subscription-service-limits.md#service-specific-limits) establecidos por Microsoft | El administrador de la nube de Azure Stack debe asignar cuotas antes de ofrecer máquinas virtuales a los usuarios. |
| Extensiones de máquina virtual |Azure admite una amplia variedad de extensiones para máquinas virtuales. Para aprender acerca de las extensiones disponibles, consulte el artículo [Extensiones y características de las máquinas virtuales](../../virtual-machines/windows/extensions-features.md).| Azure Stack admite un subconjunto de extensiones que están disponibles en Azure y cada una de las extensiones tienen versiones específicas. El administrador de la nube de Azure Stack puede elegir qué extensiones están disponibles para sus usuarios. Para ver la lista de extensiones admitidas, consulte la sección [Extensiones de máquina virtual](#virtual-machine-extensions) de este artículo. |
| Red de máquinas virtuales | Las direcciones IP públicas asignadas a la máquina virtual del inquilino son accesibles a través de Internet.<br><br><br>Azure Virtual Machines tiene un nombre de DNS fijo | Las direcciones IP públicas asignadas a una máquina virtual del inquilino son accesibles solo desde el entorno del Kit de desarrollo de Azure Stack. Un usuario debe tener acceso al Kit de desarrollo de Azure Stack a través de un [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o una [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) para conectarse a una máquina virtual que se crea en Azure Stack.<br><br>Las máquinas virtuales creadas dentro de una instancia específica de Azure Stack tienen un nombre DNS basado en el valor que se configura mediante el administrador de la nube. |
| Almacenamiento de máquina virtual | Admite [discos administrados](../../virtual-machines/windows/managed-disks-overview.md). | Los discos administrados no se admiten todavía en Azure Stack. |
| Versiones de API | Azure tiene siempre las últimas versiones de API para todas las características de la máquina virtual. | Azure Stack es compatible con servicios específicos de Azure y versiones de API específicas para estos servicios. Para ver la lista de versiones de API compatibles, consulte la sección [versiones de API](#api-versions) de este artículo. |
|Conjuntos de disponibilidad de máquinas virtuales|Varios dominios de error (2 o 3 por región)<br>Varios dominios de actualización<br>Compatible con el disco administrado|Varios dominios de error (2 o 3 por región)<br>Varios dominios de actualización (hasta 20)<br>No compatible con el disco administrado|
|Conjuntos de escalado de máquinas virtuales|Compatible con escalado automático|No compatible con escalado automático.<br>Agregar más instancias a un conjunto de escalado con el portal, las plantillas de Resource Manager o PowerShell.

## <a name="virtual-machine-sizes"></a>Tamaños de máquina virtual

Azure Stack impone límites de recursos para evitar el consumo excesivo de recursos (nivel de servicio y local del servidor). Estos límites mejoran la experiencia del inquilino, ya que reducen el impacto del consumo de recursos por parte de otros inquilinos.

- Para la salida de redes de la máquina virtual, hay extremos de ancho de banda. Los extremos de Azure Stack son los mismos que en Azure.
- En el caso de los recursos de almacenamiento, Azure Stack implementa límites de IOPS de almacenamiento para evitar el consumo excesivo básico de recursos por parte de los inquilinos para el acceso de almacenamiento.
- En el caso de las VM con varios discos de datos adjuntos, el rendimiento máximo de cada disco de datos es de 500 IOPS para HHD y de 2300 IOPS para SSD.

En la tabla siguiente se enumeran las máquinas virtuales que se admiten en Azure Stack junto con su configuración:

| Escriba           | Tamaño          | Intervalo de tamaños admitidos |
| ---------------| ------------- | ------------------------ |
|Uso general |A básico        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Uso general |Estándar A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Uso general |Serie D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Uso general |Serie Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Uso general |Serie DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Uso general |DSv2-series    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Memoria optimizada|Serie D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Memoria optimizada|Serie DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Memoria optimizada|Serie Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Memoria optimizada|Serie DSv2 -  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Los tamaños de máquina virtual y sus cantidades de recursos asociados son coherentes entre Azure y Azure Stack. Esto incluye la cantidad de memoria, el número de núcleos, y la cantidad y el tamaño de los discos de datos que se pueden crear. Sin embargo, el rendimiento del mismo tamaño de VM depende de las características subyacentes de un entorno de Azure Stack concreto.

## <a name="virtual-machine-extensions"></a>Extensiones de máquina virtual

 Azure Stack incluye un pequeño conjunto de extensiones. Las actualizaciones y las extensiones adicionales están disponibles a través de la redifusión de Marketplace.

Use el siguiente script de PowerShell para obtener la lista de extensiones de máquinas virtuales que están disponibles en su entorno de Azure Stack:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Versiones de API

Las características de la máquina virtual en Azure Stack admiten las siguientes versiones de API:

![Tipos de recursos de máquinas virtuales](media/azure-stack-vm-considerations/vm-resoource-types.png)

Puede usar el siguiente script de PowerShell para obtener las versiones de API para características de máquinas virtuales que están disponibles en su entorno de Azure Stack:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

La lista de versiones de API y tipos de recursos admitidos puede variar si el operador de nube actualiza su entorno de Azure Stack a una versión más reciente.

## <a name="windows-activation"></a>Activación de Windows

Los productos de Windows deben utilizarse de acuerdo con los derechos de uso del producto y los términos de licencia de Microsoft. Azure Stack usa la [activación automática de VM](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) para activar máquinas virtuales (VM) de Windows Server.

- El host de Azure Stack activa Windows con claves de AVMA para Windows Server 2016. Todas las VM que ejecutan Windows Server 2012 o versiones posteriores se activan automáticamente.
- Las VM que ejecutan Windows Server 2008 R2 no se activan automáticamente y se deben activar mediante [la activación de MAK](https://technet.microsoft.com/library/ff793438.aspx).

Microsoft Azure utiliza la activación de KMS para activar las VM de Windows. Si traslada una VM de Azure Stack a Azure y encuentra problemas de activación, consulte [Solución de problemas de activación de máquinas virtuales Windows de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Puede encontrar información adicional en la entrada del blog del equipo de soporte técnico de Azure [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) (Solucionar problemas de errores de activación de Windows en VM de Azure).

## <a name="next-steps"></a>Pasos siguientes

[Cree una máquina virtual Windows con PowerShell en Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)