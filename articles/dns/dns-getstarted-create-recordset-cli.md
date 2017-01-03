---
title: "Creación de un conjunto de registros y registros para una zona DNS con CLI | Microsoft Docs"
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
ms.date: 12/09/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: e377f176fe24a8e7e42d409f86d6b0093ce5e7c4

---

# <a name="create-dns-record-sets-and-records-by-using-cli"></a>Creación de conjuntos de registros y registros de DNS con CLI

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure](dns-getstarted-create-recordset-cli.md)

Este artículo le guiará a través del proceso de creación de registros y los conjuntos de registros mediante la CLI. Para ello, primero debe saber qué son los registros y los conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

En esta sección se describe cómo crear registros DNS en Azure DNS. En los ejemplos se da por hecho que ya tiene [instalada la CLI de Azure, ha iniciado sesión y ha creado una zona DNS](dns-getstarted-create-dnszone-cli.md).

Todos los ejemplos de esta página usan el tipo de registro DNS “A”. Para otros tipos de registro y más información sobre cómo administrar los registros y los conjuntos de registros DNS, consulte [Administración de registros y conjuntos de registros DNS mediante la CLI](dns-operations-recordsets-cli.md).

## <a name="create-a-record-set-and-record"></a>Creación de un nuevo conjunto de registros y de un registro

En esta sección se mostrará cómo crear registros y un conjunto de registros. En este ejemplo, se creará un conjunto de registros que tiene un nombre relativo "www" en la zona DNS "contoso.com". El nombre completo de los registros será "www.contoso.com". El tipo de registro es "A" y el período de vida es de 60 segundos. Después de completar este paso, habrá creado un conjunto de registros vacío.

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@",, incluidas las comillas. Esta es una convención común de DNS.

### <a name="1-create-a-record-set"></a>1. Creación de un conjunto de registros

Si el nuevo registro tiene el mismo nombre y tipo que un registro existente, debe agregarlo al conjunto de registros existente. Puede omitir este paso e ir a [Incorporación de registros](#add-records). Si el nuevo registro tiene un nombre y un tipo diferente al de todos los registros existentes, debe crear un nuevo conjunto de registros.

Los conjuntos de registros se crean mediante el comandos `azure network dns record-set create`. Para obtener ayuda, consulte `azure network dns record-set create -h`.  

A la hora de crear un conjunto de registros, deberá especificar el nombre del conjunto de registros, la zona, el período de vida (TTL) y el tipo de registro. 

```azurecli
azure network dns record-set create myresourcegroup contoso.com www A 60
```

Después de completar este paso, tendrá un conjunto de registros "www" vacío. Para poder usar el conjunto de registros "www" recién creado, primero es necesario agregarle registros.

### <a name="2-add-records"></a>2. Integración de registros

Los registros se agregan a los conjuntos de registros mediante `azure network dns record-set add-record`. Para obtener ayuda, consulte `azure network dns record-set add-record -h`.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se usa un conjunto de registros de tipo “A”, solo podrá especificar los registros con el parámetro `-a <IPv4 address>`. Consulte `azure network dns record-set add-record -h` para enumerar los parámetros para otros tipos de registros.

Para agregar registros A al conjunto de registros "www" creado anteriormente, puede usar el comando siguiente:

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com  www A  -a 1.2.3.4
```

### <a name="verify-name-resolution"></a>Comprobación de la resolución de nombres

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




<!--HONumber=Dec16_HO2-->


