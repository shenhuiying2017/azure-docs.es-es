---
title: "Creación de registros de DNS mediante la CLI de Azure 1.0 | Microsoft Docs"
description: "Creación de registros de host para Azure DNS. Configuración de conjuntos de registros y registros mediante la CLI de Azure 1.0"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 02b897d3-e83b-4257-b96d-5c29aa59e843
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 45599ea87b536b74cc652ef28f91a6058c7c8e4a
ms.lasthandoff: 02/27/2017

---

# <a name="create-dns-records-using-the-azure-cli-10"></a>Creación de registros de DNS mediante la CLI de Azure 1.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure 1.0](dns-getstarted-create-recordset-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-getstarted-create-recordset-cli.md)

Este artículo le guía en el proceso de creación de registros y conjuntos de registros mediante la CLI de Azure 1.0.

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

* [CLI de Azure 1.0](dns-getstarted-create-recordset-cli-nodejs.md): la CLI para los modelos de implementación clásica y de administración de recursos.
* [CLI de Azure 2.0 ](dns-getstarted-create-recordset-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos.

## <a name="introduction"></a>Introducción

Antes de crear registros DNS en Azure DNS, es necesario que comprenda cómo Azure DNS los organiza en conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para más información sobre los registros DNS en Azure DNS, consulte [DNS zones and records](dns-zones-records.md) (Zonas y registros DNS).

## <a name="create-a-record-set-and-record"></a>Creación de un nuevo conjunto de registros y de un registro

En esta sección se describe cómo crear registros DNS en Azure DNS. En los ejemplos se da por hecho que ya tiene [instalada la CLI de Azure 1.0, ha iniciado sesión y ha creado una zona DNS](dns-getstarted-create-dnszone-cli-nodejs.md).

Todos los ejemplos de esta página usan el tipo de registro DNS “A”. Para otros tipos de registro y más información sobre cómo administrar los registros y los conjuntos de registros de DNS, consulte [Manage DNS records and record sets by using the Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) (Administración de registros y conjuntos de registros de DNS mediante la CLI de Azure 1.0).

## <a name="create-a-dns-record"></a>Creación de un registro de DNS

Para crear un registro de DNS, use el comando `azure network dns record-set add-record`. Para obtener ayuda, consulte `azure network dns record-set add-record -h`.

Al crear un registro, debe especificar los nombres del grupo de recursos, de la zona y del conjunto de registros, el tipo de registro y los detalles del registro que se están creando.

Si el conjunto de registros aún no existe, este comando lo crea automáticamente. Si el conjunto de registros ya existe, este comando agrega el registro que especifique al conjunto de registros existente. 

Si se crea un conjunto de registros, se utiliza el valor predeterminado del período de vida (3600). Para ver instrucciones sobre cómo usar diferentes períodos de vida, consulte [Manage DNS records in Azure DNS using the Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) (Administración de registros y conjuntos de registros de DNS mediante la CLI de Azure 1.0).

En el ejemplo siguiente se crea un registro A denominado *www* en la zona *contoso.com* del grupo de recursos *MyResourceGroup*. La dirección IP del registro A es *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@", incluidas las comillas:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

Los parámetros utilizados para especificar los datos del registro varían según el tipo del registro. Por ejemplo, para un registro de tipo "A", especifique la dirección IPv4 con el parámetro `-a <IPv4 address>`. Consulte `azure network dns record-set add-record -h` para enumerar los parámetros para otros tipos de registros. Para ver ejemplos de cada tipo de registro, consulte [Manage DNS records and record sets by using the Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md) (Administración de registros y conjuntos de registros de DNS mediante la CLI de Azure 1.0).


## <a name="verify-name-resolution"></a>Comprobación de la resolución de nombres

Puede probar que los registros DNS están presentes en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el cmdlet de PowerShell [Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado el dominio para usar la nueva zona en Azure DNS, deberá [dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona](dns-getstarted-create-dnszone.md#test-name-servers). Asegúrese de sustituir los valores correctos de su zona de registros en el comando que aparece a continuación.

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [delegar su nombre de dominio a los servidores de nombres de Azure DNS](dns-domain-delegation.md).

Aprenda a [administrar zonas DNS con la CLI de Azure 1.0](dns-operations-dnszones-cli-nodejs.md).

Aprenda a [administrar registros y conjuntos de registros de DNS mediante la CLI de Azure 1.0](dns-operations-recordsets-cli-nodejs.md).


