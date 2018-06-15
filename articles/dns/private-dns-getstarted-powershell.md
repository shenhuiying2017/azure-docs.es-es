---
title: Introducción al uso de PowerShell con las zonas privadas de Azure DNS | Microsoft Docs
description: Aprenda a crear una zona y un registro DNS privados en Azure DNS. Esta es una guía paso a paso para crear y administrar la primera zona y el primer registro DNS privados con PowerShell.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
ms.locfileid: "30177661"
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Introducción al uso de PowerShell con las zonas privadas de Azure DNS

Este artículo le guiará por los pasos necesarios para crear una zona y un registro DNS privados con Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Para publicar una zona DNS privada en la red virtual, especifique la lista de redes virtuales que pueden resolver registros en ella.  Se denominan "redes virtuales de resolución".  También puede especificar una red virtual para la que Azure DNS mantiene los registros de nombre de host siempre que se cree o se destruya una máquina virtual o se cambie su dirección IP.  Se denominan "redes virtuales de registro".

# <a name="get-the-preview-powershell-modules"></a>Obtención de la versión preliminar de los módulos de PowerShell
Estas instrucciones asumen que ya ha instalado Azure PowerShell y que ha iniciado sesión, incluido asegurarse de que tiene los módulos necesarios para la característica de zona privada. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la zona DNS, se crea un grupo de recursos que la contenga. En el siguiente ejemplo se muestra el comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Creación de una zona DNS privada

Se crea una zona DNS mediante el cmdlet `New-AzureRmDnsZone` junto con el valor "Private" para el parámetro ZoneType. En el ejemplo siguiente se crea una zona DNS denominada *contoso.local* en el grupo de recursos denominado *MyResourceGroup* y pone la zona DNS a disposición de la red virtual denominada *MyAzureVnet*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

Tenga en cuenta que si se omite el parámetro ZoneType, la zona se creará como zona pública, por lo que es necesario si necesita crear una zona privada. 

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

# <a name="list-dns-private-zones"></a>Listado de zonas privadas de DNS

Si se omite el nombre de la zona de `Get-AzureRmDnsZone`, puede enumerar todas las zonas en un grupo de recursos. Esta operación devuelve una matriz de objetos de la zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Si se omite tanto el nombre de zona como el nombre del grupo de recursos de `Get-AzureRmDnsZone`, puede enumerar todas las zonas de la suscripción de Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Actualización de una zona privada de DNS

Los cambios en los recursos de una zona DNS se pueden realizar mediante `Set-AzureRmDnsZone`. Este cmdlet no actualiza ninguno de los conjuntos de registros de DNS de la zona (consulte [Administración de registros DNS](dns-operations-recordsets.md)). Solo se utiliza para actualizar las propiedades de los recursos de la zona. Las propiedades de zona que se pueden escribir están limitadas actualmente a [las etiquetas de Azure Resource Manager para el recurso de zona](dns-zones-records.md#tags) así como a los parámetros 'RegistrationVirtualNetworkId' y 'ResolutionVirtualNetworkId' para zonas privadas.

El siguiente ejemplo reemplaza la red virtual de registro vinculada a una zona por una nueva red virtual MyNewAzureVnet.

Tenga en cuenta que no debe especificar el parámetro ZoneType para la actualización, a diferencia de la creación. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

El siguiente ejemplo reemplaza la red virtual de resolución vinculada a una zona por una nueva red virtual MyNewAzureVnet.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Eliminación de una zona privada de DNS

Se pueden eliminar las zonas privadas de DNS mediante el cmdlet `Remove-AzureRmDnsZone` al igual que las zonas públicas.

> [!NOTE]
> Si se elimina una zona DNS, también se eliminan todos los registros DNS dentro de la zona. Esta operación no se puede deshacer. Si la zona DNS está en uso, los servicios con la zona provocarán un error cuando se elimina la zona.
>
>Para protegerse contra la eliminación accidental de zona, consulte [Proteger registros y zonas DNS](dns-protect-zones-recordsets.md).

Utilice una de las dos opciones siguientes para eliminar una zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especificar la zona con la utilización del nombre de zona y el nombre del grupo de recursos

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especificar la zona utilizando un objeto $zone

Puede especificar la zona que desee eliminar mediante un objeto `$zone` devuelto por `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

También se puede canalizar el objeto de la zona en lugar de pasarlo como parámetro:

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Mensajes de confirmación

Los cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` y `Remove-AzureRmDnsZone` todos admiten mensajes de confirmación.

Ambos `New-AzureRmDnsZone` y `Set-AzureRmDnsZone` piden confirmación si la variable de preferencia de PowerShell `$ConfirmPreference` tiene un valor de `Medium` o inferior. Debido al impacto potencialmente alto que puede tener la eliminación de una zona DNS, el cmdlet `Remove-AzureRmDnsZone` pide confirmación si la variable de PowerShell `$ConfirmPreference` tiene cualquier valor distinto de `None`.

Puesto que el valor predeterminado para `$ConfirmPreference` es `High`, solo `Remove-AzureRmDnsZone` pide confirmación de forma predeterminada.

Puede invalidar el valor actual de `$ConfirmPreference` mediante el parámetro `-Confirm`. Si especifica `-Confirm` o `-Confirm:$True`, el cmdlet solicita confirmación antes de ejecutarse. Si especifica `-Confirm:$False`, el cmdlet no solicita confirmación.

Para más información sobre `-Confirm` y `$ConfirmPreference`, consulte [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) (Acerca de las variables de preferencias).


## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, realice el paso siguiente:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de las zonas DNS privadas, consulte [Using Azure DNS for private domains](private-dns-overview.md) (Uso de Azure DNS para dominios privados).

Obtenga información sobre algunos escenarios comunes [Escenarios de zona privada](./private-dns-scenarios.md) que pueden realizarse con zonas privadas en Azure DNS.

Para aprender a administrar registros DNS en Azure DNS, consulte [Administración de registros de DNS en Azure DNS mediante PowerShell](dns-operations-recordsets.md).

