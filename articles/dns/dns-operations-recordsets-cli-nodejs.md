---
title: "Administración de registros de DNS en Azure DNS mediante la CLI de Azure 1.0 | Microsoft Docs"
description: "Administración de conjuntos de registros y registros DNS en DNS de Azure al hospedar dominios en DNS de Azure. Todos los comandos de la CLI 1.0 para operaciones en conjuntos de registros y registros."
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
ms.date: 12/20/2016
ms.author: jonatul
ms.openlocfilehash: 307b327e4c04a0461e39930114eb193791cbda9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-dns-records-in-azure-dns-using-the-azure-cli-10"></a>Administración de registros de DNS en Azure DNS mediante la CLI de Azure 1.0

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-operations-recordsets-portal.md)
> * [CLI de Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

En este artículo se muestra cómo administrar los registros de DNS para su zona DNS mediante la interfaz de la línea de comandos (CLI) multiplataforma de Azure, que se encuentra disponible para Windows, Mac y Linux. También puede administrar sus registros de DNS mediante [Azure PowerShell](dns-operations-recordsets.md) o [Azure Portal](dns-operations-recordsets-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

* [CLI de Azure 1.0](dns-operations-recordsets-cli-nodejs.md): la CLI para los modelos de implementación clásica y de administración de recursos.
* [CLI de Azure 2.0](dns-operations-recordsets-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos.

En los ejemplos de este artículo se supone que ya ha [instalado la CLI de Azure 1.0, iniciado sesión y creado una zona DNS](dns-operations-dnszones-cli-nodejs.md).

## <a name="introduction"></a>Introducción

Antes de crear registros DNS en Azure DNS, es necesario que comprenda cómo Azure DNS los organiza en conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para más información sobre los registros DNS en Azure DNS, consulte [DNS zones and records](dns-zones-records.md) (Zonas y registros DNS).

## <a name="create-a-dns-record"></a>Creación de un registro de DNS

Para crear un registro de DNS, use el comando `azure network dns record-set add-record`. Para obtener ayuda, consulte `azure network dns record-set add-record -h`.

Al crear un registro, debe especificar los nombres del grupo de recursos, de la zona y del conjunto de registros, el tipo de registro y los detalles del registro que se están creando. El nombre del conjunto de registros especificado debe ser un nombre *relativo*; es decir, debe excluir el nombre de zona.

Si el conjunto de registros aún no existe, este comando lo crea automáticamente. Si el conjunto de registros ya existe, este comando agrega el registro que especifique al conjunto de registros existente.

Si se crea un conjunto de registros, se utiliza el valor predeterminado del período de vida (3600). Para obtener instrucciones sobre cómo usar diferentes TTL, consulte [Creación de un conjunto de registros de DNS](#create-a-dns-record-set).

En el ejemplo siguiente se crea un registro A denominado *www* en la zona *contoso.com* del grupo de recursos *MyResourceGroup*. La dirección IP del registro A es *1.2.3.4*.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

Para crear un registro en el vértice de la zona (en este caso, "contoso.com"), use el nombre de registro "@", incluidas las comillas:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" A -a 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Creación de un conjunto de registros de DNS

En los ejemplos anteriores, bien se agregó el registro de DNS en un conjunto de registros existente, bien se creó el conjunto de registros *implícitamente*. También puede crear el conjunto de registros *explícitamente* antes de agregarle registros. La DNS de Azure admite conjuntos de registros "vacíos" que pueden funcionar como marcador de posición para reservar un nombre DNS antes de crear registros de DNS. Los conjuntos de registros vacíos se pueden ver en el panel de control de DNS de Azure, pero no aparecen en los servidores de nombres de DNS de Azure.

Los conjuntos de registros se crean con el comando `azure network dns record-set create`. Para obtener ayuda, consulte `azure network dns record-set create -h`.

Al crear el conjunto de registros explícitamente, podrá especificar las propiedades de este, como el [período de vida (TTL)](dns-zones-records.md#time-to-live) y los metadatos. Se pueden usar [metadatos del conjunto de registros](dns-zones-records.md#tags-and-metadata) para asociar datos específicos de la aplicación con cada conjunto de registros como pares clave-valor.

En el ejemplo siguiente se crea un conjunto de registros vacío con un TTL de 60 segundos mediante el parámetro `--ttl` (forma abreviada `-l`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --ttl 60
```

En el siguiente ejemplo, se crea un conjunto de registros con dos entradas de metadatos, "dept=finance" y "environment=production", mediante el parámetro `--metadata` (forma abreviada `-m`):

```azurecli
azure network dns record-set create MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

Tras haber creado un conjunto de registros vacío, se podrán añadir el registro mediante `azure network dns record-set add-record`, como se describe en [Creación de un registro de DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Creación de registros de otros tipos

Después de haber visto de forma detallada cómo crear registros "A", en los siguientes ejemplos se muestra cómo crear registros de otros tipos compatibles con DNS de Azure.

Los parámetros utilizados para especificar los datos del registro varían según el tipo del registro. Por ejemplo, para un registro de tipo "A", especifique la dirección IPv4 con el parámetro `-a <IPv4 address>`. Es posible obtener una lista de los parámetros de cada tipo de registro con `azure network dns record-set add-record -h`.

En cada caso, se muestra cómo crear un único registro. Se agrega el registro al conjunto de registros existente o a uno creado de forma implícita. Para obtener más información sobre cómo crear conjuntos de registros y definir explícitamente los parámetros de un conjunto de registros, consulte [Creación de un conjunto de registros de DNS](#create-a-dns-record-set).

No se proporciona un ejemplo para crear un conjunto de registros SOA, dado que los registros SOA se crean y eliminan con cada zona de DNS y no lo pueden hacer por separado. Sin embargo, [el registro SOA se puede modificar, como se muestra en un ejemplo más adelante](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Creación de un registro AAAA

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-aaaa AAAA --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-a-cname-record"></a>Creación de un registro CNAME

> [!NOTE]
> Los estándares DNS no permiten registros CNAME en el vértice de una zona (`-Name "@"`), ni permiten conjuntos de registros que contengan más de un registro.
> 
> Para más información, consulte [Registros CNAME](dns-zones-records.md#cname-records).

```azurecli
azure network dns record-set add-record  MyResourceGroup contoso.com  test-cname CNAME --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Creación de un registro MX

En este ejemplo, se utiliza el nombre de conjunto de registros "@" para crear el registro MX en el vértice de la zona (en este caso, "contoso.com").

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "@" MX --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Creación de un registro NS

```azurecli
azure network dns record-set add-record MyResourceGroup  contoso.com  test-ns NS --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Creación de un registro PTR

En este caso "my-arpa-zone.com" representa la zona ARPA que representa el intervalo IP. Cada registro PTR establecido en esta zona se corresponde con una dirección IP dentro de este intervalo IP.  El nombre de registro "10" es el último octeto de la dirección IP dentro del intervalo IP que representa dicho registro.

```azurecli
azure network dns record-set add-record MyResourceGroup my-arpa-zone.com "10" PTR --ptrdname "myservice.contoso.com"
```

### <a name="create-an-srv-record"></a>Creación de un registro SRV

Al crear un [conjunto de registros SRV](dns-zones-records.md#srv-records), especifique el *\_servicio* y el *\_protocolo* en el nombre del conjunto de registros. No es necesario incluir "@" en el nombre del conjunto de registros al crear un conjunto de registros SRV en el vértice de la zona.

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com  "_sip._tls" SRV --priority 10 --weight 5 --port 8080 --target "sip.contoso.com"
```

### <a name="create-a-txt-record"></a>Creación de un registro TXT

En el ejemplo siguiente se muestra cómo crear un registro TXT. Para más información sobre la longitud de cadena máxima admitida en registros TXT, consulte [Registros TXT](dns-zones-records.md#txt-records).

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com test-txt TXT --text "This is a TXT record"
```

## <a name="get-a-record-set"></a>Recuperación de un conjunto de registros

Para recuperar un conjunto de registros existente, use `azure network dns record-set show`. Para obtener ayuda, consulte `azure network dns record-set show -h`.

Al igual que sucede con la creación de un registro o conjunto de registros, el nombre del conjunto de registros especificado debe ser un nombre *relativo*, lo que significa que debe excluir el nombre de zona. También debe especificar el tipo de registro, la zona que contiene el conjunto de registros y el grupo de recursos que contiene la zona.

En el ejemplo siguiente se recupera el registro *www* de tipo A de la zona *contoso.com*, que se encuentra en el grupo de recursos *MyResourceGroup*:

```azurecli
azure network dns record-set show MyResourceGroup contoso.com www A
```

## <a name="list-record-sets"></a>Enumeración de conjuntos de registros

Puede mostrar todos los registros de una zona DNS con el comando `azure network dns record-set list` . Para obtener ayuda, consulte `azure network dns record-set list -h`.

En este ejemplo, se devuelve todos los conjuntos de registros de la zona *contoso.com*, que se encuentra en el grupo de recursos *MyResourceGroup*, con independencia del nombre o del tipo de registro:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com
```

En este ejemplo, se devuelven todos los conjuntos de registros que coinciden con el tipo de registro especificado (en este caso, los registros "A"):

```azurecli
azure network dns record-set list MyResourceGroup contoso.com --type A
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adición de un registro a un conjunto de registros existente

Puede usar `azure network dns record-set add-record` tanto para crear un registro en un nuevo conjunto de registros como para agregar un registro a un conjunto de registros existente.

Para obtener más información, consulte las secciones [Creación de un registro de DNS](#create-a-dns-record) y [Creación de registros de otros tipos](#create-records-of-other-types).

## <a name="remove-a-record-from-an-existing-record-set"></a>Quite un registro de un conjunto de registros existente.

Para quitar un registro de DNS de un conjunto de registros existente, use `azure network dns record-set delete-record`. Para obtener ayuda, consulte `azure network dns record-set delete-record -h`.

Este comando elimina un registro de DNS de un conjunto de registros. Aunque se elimine el último registro de un conjunto de registros, dicho conjunto de registros **no** se elimina. En su lugar, se conservará un conjunto de registros vacío. Si desea eliminar el conjunto de registros, consulte [Eliminación de un conjunto de registros](#delete-a-record-set).

Debe especificar el registro que desee eliminar y la zona de la que se debe eliminar; para ello, use los mismos parámetros empleados al crear el registro con `azure network dns record-set add-record`. Estos parámetros se describen en las secciones [Creación de un registro de DNS](#create-a-dns-record) y [Creación de registros de otros tipos](#create-records-of-other-types).

Este comando solicita confirmación. Este mensaje se puede suprimir con el modificador `--quiet` (forma abreviada `-q`).

En el ejemplo siguiente se elimina el registro A con el valor "1.2.3.4" del conjunto de registros con el nombre *www* de la zona *contoso.com*, que se encuentra en el grupo de recursos *MyResourceGroup*. Se suprime el mensaje de confirmación.

```azurecli
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4 --quiet
```

## <a name="modify-an-existing-record-set"></a>Modificación de un conjunto de registros existente

Cada conjunto de registros contiene un [time-to-live (TTL)](dns-zones-records.md#time-to-live), [metadatos](dns-zones-records.md#tags-and-metadata)y los registros de DNS. En las siguientes secciones se explican cómo modificar cada una de estas propiedades.

### <a name="to-modify-an-a-aaaa-mx-ns-ptr-srv-or-txt-record"></a>Para modificar un registro A, AAAA, MX, NS, PTR, SRV o TXT

Para modificar un registro existente de tipo A, AAAA, MX, NS, PTR, SRV o TXT, debe agregar primero un nuevo registro y, después, eliminar el existente. Para obtener instrucciones detalladas sobre cómo eliminar y agregar registros, consulte las secciones anteriores de este artículo.

En el ejemplo siguiente se muestra cómo modificar un registro "A", de la dirección IP 1.2.3.4 a la 5.6.7.8:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www A -a 5.6.7.8
azure network dns record-set delete-record MyResourceGroup contoso.com www A -a 1.2.3.4
```

### <a name="to-modify-a-cname-record"></a>Para modificar un registro CNAME

Si desea modificar un registro CNAME, utilice `azure network dns record-set add-record` para agregar el nuevo valor del registro. A diferencia de otros tipos de registros, un conjunto de registros CNAME solo puede contener un único registro. Por lo tanto, el registro existente se *sustituye* cuando se agrega el nuevo registro y no hace falta eliminarlo por separado.  Se le pedirá que acepte esta sustitución.

En el ejemplo se modifica el conjunto de registros CNAME *www* de la zona *contoso.com*, que se encuentra en el grupo de recursos *MyResourceGroup*, para que apunte a "www.fabrikam.net" en lugar de a su valor existente:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com www CNAME --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Para modificar un registro SOA

Utilice `azure network dns record-set set-soa-record` para modificar el SOA de una zona DNS especificada. Para obtener ayuda, consulte `azure network dns record-set set-soa-record -h`.

En el ejemplo siguiente se muestra cómo establecer la propiedad "email" del registro SOA de la zona *contoso.com* del grupo de recursos *MyResourceGroup*:

```azurecli
azure network dns record-set set-soa-record rg1 contoso.com --email admin.contoso.com
```


### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar los registros NS en el vértice de zona

El conjunto de registros NS en el vértice de zona se crea automáticamente con cada zona DNS. Este conjunto de registros contiene los nombres de los servidores de nombres de Azure DNS asignados a la zona.

Puede agregar más servidores de nombres a este conjunto de registros NS, para admitir dominios de hospedaje conjunto con más de un proveedor DNS. También puede modificar el TTL y los metadatos de este conjunto de registros. Sin embargo, no puede quitar ni modificar los servidores de nombres de Azure DNS rellenados previamente.

Tenga en cuenta que esto solo se aplica al conjunto de registros NS en el vértice de zona. Otros conjuntos de registros NS de su zona (como los que se usan para delegar zonas secundarias) se pueden modificar sin restricciones.

En el ejemplo siguiente se muestra cómo agregar un servidor de nombres adicional al conjunto de registros NS en el vértice de zona:

```azurecli
azure network dns record-set add-record MyResourceGroup contoso.com "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Para modificar el TTL de un conjunto de registros existente

Para modificar el TTL de un conjunto de registros existente, utilice `azure network dns record-set set`. Para obtener ayuda, consulte `azure network dns record-set set -h`.

En el ejemplo siguiente se muestra cómo modificar el TTL de un conjunto de registros, en este caso a 60 segundos:

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --ttl 60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Para modificar los metadatos de un conjunto de registros existente

Se pueden usar [metadatos del conjunto de registros](dns-zones-records.md#tags-and-metadata) para asociar datos específicos de la aplicación con cada conjunto de registros como pares clave-valor. Para modificar los metadatos de un conjunto de registros existente, utilice `azure network dns record-set set`. Para obtener ayuda, consulte `azure network dns record-set set -h`.

En el siguiente ejemplo, se muestra cómo modificar un conjunto de registros con dos entradas de metadatos, "dept=finance" y "environment=production", mediante el parámetro `--metadata` (forma abreviada `-m`): Tenga en cuenta que los metadatos existentes se *sustituyen* por los valores especificados.

```azurecli
azure network dns record-set set MyResourceGroup contoso.com www A --metadata "dept=finance;environment=production"
```

## <a name="delete-a-record-set"></a>Eliminación de un conjunto de registros

Los conjuntos de registros pueden eliminarse mediante el comando `azure network dns record-set delete`. Para obtener ayuda, consulte `azure network dns record-set delete -h`. Al eliminar un conjunto de registros también se eliminan todos los registros que contiene.

> [!NOTE]
> No se pueden eliminar los conjuntos de registros SOA y NS en el vértice de zona (`-Name "@"`).  Se crean automáticamente cuando se crea la zona y se eliminan automáticamente cuando se elimina.

En el ejemplo siguiente se elimina el conjunto de registros con el nombre *www* de tipo A de la zona *contoso.com*, la cual se encuentra en el grupo de recursos *MyResourceGroup*:

```azurecli
azure network dns record-set delete MyResourceGroup contoso.com www A
```

Se le pide que confirme la operación de eliminación. Para suprimir este mensaje, utilice el modificador `--quiet` (forma abreviada `-q`).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [zonas y registros en Azure DNS](dns-zones-records.md).
<br>
Aprenda a [proteger las zonas y los registros](dns-protect-zones-recordsets.md) cuando se usa Azure DNS.
