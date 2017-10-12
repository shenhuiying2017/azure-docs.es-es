---
title: "Creación de registros DNS personalizados para una aplicación web | Microsoft Docs"
description: "Creación de registros DNS de dominios personalizados para aplicaciones web mediante DNS de Azure."
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 6c16608c-4819-44e7-ab88-306cf4d6efe5
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d4b0aa817c3fd7f3304b5122ac584166d8079d3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-dns-records-for-a-web-app-in-a-custom-domain"></a>Creación de registros DNS para una aplicación web en un dominio personalizado

Puede utilizar DNS de Azure para hospedar un dominio personalizado para aplicaciones web. Por ejemplo, va a crear una aplicación web de Azure y desea que los usuarios obtengan acceso a ella con la utilización de contoso.com o www.contoso.com como FQDN.

Para ello, deberá crear dos registros:

* Un registro raíz "A" que apunta a contoso.com
* Un registro "CNAME" para el nombre www que apunta al registro A

Tenga en cuenta que si crea un registro A para una aplicación web en Azure, este debe actualizarse manualmente si la dirección IP subyacente de la aplicación web cambia.

## <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar, debe crear una zona DNS en DNS de Azure y delegar la zona del registrador a DNS de Azure.

1. Para crear una zona DNS, siga los pasos descritos en [Introducción a DNS de Azure](dns-getstarted-create-dnszone.md).
2. Para delegar su DNS a DNS de Azure, siga los pasos descritos en [Delegación de un dominio en DNS de Azure](dns-domain-delegation.md).

Después de crear una zona y delegarla a DNS de Azure, podrá crear registros para el dominio personalizado.

## <a name="1-create-an-a-record-for-your-custom-domain"></a>1. Creación de un registro A para un dominio personalizado

Un registro A se usa para asignar un nombre a su dirección IP. En el ejemplo siguiente, asignaremos @ como un registro A a una dirección IPv4:

### <a name="step-1"></a>Paso 1

Cree un registro y asígnelo a una variable $rs

```powershell
$rs= New-AzureRMDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 600
```

### <a name="step-2"></a>Paso 2

Agregue el valor IPv4 al conjunto de registros creado previamente "@" mediante la variable $rs asignada. El valor de IPv4 asignado será la dirección IP de la aplicación web.

Para buscar la dirección IP para una aplicación web, siga los pasos descritos en [Configuración de un nombre de dominio personalizado en Azure App Service](../app-service/app-service-web-tutorial-custom-domain.md).

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Ipv4Address "<your web app IP address>"
```

### <a name="step-3"></a>Paso 3

Confirme los cambios introducidos en el conjunto de registros. Use `Set-AzureRMDnsRecordSet` para cargar los cambios del conjunto de registros en DNS de Azure:

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="2-create-a-cname-record-for-your-custom-domain"></a>2. Creación de un registro CNAME para el dominio personalizado

Si el dominio ya está administrado por DNS de Azure (consulte [Delegación de dominios DNS](dns-domain-delegation.md)), puede utilizar el ejemplo siguiente para crear un registro CNAME para contoso.azurewebsites.net.

### <a name="step-1"></a>Paso 1

Abra PowerShell y cree un nuevo conjunto de registros CNAME y asígnelo a una variable $rs. Este ejemplo creará un tipo de conjunto de registros CNAME con un "período de vida" de 600 segundos en la zona DNS denominada "contoso.com".

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName contoso.com -ResourceGroupName myresourcegroup -Name "www" -RecordType "CNAME" -Ttl 600
```

El ejemplo siguiente es la respuesta.

```
Name              : www
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Paso 2

Una vez creado el conjunto de registros CNAME, deberá crear un valor de alias que apuntará a la aplicación web.

Mediante la variable asignada previamente "$rs", puede usar el comando de PowerShell siguiente para crear el alias para la aplicación web contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "contoso.azurewebsites.net"
```

El ejemplo siguiente es la respuesta.

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Paso 3

Confirme los cambios mediante el cmdlet `Set-AzureRMDnsRecordSet` :

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

Puede validar que el registro se ha creado correctamente consultando “www.contoso.com” con nslookup, como se muestra a continuación:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net
```

## <a name="create-an-awverify-record-for-web-apps"></a>Creación de un registro "awverify" para aplicaciones web

Si decide usar un registro A para la aplicación web, debe seguir un proceso de verificación para asegurarse de que es el titular del dominio personalizado. Este paso de comprobación se realiza mediante la creación de un registro CNAME especial denominado "awverify". Esta sección se aplica solo a los registros A.

### <a name="step-1"></a>Paso 1

Cree el registro "awverify". En el ejemplo siguiente, el registro "awverify" se creará para contoso.com para verificar la titularidad del dominio personalizado.

```powershell
$rs = New-AzureRMDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "myresourcegroup" -Name "awverify" -RecordType "CNAME" -Ttl 600
```

El ejemplo siguiente es la respuesta.

```
Name              : awverify
ZoneName          : contoso.com
ResourceGroupName : myresourcegroup
Ttl               : 600
Etag              : 8baceeb9-4c2c-4608-a22c-229923ee1856
RecordType        : CNAME
Records           : {}
Tags              : {}
```

### <a name="step-2"></a>Paso 2

Una vez creado el conjunto de registros "awverify", asigne el alias del conjunto de registros CNAME. En el ejemplo siguiente, se asignará el alias del conjunto de registros CNAME a awverify.contoso.azurewebsites.net.

```powershell
Add-AzureRMDnsRecordConfig -RecordSet $rs -Cname "awverify.contoso.azurewebsites.net"
```

El ejemplo siguiente es la respuesta.

```
    Name              : awverify
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {awverify.contoso.azurewebsites.net}
    Tags              : {}
```

### <a name="step-3"></a>Paso 3

Confirme los cambios mediante el cmdlet `Set-AzureRMDnsRecordSet cmdlet`, como se muestra en el comando siguiente.

```powershell
Set-AzureRMDnsRecordSet -RecordSet $rs
```

## <a name="next-steps"></a>Pasos siguientes

Siga los pasos descritos en [Configurar un nombre de dominio personalizado en el Servicio de aplicaciones de Azure](../app-service/app-service-web-tutorial-custom-domain.md) para configurar la aplicación web para que use un dominio personalizado.
