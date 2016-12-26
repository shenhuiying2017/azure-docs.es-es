---
title: "Creación de una zona DNS con CLI| Microsoft Docs"
description: "Obtenga información sobre cómo crear zonas DNS para Azure DNS. Esta es una guía paso a paso para crear y administrar su primera zona DNS con la CLI de Azure."
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
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: bfbffe7843bc178cdf289c999925c690ab82e922
ms.openlocfilehash: 5bbd490925e5e25f10044af55af49daa494ee026

---

# <a name="create-an-azure-dns-zone-using-cli"></a>Creación de una zona DNS de Azure con CLI

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [CLI de Azure](dns-getstarted-create-dnszone-cli.md)

Este artículo le guiará por los pasos necesarios para crear una zona DNS mediante la CLI de Azure multiplataforma, que está disponible para Windows, Mac y Linux. También puede crear una zona DNS con PowerShell o el Portal de Azure.

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar con la configuración, compruebe que dispone de los elementos siguientes.

* Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Debe instalar la versión más reciente de la CLI de Azure, disponible para Windows, Linux o Mac. Puede consultar mas información disponible en [Instalación de la CLI de Azure](../xplat-cli-install.md).

## <a name="step-1---sign-in-and-create-a-resource-group"></a>Paso 1: Inicio de sesión y creación de un grupo de recursos

### <a name="switch-cli-mode"></a>Cambio del modo de CLI

La DNS de Azure usa el Administrador de recursos de Azure. Asegúrese de cambiar al modo de CLI para usar los comandos ARM.

```azurecli
azure config mode arm
```

### <a name="sign-in-to-your-azure-account"></a>Inicio de sesión en la cuenta de Azure.

Se le pedirá que se autentique con sus credenciales. Tenga en cuenta que solo puede usar cuentas de OrgID.

```azurecli
azure login
```

### <a name="select-the-subscription"></a>Selección de la suscripción

Compruebe las suscripciones para la cuenta.

```azurecli
azure account list
```

Elección de la suscripción de Azure que se va a usar.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Sin embargo, puesto que todos los recursos DNS son globales y no regionales, la elección de la ubicación del grupo de recursos no incide en DNS de Azure.

Puede omitir este paso si utiliza un grupo de recursos existente.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registro del proveedor de recursos

El proveedor de recursos Microsoft.Network administra el servicio DNS de Azure. La suscripción a Azure debe estar registrada para usar este proveedor de recursos antes de utilizar DNS de Azure. Se trata de una operación única para cada suscripción.

```azurecli
azure provider register --namespace Microsoft.Network
```

## <a name="step-2---create-a-dns-zone"></a>Paso 2: Creación de una zona DNS

Una zona DNS se crea con el comando `azure network dns zone create` . Para ver la ayuda sobre este comando, escriba `azure network dns zone create -h`.

En el ejemplo siguiente, se crea una zona DNS llamada *contoso.com* en el grupo de recursos *MyResourceGroup*. Utilice el ejemplo para crear una zona DNS, sustituyendo los valores por los suyos.

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="step-3---verify"></a>Paso 3: Comprobación

### <a name="view-records"></a>Visualización de los registros

Al crear una zona DNS, también se crean los siguientes registros DNS:

* El registro "Inicio de autoridad" (SOA). Se encuentra en la raíz de cada zona DNS.
* Los registros de servidor de nombres (NS) autoritativo. Estos muestran qué servidores de nombres hospedan la zona. DNS de Azure usa un grupo de servidores de nombres, por lo que se pueden asignar diferentes servidores de nombres a zonas distintas en DNS de Azure. Consulte [Delegación de un dominio a DNS de Azure](dns-domain-delegation.md) para obtener más información.

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

Si aún no ha delegado el dominio para usar la nueva zona en DNS en Azure, deberá dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona. Los servidores de nombres de su zona se proporcionan en los registros de NS, como se muestra con el comando "azure network dns-record-set show" mencionado anteriormente. Asegúrese de sustituir los valores correctos de su zona en el comando que aparece a continuación.

En el ejemplo siguiente se usa DIG para consultar el dominio contoso.com usando los servidores de nombres asignados para la zona DNS. La consulta tiene que señalar a un servidor de nombres para el que se usó *@\<servidor de nombres para la zona\>* y el nombre de zona con DIG.

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

## <a name="next-steps"></a>Pasos siguientes

Después de crear una zona DNS, [cree registros y conjuntos de registros](dns-getstarted-create-recordset-cli.md) para crear los registros DNS para el dominio de Internet.




<!--HONumber=Dec16_HO2-->


