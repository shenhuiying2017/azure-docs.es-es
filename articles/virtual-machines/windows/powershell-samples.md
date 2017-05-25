---
title: "Ejemplos de PowerShell de máquina virtual de Azure | Microsoft Docs"
description: "Ejemplos de PowerShell de máquina virtual de Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/16/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: c57bf0efc1eb97d34d76483b8889d884ccfd0988
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="azure-virtual-machine-powershell-samples"></a>Ejemplos de PowerShell de máquina virtual de Azure

En la tabla siguiente se incluyen vínculos a ejemplos de scripts de PowerShell que crean y administran máquinas virtuales Windows.

| | |
|---|---|
|**Creación de máquinas virtuales**||
| [Creación una máquina virtual completamente configurada](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea un grupo de recursos, una máquina virtual y todos los recursos relacionados.|
| [Creación de una máquina virtual y ejecución de un script de configuración](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual y usa la extensión de script personalizado de Azure para instalar IIS. |
| [Creación de una máquina virtual y ejecución de una configuración de DSC](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual y usa la extensión de configuración de estado deseado (DSC) de Azure para instalar IIS. |
| [Creación de una máquina virtual desde un disco del SO administrado](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual conectando un disco administrado como disco del SO. |
| [Creación de una VM a partir de una instantánea](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual desde una instantánea creando primero un disco administrado de instantánea y asociando después el nuevo disco administrado como disco del sistema operativo. |
|**Supervisión de máquinas virtuales**||
| [Supervisión de una máquina virtual con Operations Management Suite](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual, instala el agente de Operations Management Suite e inscribe la máquina virtual en un área de trabajo de OMS.  |
| | |

