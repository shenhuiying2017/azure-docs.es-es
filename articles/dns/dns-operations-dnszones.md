<properties 
   pageTitle="Administración de zonas DNS con PowerShell | Microsoft Azure" 
   description="Puede administrar zonas DNS con Azure Powershell. Actualización, eliminación y creación de zonas DNS en DNS de Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Cómo administrar zonas DNS con PowerShell

> [AZURE.SELECTOR]
- [CLI de Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



En este artículo se describe cómo administrar zonas DNS mediante el uso de PowerShell. Para seguir estos pasos, necesitará instalar la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager (1.0 o posterior). Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell.


## Creación de una zona DNS

Para crear una zona DNS, consulte [Introducción a DNS de Azure con PowerShell](dns-getstarted-create-dnszone.md).

## Recuperación de una zona DNS

Para recuperar una zona DNS, use el cmdlet `Get-AzureRmDnsZone`. Esta operación devuelve un objeto de la zona DNS correspondiente a una zona existente en DNS de Azure. El objeto contiene datos sobre la zona (por ejemplo, el número de conjuntos de registros), pero no contiene los conjuntos de registros.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## Enumeración de zonas DNS

Si se omite el nombre de la zona de `Get-AzureRmDnsZone`, puede enumerar todas las zonas en un grupo de recursos. Esta operación devuelve una matriz de objetos de la zona.

	$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## Actualización de una zona DNS

Los cambios en los recursos de una zona DNS se pueden realizar mediante `Set-AzureRmDnsZone`. Con esta operación no se actualizan los conjuntos de registros DNS de la zona (consulte [Administración de registros DNS](dns-operations-recordsets.md)). Solo se utiliza para actualizar las propiedades de los recursos de la zona. Esto se limita actualmente a las “etiquetas” del Administrador de recursos de Azure para los recursos de la zona. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obtener más información.

Utilice una de las dos opciones siguientes para actualizar las zonas DNS:

### Especificar la zona con la utilización del nombre de zona y el grupo de recursos

	Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### Especificar la zona utilizando un objeto $zone

Especifique la zona utilizando un objeto $zone de `Get-AzureRmDnsZone`. Al usar `Set-AzureRmDnsZone` con un objeto $zone, se usarán las comprobaciones de ETag para garantizar que no se sobrescriben los cambios simultáneos. Puede usar el elemento opcional *-Overwrite* para suprimir estas comprobaciones. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obtener más información.


	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	<..modify $zone.Tags here...>
	Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## Eliminación de una zona DNS

Las zonas DNS pueden eliminarse con el cmdlet Remove-AzureRmDnsZone.
 
Antes de eliminar una zona DNS de DNS de Azure, deberá eliminar todos los conjuntos de registros, salvo los registros NS y SOA de la raíz de la zona que se crearon automáticamente cuando se creó la zona.

Utilice una de las dos opciones siguientes para eliminar una zona DNS:

### Especificar la zona con la utilización del nombre de zona y el nombre del grupo de recursos

Esta operación tiene un elemento opcional *-Force* que suprime el mensaje para confirmar que desea eliminar la zona DNS.

	Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

### Especificar la zona utilizando un objeto $zone 

Especifique la zona utilizando un objeto $zone de `Get-AzureRmDnsZone`. Esta operación tiene un elemento opcional *-Force* que suprime el mensaje para confirmar que desea eliminar la zona DNS. Como con `Set-AzureRmDnsZone`, especificar la zona utilizando un objeto $zone permite realizar las comprobaciones de ETag para asegurarse de que no se eliminan los cambios simultáneos. <BR> La etiqueta opcional *-Overwrite* suprime estas comprobaciones. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obtener más información.

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



También se puede canalizar el objeto de la zona en lugar de pasarlo como parámetro:

	Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## Pasos siguientes

Después de crear una zona DNS, cree [conjuntos de registros y registros](dns-getstarted-create-recordset.md) para iniciar la resolución de nombres para el dominio de Internet.

<!---HONumber=AcomDC_0518_2016-->