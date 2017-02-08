---
title: "Administración de registros de DNS inversos para los servicios de Azure (clásicos) mediante PowerShell | Microsoft Docs"
description: "Cómo administrar registros de DNS inversos o registros PTR para servicios Azure usando PowerShell en el modelo de implementación clásico. "
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 9c24d176-6bce-4277-a14c-80fe44a20a87
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: fe5c9cfe0edd769635301fdcb40fc1f71d430165


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Administración de registros de DNS inversos para los servicios de Azure (clásicos) mediante Azure PowerShell

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]


> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Obtenga información sobre cómo [realizar estos pasos con el modelo de Resource Manager](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validación de registros de DNS inversos
Para asegurarse de que un tercero no pueda crear registros de DNS inversos que se asignen a sus dominios DNS, Azure solo permite la creación de un registro de DNS inverso en el que se cumpla una de las siguientes condiciones:

* El FQDN DNS inverso es el nombre del servicio en la nube para el que se ha especificado o cualquier nombre de servicio en la nube dentro de la misma suscripción. Por ejemplo, el DNS inverso es "contosoapp1.cloudapp.net.".
* El FQDN DNS inverso se resuelve directamente en el nombre o la IP del servicio en la nube para el que se ha especificado, o bien en cualquier nombre o IP de servicio en la nube dentro de la misma suscripción. Por ejemplo, el DNS es "app1.contoso.com.", que es un alias CNAME para contosoapp1.cloudapp.net.

Solo se realizan comprobaciones de validación cuando la propiedad DNS inversa de un servicio en la nube se define o modifica. No se llevan a cabo revalidaciones periódicas.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Adición de DNS inverso a los servicios en la nube existentes
Puede agregar un registro DNS inverso a un servicio en la nube existente usando el cmdlet “Set-AzureService”.

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Creación de un servicio en la nube con un DNS inverso
Puede agregar un nuevo servicio en la nube con la propiedad DNS inversa especificada usando el cmdlet “Set-AzureService”.

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Visualización de DNS inverso para los servicios en la nube existentes
Puede ver el valor configurado para un servicio en la nube existente usando el cmdlet “Get-AzureService”:

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Eliminación de DNS inverso de los servicios en la nube existentes
Puede quitar una propiedad DNS inversa desde un servicio en la nube existente usando el cmdlet “Set-AzureService”. Para ello, deje en blanco el valor de la propiedad DNS inversa:

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]




<!--HONumber=Dec16_HO2-->


