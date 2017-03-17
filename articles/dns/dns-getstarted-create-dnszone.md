---
title: "Introducción a DNS de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo crear zonas DNS para Azure DNS. Se trata de una guía paso a paso para crear y administrar su primera zona DNS con Azure PowerShell."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>Creación de una zona DNS mediante PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI de Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-getstarted-create-dnszone-cli.md)

Este artículo lo guiará por los pasos necesarios para crear una zona DNS con Azure PowerShell. También se puede crear una zona DNS mediante la [CLI de Azure](dns-getstarted-create-dnszone-cli.md) multiplataforma o [Azure Portal](dns-getstarted-create-dnszone-portal.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Creación de una zona DNS

Una zona DNS se crea mediante el cmdlet `New-AzureRmDnsZone` . En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>Verificación de la zona DNS

### <a name="view-records"></a>Visualización de los registros

Al crear una zona DNS, también se crean los siguientes registros DNS:

* El registro *Inicio de autoridad* (SOA). Este registro se encuentra en la raíz de cada zona DNS.
* Los registros de servidor de nombres (NS) autoritativo. Estos registros muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Para más información, consulte [Delegación de dominios en Azure DNS](dns-domain-delegation.md).

Para ver estos registros, use `Get-AzureRmDnsRecordSet`:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> Los conjuntos de registros de la raíz (o la *cúspide*) de una zona DNS usan **@** como el nombre del conjunto de registros.

### <a name="test-name-servers"></a>Prueba de los servidores de nombres

Puede probar que la zona DNS esté presente en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el [cmdlet de PowerShell Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado el dominio para usar la nueva zona en DNS en Azure, deberá dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en los registros de NS, como se muestra con el comando `Get-AzureRmDnsRecordSet` mencionado anteriormente. Asegúrese de sustituir los valores correctos de su zona en el siguiente ejemplo:

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>Pasos siguientes

Después de crear una zona DNS, [cree registros y conjuntos de registros](dns-getstarted-create-recordset.md) para crear los registros DNS para el dominio de Internet.

