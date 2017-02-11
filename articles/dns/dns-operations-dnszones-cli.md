---
title: "Administración de zonas DNS con CLI | Microsoft Docs"
description: "Puede administrar zonas DNS con CLI de Azure. Actualización, eliminación y creación de zonas DNS en DNS de Azure"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 927503bb18a63db1da2c92e034dbccb7707afab4

---

# <a name="how-to-manage-dns-zones-using-cli"></a>Cómo administrar zonas DNS con CLI

> [!div class="op_single_selector"]
> * [CLI de Azure](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

Esta guía explica le mostrará cómo administrar los recursos de zona DNS mediante la CLI de Azure multiplataforma.

En estas instrucciones se usa la CLI de Microsoft Azure. Asegúrese de actualizar a la CLI de Azure más reciente para utilizar los comandos de Azure DNS. Puede instalar la CLI de Azure para Windows, Linux o MAC. Puede consultar mas información disponible en [Instalación de la CLI de Azure](../xplat-cli-install.md).

DNS de Azure es un servicio exclusivo del Administrador de recursos de Azure. No tiene un modelo de implementación "clásico". Deberá asegurarse de que la CLI de Azure está configurada para utilizar el modo de Resource Manager. Puede hacerlo si utiliza el comando `azure config mode arm`.

Si ve el mensaje " *error: 'dns' is not an azure command*", lo más probable es que se deba a que está usando CLI de Azure en modo Azure Service Management, no en el modo de Resource Manager.

Los comandos de la CLI relacionados con Azure DNS comienzan por `azure network dns`. La ayuda está disponible para cada comando con la opción `--help` (formato abreviado `-h`).  Por ejemplo:

```azurecli
azure network dns -h
azure network dns zone -h
azure network dns zone create -h
```

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

Una zona DNS se crea con el comando `azure network dns zone create` . Para obtener ayuda, consulte `azure network dns zone create -h`.

Los ejemplos siguientes muestran cómo crear una zona DNS con o sin [etiquetas de Azure Resource Manager](dns-zones-records.md#tags).

### <a name="to-create-a-dns-zone"></a>Creación de una zona DNS

En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear la zona DNS, sustituyendo los valores por los suyos.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Para crear una zona DNS con etiquetas.

En el ejemplo siguiente se muestra cómo crear una zona DNS con dos etiquetas, *project = demo* y *env = test*, con el parámetro `--tags` (forma abreviada `-t`).

```azurecli
azure network dns zone create MyResourceGroup contoso.com -t "project=demo";"env=test"
```

## <a name="get-a-dns-zone"></a>Recuperación de una zona DNS

Para recuperar una zona DNS, use `azure network dns zone show`. Para obtener ayuda, consulte `azure network dns zone show -h`.

El ejemplo siguiente devuelve la zona DNS *contoso.com* y sus datos asociados del grupo de recursos *MyResourceGroup*. 

```azurecli
azure network dns zone show MyResourceGroup contoso.com

info:    Executing command network dns zone show
+ Looking up the dns zone "contoso.com"
data:    Id                              : /subscriptions/.../contoso.com
data:    Name                            : contoso.com
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Tags                            : project=demo;env=test
info:    network dns zone show command OK
```

Tenga en cuenta que `azure network dns zone show` no devuelve los registros DNS. Para enumerar los registros DNS, utilice `azure network dns record-set list`.


## <a name="list-dns-zones"></a>Enumeración de zonas DNS

Para enumerar las zonas DNS, utilice `azure network dns zone list`. Para obtener ayuda, consulte `azure network dns zone list -h`.

Si se especifica el grupo de recursos, se enumeran solo esas zonas dentro del grupo de recursos:

```azurecli
azure network dns zone list MyResourceGroup
```

Si se omite el grupo de recursos, se enumeran todas las zonas en la suscripción:

```azurecli
azure network dns zone list 
```

## <a name="update-a-dns-zone"></a>Actualización de una zona DNS

Los cambios en los recursos de una zona DNS se pueden realizar con `azure network dns zone set`. Para obtener ayuda, consulte `azure network dns zone set -h`.

Este comando no actualiza los conjuntos de registros DNS de la zona (consulte [Administración de registros DNS](dns-operations-recordsets.md)). Solo se utiliza para actualizar las propiedades de los recursos de la zona. Estas propiedades están actualmente limitadas a las ["etiquetas" Azure Resource Manager ](dns-zones-records.md#tags) para los recursos de la zona.

En el ejemplo siguiente se muestra cómo actualizar las etiquetas en una zona DNS. Las etiquetas existentes se reemplazan por el valor especificado.

```azurecli
azure network dns zone set MyResourceGroup contoso.com -t "team=support"
```

## <a name="delete-a-dns-zone"></a>Eliminación de una zona DNS

Las zonas DNS se pueden eliminar mediante `azure network dns zone delete`. Para obtener ayuda, consulte `azure network dns zone delete -h`.

> [!NOTE]
> Si se elimina una zona DNS, también se eliminarán todos los registros DNS en la zona. Esta operación no se puede deshacer. Si la zona DNS está en uso, los servicios con la zona provocarán un error cuando se elimina la zona.
>
>Para protegerse contra la eliminación accidental de zona, consulte [Proteger registros y zonas DNS](dns-protect-zones-recordsets.md).

Este comando solicita confirmación. El conmutador `--quiet` opcional (forma abreviada `-q`) elimina esta confirmación.

En el ejemplo siguiente se muestra cómo eliminar la zona *contoso.com* del grupo de recursos *MyResourceGroup*.

```azurecli
azure network dns zone delete MyResourceGroup contoso.com
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía se explica cómo [administrar conjuntos de registros y registros](dns-getstarted-create-recordset-cli.md) en la zona DNS.
<br>
Aprenda a cómo [delegar el dominio a Azure DNS](dns-domain-delegation.md).




<!--HONumber=Dec16_HO2-->


