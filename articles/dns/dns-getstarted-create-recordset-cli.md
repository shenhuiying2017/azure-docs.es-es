---
title: Creación de un conjunto de registros y registros para una zona DNS con CLI | Microsoft Docs
description: Creación de registros host para DNS de Azure. Configuración de conjuntos de registros y registros mediante CLI
services: dns
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''

ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: sewhee

---
# Creación de conjuntos de registros y registros de DNS con CLI
> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure](dns-getstarted-create-recordset-cli.md)
> 
> 

Este artículo le guiará a través del proceso de creación de registros y los conjuntos de registros mediante la CLI. Después de crear la zona DNS, deberá agregar los registros DNS para su dominio. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

[!INCLUDE [incluir-DNS-acerca-de-los-registros](../../includes/dns-about-records-include.md)]

## Creación de un registro y un conjunto de registros
En esta sección se mostrará cómo crear registros y un conjunto de registros. En este ejemplo, se creará un conjunto de registros que tiene un nombre relativo "www" en la zona DNS "contoso.com". El nombre completo de los registros será "www.contoso.com". El tipo de registro es "A" y el período de vida es de 60 segundos. Después de completar este paso, habrá creado un conjunto de registros vacío.

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@", incluidas las comillas. Esta es una convención común de DNS.

### 1\. Creación de un conjunto de registros
Para crear un conjunto de registros, use `azure network dns record-set create`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de registros, el tipo de registro y el período de vida. Si no se define el parámetro `--ttl`, el valor predeterminado es 4 (en segundos). Después de completar este paso, tendrá un conjunto de registros "www" vacío.

*Uso: network dns record-set create <grupo de recursos><nombre de zona dns><nombre><tipo> <ttl>*

    azure network dns record-set create myresourcegroup  contoso.com  www A  60

### 2\. Integración de registros
Para poder usar el conjunto de registros "www" recién creado, es necesario agregarle registros. Los registros se agregan a los conjuntos de registros mediante `azure network dns record-set add-record`.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se usa un conjunto de registros de tipo "A", solo se podrán especificar registros con el parámetro `-a <IPv4 address>`.

Puede agregar registros *A* de IPv4 al conjunto de registros "www" con el comando siguiente:

*Uso: network dns record-set add-record <grupo de recursos><nombre de zona dns><nombre del conjunto de registrso><tipo>*

    azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 134.170.185.46

## Ejemplos de tipo de registros adicionales
Los ejemplos siguientes muestran cómo crear un conjunto de registros de cada tipo de registro. Cada conjunto de registros contiene un único registro.

[!INCLUDE [incluir-incorporación-dns-cli](../../includes/dns-add-record-cli-include.md)]

## Pasos siguientes
Para administrar los registros y los conjuntos de registros, consulte [Administración de registros y conjuntos de registros DNS mediante la CLI](dns-operations-recordsets-portal.md).

Para más información acerca de DNS de Azure, consulte la [Introducción a DNS de Azure](dns-overview.md).

<!---HONumber=AcomDC_1005_2016-->