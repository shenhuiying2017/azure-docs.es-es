---
title: "Creación de una zona DNS mediante la CLI de Azure 2.0 | Microsoft Docs"
description: "Obtenga información sobre cómo crear zonas DNS para Azure DNS. Esta es una guía paso a paso para crear y administrar su primera zona DNS con la CLI de Azure 2.0."
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
ms.date: 02/27/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1481fcb070f383d158c5a6ae32504e498de4a66b
ms.openlocfilehash: 00a4dfbe070129ce6dc84f006d793007d55dba3f
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-dns-zone-using-azure-cli-20"></a>Creación de una zona DNS de Azure con la CLI de Azure 2.0

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

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Configuración de la CLI de Azure 2.0 para Azure DNS

### <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes.

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).

* Instale la versión más reciente de la CLI de Azure, disponible para Windows, Linux o Mac. Puede consultar más información en [Instalación de la CLI de Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Inicio de sesión en la cuenta de Azure.

Abra una ventana de consola y autentíquese con sus credenciales. Para más información, consulte cómo iniciar sesión en Azure desde la CLI de Azure.

```azurecli
az login
```

### <a name="select-the-subscription"></a>Selección de la suscripción

Compruebe las suscripciones para la cuenta.

```azurecli
az account list
```

### <a name="choose-which-of-your-azure-subscriptions-to-use"></a>Elección de la suscripción de Azure que se va a usar.

```azurecli
az account set --subscription "subscription name"
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

Puede omitir este paso si utiliza un grupo de recursos existente.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Creación de una zona DNS

Una zona DNS se crea con el comando `az network dns zone create` . Para ver la ayuda sobre este comando, escriba `az network dns zone create --help`.

En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```azurecli
az network dns zone create --resource-group myresourcegroup --name contoso.com
```

## <a name="verify-your-dns-zone"></a>Verificación de la zona DNS

### <a name="view-records"></a>Visualización de los registros

Al crear una zona DNS, también se crean los siguientes registros DNS:

* El registro *Inicio de autoridad* (SOA). Este registro se encuentra en la raíz de cada zona DNS.
* Los registros de servidor de nombres (NS) autoritativo. Estos registros muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Para más información, consulte [Delegación de dominios en Azure DNS](dns-domain-delegation.md).

Para ver estos registros, use `az network dns record-set list`:

```azurecli
az network dns record-set list --resource-group MyResourceGroup --zone-name contoso.com
```

La respuesta para `az network dns record-set list` se muestra a continuación:

```json
[
  {
    "etag": "510f4711-8b7f-414e-b8d5-c0383ea3d62e",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@",
    "metadata": null,
    "name": "@",
    "nsRecords": [
      {
        "nsdname": "ns1-04.azure-dns.com."
      },
      {
        "nsdname": "ns2-04.azure-dns.net."
      },
      {
        "nsdname": "ns3-04.azure-dns.org."
      },
      {
        "nsdname": "ns4-04.azure-dns.info."
      }
    ],
    "resourceGroup": "myresourcegroup",
    "ttl": 172800,
    "type": "Microsoft.Network/dnszones/NS"
  },
  {
    "etag": "0e48e82f-f194-4d3e-a864-7e109a95c73a",
    "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@",
    "metadata": null,
    "name": "@",
    "resourceGroup": "myresourcegroup",
    "soaRecord": {
      "email": "azuredns-hostmaster.microsoft.com",
      "expireTime": 2419200,
      "host": "ns1-04.azure-dns.com.",
      "minimumTtl": 300,
      "refreshTime": 3600,
      "retryTime": 300,
      "serialNumber": 1
    },
    "ttl": 3600,
    "type": "Microsoft.Network/dnszones/SOA"
  }
]
```

> [!NOTE]
> Los conjuntos de registros de la raíz (o la *cúspide*) de una zona DNS usan **@** como el nombre del conjunto de registros.

### <a name="test-name-servers"></a>Prueba de los servidores de nombres

Puede probar que la zona DNS está presente en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el cmdlet de PowerShell `Resolve-DnsName`.

Si aún no ha delegado el dominio para usar la nueva zona en DNS en Azure, deberá dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en los registros de NS, como se muestra con el comando `az network dns record-set list`.

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


