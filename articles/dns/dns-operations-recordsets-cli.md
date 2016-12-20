---
title: "Administración de registros y conjuntos de registros en DNS de Azure con la CLI de Azure | Microsoft Docs"
description: "Administración de conjuntos de registros y registros DNS en DNS de Azure al hospedar dominios en DNS de Azure. Todos los comandos de CLI para operaciones en conjuntos de registros y registros."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: jonatul
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: 2bc18d618cf8838209bea9f8a2d323e3b1042709

---

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Administración de registros y conjuntos de registros DNS mediante la CLI

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-operations-recordsets-portal.md)
> * [CLI de Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

En este artículo se explica cómo administrar conjuntos de registros y registros de zonas DNS con la interfaz de la línea de comandos (CLI) de Azure multiplataforma.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es una colección de registros de una zona con el mismo nombre y el mismo tipo. Para más información, consulte [Descripción de los registros y los conjuntos de registros](dns-getstarted-create-recordset-cli.md).

## <a name="configure-the-cross-platform-azure-cli"></a>Configuración de la CLI de Azure multiplataforma

DNS de Azure es un servicio exclusivo del Administrador de recursos de Azure. No tiene una instancia de Azure Service Management API. Asegúrese de que la CLI de Azure está configurada para utilizar el modo de Resource Manager mediante el comando `azure config mode arm` .

Si ve " **error: 'dns' is not an azure command**", lo más probable es que se deba a que está usando CLI de Azure en modo Azure Service Management, no en el modo de Resource Manager.

## <a name="create-a-new-record-set-and-record"></a>Creación de un nuevo conjunto de registros y un registro

Para crear un conjunto de registros en el Portal de Azure, consulte [Creación de conjuntos de registros y registros de DNS con CLI](dns-getstarted-create-recordset-cli.md).

## <a name="retrieve-a-record-set"></a>Recuperación de un conjunto de registros

Para recuperar un conjunto de registros existente, use `azure network dns record-set show`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de registros y el tipo de registro. Use el ejemplo siguiente y reemplace los valores por los suyos propios.

```azurecli
azure network dns record-set show myresourcegroup contoso.com www A
```

## <a name="list-record-sets"></a>Enumeración de conjuntos de registros

Puede mostrar todos los registros de una zona DNS con el comando `azure network dns record-set list` . En ambos casos deberá especificar el nombre del grupo de recursos y el nombre de zona.

### <a name="to-list-all-record-sets"></a>Para mostrar todos los conjuntos de registros

Este ejemplo devolverá todos los conjuntos de registros, independientemente del nombre o tipo de registro:

```azurecli
azure network dns record-set list myresourcegroup contoso.com
```

### <a name="to-list-record-sets-of-a-given-type"></a>Para mostrar los conjuntos de registros de un tipo determinado

Este ejemplo devuelve todos los conjuntos de registros que coinciden con el tipo de registro determinado (en este caso, los registros "A"):

```azurecli
azure network dns record-set list myresourcegroup contoso.com A
```

## <a name="add-a-record-to-a-record-set"></a>Incorporación de un registro a un conjunto de registros

Los registros se agregan a los conjuntos de registros mediante el comando `azure network dns record-set add-record`. Los parámetros para agregar registros a un conjunto de registros varían según el tipo de registro que se está configurando. Por ejemplo, cuando se utiliza un conjunto de registros de tipo "A", solo puede especificar registros con el parámetro `-a <IPv4 address>`.

Para crear un conjunto de registros, use el comando `azure network dns record-set create`. Especifique el grupo de recursos, el nombre de zona, el nombre relativo del conjunto de recursos, el tipo de registro y el período de vida (TTL). Si no se define el parámetro `--ttl` , el valor predeterminado (en segundos) es 4.

```azurecli
azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300
```

Después de crear el conjunto de registros "A", agregue la dirección IPv4 utilizando el comando `azure network dns record-set add-record`.

```azurecli
azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1
```

En los ejemplos siguientes se muestra cómo crear un conjunto de registros de cada tipo de registro. Cada conjunto de registros contiene un único registro.

[!INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]

## <a name="update-a-record-in-a-record-set"></a>Actualización de un registro en un conjunto de registros

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Para agregar otra dirección IP (1.2.3.4) a un conjunto de registros A existente (www):

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Para quitar un valor existente de un conjunto de registros

Use `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK

## <a name="remove-a-record-from-a-record-set"></a>Eliminación de un registro de un conjunto de registros

Los registros pueden eliminarse de un conjunto de registros con `azure network dns record-set delete-record`. Todos los parámetros del registro que se va a eliminar deben coincidir exactamente con los del registro existente.

Al eliminar el último registro de un conjunto de registros, no se elimina el conjunto de registros. Consulte la sección [Eliminación de un conjunto de registros](#delete)de este artículo para más información.

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

azure network dns record-set delete myresourcegroup contoso.com www A
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Eliminación de un registro AAAA de un conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Eliminación de un registro CNAME de un conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Eliminación de un registro MX de un conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5
```

### <a name="remove-an-ns-record-from-record-set"></a>Eliminación de un registro NS de un conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"
```

### <a name="remove-a-ptr-record-from-a-record-set"></a>Eliminación de un registro PTR de un conjunto de registros

En este caso, 'my-arpa-zone.com' representa la zona ARPA que representa el intervalo IP.  Cada registro PTR establecido en esta zona se corresponde con una dirección IP dentro de este intervalo IP.

```azurecli
azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Eliminación de un registro SRV de un conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Eliminación de un registro TXT de un conjunto de registros

```azurecli
azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"
```

## <a name="a-namedeleteadelete-a-record-set"></a><a name="delete"></a>Eliminación de un conjunto de registros

Los conjuntos de registros pueden eliminarse mediante el cmdlet `Remove-AzureRmDnsRecordSet` . No se pueden eliminar los conjuntos de registros SOA ni NS en el vértice de zona (nombre = "@")) que se crearon automáticamente cuando se creó la zona. Se eliminarán automáticamente si se elimina la zona.

En el ejemplo siguiente, el conjunto de registros A "test-a" se eliminará de la zona DNS contoso.com:

```azurecli
azure network dns record-set delete myresourcegroup contoso.com  "test-a" A
```

Se puede usar el modificador opcional `-q` para suprimir el mensaje de confirmación.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre DNS de Azure, consulte [Introducción a DNS de Azure](dns-overview.md). Para más información acerca de la automatización de DNS, consulte [Creación de conjuntos de registros y zonas DNS con el SDK de .NET](dns-sdk.md).

Si desea trabajar con registros DNS inversos, consulte [Administración de registros de DNS inversos para los servicios mediante la CLI de Azure](dns-reverse-dns-record-operations-cli.md).



<!--HONumber=Nov16_HO3-->


