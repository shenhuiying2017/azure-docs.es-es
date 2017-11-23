---
title: "Introducción al uso de PowerShell con las zonas privadas de Azure DNS | Microsoft Docs"
description: "Aprenda a crear una zona y un registro DNS privados en Azure DNS. Esta es una guía paso a paso para crear y administrar la primera zona y el primer registro DNS privados con PowerShell."
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d71e2391b6415b2403447479dea4fd0a3b818ed0
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2017
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Introducción al uso de PowerShell con las zonas privadas de Azure DNS

Este artículo le guiará por los pasos necesarios para crear una zona y un registro DNS privados con Azure PowerShell.

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Para publicar una zona DNS privada en la red virtual, especifique la lista de redes virtuales que pueden resolver registros en ella.  Se denominan "redes de resolución".  También puede especificar un conjunto de redes virtuales para el que Azure DNS mantendrá los registros de nombre de host siempre que se cree o se destruya una máquina virtual o le cambie el IP.  Se denominan "redes de registro".

Como esta característica actualmente es una versión preliminar administrada, se proporcionará una versión preliminar del módulo de PowerShell.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la zona DNS, se crea un grupo de recursos que la contenga. A continuación, se muestra el comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

Una zona DNS se crea mediante el cmdlet `New-AzureRmDnsZone` . En el ejemplo siguiente se crea una zona DNS denominada *contoso.local* en el grupo de recursos denominado *MyResourceGroup* y pone la zona DNS a disposición de la red virtual denominada *MyAzureVnet*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Si necesita que Azure cree automáticamente los registros de nombre de host en la zona, use el parámetro *RegistrationVirtualNetworkId* en lugar de *ResolutionVirtualNetworkId*.  Las redes virtuales de registro se habilitan automáticamente para la resolución.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```


## <a name="create-a-dns-record"></a>Creación de un registro de DNS

Los conjuntos de registros se crean mediante el cmdlet `New-AzureRmDnsRecordSet`. En el ejemplo siguiente se crea un registro con el nombre relativo "db" en la zona DNS "contoso.local" del grupo de recursos "MyResourceGroup". El nombre completo del conjunto de registros es "db.contoso.local". El tipo de registro es "A", con la dirección IP 10.0.0.4 y el valor de TTL es de 3600 segundos.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Para ver otros tipos de registros y conjuntos de registros con más de un registro, y para modificar los registros existentes, consulte [Administración de registros de DNS en Azure DNS mediante Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Visualización de los registros

Para enumerar los registros DNS de su zona, use lo siguiente:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, realice el paso siguiente:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las zonas DNS privadas, consulte [Using Azure DNS for private domains](private-dns-overview.md) (Uso de Azure DNS para dominios privados).

Para aprender a administrar registros DNS en Azure DNS, consulte [Administración de registros de DNS en Azure DNS mediante PowerShell](dns-operations-recordsets.md).

