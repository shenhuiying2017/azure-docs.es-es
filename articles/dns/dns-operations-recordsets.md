---
title: "Administración de registros y conjuntos de registros DNS mediante Azure Portal | Microsoft Docs"
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
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: e3f7967843b3d9e38c79b45d90e333192fd3a900

---

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Administración de registros y conjuntos de registros DNS mediante PowerShell

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-operations-recordsets-portal.md)
> * [CLI de Azure](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

En este artículo se explica cómo administrar conjuntos de registros y registros de zonas DNS con Windows PowerShell.

Es importante comprender la diferencia entre los conjuntos de registros de DNS y los registros DNS individuales. Un conjunto de registros es la colección de registros de una zona con el mismo nombre y el mismo tipo. Para obtener más información, consulte [Creación de registros y conjuntos de registros de DNS mediante el Portal de Azure](dns-getstarted-create-recordset-portal.md).

Para administrar los registros y los conjuntos de registros, tendrá que instalar la versión más reciente de los cmdlets de PowerShell de Azure Resource Manager. Para más información, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Para obtener más información sobre el uso de PowerShell, consulte [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

## <a name="create-a-new-record-set-and-a-record"></a>Creación de un nuevo conjunto de registros y un registro

Para crear un conjunto de registros mediante PowerShell, consulte [Creación de conjuntos de registros y registros de DNS con CLI](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Recuperación de un conjunto de registros

Para recuperar un conjunto de registros existente, use `Get-AzureRmDnsRecordSet`. Especifique el nombre relativo, el tipo de registro y la zona del conjunto de registros.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

Al igual que con `New-AzureRmDnsRecordSet`, el nombre del registro debe ser un nombre relativo, es decir, que no incluya el nombre de la zona.

La zona se puede especificar mediante el nombre de zona y el nombre del grupo de recursos. o mediante un objeto de zona:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -Zone $zone
```

`Get-AzureRmDnsRecordSet` devuelve un objeto local que representa el conjunto de registros que se creó en DNS de Azure.

## <a name="list-record-sets"></a>Enumeración de conjuntos de registros

También puede utilizarse `Get-AzureRmDnsRecordSet` para enumerar conjuntos de registros si omite los parámetros *–Name* y *–RecordType*.

### <a name="to-list-all-record-sets"></a>Para mostrar todos los conjuntos de registros

Este ejemplo devolverá todos los conjuntos de registros, independientemente del nombre o tipo de registro:

```powershell
$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="to-list-record-sets-of-a-given-record-type"></a>Para mostrar los conjuntos de registros de un tipo de registro determinado

En este ejemplo se devolverán todos los conjuntos de registros que coincidan con el tipo de registro determinado. En este caso, el conjunto de registros que se devuelven son los registros A:

```powershell
$list = Get-AzureRmDnsRecordSet -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
```

La zona se puede especificar mediante los parámetros `-ZoneName` y `-ResourceGroupName` (tal y como se muestran) o especificando un objeto de zona:

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$list = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-a-record-set"></a>Incorporación de un registro a un conjunto de registros

Los registros se agregan a los conjuntos de registros mediante el cmdlet `Add-AzureRmDnsRecordConfig` . Se trata de una operación sin conexión. Solo se cambia el objeto local que representa el conjunto de registros.

Los parámetros para agregar registros a un conjunto de registros varían según el tipo de conjunto de registros. Por ejemplo, cuando se utiliza un conjunto de registros de tipo A, solo podrá especificar los registros con el parámetro *-IPv4Address*.

Se pueden agregar más registros a cada conjunto de registros mediante llamadas adicionales a `Add-AzureRmDnsRecordConfig`. Puede agregar hasta 20 registros a cualquier conjunto de registros. Sin embargo, los conjuntos de registros de tipo CNAME pueden contener como máximo 1 registro, y un conjunto de registros no puede contener 2 registros idénticos. Se pueden crear conjuntos de registros vacíos (sin ningún registro), pero no aparecen en los servidores de nombres DNS de Azure.

Una vez que el conjunto de registros contiene la colección deseada de registros, debe confirmarse mediante el cmdlet `Set-AzureRmDnsRecordSet` . Esta acción reemplaza el conjunto de registros existente en DNS de Azure por el conjunto de registros especificado.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Para crear un conjunto de registros A con un único registro

```powershell
$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

La secuencia de operaciones para eliminar un registro de un conjunto de registros también puede *canalizarse*; es decir, pasar el objeto del conjunto de registros usando la canalización en lugar de como un parámetro. Por ejemplo:

```powershell
New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="additional-record-type-examples"></a>Ejemplos de tipo de registros adicionales

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modificación de conjuntos de registros existentes

Los pasos para modificar un conjunto de registros que ya existan son similares a los pasos que hay que seguir al crear registros. La secuencia de operaciones es la siguiente:

1. Recupere el conjunto de registros existente mediante `Get-AzureRmDnsRecordSet`.
2. Modifique el conjunto de registros; para ello, agregue registros, elimine registros, cambie los parámetros de los registros o cambie el TTL del conjunto de registros. Se trata de una operación sin conexión. Solo se cambia el objeto local que representa el conjunto de registros.
3. Confirme los cambios mediante el cmdlet `Set-AzureRmDnsRecordSet` . Esta acción reemplaza el conjunto de registros existente en DNS de Azure.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Para actualizar un registro en un conjunto de registros existente

En este ejemplo, se cambiará la dirección IP de un registro A existente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Ipv4Address = "134.170.185.46"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

El cmdlet `Set-AzureRmDnsRecordSet` usa comprobaciones de ETag para asegurarse de que no se sobrescriben los cambios simultáneos. Utilice la marca *-Overwrite* para suprimir estas comprobaciones. Consulte [Acerca de ETags y etiquetas](dns-getstarted-create-dnszone.md#tagetag)para obtener más información.

### <a name="to-modify-an-soa-record"></a>Para modificar un registro SOA

No puede agregar ni eliminar registros del conjunto de registros SOA creado automáticamente en el ápice de zona (nombre = "@").), pero puede modificar cualquiera de los parámetros del registro SOA (excepto Host) y del TTL del conjunto de registros.

En el ejemplo siguiente se muestra cómo cambiar la propiedad *Email* del registro SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar los registros NS en el vértice de zona

No puede agregar, eliminar ni modificar los registros en el conjunto de registros NS creado automáticamente en el vértice de zona (nombre = "@").). El único cambio permitido es la modificación del TTL del conjunto de registros.

En el ejemplo siguiente se muestra cómo cambiar la propiedad TTL del conjunto de registros NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
$rs.Ttl = 300
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-add-records-to-an-existing-record-set"></a>Para incorporar registros a un conjunto de registros existente

En este ejemplo, agregamos 2 registros MX más al conjunto de registros existente:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="remove-a-record-from-an-existing-record-set"></a>Eliminación de un registro de un conjunto de registros existente

Los registros pueden eliminarse de un conjunto de registros con `Remove-AzureRmDnsRecordConfig`. Todos los parámetros del registro que se va a eliminar deben coincidir exactamente con los del registro existente. Los cambios deben confirmarse mediante `Set-AzureRmDnsRecordSet`.

Al eliminar el último registro de un conjunto de registros, no se elimina el conjunto de registros. Consulte [Eliminación de un conjunto de registros](#delete-a-record-set) a continuación para obtener más información.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

La secuencia de operaciones para eliminar un registro de un conjunto de registros también puede canalizarse; es decir, pasar el objeto del conjunto de registros usando la canalización en lugar de como un parámetro. Por ejemplo:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet
```

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Eliminación de un registro AAAA de un conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-cname-record-from-a-record-set"></a>Eliminación de un registro CNAME de un conjunto de registros

Puesto que un conjunto de registros CNAME puede contener como máximo un registro, al eliminar dicho registro, un conjunto de registros se quedará vacío.

```powershell
$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-mx-record-from-a-record-set"></a>Eliminación de un registro MX de un conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-ns-record-from-record-set"></a>Eliminación de un registro NS de un conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-an-srv-record-from-a-record-set"></a>Eliminación de un registro SRV de un conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="remove-a-txt-record-from-a-record-set"></a>Eliminación de un registro TXT de un conjunto de registros

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

## <a name="delete-a-record-set"></a>Eliminación de un conjunto de registros

Los conjuntos de registros pueden eliminarse mediante el cmdlet `Remove-AzureRmDnsRecordSet` . No se pueden eliminar los conjuntos de registros SOA ni NS en el vértice de zona (nombre = "@")) que se crearon automáticamente cuando se creó la zona. Se eliminarán automáticamente si se elimina la zona.

Utilice uno de los tres métodos siguientes para eliminar un conjunto de registros:

### <a name="specify-all-the-parameters-by-name"></a>Especificación de todos los parámetros por nombre

Se puede utilizar el elemento opcional *-Force* para suprimir el mensaje de confirmación.

```powershell
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Especificación del conjunto de registros por nombre y tipo, y especificación de la zona por objeto

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]
```

### <a name="specify-the-record-set-by-object"></a>Especificación del conjunto de registros por objeto

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsRecordSet -RecordSet $rs [-Overwrite] [-Force]
```

Al especificar el conjunto de registros con un objeto, las comprobaciones de ETag pueden garantizar que no se eliminan los cambios simultáneos. La marca opcional *-Overwrite* suprime estas comprobaciones. Consulte [Etags y etiquetas](dns-getstarted-create-dnszone.md#tagetag) para obtener más información.

El objeto del conjunto de registros también puede canalizarse en lugar de pasarse como un parámetro:

```powershell
Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre DNS de Azure, consulte [Introducción a DNS de Azure](dns-overview.md). Para obtener más información sobre la automatización de DNS, consulte [Creación de conjuntos de registros y zonas DNS con el SDK de .NET](dns-sdk.md).

Para obtener más información sobre los registros DNS inversos, consulte [Administración de registros de DNS inversos para los servicios mediante PowerShell](dns-reverse-dns-record-operations-ps.md).



<!--HONumber=Nov16_HO3-->


