<properties
   pageTitle="Creación de un conjunto de registros y registros para una zona DNS con CLI | Microsoft Azure"
   description="Creación de registros host para DNS de Azure. Configuración de conjuntos de registros y registros mediante CLI"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# Creación de conjuntos de registros y registros de DNS con CLI

> [AZURE.SELECTOR]
- [Portal de Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [CLI de Azure](dns-getstarted-create-recordset-cli.md)


Este artículo le guiará a través de la creación de registros y conjuntos de registros usando CLI. Después de crear la zona DNS, deberá agregar los registros DNS para su dominio. Para ello, primero tiene que entender lo que son los registros y los conjuntos de registros de DNS.

[AZURE.INCLUDE [incluir-DNS-acerca-de-los-registros](../../includes/dns-about-records-include.md)]

## Creación de un nuevo conjunto de registros y de un registro

En esta sección se mostrará cómo crear registros y un conjunto de registros. En este ejemplo, se creará un conjunto de registros que tiene un nombre relativo *www* en la zona DNS *contoso.com*. El nombre completo de los registros será *www.contoso.com*. El tipo de registro es *A* y el valor de TTL es de 60 segundos. Después de completar este paso, habrá creado un conjunto de registros vacío.

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@", incluidas las comillas. Esta es una convención común de DNS.

### 1\. Creación de un conjunto de registros

Para crear un conjunto de registros, use `azure network dns record-set create`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de recursos, el tipo de registro y el período de vida (TTL). Si no se define el parámetro--ttl, el valor predeterminado es 4 (en segundos). Después de completar este paso, tendrá un conjunto de registros "www" vacío.
	
*Uso: creación del conjunto de registros de dns de red<resource-group> <dns-zone-name> <name> <type> <ttl>*

	azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\. Integración de registros

Para poder usar el conjunto de registros *www* recién creado, es necesario añadirle registros. Los registros se agregan a los conjuntos de registros mediante el comando `azure network dns record-set add-record`.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se usa un conjunto de registros de tipo *A*, solo se podrán especificar registros con el parámetro `-a <IPv4 address>`.

Puede agregar registros de IPv4 *A* al conjunto de registros *www* con el comando siguiente:

*Uso: integración de registros al conjunto de registros de dns de red <resource-group> <dns-zone-name> <record-set-name> <type>*

	azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## Ejemplos de tipo de registros adicionales

Los ejemplos siguientes muestran cómo crear un conjunto de registros de cada tipo de registro, cada uno contiene un único registro.

[AZURE.INCLUDE [incluir-incorporación-dns-cli](../../includes/dns-add-record-cli-include.md)]

## Pasos siguientes

Para administrar los registros y los conjuntos de registros, consulte [Cómo administrar registros DNS con CLI](dns-operations-recordsets-cli.md).

Para más información acerca de DNS de Azure, consulte la [Introducción a DNS de Azure](dns-overview.md).

<!---HONumber=AcomDC_0525_2016-->