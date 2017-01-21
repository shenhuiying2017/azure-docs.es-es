---
title: "Creación de registros de DNS mediante la CLI de Azure | Microsoft Docs"
description: "Creación de registros host para DNS de Azure. Configuración de conjuntos de registros y registros mediante CLI"
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
ms.sourcegitcommit: 18a21cdc0f9641356dfaf6f6d93edfcac11af210
ms.openlocfilehash: 790af1544ed86155f5f864f3914b5fd1c4f42f4b

---

# <a name="create-dns-records-using-the-azure-cli"></a>Creación de registros de DNS mediante la CLI de Azure

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure](dns-getstarted-create-recordset-cli.md)

Este artículo le guiará a través de la creación de registros y conjuntos de registros mediante la CLI de Azure.

## <a name="introduction"></a>Introducción

Antes de crear registros DNS en Azure DNS, es necesario que comprenda cómo Azure DNS los organiza en conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para más información sobre los registros DNS en Azure DNS, consulte [DNS zones and records](dns-zones-records.md) (Zonas y registros DNS).

## <a name="create-a-record-set-and-record"></a>Creación de un nuevo conjunto de registros y de un registro

En esta sección se describe cómo crear registros DNS en Azure DNS. En los ejemplos se da por hecho que ya tiene [instalada la CLI de Azure, ha iniciado sesión y ha creado una zona DNS](dns-getstarted-create-dnszone-cli.md).

Todos los ejemplos de esta página usan el tipo de registro DNS “A”. Para otros tipos de registro y más información sobre cómo administrar los registros y los conjuntos de registros DNS, consulte [Administración de registros y conjuntos de registros DNS mediante la CLI](dns-operations-recordsets-cli.md).

## <a name="create-a-dns-record"></a>Creación de un registro de DNS

Para crear un registro de DNS, use el comando `azure network dns record-set add-record`. Para obtener ayuda, consulte `azure network dns record-set add-record -h`.

Al crear un registro, debe especificar los nombres del grupo de recursos, de la zona y del conjunto de registros, el tipo de registro y los detalles del registro que se están creando.

Si el conjunto de registros aún no existe, este comando lo crea automáticamente. Si el conjunto de registros ya existe, este comando agrega el registro que especifique al conjunto de registros existente. 

Si se crea un conjunto de registros, se utiliza el valor predeterminado del período de vida (3600). Para ver instrucciones sobre cómo usar diferentes períodos de vida, consulte [Administración de registros y conjuntos de registros DNS mediante la CLI](dns-operations-recordsets-cli.md).

En el ejemplo siguiente se crea un registro A denominado *www* en la zona *contoso.com* del grupo de recursos *MyResourceGroup*. La dirección IP del registro A es *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@",, incluidas las comillas:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

Los parámetros utilizados para especificar los datos del registro varían según el tipo del registro. Por ejemplo, para un registro de tipo "A", especifique la dirección IPv4 con el parámetro `-a <IPv4 address>`. Consulte `azure network dns record-set add-record -h` para enumerar los parámetros para otros tipos de registros. Para ver ejemplos de cada tipo de registro, consulte [Administración de registros y conjuntos de registros DNS mediante la CLI](dns-operations-recordsets-cli.md).


## <a name="verify-name-resolution"></a>Comprobación de la resolución de nombres

Puede probar que los registros DNS están presentes en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el cmdlet de PowerShell [Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado el dominio para usar la nueva zona en Azure DNS, deberá [dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona](dns-getstarted-create-dnszone.md#test-name-servers). Asegúrese de sustituir los valores correctos de su zona de registros en el comando que aparece a continuación.

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [delegar su nombre de dominio a los servidores de nombres de Azure DNS](dns-domain-delegation.md).

Obtenga información sobre cómo [administrar zonas DNS con la CLI de Azure](dns-operations-dnszones-cli.md).

Obtenga información sobre cómo [administrar registros y conjuntos de registros DNS mediante la CLI de Azure](dns-operations-recordsets-cli.md).




<!--HONumber=Dec16_HO3-->


