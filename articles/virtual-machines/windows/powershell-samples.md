---
title: Ejemplos de PowerShell de máquina virtual de Azure | Microsoft Docs
description: Ejemplos de PowerShell de máquina virtual de Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 27f926a9a5d30d28e2721ffa52695292f794a501
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
ms.locfileid: "31522438"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Ejemplos de PowerShell de máquina virtual de Azure

En la tabla siguiente se incluyen vínculos a ejemplos de scripts de PowerShell que crean y administran máquinas virtuales Windows.

| | |
|---|---|
|**Creación de máquinas virtuales**||
| [Creación rápida de una máquina virtual](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un grupo de recursos, una máquina virtual y todos los recursos relacionados con el mínimo de mensajes.|
| [Creación una máquina virtual completamente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un grupo de recursos, una máquina virtual y todos los recursos relacionados.|
| [Creación de máquinas virtuales de alta disponibilidad](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea varias máquinas virtuales de alta disponibilidad y una configuración de equilibrio de carga.|
| [Creación de una máquina virtual y ejecución de un script de configuración](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual y usa la extensión de script personalizado de Azure para instalar IIS. |
| [Creación de una máquina virtual y ejecución de una configuración de DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual y usa la extensión de configuración de estado deseado (DSC) de Azure para instalar IIS. |
| [Carga de un disco duro virtual y creación de máquinas virtuales](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Carga un archivo de disco duro virtual local en Azure, crea una imagen desde el disco duro virtual y, después, crea una máquina virtual a partir de esa imagen. |
| [Creación de una máquina virtual desde un disco administrado](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual conectando un disco administrado como disco del SO. |
| [Creación de una VM a partir de una instantánea](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual desde una instantánea creando primero un disco administrado de instantánea y asociando después el nuevo disco administrado como disco del sistema operativo. |
|**Administración del almacenamiento**||
| [Creación de un disco administrado desde un disco duro virtual en la misma u otra suscripción](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un disco administrado a partir de un disco duro virtual especializado como un disco del sistema operativo, o a partir de un disco duro virtual de datos como un disco de datos en la misma u otra suscripción.  |
| [Creación de un disco administrado a partir de una instantánea](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un disco administrado a partir de una instantánea. |
| [Copiar el disco administrado en la misma suscripción o en otra](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copia el disco administrado en la misma u otra suscripción pero en la misma región que el disco administrado primario. 
| [Exportar una instantánea como un disco duro virtual a una cuenta de almacenamiento](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exporta una instantánea administrada como un disco duro virtual a una cuenta de almacenamiento en una región diferente. |
| [Creación de una instantánea a partir de un disco duro virtual](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una instantánea a partir de un disco duro virtual para crear varios discos administrados idénticos a partir de la instantánea en poco tiempo.  |
| [Copia de una instantánea en la misma u otra suscripción](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Copia la instantánea en la misma u otra suscripción, pero en la misma región que la instantánea primaria. |
|**Protección de las máquinas virtuales**||
| [Cifrado de una máquina virtual y discos de datos](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Crea una instancia de Azure Key Vault, una clave de cifrado y una entidad de servicio, y luego cifra una máquina virtual. |
|**Supervisión de máquinas virtuales**||
| [Supervisión de una máquina virtual con Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual, instala el agente de Operations Management Suite e inscribe la máquina virtual en un área de trabajo de OMS.  |
| | |

