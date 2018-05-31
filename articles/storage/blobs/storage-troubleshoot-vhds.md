---
title: Solución de problemas de discos asociados a máquinas virtuales de Azure | Microsoft Docs
description: Azure Blob Storage está diseñado para almacenar grandes cantidades de datos de objetos no estructurados, como datos de texto o binarios. Las aplicaciones pueden acceder a objetos de Blob Storage desde PowerShell o la CLI de Azure, desde código mediante bibliotecas de cliente de Azure Storage o a través de REST.
services: storage
author: genlin
manager: cshepard
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 766062b085c359499046151f337921a51d948715
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362713"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Solución de problemas de discos asociados a máquinas virtuales de Azure 

Las máquinas virtuales (VM) de Azure usan discos duros virtuales (VHD) para el disco del sistema operativo y cualquier disco de datos asociado. Los discos duros virtuales se almacenan como blobs en páginas en una o varias cuentas de Azure Storage. Este artículo le dirige al contenido de solución de problemas comunes que pueden surgir con los discos duros virtuales. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Solución de problemas de errores de eliminación de almacenamiento en una máquina virtual

En algunos casos, puede encontrar un error al eliminar un recurso de almacenamiento cuando una máquina virtual de una implementación de Resource Manager contiene discos duros virtuales no asociados. Para obtener ayuda para resolver este problema, consulte uno de los siguientes artículos: 

  * En máquinas virtuales Linux: [Storage deletion errors in Resource Manager deployment](../../virtual-machines/linux/storage-resource-deletion-errors.md) (Errores de eliminación de almacenamiento en implementaciones de Resource Manager)  
  * En máquinas virtuales Windows: [Storage deletion errors in Resource Manager deployment](../../virtual-machines/windows/storage-resource-deletion-errors.md) (Errores de eliminación de almacenamiento en implementaciones de Resource Manager)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solución de problemas de reinicios inesperados de máquinas virtuales con discos duros virtuales asociados

Si se encuentra con que una máquina virtual con un gran número de discos duros virtuales asociados se reinicia inesperadamente, consulte uno de los siguientes artículos:

  * En máquinas virtuales Linux: [Unexpected reboots of VMs with attached VHDs](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md) (Reinicios inesperados de máquinas virtuales con discos duros virtuales asociados)
  * En máquinas virtuales Windows: [Unexpected reboots of VMs with attached VHDs](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md) (Reinicios inesperados de máquinas virtuales con discos duros virtuales asociados)
