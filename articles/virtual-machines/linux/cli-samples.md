---
title: Ejemplos de la CLI de Azure | Microsoft Docs
description: Ejemplos de la CLI de Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: a104e231ed079053ed5671703fe39b853dc094a7
ms.contentlocale: es-es
ms.lasthandoff: 05/16/2017


---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Ejemplos de la CLI de Azure para máquinas virtuales Linux

En la tabla siguiente se incluyen vínculos a scripts de Bash creados con la CLI de Azure.

| | |
|---|---|
|**Creación de máquinas virtuales**||
| [Creación de una máquina virtual](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una máquina virtual Linux con una configuración mínima. |
| [Creación una máquina virtual completamente configurada](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Crea un grupo de recursos, una máquina virtual y todos los recursos relacionados.|
| [Creación de máquinas virtuales de alta disponibilidad](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Crea varias máquinas virtuales de alta disponibilidad y una configuración de equilibrio de carga. |
| [Creación de una máquina virtual con Docker habilitado](./../scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una máquina virtual, la configura como un host Docker y ejecuta un contenedor NGINX. |
| [Creación de una máquina virtual y ejecución de un script de configuración](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una máquina virtual y usa la extensión de script personalizado de Azure para instalar NGINX. |
| [Creación de una máquina virtual con WordPress instalado](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una máquina virtual y usa la extensión de script personalizado de Azure para instalar WordPress. |
| [Creación de una máquina virtual desde un disco administrado](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual conectando un disco administrado como disco del SO. |
| [Creación de una VM a partir de una instantánea](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Crea una máquina virtual desde una instantánea creando primero un disco administrado de instantánea y, a continuación, asociar el nuevo disco administrado como disco del SO. |
|**Máquinas virtuales de red**||
| [Protección del tráfico de red entre máquinas virtuales](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Crea dos máquinas virtuales, todos los recursos relacionados y grupos de seguridad de red internos y externos (NSG). |
|**Supervisión de máquinas virtuales**||
| [Supervisión de una máquina virtual con Operations Management Suite](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Crea una máquina virtual, instala el agente de Operations Management Suite e inscribe la máquina virtual en un área de trabajo de OMS.  |
|**Solución de problemas de máquinas virtuales**||
| [Solución de problemas de un disco de sistema operativo de máquina virtual](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | Permite montar el disco del sistema operativo a partir de una máquina virtual como un disco de datos en una segunda máquina virtual. |
| | |

