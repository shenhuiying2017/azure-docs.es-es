---
title: "Navegación y selección de imágenes de máquina virtual Windows | Microsoft Docs"
description: "Obtenga información sobre cómo determinar el publicador, la oferta y la SKU de las imágenes al crear una máquina virtual Windows con el modelo de implementación del Administrador de recursos."
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 28bb214570fcca94c5ceb6071c4851b81ec00c8d
ms.lasthandoff: 04/27/2017


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell"></a>Navegación y selección de imágenes de máquina virtual Windows en Azure con PowerShell
En este tema se describe cómo buscar publicadores de imágenes de máquina virtual, ofertas, SKU y versiones de cada ubicación en la que podría realizar una implementación. Para dar un ejemplo, algunas de las imágenes de máquina virtual Windows más utilizadas son:

## <a name="table-of-commonly-used-windows-images"></a>Tabla de imágenes de Windows más usadas
| PublisherName | Oferta | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |WS2012R2 SQL2014 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |WS2012R2 SQL2014 |Enterprise-Optimized-for-OLTP |
| Microsoft Windows Server |Windows Server |Centro de datos de 2012-R2 |
| Microsoft Windows Server |Windows Server |Centro de datos de 2012 |
| Microsoft Windows Server |Windows Server |2008-R2-SP1 |
| Microsoft Windows Server |Windows Server |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

## <a name="find-azure-images-with-powershell"></a>Búsqueda de imágenes de Azure con PowerShell
> [!NOTE]
> Instale y configure la [versión más reciente de Azure PowerShell](/powershell/azure/overview). Si usa módulos de Azure PowerShell anteriores a la versión 1.0, seguirá usando estos comandos, pero primero debe `Switch-AzureMode AzureResourceManager`. 
> 
> 

Al crear una nueva máquina virtual con el Administrador de recursos de Azure, en algunos casos deberá especificar una imagen con la combinación de las propiedades de imagen siguientes:

* Publicador
* Oferta
* SKU

Por ejemplo, estos valores son necesarios para el cmdlet `Set-AzureRMVMSourceImage` de PowerShell o con un archivo de plantilla de grupo de recursos en el que debe especificar el tipo de máquina virtual que se creará.

Si necesita determinar estos valores, puede explorar las imágenes para determinar estos valores:

1. Listado de los publicadores de imágenes.
2. Para un publicador determinado, enumeración de sus ofertas.
3. Para una oferta determinada, enumeración de sus SKU.

En primer lugar, muestre los publicadores con los siguientes comandos:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Rellene el nombre del publicador elegido y ejecute los siguientes comandos:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Rellene el nombre de la oferta elegida y ejecute los siguientes comandos:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

En la pantalla del comando `Get-AzureRMVMImageSku` , tiene toda la información que necesita para especificar la imagen para una nueva máquina virtual.

A continuación se muestra un ejemplo completo:

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

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

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Para la oferta "WindowsServer":

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

En esta lista, copie el nombre de SKU elegido, y ya tiene toda la información para el cmdlet `Set-AzureRMVMSourceImage` de PowerShell o una plantilla de grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes
Ahora puede elegir con precisión la imagen que desea utilizar. Para crear una máquina virtual rápidamente con la información de imágenes que acaba de encontrar, o para usar una plantilla con esa información, consulte [Creación de una máquina virtual Windows con Resource Manager y PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

