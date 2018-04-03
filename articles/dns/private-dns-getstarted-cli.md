---
title: Introducción al uso de la CLI de Azure 2.0 con Azure DNS Private Zones | Microsoft Docs
description: Aprenda a crear una zona y un registro DNS privados en Azure DNS. Esta es una guía paso a paso para crear y administrar la primera zona y el primer registro DNS privados con la CLI de Azure 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Introducción al uso de la CLI de Azure 2.0 con Azure DNS Private Zones

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [CLI de Azure 2.0](private-dns-getstarted-cli.md)

En este artículo se recorren los pasos necesarios para la primera zona y el primer registro DNS privados mediante la CLI de Azure 2.0 multiplataforma, que está disponible para Windows, Mac y Linux. Estos pasos también se pueden realizar con Azure PowerShell.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Para publicar una zona DNS privada en la red virtual, especifique la lista de redes virtuales que pueden resolver registros en ella.  Se denominan "redes virtuales de resolución".  También puede especificar una red virtual para la que Azure DNS mantiene los registros de nombre de host siempre que se cree o se destruya una máquina virtual o se cambie su dirección IP.  Se denominan "redes virtuales de registro".

En estas instrucciones se supone que ya ha instalado la CLI de Azure 2.0 y ha iniciado sesión, y que también ha instalado la extensión de la CLI necesaria que admite zonas privadas. Para obtener ayuda, consulte [Cómo administrar zonas DNS en Azure DNS con la CLI de Azure 2.0](dns-operations-dnszones-cli.md).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Para instalar o usar la característica Azure DNS Private Zones (versión preliminar pública)
La característica Azure DNS Private Zones se publica en versión preliminar pública a través de una extensión de la CLI de Azure. Instale la extensión de la CLI de Azure de "dns" 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la zona DNS, se crea un grupo de recursos para que contenga la zona DNS. A continuación, se muestra el comando.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Creación de una zona DNS privada

Las zonas DNS privadas se crean con el comando `az network dns zone create`. Para ver la ayuda sobre este comando, escriba `az network dns zone create --help`.

En el ejemplo siguiente se crea una zona privada de DNS denominada *contoso.local* en el grupo de recursos *MyResourceGroup* y la pone a disposición (la vincula a) de la red virtual *MyAzureVnet* mediante el parámetro resolution-vnets. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Nota: En el ejemplo anterior, la red virtual "MyAzureVnet" pertenece al mismo grupo de recursos y suscripción que la zona privada. Si necesita vincular una red virtual que pertenece a otro grupo de recursos o suscripción, debe especificar el identificador completo de Azure Resource Manager, en lugar de solo el nombre de la red virtual para el parámetro --resolution-vnets. 

Si necesita que Azure cree automáticamente registros de nombre de host en la zona, use el parámetro *registration-vnets*, en lugar de *resolution-vnets*.  Las redes virtuales de registro se habilitan automáticamente para la resolución.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Creación de un registro de DNS

Para crear un registro de DNS, use el comando `az network dns record-set [record type] add-record`. Para obtener ayuda con los registros A, por ejemplo, vea `azure network dns record-set A add-record --help`.

En el ejemplo siguiente se crea un registro con el nombre relativo "ip1" en la zona DNS "contoso.local" del grupo de recursos "MyResourceGroup". El nombre completo del conjunto de registros es "ip1.contoso.local". El tipo de registro es "A", con la dirección IP 10.0.0.1.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Para ver otros tipos de registros, conjuntos de registros con más de un registro, valores de TTL alternativos, y para modificar los registros existentes, consulte [Administración de registros de DNS en Azure DNS mediante la CLI de Azure 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Visualización de los registros

Para enumerar los registros DNS de su zona, use lo siguiente:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Obtención de una zona DNS privada

Para recuperar una zona DNS privada, use `az network dns zone show`. Para obtener ayuda, consulte `az network dns zone show --help`.

El ejemplo siguiente devuelve la zona DNS *contoso.local* y sus datos asociados del grupo de recursos *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

El ejemplo siguiente es la respuesta.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
}
```

Tenga en cuenta que `az network dns zone show` no devuelve los registros DNS. Para enumerar los registros DNS, utilice `az network dns record-set list`.


## <a name="list-dns-zones"></a>Enumeración de zonas DNS

Para enumerar las zonas DNS, utilice `az network dns zone list`. Para obtener ayuda, consulte `az network dns zone list --help`.

Si se especifica el grupo de recursos, se enumeran solo esas zonas dentro del grupo de recursos:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Si se omite el grupo de recursos, se enumeran todas las zonas en la suscripción:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Actualización de una zona DNS

Los cambios en los recursos de una zona DNS se pueden realizar con `az network dns zone update`. Para obtener ayuda, consulte `az network dns zone update --help`.

Este comando no actualiza los conjuntos de registros DNS de la zona (consulte [Administración de registros DNS](dns-operations-recordsets-cli.md)). Solo se utiliza para actualizar las propiedades de los recursos de la zona. En las zonas privadas se pueden actualizar las redes virtuales de resolución o de registro vinculadas a una zona. 

En el ejemplo siguiente se muestra cómo actualizar la red virtual de resolución vinculada a una zona DNS privada. La red virtual de resolución vinculada existente se reemplaza por la nueva red virtual especificada.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Eliminar una zona DNS

Las zonas DNS se pueden eliminar mediante `az network dns zone delete`. Para obtener ayuda, consulte `az network dns zone delete --help`.

> [!NOTE]
> Si se elimina una zona DNS, también se eliminan todos los registros DNS dentro de la zona. Esta operación no se puede deshacer. Si la zona DNS está en uso, los servicios con la zona provocarán un error cuando se elimina la zona.
>
>Para protegerse contra la eliminación accidental de zona, consulte [Proteger registros y zonas DNS](dns-protect-zones-recordsets.md).

Este comando solicita confirmación. El modificador `--yes` opcional suprime este mensaje.

En el siguiente ejemplo se muestra cómo eliminar la zona *contoso.local* del grupo de recursos *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Eliminación de todos los recursos
 
Para eliminar todos los recursos creados en este artículo, realice el paso siguiente:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure DNS, lea [Introducción a DNS de Azure](dns-overview.md).

Si quiere saber cómo administrar zonas DNS en Azure DNS, consulte [Cómo administrar zonas DNS en Azure DNS con la CLI de Azure 2.0](dns-operations-dnszones-cli.md).

Para aprender a administrar registros DNS en Azure DNS, consulte [Administración de registros de DNS en Azure DNS mediante la CLI de Azure 2.0](dns-operations-recordsets-cli.md).
