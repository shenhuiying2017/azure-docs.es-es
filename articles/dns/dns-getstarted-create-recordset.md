<properties
   pageTitle="Creación de un conjunto de registros y registros para una zona DNS con PowerShell | Microsoft Azure"
   description="Creación de registros host para DNS de Azure. Configuración de conjuntos de registros y registros mediante PowerShell"
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
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# Creación de conjuntos de registros y registros de DNS con CLI


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI de Azure](dns-getstarted-create-recordset-cli.md)

Este artículo le guiará a través de la creación de registros y conjuntos de registros usando PowerShell. Después de crear la zona DNS, deberá agregar los registros DNS para su dominio. Para ello, primero tiene que entender lo que son los registros y los conjuntos de registros de DNS.

[AZURE.INCLUDE [incluir-DNS-acerca-de-los-registros](../../includes/dns-about-records-include.md)]

## Antes de empezar

Compruebe que tiene instalada la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell.

## Creación de un nuevo conjunto de registros y de un registro

En esta sección se mostrará cómo crear registros y un conjunto de registros.


### 1\. su suscripción 

Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

	Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

	Get-AzureRmSubscription 

Especifique la suscripción que quiere usar.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Para más información sobre el uso de PowerShell, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).


### 2\. Creación de un conjunto de registros

Los conjuntos de registros se crean con el cmdlet `New-AzureRmDnsRecordSet`. A la hora de crear un conjunto de registros, deberá especificar el nombre del conjunto de registros, la zona, el período de vida (TTL) y el tipo de registro.

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@", incluidas las comillas. Esta es una convención común de DNS.

En el ejemplo que aparece a continuación, se creará un conjunto de registros que tiene un nombre relativo *www* en la zona DNS *contoso.com*. El nombre completo de los registros será *www.contoso.com*, el tipo de registro es *A*, y el período de vida es de 60 segundos. Después de completar este paso, tendrá un conjunto de registros *www* vacío que se asigna a la variable *$rs*.

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### Si un conjunto de registros ya existe

Si un conjunto de registros ya existe, el comando generará un error a menos que se use el elemento *-Overwrite*. La opción *-Overwrite* generará un mensaje de confirmación, que puede suprimirse con el elemento *-Force*.


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


En el ejemplo anterior, la zona se especifica utilizando el nombre de zona y el nombre de grupo de recursos. Como alternativa, puede especificar un objeto de zona, como el que devuelve `Get-AzureRmDnsZone` o `New-AzureRmDnsZone`.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet` devuelve un objeto local que representa el conjunto de registros que se crea en DNS de Azure.

### 3\. Incorporación de un registro

Para poder usar el conjunto de registros *www* recién creado, es necesario añadirle registros. Puede agregar registros de IPv4 *A* al conjunto de registros *www* con el ejemplo siguiente. Este ejemplo se basa en la variable que estableció en el paso anterior ($rs).

Agregar registros a un conjunto de registros mediante `Add-AzureRmDnsRecordConfig` es una operación sin conexión. Se actualiza únicamente la variable local *$rs*.


	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### 4\. Confirmación de los cambios

Confirme los cambios introducidos en el conjunto de registros. Use `Set-AzureRmDnsRecordSet` para cargar los cambios del conjunto de registros en DNS de Azure.

	Set-AzureRmDnsRecordSet -RecordSet $rs

### 5\. Recuperación del conjunto de registros

Puede recuperar el conjunto de registros desde DNS de Azure con `Get-AzureRmDnsRecordSet` tal y como se muestra en el siguiente ejemplo.


	Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


	Name              : www
	ZoneName          : contoso.com
	ResourceGroupName : MyAzureResourceGroup
	Ttl               : 3600
	Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
	RecordType        : A
	Records           : {134.170.185.46, 134.170.188.221}
	Tags              : {}


También puede usar nslookup u otras herramientas DNS para consultar el nuevo conjunto de registros.

Si aún no ha delegado el dominio en los servidores de nombres de DNS de Azure, será necesario especificar explícitamente la dirección del servidor de nombres de la zona.


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## Ejemplos de tipo de registros adicionales


Los ejemplos siguientes muestran cómo crear un conjunto de registros de cada tipo de registro, cada uno contiene un único registro.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## Pasos siguientes

[Administración de zonas DNS](dns-operations-dnszones.md)

[Administración de registros DNS](dns-operations-recordsets.md)

[Automatización de operaciones de Azure con el SDK de .NET](dns-sdk.md)
 

<!---HONumber=AcomDC_0518_2016-->