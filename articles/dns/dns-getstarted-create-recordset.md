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



# Creación de conjuntos de registros y registros de DNS con PowerShell


> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI de Azure](dns-getstarted-create-recordset-cli.md)

Este artículo le guiará a través del proceso de creación de registros y los conjuntos de registros mediante Windows PowerShell. Después de crear la zona DNS, agregue los registros DNS para su dominio. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

[AZURE.INCLUDE [incluir-DNS-acerca-de-los-registros](../../includes/dns-about-records-include.md)]

## Comprobación de que se dispone de la versión más reciente de PowerShell.

Compruebe que tiene instalada la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para más información sobre cómo instalar los cmdlets de PowerShell.

## Creación de un nuevo conjunto de registros y de un registro

En esta sección se describe cómo crear un conjunto de registros y un registro.


### 1\. su suscripción

Abre la consola de PowerShell y conéctate a tu cuenta. Use el siguiente ejemplo para ayudarle a conectarse:

	Login-AzureRmAccount

Compruebe las suscripciones para la cuenta.

	Get-AzureRmSubscription

Especifique la suscripción que quiere usar.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Para más información sobre el uso de PowerShell, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).


### 2\. Creación de un conjunto de registros

Los conjuntos de registros se crean mediante el cmdlet `New-AzureRmDnsRecordSet`. A la hora de crear un conjunto de registros, deberá especificar el nombre del conjunto de registros, la zona, el período de vida (TTL) y el tipo de registro.

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@", incluidas las comillas. Esta es una convención común de DNS.

En el ejemplo siguiente se crea un conjunto de registros con el nombre relativo "www" en la zona DNS "contoso.com". El nombre completo del conjunto de registros es www.contoso.com. El tipo de registro es "A" y el valor de TTL es de 60 segundos. Después de completar este paso, tendrá un conjunto de registros "www" vacío que se asigna a la variable *$rs*.

	$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### Si un conjunto de registros ya existe

Si un conjunto de registros ya existe, el comando genera un error a menos que se use el conmutador *-Overwrite*. La opción *-Overwrite* genera un mensaje de confirmación, que puede suprimirse con el conmutador *-Force*.


	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


En este ejemplo, la zona se especifica mediante el nombre de la zona y el nombre del grupo de recursos. Como alternativa, puede especificar un objeto de zona, como el que devuelve `Get-AzureRmDnsZone` o `New-AzureRmDnsZone`.

	$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
	$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet` devuelve un objeto local que representa el conjunto de registros que se creó en DNS de Azure.

### 3\. Incorporación de un registro

Para poder usar el conjunto de registros "www" recién creado, es necesario añadirle registros. Puede agregar registros de IPv4 *A* al conjunto de registros "www" con el ejemplo siguiente. Este ejemplo se basa en la variable *$rs* que estableció en el paso anterior.

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


También puede usar la herramienta nslookup u otras herramientas DNS para consultar el nuevo conjunto de registros.

Si aún no ha delegado el dominio en los servidores de nombres de DNS de Azure, debe especificar explícitamente el nombre, el servidor y la dirección de la zona.


	nslookup www.contoso.com ns1-01.azure-dns.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	Name:    www.contoso.com
	Addresses:  134.170.185.46
    	        134.170.188.221

## Creación de un conjunto de registros de cada tipo con un único registro


En los ejemplos siguientes se muestra cómo crear un conjunto de registros de cada tipo de registro. Cada conjunto de registros contiene un único registro.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## Pasos siguientes

[Cómo administrar zonas DNS con PowerShell](dns-operations-dnszones.md)

[Administración de registros y conjuntos de registros DNS mediante PowerShell](dns-operations-recordsets.md)

[Automate Azure operations with .NET SDK](dns-sdk.md) (Automatización de operaciones de Azure con el SDK de .NET)

<!---HONumber=AcomDC_0608_2016-->