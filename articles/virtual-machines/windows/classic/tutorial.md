---
title: "Creación de una máquina virtual en Azure Portal | Microsoft Docs"
description: "Cree una máquina virtual de Windows en el Portal de Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 0981872ff819fdf49a9cc97afce3c212013ce76b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Creación de una máquina virtual que ejecuta Windows en el Portal de Azure
> [!div class="op_single_selector"]
> * [Portal de Azure](tutorial.md)
> * [PowerShell: implementación clásica](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Aprenda a [realizar estos pasos con el modelo de implementación de Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) desde **Azure Portal**.

En este tutorial se muestra cómo crear una máquina virtual de Azure con Windows en Azure Portal. Vamos a usar una imagen de Windows Server como ejemplo, pero es solo una de las muchas imágenes que Azure ofrece. Tenga en cuenta que las opciones de imagen dependen de su suscripción. Por ejemplo, las imágenes de escritorio de Windows pueden estar disponibles para los suscriptores de MSDN.

En esta sección se muestra cómo utilizar el **panel** en Azure Portal para seleccionar y luego crear la máquina virtual.

También puede crear máquinas virtuales con sus [propias imágenes](createupload-vhd.md). Para obtener más información sobre este y otros métodos, consulte [Diferentes formas de crear una máquina virtual de Windows](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<!-- 02/27/2017 Video removed as it was based on the classic portal. -->

## <a id="createvirtualmachine"> </a>Creación de la máquina virtual
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [crear una máquina virtual mediante el modelo de implementación de Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) en Azure Portal.
* Iniciar sesión en la nueva máquina virtual. Para obtener instrucciones, consulte el artículo sobre el [inicio de sesión en una máquina virtual que ejecute Windows Server](connect-logon.md).
* Acople un disco para almacenar los datos. Puede acoplar tanto discos vacíos como discos que contienen datos. Para obtener instrucciones, consulte [Conexión de un disco de datos a una máquina virtual Windows creada con el modelo de implementación clásica](attach-disk.md).
