<properties 
   pageTitle="Operaciones en zonas DNS | Microsoft Azure"
	description="Puede administrar zonas DNS mediante cmdlets de Azure PowerShell o la CLI. Actualización, eliminación y creación de zonas DNS en DNS de Azure"
	services="dns"
	documentationCenter="na"
	authors="joaoma"
	manager="Adinah"
	editor=""/>

<tags
   ms.service="dns"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/02/2015"
	ms.author="joaoma"/>

# Administración de zonas DNS

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [Azure Powershell](dns-operations-dnszones.md)

En esta guía se explica cómo administrar zonas DNS. En ella se detallará la secuencia de operaciones que hay que realizar para administrar la zona DNS.

## Creación de una zona DNS

Para crear una zona DNS para hospedar un dominio, use `azure network dns zone create`:

		Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

La operación crea una nueva zona DNS en DNS de Azure. También puede especificar una matriz de etiquetas del Administrador de recursos de Azure; para obtener más información, consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags).

El nombre de la zona debe ser único en el grupo de recursos y la zona no debe existir aún, de lo contrario se producirá un error de la operación.

El mismo nombre de zona podrá reutilizarse en un grupo de recursos distinto o en una suscripción a Azure diferente. Cuando varias zonas comparten el mismo nombre, a cada instancia se le asignarán distintas direcciones de servidores de nombres, y solo se podrá delegar una instancia desde el dominio primario. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener más información.

## Recuperación de una zona DNS

Para recuperar una zona DNS, use `azure network dns zone show`:

	azure network dns zone show myresourcegroup contoso.com

La operación devuelve una zona DNS con su Id., el número de conjuntos de registros y las etiquetas.


## Enumeración de zonas DNS

Para recuperar las zonas DNS dentro de un grupo de recursos, use `azure network dns zone list`:

	azure network dns zone list myresourcegroup


## Actualización de una zona DNS

Los cambios en los recursos de una zona DNS se pueden realizar con `azure network dns zone set`. Con esta operación no se actualizan los conjuntos de registros DNS de la zona (consulte [Administración de registros DNS](dns-operations-recordsets.md)). Solo se utiliza para actualizar las propiedades de los recursos de la zona. Esto se limita actualmente a las “etiquetas” del Administrador de recursos de Azure para los recursos de la zona. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obtener más información.

	azure network dns zone set myresourcegroup contoso.com -t prod=value2

## Eliminación de una zona DNS

Las zonas DNS se pueden eliminar mediante `azure network dns zone delete`.
 
Antes de eliminar una zona DNS de DNS de Azure, deberá eliminar todos los conjuntos de registros, salvo los registros NS y SOA de la raíz de la zona que se crearon automáticamente cuando se creó la zona.

	azure network dns zone delete myresourcegroup contoso.com 

Esta operación tiene un modificador opcional “-q” que suprime el mensaje para confirmar que desea eliminar la zona DNS.


## Pasos siguientes


[Administración de registros DNS](dns-operations-recordsets-cli.md)

[Automatización de operaciones de Azure con el SDK de .NET](dns-sdk.md)

<!---HONumber=September15_HO1-->