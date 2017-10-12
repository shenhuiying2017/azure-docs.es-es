---
title: "Administración de registros DNS en Azure DNS mediante Azure PowerShell | Microsoft Docs"
description: "Administración de conjuntos de registros y registros DNS en DNS de Azure al hospedar dominios en DNS de Azure. Todos los comandos de PowerShell para operaciones en conjuntos de registros y registros."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
ms.openlocfilehash: 2962e30e5d9c60b8e786e2ba79647cabfc5925cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Administración de conjuntos de registros y registros de DNS en Azure DNS mediante Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-operations-recordsets-portal.md)
> * [CLI de Azure 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [CLI de Azure 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

En este artículo se muestra cómo administrar registros DNS para su zona DNS mediante Azure PowerShell. Los registros de DNS también se pueden administrar mediante la [CLI de Azure](dns-operations-recordsets-cli.md) multiplataforma o [Azure Portal](dns-operations-recordsets-portal.md).

En los ejemplos de este artículo se supone que ya ha [instalado Azure PowerShell, iniciado sesión y creado una zona DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Introducción

Antes de crear registros DNS en Azure DNS, es necesario que comprenda cómo Azure DNS los organiza en conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para más información sobre los registros DNS en Azure DNS, consulte [DNS zones and records](dns-zones-records.md) (Zonas y registros DNS).


## <a name="create-a-new-dns-record"></a>Creación de un nuevo registro DNS

Si el nuevo registro tiene el mismo nombre y tipo que un registro existente, debe [agregarlo al conjunto de registros existente](#add-a-record-to-an-existing-record-set). Si el nuevo registro tiene un nombre y un tipo diferentes al de todos los registros existentes, debe crear un nuevo conjunto de registros. 

### <a name="create-a-records-in-a-new-record-set"></a>Creación de registros "D" en un nuevo conjunto de registros

Los conjuntos de registros se crean mediante el cmdlet `New-AzureRmDnsRecordSet`. Al crear un conjunto de registros, deberá especificar el nombre del conjunto de registros, la zona, el período de vida (TTL), el tipo de registro y los registros que se crearán.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se usa un conjunto de registros de tipo "A", deberá especificar la dirección IP mediante el parámetro `-IPv4Address`. Para otros tipos de registros se usan otros parámetros. Para más información, consulte [Ejemplos de tipos de registros adicionales](#additional-record-type-examples).

En el ejemplo siguiente se crea un conjunto de registros con el nombre relativo "www" en la zona DNS "contoso.com". El nombre completo del conjunto de registros es www.contoso.com. El tipo de registro es "D" y el valor de TTL es de 3600 segundos. El conjunto de registros contiene un único registro con la dirección IP "1.2.3.4".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Para crear un conjunto de registros en el "vértice" de una zona (en este caso, "contoso.com"), use el nombre del conjunto de registros "@" (excluidas las comillas):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Si necesita crear un conjunto de registros que contenga más de un registro, primero cree una matriz local y agregue los registros, luego pase la matriz a `New-AzureRmDnsRecordSet` de la forma siguiente:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

Se pueden usar [metadatos del conjunto de registros](dns-zones-records.md#tags-and-metadata) para asociar datos específicos de la aplicación con cada conjunto de registros como pares clave-valor. En el ejemplo siguiente se muestra cómo crear un conjunto de registros con dos entradas de metadatos: "dept=finance" y "environment=production".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS también admite conjuntos de registros "vacíos" que pueden funcionar como marcador de posición para reservar un nombre DNS antes de crear registros DNS. Los conjuntos de registros vacíos son visibles en el panel de control de Azure DNS, pero aparecen en los servidores de nombres de Azure DNS. En el ejemplo siguiente se crea un conjunto de registros vacío:

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Creación de registros de otros tipos

Después de haber visto de forma detallada como crear registros "A", en los siguientes ejemplos se muestra cómo crear registros de otros tipos compatibles con Azure DNS.

En cada caso, se muestra cómo crear un nuevo conjunto de registros que contiene un único registro. Los ejemplos anteriores para registros "A" se pueden adaptar para crear conjuntos de registros de otros tipos que contengan varios registros, con metadatos, o crear conjuntos de registros vacíos.

No se proporciona un ejemplo para crear un conjunto de registros SOA, dado que los registros SOA se crean y eliminan con cada zona de DNS y no lo pueden hacer por separado. Sin embargo, [el registro SOA se puede modificar, como se muestra en un ejemplo más adelante](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Creación de un conjunto de registros AAAA con un único registro

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Creación de un conjunto de registros CNAME con un único registro

> [!NOTE]
> Los estándares DNS no permiten registros CNAME en el vértice de una zona (`-Name '@'`), ni permiten conjuntos de registros que contengan más de un registro.
> 
> Para más información, consulte [Registros CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Creación de un conjunto de registros MX con un único registro

En este ejemplo, se utiliza el nombre de conjunto de registros "@" para crear el registro MX en el vértice de la zona (por ejemplo, "contoso.com").


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Creación de un conjunto de registros NS con un único registro

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Creación de un conjunto de registros PTR con un único registro

En este caso, "my-arpa-zone.com" representa la zona de búsqueda inversa ARPA que representa el intervalo IP. Cada registro PTR establecido en esta zona se corresponde con una dirección IP dentro de este intervalo IP. El nombre de registro "10" es el último octeto de la dirección IP dentro del intervalo IP que representa dicho registro.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Creación de un conjunto de registros SRV con un único registro

Al crear un [conjunto de registros SRV](dns-zones-records.md#srv-records), especifique el *\_servicio* y el *\_protocolo* en el nombre del conjunto de registros. No es necesario incluir "@" en el nombre del conjunto de registros al crear un conjunto de registros SRV en el vértice de la zona.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Crear un conjunto de registros TXT con un único registro

En el ejemplo siguiente se muestra cómo crear un registro TXT. Para más información sobre la longitud de cadena máxima admitida en registros TXT, consulte [Registros TXT](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Recuperación de un conjunto de registros

Para recuperar un conjunto de registros existente, use `Get-AzureRmDnsRecordSet`. Este cmdlet devuelve un objeto local que representa el conjunto de registros en Azure DNS.

Al igual que con `New-AzureRmDnsRecordSet`, el nombre del conjunto de registros dado debe ser un nombre *relativo*, lo que significa que debe excluir el nombre de zona. También se debe especificar el tipo de registro y la zona que contiene el conjunto de registros.

En el ejemplo siguiente se muestra cómo recuperar un conjunto de registros. En este ejemplo, la zona se especifica mediante los parámetros `-ZoneName` y `-ResourceGroupName`.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Como alternativa, también puede especificar la zona mediante un objeto de zona, que se pasa mediante el parámetro `-Zone`.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Enumeración de conjuntos de registros

También puede usar `Get-AzureRmDnsZone` para mostrar conjuntos de registros de una zona y omitir los parámetros `-Name` o `-RecordType`.

En el ejemplo siguiente se devuelven todos los conjuntos de registros de la zona:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

En el ejemplo siguiente se muestra cómo se pueden recuperar todos los conjuntos de registros de un tipo dado mediante la especificación del tipo de registro y la omisión del nombre del conjunto de registros:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Para recuperar todos los conjuntos de registros con un nombre específico de todos los tipos de registros, debe recuperar todos los conjuntos de registros y luego filtrar los resultados:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

En todos los ejemplos anteriores, la zona se puede especificar mediante los parámetros `-ZoneName` y `-ResourceGroupName` (como se muestra) o por medio de un objeto de zona:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Adición de un registro a un conjunto de registros existente

Para agregar un registro a un conjunto de registros existente, siga estos tres pasos:

1. Obtenga el conjunto de registros existente.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Agregue el nuevo registro al conjunto de registros local. Esta operación se realiza sin conexión.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme el cambio al servicio Azure DNS. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Al usar `Set-AzureRmDnsRecordSet` *, se sustituye* el conjunto de registros existente en Azure DNS (y todos los registros que contiene) por el conjunto de registros especificado. Se usan [comprobaciones de ETag](dns-zones-records.md#etags) para garantizar que los cambios simultáneos no se sobrescriban. Puede usar el modificador `-Overwrite` opcional para suprimir estas comprobaciones.

Esta secuencia de operaciones también se puede *canalizar*, es decir, pasar el objeto de conjunto de registros mediante la canalización en lugar de como un parámetro.

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Los ejemplos anteriores muestran cómo agregar un registro "A" a un conjunto de registros existente de tipo "A". Se usa una secuencia parecida de operaciones para agregar registros a conjunto de registros de otros tipos, sustituyendo el parámetro `-Ipv4Address` de `Add-AzureRmDnsRecordConfig` por otros parámetros específicos de cada tipo de registro. Los parámetros de cada tipo de registro son los mismos que para el cmdlet `New-AzureRmDnsRecordConfig`, como se muestra en [Ejemplos de tipos de registros adicionales](#additional-record-type-examples) anteriormente.

Los conjuntos de registros de tipo "CNAME" o "SOA" no pueden contener más de un registro. Esta restricción surge de los estándares de DNS. No es una limitación de Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Eliminación de un registro de un conjunto de registros existente

El proceso para quitar un registro de un conjunto de registros es similar al proceso para agregar un registro a un conjunto de registros existente:

1. Obtenga el conjunto de registros existente.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Quite el registro del objeto del conjunto de registros local. Esta operación se realiza sin conexión. Todos los parámetros del registro que se va a eliminar deben coincidir exactamente con los del registro existente.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme el cambio al servicio Azure DNS. Use el modificador `-Overwrite` para suprimir las [comprobaciones de ETag](dns-zones-records.md#etags) para cambios simultáneos.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

El uso de la secuencia anterior para quitar el último registro de un conjunto de registros no elimina el conjunto de registros, sino que deja un conjunto de registros vacío. Para quitar completamente un conjunto de registros, consulte [Eliminación de un conjunto de registros](#delete-a-record-set).

Lo mismo que para agregar registros a un conjunto de registros, la secuencia de operaciones para quitar un conjunto de registros también se puede canalizar:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Se admiten diferentes tipos de registros pasando los parámetros adecuados específicos del tipo a `Remove-AzureRmDnsRecordSet`. Los parámetros de cada tipo de registro son los mismos que para el cmdlet `New-AzureRmDnsRecordConfig`, como se muestra en [Ejemplos de tipos de registros adicionales](#additional-record-type-examples) anteriormente.


## <a name="modify-an-existing-record-set"></a>Modificación de un conjunto de registros existente

Los pasos para modificar un conjunto de registros existente son parecidos a los que se realizan al agregar o quitar registros de un conjunto de registros:

1. Recupere el conjunto de registros existente mediante `Get-AzureRmDnsRecordSet`.
2. Para modificar el objeto de conjunto de registros local:
    * Agregue o quite registros.
    * Cambie los parámetros de registros existentes.
    * Cambie los metadatos del conjunto de registros y el período de vida (TTL).
3. Confirme los cambios mediante el cmdlet `Set-AzureRmDnsRecordSet` . Esta acción *reemplaza* el conjunto de registros existente de Azure DNS por el conjunto de registros especificado.

Al usar `Set-AzureRmDnsRecordSet`, se emplean [comprobaciones de ETag](dns-zones-records.md#etags) para garantizar que los cambios simultáneos no se sobrescriban. Puede usar el modificador `-Overwrite` opcional para suprimir estas comprobaciones.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para actualizar un registro en un conjunto de registros existente

En este ejemplo, se cambiará la dirección IP de un registro A existente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Para modificar un registro SOA

No puede agregar ni quitar registros del conjunto de registros SOA creado automáticamente en el vértice de zona (`-Name "@"`, comillas incluidas). Sin embargo, puede modificar cualquiera de los parámetros del registro SOA (excepto "Host") y del conjunto de registros TTL.

En el ejemplo siguiente se muestra cómo cambiar la propiedad *Email* del registro SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar los registros NS en el vértice de zona

El conjunto de registros NS en el vértice de zona se crea automáticamente con cada zona DNS. Este conjunto de registros contiene los nombres de los servidores de nombres de Azure DNS asignados a la zona.

Puede agregar más servidores de nombres a este conjunto de registros NS, para admitir dominios de hospedaje conjunto con más de un proveedor DNS. También puede modificar el TTL y los metadatos de este conjunto de registros. Sin embargo, no puede quitar ni modificar los servidores de nombres de Azure DNS rellenados previamente.

Tenga en cuenta que esto solo se aplica al conjunto de registros NS en el vértice de zona. Otros conjuntos de registros NS de su zona (como los que se usan para delegar zonas secundarias) se pueden modificar sin restricciones.

En el ejemplo siguiente se muestra cómo agregar un servidor de nombres adicional al conjunto de registros NS en el vértice de zona:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Para modificar los metadatos del conjunto de registros:

Se pueden usar [metadatos del conjunto de registros](dns-zones-records.md#tags-and-metadata) para asociar datos específicos de la aplicación con cada conjunto de registros como pares clave-valor.

En el ejemplo siguiente se muestra cómo modificar los metadatos de un conjunto de registros existente:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Eliminación de un conjunto de registros

Los conjuntos de registros pueden eliminarse mediante el cmdlet `Remove-AzureRmDnsRecordSet` . Al eliminar un conjunto de registros también se eliminan todos los registros que contiene.

> [!NOTE]
> No se pueden eliminar los conjuntos de registros SOA y NS en el vértice de zona (`-Name '@'`).  Los DNS de Azure se crean automáticamente cuando se genera la zona y se eliminan automáticamente cuando se elimina.

En el ejemplo siguiente se muestra cómo eliminar un conjunto de registros. En este ejemplo, el nombre del conjunto de registros, el tipo de conjunto de registros, el nombre de zona y el grupo de recursos se especifican de manera explícita.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Como alternativa, el conjunto de registros se puede especificar por nombre y tipo, y la zona se puede especificar mediante un objeto:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Como tercera opción, el propio conjunto de registros se puede especificar mediante un objeto de conjunto de registros:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Cuando se especifica el conjunto de registros que se va a eliminar mediante un objeto de conjunto de registros, se usan [comprobaciones de ETag](dns-zones-records.md#etags) para garantizar que no se eliminan los cambios simultáneos. Puede usar el modificador `-Overwrite` opcional para suprimir estas comprobaciones.

El objeto del conjunto de registros también puede canalizarse en lugar de pasarse como un parámetro:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Mensajes de confirmación

Los cmdlets `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet` y `Remove-AzureRmDnsRecordSet` todos admiten mensajes de confirmación.

Cada cmdlet pide confirmación si la variable de preferencia de PowerShell `$ConfirmPreference` tiene un valor de `Medium` o inferior. Dado que el valor predeterminado de `$ConfirmPreference` es `High`, estos mensajes no se proporcionan cuando se usa la configuración predeterminada de PowerShell.

Puede invalidar el valor actual de `$ConfirmPreference` mediante el parámetro `-Confirm`. Si especifica `-Confirm` o `-Confirm:$True`, el cmdlet solicita confirmación antes de ejecutarse. Si especifica `-Confirm:$False`, el cmdlet no solicita confirmación. 

Para más información sobre `-Confirm` y `$ConfirmPreference`, consulte [About Preference Variables](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables) (Acerca de las variables de preferencias).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [zonas y registros en Azure DNS](dns-zones-records.md).
<br>
Aprenda a [proteger las zonas y los registros](dns-protect-zones-recordsets.md) cuando se usa Azure DNS.
<br>
Revise la [documentación de referencia de PowerShell para Azure DNS](/powershell/module/azurerm.dns).
