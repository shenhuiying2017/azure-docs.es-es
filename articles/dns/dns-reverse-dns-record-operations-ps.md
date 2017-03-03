---
title: "Administración de registros DNS inversos en Azure DNS mediante PowerShell | Microsoft Docs"
description: Azure DNS permite administrar registros de DNS inversos o registros PTR de servicios Azure mediante PowerShell en Resource Manager
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: b95703c5-e94e-4009-ab37-0c3f7908783c
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dd020bf625510eb90af2e1ad19c155831abd7e75
ms.openlocfilehash: 78de3b8dd2d8bd0992bfbacb1079825dca486442
ms.lasthandoff: 02/10/2017


---
# <a name="manage-reverse-dns-records-for-your-azure-services-using-powershell"></a>Administración de registros de DNS inversos en servicios de Azure mediante PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Para más información sobre el modelo de implementación clásica, consulte [Administración de registros de DNS inversos para los servicios de Azure (clásicos) mediante Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validación de registros de DNS inversos

Para asegurarse de que un tercero no pueda crear registros de DNS inversos que se asignen a sus dominios DNS, Azure solo permite la creación de un registro de DNS inverso en el que se cumpla una de las siguientes condiciones:

* El valor de "ReverseFqdn" es el mismo que el de "Fqdn" para el recurso de dirección IP pública para el que se ha especificado o el de "Fqdn" para cualquier dirección IP pública dentro de la misma suscripción. Por ejemplo, "ReverseFqdn" es "contosoapp1.northus.cloudapp.azure.com".
* El valor de "ReverseFqdn" se resuelve directamente en el nombre o la IP de la dirección IP pública para la que se ha especificado, o en el IP o "Fqdn" de cualquier dirección IP pública dentro de la misma suscripción. Por ejemplo, "ReverseFqdn" es "app1.contoso.com", que es un alias CNAME para "contosoapp1.northus.cloudapp.azure.com".

Solo se realizan comprobaciones de validación cuando la propiedad DNS inversa de una dirección IP pública se define o modifica. No se llevan a cabo revalidaciones periódicas.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Adición de DNS inversos a las direcciones IP públicas existentes

Puede agregar DNS inversos a una dirección IP pública existente usando el cmdlet `Set-AzureRmPublicIpAddress`:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

Si desea agregar un DNS inverso a una dirección IP pública existente que no tenga aún un nombre DNS, debe especificar también un nombre DNS. Puede realizar la adición para conseguir esto mediante el cmdlet "Set-AzureRmPublicIpAddress":

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings = New-Object -TypeName "Microsoft.Azure.Commands.Network.Models.PSPublicIpAddressDnsSettings"
$pip.DnsSettings.DomainNameLabel = "contosoapp1"
$pip.DnsSettings.ReverseFqdn = "contosoapp1.westus.cloudapp.azure.com."
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Creación de una dirección IP pública con un DNS inverso

Puede agregar una nueva dirección IP pública con la propiedad DNS inversa especificada usando el cmdlet `New-AzureRmPublicIpAddress`:

```powershell
New-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS" -Location "WestUS" -AllocationMethod Dynamic -DomainNameLabel "contosoapp2" -ReverseFqdn "contosoapp2.westus.cloudapp.azure.com."
```

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Visualización de DNS inversos para las direcciones IP públicas existentes

Puede ver el valor configurado para una dirección IP pública existente usando el cmdlet `Get-AzureRmPublicIpAddress`:

```powershell
Get-AzureRmPublicIpAddress -Name "PublicIP2" -ResourceGroupName "NRP-DemoRG-PS"
```

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Eliminación de DNS inversos de direcciones IP públicas existentes

Puede quitar una propiedad DNS inversa de una dirección IP pública existente usando el cmdlet `Set-AzureRmPublicIpAddress`. Para ello, se deja en blanco el valor de la propiedad ReverseFqdn:

```powershell
$pip = Get-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName "NRP-DemoRG-PS"
$pip.DnsSettings.ReverseFqdn = ""
Set-AzureRmPublicIpAddress -PublicIpAddress $pip
```

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


