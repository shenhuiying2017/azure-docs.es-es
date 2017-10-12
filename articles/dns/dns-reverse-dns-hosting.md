---
title: "Alojamiento de zonas de búsqueda inversa DNS en DNS de Azure | Microsoft Docs"
description: "Aprenda a usar DNS de Azure para hospedar las zonas de búsqueda inversa de DNS para los intervalos IP"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: jonatul
ms.openlocfilehash: 3e10b25d2f9b91c96af2958fef6dc6a4fdbff301
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="hosting-reverse-dns-lookup-zones-in-azure-dns"></a>Alojamiento de zonas de búsqueda inversa DNS en DNS de Azure

Este artículo explica cómo hospedar las zonas de búsqueda inversa DNS para los intervalos IP asignados en DNS de Azure. Los intervalos IP representados por la zona de búsqueda inversa deben asignarse a su organización, normalmente por su ISP.

Para configurar DNS inverso para la dirección IP propiedad de Azure asignada a su servicio de Azure, consulte cómo [configurar la búsqueda inversa de las direcciones IP asignadas a su servicio Azure](dns-reverse-dns-for-azure-services.md).

Antes de leer este artículo, debe estar familiarizado con [Introducción a DNS inverso y compatibilidad en Azure](dns-reverse-dns-overview.md).

Este artículo le guiará por los pasos necesarios para crear su primera zona de búsqueda inversa DNS y su primer registro de DNS con Azure Portal, Azure PowerShell, CLI de Azure 1.0 o CLI de Azure 2.0.

## <a name="create-a-reverse-lookup-dns-zone"></a>Creación de una zona de búsqueda inversa DNS

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com)
1. En el menú Concentrador, haga clic en **Nuevo** > **Redes** > y, luego, en **Zona DNS** para abrir la hoja **Crear zona DNS**.

   ![Zona DNS](./media/dns-reverse-dns-hosting/figure1.png)

