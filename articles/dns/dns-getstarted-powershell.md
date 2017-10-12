---
title: "Introducción a Azure DNS PowerShell | Microsoft Docs"
description: "Obtenga información sobre cómo crear una zona y un registro DNS en Azure DNS. Esta es una guía paso a paso para crear y administrar su primera zona y su primer registro DNS con PowerShell."
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: 48f7ba325f61b4a91c0208b4c99058da801bee19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-using-powershell"></a>Introducción a DNS de Azure con PowerShell

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [CLI de Azure 1.0](dns-getstarted-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-getstarted-cli.md)

Este artículo lo guiará por los pasos necesarios para crear una zona DNS con Azure PowerShell. También puede llevar a cabo estos pasos con Azure Portal o la CLI de Azure multiplataforma.

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Finalmente, para publicar la zona DNS en Internet, debe configurar los servidores de nombres para el dominio. A continuación, se describen cada uno de estos pasos.

En estas instrucciones se da por hecho que ya haya instalado Azure PowerShell e iniciado sesión en ella. Para obtener ayuda, consulte [Cómo administrar zonas DNS en Azure DNS con PowerShell](dns-operations-dnszones.md).

## <a name="create-the-resource-group"></a>Creación del grupo de recursos

Antes de crear la zona DNS, se crea un grupo de recursos para que contenga la zona DNS. A continuación, se muestra el comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

Una zona DNS se crea mediante el cmdlet `New-AzureRmDnsZone` . En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Creación de un registro de DNS

Los conjuntos de registros se crean mediante el cmdlet `New-AzureRmDnsRecordSet`. En el ejemplo siguiente se crea un conjunto de registros con el nombre relativo "www" en la zona DNS "contoso.com" del grupo de recursos "MyResourceGroup". El nombre completo del conjunto de registros es www.contoso.com. El tipo de registro es "A", con la dirección IP 1.2.3.4 y el valor de TTL es de 3600 segundos.

```powershell
New-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Para ver otros tipos de registros y conjuntos de registros con más de un registro, y para modificar los registros existentes, consulte [Administración de registros de DNS en Azure DNS mediante Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Visualización de los registros

Para enumerar los registros DNS de su zona, use lo siguiente:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```


## <a name="update-name-servers"></a>Actualización de los servidores de nombres

Una vez haya comprobado que la zona y los registros DNS se han configurado correctamente, tiene que configurar el nombre de dominio para usar los servidores de nombres DNS de Azure. De esta forma, otros usuarios en Internet podrán encontrar los registros DNS.

Los servidores de nombres de su zona se proporcionan con el cmdlet `Get-AzureRmDnsZone`:

```powershell
Get-AzureRmDnsZone -ZoneName contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

Estos servidores de nombres deben configurarse con el registrador de nombres de dominio (donde adquirió el nombre de dominio). El registrador ofrece la opción de configurar los servidores de nombres para el dominio. Si quiere obtener más información, consulte [Delegación del dominio en DNS de Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Para eliminar todos los recursos creados en este artículo, realice el paso siguiente:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre Azure DNS, lea [Introducción a DNS de Azure](dns-overview.md).

Si quiere saber cómo administrar zonas DNS en Azure DNS, consulte [Cómo administrar zonas DNS en Azure DNS con PowerShell](dns-operations-dnszones.md).

Para aprender a administrar registros DNS en Azure DNS, consulte [Administración de registros de DNS en Azure DNS mediante PowerShell](dns-operations-recordsets.md).

