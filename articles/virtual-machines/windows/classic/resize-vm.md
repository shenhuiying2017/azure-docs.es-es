---
title: "Cambio de tamaño de una VM de Windows en el modelo de implementación clásica - Azure | Microsoft Docs"
description: "Cambio de tamaño de una máquina virtual Windows clásica creada con el modelo de implementación clásica utilizando Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: Drewm3
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 4277bc8394c7ba140291e9dc776162e87deab96b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Cambio de tamaño de una máquina virtual Windows creada en el modelo de implementación clásica
En este artículo se muestra cómo cambiar de tamaño una máquina virtual Windows creada con el modelo de implementación clásica utilizando Azure PowerShell.

Al considerar la posibilidad de cambiar el tamaño de una máquina virtual, hay dos conceptos que controlan el intervalo de tamaños disponibles para modificar el tamaño de la máquina virtual. El primer concepto es la región en la que se implementa la máquina virtual. La lista de tamaños de máquina virtual disponibles en la región está en la pestaña Servicios de la página web de regiones de Azure. El segundo concepto es el hardware físico que hospeda la máquina virtual. Los servidores físicos que hospedan máquinas virtuales se agrupan en clústeres de hardware físico común. El método de cambiar un tamaño máquina virtual será diferente en función de si el nuevo tamaño de máquina virtual deseado es compatible con el clúster de hardware que hospeda la máquina virtual.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. También puede [cambiar el tamaño de una máquina virtual con el modelo de implementación de Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="add-your-account"></a>Adición de la cuenta
Debe configurar Azure PowerShell para usar los recursos de Azure de clásicos. Siga estos pasos para configurar Azure PowerShell con el fin de administrar recursos clásicos.

1. En el símbolo del sistema de PowerShell, escriba `Add-AzureAccount` y haga clic en **Entrar**. 
2. Escriba la dirección de correo electrónico asociada a su suscripción de Azure y haga clic en **Continuar**. 
3. Escriba la contraseña de su cuenta. 
4. Haga clic en **Iniciar sesión**. 

## <a name="resize-in-the-same-hardware-cluster"></a>Cambio de tamaño en el mismo clúster de hardware
Para cambiar el tamaño de una máquina virtual a uno disponible en el clúster de hardware que hospeda la máquina virtual, realice los pasos siguientes.

1. Ejecute el siguiente comando de PowerShell para enumerar los tamaños de máquina virtual disponibles en el clúster de hardware que hospeda el servicio en la nube que contiene la máquina virtual.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. Ejecute los siguientes comandos para cambiar el tamaño de la máquina virtual.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Cambio de tamaño en un nuevo clúster de hardware
Para cambiar el tamaño de una máquina virtual a uno que no esté disponible en el clúster de hardware que hospeda la máquina virtual, deben volver a crearse el servicio en la nube y todas las máquinas virtuales de dicho servicio. Los servicios en la nube se hospedan en un clúster de hardware único, de modo que todas las máquinas virtuales de este deben tener un tamaño que se admita en un clúster de hardware. Los pasos siguientes describen cómo cambiar el tamaño de una máquina virtual eliminando el servicio en la nube y volviéndolo a crear de nuevo.

1. Ejecute el siguiente comando de PowerShell para enumerar los tamaños de máquina virtual disponibles en la región. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. Tome nota de todos los valores de configuración de cada máquina virtual del servicio en la nube que contiene la máquina virtual cuyo tamaño se cambiará. 
3. Elimine todas las máquinas virtuales del servicio en la nube seleccionando la opción para conservar los discos de cada máquina virtual.
4. Vuelva a crear la máquina virtual para cambiar su tamaño usando el tamaño de máquina virtual que quiera.
5. Vuelva a crear el resto de las máquinas virtuales que estaban en el servicio en la nube con un tamaño de máquina virtual disponible en el clúster de hardware que ahora hospeda el servicio en la nube.

[Aquí](https://github.com/Azure/azure-vm-scripts) puede encontrar un script de ejemplo para eliminar y volver a crear un servicio en la nube con un nuevo tamaño de máquina virtual. 

## <a name="next-steps"></a>Pasos siguientes
* [Cambie el tamaño de una máquina virtual con el modelo de implementación de Resource Manager](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