1. En la hoja **Crear zona DNS**, asígnele un nombre a la zona DNS. El nombre de la zona está diseñado de forma diferente para los prefijos IPv4 e IPv6. Use cualquiera de la instrucciones para [IPV4](#ipv4) o [IPv6](#ipv6) a fin de asignar un nombre a la zona. Cuando haya terminado, haga clic en **Crear** para crear la zona.

### <a name="ipv4"></a>IPv4

El nombre de una zona de búsqueda inversa IPv4 se basa en el intervalo IP que representa. Tendrá el siguiente formato: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Para ver ejemplos, consulte [Introducción a DNS inverso y compatibilidad en Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Al crear zonas de búsqueda inversa de DNS sin clases en DNS de Azure, debe usar un guion (`-`) en lugar de una barra diagonal ('/ ') en el nombre de la zona.
>
> Por ejemplo, para el intervalo IP 192.0.2.128/26, debe utilizar `128-26.2.0.192.in-addr.arpa` como nombre de zona en lugar de `128/26.2.0.192.in-addr.arpa`.
>
> Esto se debe a que, aunque ambos sean compatibles con los estándares DNS, los nombres de zona DNS que contienen el carácter de barra diagonal (`/`) no se admiten en DNS de Azure.

En el ejemplo siguiente se muestra cómo crear una zona DNS inversa de 'Clase C' denominada `2.0.192.in-addr.arpa` en DNS de Azure mediante Azure Portal:

 ![Creación de una zona DNS](./media/dns-reverse-dns-hosting/figure2.png)

'Ubicación del grupo de recursos' define la ubicación para el grupo de recursos y no tiene efecto alguno sobre la zona DNS. La ubicación de la zona DNS siempre es 'global' y no se muestra.

Los ejemplos siguientes muestran cómo realizar esta tarea con Azure PowerShell y la CLI de Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI de Azure 1.0

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>CLI de Azure 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

El nombre de una zona de búsqueda inversa IPv6 debe tener el formato siguiente: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Para ver ejemplos, consulte [Introducción a DNS inverso y compatibilidad en Azure](dns-reverse-dns-overview.md#ipv6).


En el ejemplo siguiente se muestra cómo crear una zona de búsqueda inversa DNS IPv6 denominada `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` en DNS de Azure mediante Azure Portal:

 ![Creación de una zona DNS](./media/dns-reverse-dns-hosting/figure3.png)

'Ubicación del grupo de recursos' define la ubicación para el grupo de recursos y no tiene efecto alguno sobre la zona DNS. La ubicación de la zona DNS siempre es 'global' y no se muestra.

Los ejemplos siguientes muestran cómo realizar esta tarea con Azure PowerShell y la CLI de Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azurecli-10"></a>CLI de Azure 1.0

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azurecli-20"></a>CLI de Azure 2.0

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegación de una zona de búsqueda inversa DNS

Al haber creado la zona de búsqueda inversa DNS, debe asegurarse de que la zona se delega desde la zona primaria. La delegación de DNS permite que el proceso de resolución de nombres DNS encuentre los servidores de nombres que hospedan la zona de búsqueda inversa DNS. Esto permite a los servidores de nombres responder a consultas DNS inversas para las direcciones IP en el intervalo de direcciones.

Para las zonas de búsqueda directa, el proceso de delegación de una zona DNS se describe en [Delegación de un dominio en DNS de Azure](dns-delegate-domain-azure-dns.md). La delegación de zonas de búsqueda inversa funciona del mismo modo. La única diferencia es que debe configurar los servidores de nombres con el ISP que ha proporcionado el intervalo IP, en lugar de su registrador de nombres de dominio.

## <a name="create-a-dns-ptr-record"></a>Creación de un registro PTR DNS

### <a name="ipv4"></a>IPv4

En el ejemplo siguiente, se le guiará a través del proceso de creación de un registro PTR en una zona DNS inversa mediante Azure Portal. Para crear otros tipos de registros y modificar los que ya existan, consulte [Administración de registros y conjuntos de registros DNS mediante Azure Portal](dns-operations-recordsets-portal.md).

1.  En la parte superior de la hoja **Zona DNS**, seleccione **+ Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.

 ![Zona DNS](./media/dns-reverse-dns-hosting/figure4.png)

1. En la hoja **Agregar conjunto de registros**. 
1. Seleccione **PTR** en el menú "**Tipo**" de registro.  
1. El nombre del conjunto de registros para un registro PTR debe ser el resto de la dirección IPv4 en orden inverso. En este ejemplo, los tres primeros octetos ya están rellenados como parte del nombre de la zona (.2.0.192). Por lo tanto, solo se proporciona el último octeto en el campo de nombre. Por ejemplo, podría denominar el conjunto de registros "**15**" para un recurso cuya dirección IP es 192.0.2.15.  
1. En el campo "**Nombre de dominio**", escriba el nombre de dominio completo (FQDN) del recurso mediante la dirección IP.
1. Haga clic en **Aceptar** en la parte inferior de la hoja para crear el registro DNS.

 ![Agregar conjunto de registros](./media/dns-reverse-dns-hosting/figure5.png)

Los ejemplos siguientes muestran cómo realizar esta tarea con PowerShell y la CLI de Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azurecli-10"></a>CLI de Azure 1.0

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azurecli-20"></a>CLI de Azure 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

En el ejemplo siguiente, se le guiará a través del proceso de creación de un registro 'PTR'. Para crear otros tipos de registros y modificar los que ya existan, consulte [Administración de registros y conjuntos de registros DNS mediante Azure Portal](dns-operations-recordsets-portal.md).

1. En la parte superior de la hoja **Zona DNS**, seleccione **+ Conjunto de registros** para abrir la hoja **Agregar conjunto de registros**.

  ![Hoja de zona DNS](./media/dns-reverse-dns-hosting/figure6.png)

2. En la hoja **Agregar conjunto de registros**. 
3. Seleccione **PTR** en el menú "**Tipo**" de registro.  
4. El nombre del conjunto de registros para un registro PTR debe ser el resto de la dirección IPv6 en orden inverso. No puede contener ninguna compresión de ceros. En este ejemplo, los primeros 64 bits de IPv6 se rellenan como parte del nombre de zona (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Por lo tanto, solo se proporcionan los últimos 64 bits en el campo de nombre. Los últimos 64 bits de la dirección IP se escriben en orden inverso, con un punto como delimitador entre cada número hexadecimal. Por ejemplo, podría denominar el conjunto de registros "**e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f**" para un recurso cuya dirección IP es 2001:0db8:abdc:0000:f524:10bc:1af9:405e.  
5. En el campo "**Nombre de dominio**", escriba el nombre de dominio completo (FQDN) del recurso mediante la dirección IP.
6. Haga clic en **Aceptar** en la parte inferior de la hoja para crear el registro DNS.

![Hoja Agregar conjunto de registros](./media/dns-reverse-dns-hosting/figure7.png)

Los ejemplos siguientes muestran cómo realizar esta tarea con PowerShell y la CLI de Azure:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzureRmDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azurecli-10"></a>CLI de Azure 1.0

```
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azurecli-20"></a>CLI de Azure 2.0

```azurecli
    az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Visualización de los registros

Para ver los registros que ha creado, vaya a la zona DNS en Azure Portal. En la parte inferior de la hoja **Zona DNS**, puede consultar los registros correspondientes a la zona DNS. Debería ver los registros SOA y NS predeterminados, que se crean en cada zona, además de todos los nuevos que haya creado.

### <a name="ipv4"></a>IPv4

Hoja de zona DNS, que muestra los registros PTR de IPv4:

![Hoja de zona DNS](./media/dns-reverse-dns-hosting/figure8.png)

Los ejemplos siguientes muestran cómo ver los registros PTR con PowerShell o la CLI de Azure:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI de Azure 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli-20"></a>CLI de Azure 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Hoja de zona DNS, que muestra los registros PTR de IPv6:

![Hoja de zona DNS](./media/dns-reverse-dns-hosting/figure9.png)

Los ejemplos siguientes muestran cómo ver los registros con PowerShell o la CLI de Azure:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzureRmDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-cli-10"></a>CLI de Azure 1.0

```azurecli
    azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli-20"></a>CLI de Azure 2.0

```azurecli
    azure network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>P+F

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>¿Puedo hospedar zonas de búsqueda inversa DNS para mis bloques IP asignados por el ISP en DNS de Azure?

Sí. El hospedaje de zonas de búsqueda inversa (ARPA) para sus propios intervalos de IP en DNS de Azure está totalmente permitido.

Cree la zona de búsqueda inversa en DNS de Azure tal como se explica en este artículo y trabaje con su ISP para [delegar la zona](dns-domain-delegation.md).  Después puede administrar los registros PTR de cada búsqueda inversa igual que otros tipos de registros.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>¿Cuánto cuesta el hospedaje de mi zona de búsqueda inversa DNS?

El hospedaje de una zona de búsqueda inversa DNS para su bloque de direcciones IP asignadas por el ISP en DNS de Azure se cobra según las [tarifas estándar de DNS de Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Puedo hospedar zonas de búsqueda inversa DNS para direcciones IPv4 e IPv6 en DNS de Azure?

Sí. En este artículo se explica cómo crear zonas de búsqueda inversa DNS de IPv4 e IPv6 en DNS de Azure.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>¿Puedo importar una zona de búsqueda inversa DNS existente?

Sí. Puede utilizar la CLI de Azure para importar las zonas DNS existentes a DNS de Azure. Esto funciona para las zonas de búsqueda directa y las zonas de búsqueda inversa.

Para más información, consulte [Importación y exportación de un archivo de zona DNS mediante la CLI de Azure](dns-import-export.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los registros de DNS inverso, consulte información sobre la [búsqueda de DNS inverso en Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Aprenda a [administrar registros de DNS inversos para servicios de Azure](dns-reverse-dns-for-azure-services.md).
