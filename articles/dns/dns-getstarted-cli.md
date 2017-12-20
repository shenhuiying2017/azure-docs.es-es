---
title: "Introducción a Azure DNS con la CLI de Azure 2.0 | Microsoft Docs"
description: "Obtenga información sobre cómo crear una zona y un registro DNS en Azure DNS. Esta es una guía paso a paso para crear y administrar su primera zona y su primer registro DNS con la CLI de Azure 2.0."
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: 76782ac1e78cd0f7da4bc1aad8eff00d79865ed7
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Introducción a Azure DNS con la CLI de Azure 2.0

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI de Azure 2.0](dns-getstarted-cli.md)

Este artículo lo guiará por los pasos necesarios para crear su primera zona y su primer registro DNS mediante la CLI de Azure 2.0 multiplataforma, que está disponible para Windows, Mac y Linux. También puede llevar a cabo estos pasos con Azure Portal o Azure PowerShell.

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Finalmente, para publicar la zona DNS en Internet, debe configurar los servidores de nombres para el dominio. A continuación, se describen cada uno de estos pasos.

En estas instrucciones se da por hecho que ya haya instalado la CLI de Azure 2.0 e iniciado sesión en ella. Para obtener ayuda, consulte [Cómo administrar zonas DNS en Azure DNS con la CLI de Azure 2.0](dns-operations-dnszones-cli.md).

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la zona DNS, se crea un grupo de recursos para que contenga la zona DNS. A continuación, se muestra el comando.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

Una zona DNS se crea con el comando `az network dns zone create` . Para ver la ayuda sobre este comando, escriba `az network dns zone create -h`.

En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```


## <a name="create-a-dns-record"></a>Creación de un registro de DNS

Para crear un registro de DNS, use el comando `az network dns record-set [record type] add-record`. Para obtener ayuda con los registros A, por ejemplo, vea `azure network dns record-set A add-record -h`.

En el ejemplo siguiente se crea un conjunto de registros con el nombre relativo "www" en la zona DNS "contoso.com" del grupo de recursos "MyResourceGroup". El nombre completo del conjunto de registros es www.contoso.com. El tipo de registro es "A", con la dirección IP "1.2.3.4", y se utiliza un valor predeterminado TTL de 3600 segundos (1 hora).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Para ver otros tipos de registros, conjuntos de registros con más de un registro, valores de TTL alternativos, y para modificar los registros existentes, consulte [Administración de registros de DNS en Azure DNS mediante la CLI de Azure 2.0](dns-operations-recordsets-cli.md).


## <a name="view-records"></a>Visualización de los registros

Para enumerar los registros DNS de su zona, use lo siguiente:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```


## <a name="update-name-servers"></a>Actualización de los servidores de nombres

Una vez haya comprobado que la zona y los registros DNS se han configurado correctamente, tiene que configurar el nombre de dominio para usar los servidores de nombres DNS de Azure. De esta forma, otros usuarios en Internet podrán encontrar los registros DNS.

Los servidores de nombres de su zona se proporcionan con el comando `az network dns zone show`. Para ver los nombres del servidor de nombres, utilice la salida JSON, como se muestra en el ejemplo siguiente.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Estos servidores de nombres deben configurarse con el registrador de nombres de dominio (donde adquirió el nombre de dominio). El registrador ofrece la opción de configurar los servidores de nombres para el dominio. Si quiere obtener más información, consulte [Delegación del dominio en DNS de Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Eliminación de todos los recursos
 
Para eliminar todos los recursos creados en este artículo, realice el paso siguiente:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure DNS, lea [Introducción a DNS de Azure](dns-overview.md).

Si quiere saber cómo administrar zonas DNS en Azure DNS, consulte [Cómo administrar zonas DNS en Azure DNS con la CLI de Azure 2.0](dns-operations-dnszones-cli.md).

Para aprender a administrar registros DNS en Azure DNS, consulte [Administración de registros de DNS en Azure DNS mediante la CLI de Azure 2.0](dns-operations-recordsets-cli.md).
