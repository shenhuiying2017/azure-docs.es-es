---
title: "Selección de imágenes de máquina virtual Windows en Azure | Microsoft Docs"
description: "Obtenga información sobre cómo usar Azure PowerShell para determinar el publicador, la oferta, la SKU y la versión para imágenes de VM de Marketplace."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 630f555b003b0efc45b372a7009dbf036aa8c737
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Búsqueda de imágenes de VM de Windows en Azure Marketplace con Azure PowerShell

En este tema se describe cómo usar Azure PowerShell para buscar imágenes de VM en Azure Marketplace. Utilice esta información para especificar una imagen de Marketplace cuando cree una máquina virtual Windows.

Asegúrese de que ha instalado y configurado el [módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps) más reciente.



## <a name="table-of-commonly-used-windows-images"></a>Tabla de imágenes de Windows más usadas
| PublisherName | Oferta | SKU |
|:--- |:--- |:--- |:--- |
| Microsoft Windows Server |Windows Server |2016-Datacenter |
| Microsoft Windows Server |Windows Server |2016-Datacenter-Server-Core |
| Microsoft Windows Server |Windows Server |2016-Datacenter-with-Containers |
| Microsoft Windows Server |Windows Server |2016-Nano-Server |
| Microsoft Windows Server |Windows Server |Centro de datos de 2012-R2 |
| Microsoft Windows Server |Windows Server |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>Búsqueda de imágenes específicas


Al crear una nueva máquina virtual con el Administrador de recursos de Azure, en algunos casos deberá especificar una imagen con la combinación de las propiedades de imagen siguientes:

* Publicador
* Oferta
* SKU

Por ejemplo, use estos valores con el cmdlet de PowerShell [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) o con una plantilla de grupo de recursos en la que debe especificar el tipo de MV que crear.

Si tiene que determinar estos valores, puede ejecutar los cmdlets [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) y [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) para ir a las imágenes. Determine estos valores:

1. Listado de los publicadores de imágenes.
2. Para un publicador determinado, enumeración de sus ofertas.
3. Para una oferta determinada, enumeración de sus SKU.

En primer lugar, muestre los publicadores con los siguientes comandos:

```azurepowershell-interactive
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Rellene el nombre del publicador elegido y ejecute los siguientes comandos:

```azurepowershell-interactive
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Rellene el nombre de la oferta elegida y ejecute los siguientes comandos:

```azurepowershell-interactive
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

En el resultado del comando `Get-AzureRMVMImageSku`, tiene toda la información que necesita para especificar la imagen para una nueva máquina virtual.

A continuación se muestra un ejemplo completo:

```azurepowershell-interactive
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Salida:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Para el publicador de "MicrosoftWindowsServer":

```azurepowershell-interactive
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Salida:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

Para la oferta "WindowsServer":

```azurepowershell-interactive
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Salida:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

En esta lista, copie el nombre de SKU elegido, y ya tiene toda la información para el cmdlet `Set-AzureRMVMSourceImage` de PowerShell o una plantilla de grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes
Ahora puede elegir con precisión la imagen que desea utilizar. Para crear una máquina virtual rápidamente con la información de imagen, que acaba de encontrar, vea [Creación de una máquina virtual Windows con PowerShell](quick-create-powershell.md).

