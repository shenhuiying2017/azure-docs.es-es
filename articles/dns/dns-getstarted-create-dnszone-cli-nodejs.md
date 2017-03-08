---
title: "Creación de una zona DNS mediante la CLI de Azure 1.0 | Microsoft Docs"
description: "Obtenga información sobre cómo crear zonas DNS para Azure DNS. Esta es una guía paso a paso para crear y administrar su primera zona DNS con la CLI de Azure 1.0."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 1e8645e8554bc8f8ec72a6df98bfe9ffc5be13b3
ms.lasthandoff: 02/27/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-10"></a>Creación de una zona de Azure DNS con la CLI de Azure 1.0

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI de Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-getstarted-create-dnszone-cli.md)

Este artículo le guiará por los pasos necesarios para crear una zona DNS mediante la CLI de Azure multiplataforma, que está disponible para Windows, Mac y Linux. También puede crear una zona DNS con [Azure PowerShell](dns-getstarted-create-dnszone.md) o [Azure Portal](dns-getstarted-create-dnszone-portal.md).

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

* [CLI de Azure 1.0](dns-getstarted-create-dnszone-cli-nodejs.md): la CLI para los modelos de implementación clásica y de administración de recursos.
* [CLI de Azure 2.0 ](dns-getstarted-create-dnszone-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos.

## <a name="introduction"></a>Introducción

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

Una zona DNS se crea con el comando `azure network dns zone create` . Para ver la ayuda sobre este comando, escriba `azure network dns zone create -h`.

En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificación de la zona DNS

### <a name="view-records"></a>Visualización de los registros

Al crear una zona DNS, también se crean los siguientes registros DNS:

* El registro *Inicio de autoridad* (SOA). Este registro se encuentra en la raíz de cada zona DNS.
* Los registros de servidor de nombres (NS) autoritativo. Estos registros muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Para más información, consulte [Delegación de dominios en Azure DNS](dns-domain-delegation.md).

Para ver estos registros, use `azure network dns-record-set list`:

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> Los conjuntos de registros de la raíz (o la *cúspide*) de una zona DNS usan **@** como el nombre del conjunto de registros.

### <a name="test-name-servers"></a>Prueba de los servidores de nombres

Puede probar que la zona DNS está presente en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el cmdlet de PowerShell `Resolve-DnsName`.

Si aún no ha delegado el dominio para usar la nueva zona en DNS en Azure, deberá dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en los registros de NS, como se muestra con el comando `azure network dns record-set list`.

En el ejemplo siguiente se usa DIG para consultar el dominio contoso.com con los servidores de nombres asignados a la zona DNS. Asegúrese de sustituir los valores correctos de su zona.

```
  > dig @ns1-01.azure-dns.com contoso.com
  
  <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
(1 server found)
global options: +cmd
  Got answer:
->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
  flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
  WARNING: recursion requested but not available

  OPT PSEUDOSECTION:
  EDNS: version: 0, flags:; udp: 4000
  QUESTION SECTION:
contoso.com.                        IN      A

  AUTHORITY SECTION:
contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

Query time: 93 msec
SERVER: 208.76.47.5#53(208.76.47.5)
WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
MSG SIZE  rcvd: 120
```

## <a name="next-steps"></a>Pasos siguientes

Después de crear una zona DNS, [cree conjuntos de registros de DNS y registros](dns-getstarted-create-recordset-cli.md) en su zona.


