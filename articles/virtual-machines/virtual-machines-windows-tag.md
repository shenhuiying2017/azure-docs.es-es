---
title: "Cómo etiquetar un recurso de máquina virtual Windows en Azure | Microsoft Docs"
description: "Infórmese acerca del etiquetado de una máquina virtual Azure creada con el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
translationtype: Human Translation
ms.sourcegitcommit: 6b80fa78fea505cd22bae2925ce1affbc1e48ab9
ms.openlocfilehash: 57b88baeeb5c27cfdf142c77aef37b66bb6640d6


---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Etiquetado de una máquina virtual en Azure
En este artículo se describen diferentes maneras de etiquetar una máquina virtual Windows en Azure por medio del modelo de implementación de Resource Manager. Las etiquetas son pares clave-valor definidos por el usuario que se pueden colocar directamente en un recurso o un grupo de recursos. Actualmente, Azure admite un máximo de 15 etiquetas por recurso y grupo de recursos. Las etiquetas se pueden colocar en un recurso en el momento de su creación, o bien se pueden agregar a un recurso existente. Tenga en cuenta que las etiquetas solo son compatibles con los recursos creados mediante el modelo de implementación de Resource Manager. Si desea etiquetar una máquina virtual Linux, consulte [How to tag a Linux virtual machine in Azure](virtual-machines-linux-tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)(Etiquetado de una máquina virtual Linux en Azure).

[!INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Etiquetado con PowerShell
Para crear, agregar y eliminar etiquetas a través de PowerShell, primero es preciso configurar el [entorno de PowerShell con Azure Resource Manager][PowerShell environment with Azure Resource Manager]. Una vez completada la configuración, puede colocar etiquetas en los recursos de proceso, red y almacenamiento tanto al crear el recurso a través de PowerShell como después de haberlo creado. En este artículo se centrará en la visualización y edición de etiquetas en máquinas virtuales.

En primer lugar, navegue a una máquina virtual a través del cmdlet `Get-AzureRmVM` .

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Si la máquina virtual ya contiene etiquetas, verá todas ellas en el recurso:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si desea agregar etiquetas a través de PowerShell, puede usar el comando `Set-AzureRmResource` . Tenga en cuenta que si actualiza las etiquetas a través de PowerShell, se actualizan todas ellas en conjunto. Por tanto, si va a agregar una etiqueta a un recurso que ya tiene etiquetas, tendrá que incluir todas las etiquetas que desea colocar en el recurso. A continuación se muestra un ejemplo de cómo agregar etiquetas adicionales a un recurso mediante los cmdlets de PowerShell.

Este primer cmdlet establece todas las etiquetas colocadas en *MyTestVM* en la variable *$tags*, para lo que emplea las propiedades `Get-AzureRmResource` y `Tags`.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

El segundo comando muestra las etiquetas de la variable especificada.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment

El tercer comando agrega una etiqueta adicional a la variable *tags* . Observe el uso de **+=** para anexar el nuevo par de clave-valor a la lista de *tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

El cuarto comando establece todas las etiquetas definidas en la variable *tags* en el recurso especificado. En este caso, es MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

El quinto comando muestra todas las etiquetas en el recurso. Como puede ver, *Location* está definido como una etiqueta, con *MyLocation* como valor.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value        MyDepartment
        Name        Department
        Value        MyApp1
        Name        Application
        Value        MyName
        Name        Created By
        Value        Production
        Name        Environment
        Value        MyLocation
        Name        Location

Para más información sobre el etiquetado a través de PowerShell, consulte los [cmdlets de recursos de Azure][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre el etiquetado de los recursos de Azure, consulte [Información general de Azure Resource Manager][Azure Resource Manager Overview] y [Uso de etiquetas para organizar los recursos de Azure][Using Tags to organize your Azure Resources].
* Para ver cómo las etiquetas pueden ayudarle a administrar el uso de los recursos de Azure, consulte [Comprender la factura de Microsoft Azure][Understanding your Azure Bill] y [Obtención de información sobre el consumo de recursos de Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[PowerShell environment with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Azure Resource Cmdlets]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Azure Resource Manager Overview]: ../azure-resource-manager/resource-group-overview.md
[Using Tags to organize your Azure Resources]: ../azure-resource-manager/resource-group-using-tags.md
[Understanding your Azure Bill]: ../billing/billing-understand-your-bill.md
[Gain insights into your Microsoft Azure resource consumption]: ../billing/billing-usage-rate-card-overview.md



<!--HONumber=Jan17_HO4-->


