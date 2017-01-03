---
title: "Creación de un conjunto de registros y registros para una zona DNS con PowerShell | Microsoft Docs"
description: "Creación de registros de host para Azure DNS. Configuración de conjuntos de registros y registros mediante PowerShell"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f4c17d03ff637659a7bc7cde378878d8a4827b80
ms.openlocfilehash: 175e8620828a2b0a0aff6de0b1a39860ea59514b

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Creación de conjuntos de registros y registros de DNS con PowerShell

> [!div class="op_single_selector"]
> * [Portal de Azure](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [CLI de Azure](dns-getstarted-create-recordset-cli.md)

Este artículo le guiará a través del proceso de creación de registros y conjuntos de registros mediante Azure PowerShell.

## <a name="introduction"></a>Introducción

Antes de crear registros DNS en Azure DNS, es necesario que comprenda cómo Azure DNS los organiza en conjuntos de registros DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Para más información sobre los registros DNS en Azure DNS, consulte [DNS zones and records](dns-zones-records.md) (Zonas y registros DNS).

## <a name="create-a-record-set-and-record"></a>Creación de un nuevo conjunto de registros y de un registro

En esta sección se describe cómo crear registros DNS en Azure DNS. En los ejemplos se supone que ya ha [instalado Azure PowerShell, iniciado sesión y creado una zona DNS](dns-getstarted-create-dnszone.md).

Todos los ejemplos de esta página usan el tipo de registro DNS “A”. Para ver otros tipos de registro y más información acerca cómo administrar los registros y los conjuntos de registros DNS, consulte [Administración de registros y conjuntos de registros DNS mediante PowerShell](dns-operations-recordsets.md).

Si el nuevo registro tiene el mismo nombre y tipo que un registro existente, debe [agregarlo al conjunto de registros existente](#add-a-record-to-an-existing-record-set). Si el nombre y tipo del nuevo registro son distintos de los de los registros existentes, será preciso [crear un nuevo conjunto de registros](#create-records-in-a-new-record-set). 

### <a name="create-records-in-a-new-record-set"></a>Creación de registros en un conjunto de registros nuevo

Los conjuntos de registros se crean mediante el cmdlet `New-AzureRmDnsRecordSet`. Al crear un conjunto de registros, deberá especificar el nombre del conjunto de registros, la zona, el período de vida (TTL), el tipo de registro y los registros que se crearán.

Para crear un conjunto de registros en el vértice de la zona (en este caso, "contoso.com"), utilice el nombre de registro "@",, incluidas las comillas. Esta es una convención común de DNS.

En el ejemplo siguiente se crea un nuevo conjunto de registros con el nombre relativo "www" en la zona DNS "contoso.com". El nombre completo del conjunto de registros es www.contoso.com. El tipo de registro es "A" y el valor de TTL es de 3600 segundos. El conjunto de registros contiene un único registro con la dirección IP "1.2.3.4".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

Si necesita crear un conjunto de registros nuevo que contenga más de un registro, primero debe crear una matriz local que contenga los registros que se van a agregar.  Esto se pasa a `New-AzureRmDnsRecordSet` como se indica a continuación:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>Adición de un registro a un conjunto de registros existente

Para agregar un registro a un conjunto de registros existente, siga estos tres pasos:

1. Obtenga el conjunto de registros existente.

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Agregue el nuevo registro al conjunto de registros local. Esta operación se realiza sin conexión.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Confirme el cambio al servicio Azure DNS 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>Comprobación de la resolución de nombres

Puede probar que los registros DNS están presentes en los servidores de nombres de Azure DNS con herramientas DNS tales como nslookup, DIG o el cmdlet de PowerShell [Resolve-DnsName](https://technet.microsoft.com/library/jj590781.aspx).

Si aún no ha delegado el dominio para usar la nueva zona en Azure DNS, deberá [dirigir la consulta de DNS directamente a uno de los servidores de nombres de la zona](dns-getstarted-create-dnszone.md#test-name-servers). Asegúrese de sustituir los valores correctos de su zona de registros en el siguiente ejemplo:

    nslookup
    > set type=A
    > server ns1-01.azure-dns.com
    > www.contoso.com

    Server:  ns1-01.azure-dns.com
    Address:  40.90.4.1

    Name:    www.contoso.com
    Address:  1.2.3.4


## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [delegar su nombre de dominio a los servidores de nombres de Azure DNS](dns-domain-delegation.md).

Aprenda a [administrar zonas DNS mediante PowerShell](dns-operations-dnszones.md).

Aprenda a [administrar registros y conjuntos de registros DNS mediante PowerShell](dns-operations-recordsets.md).





<!--HONumber=Jan17_HO1-->


