---
title: "Administración de registros de DNS inversos para servicios Azure mediante la CLI de Azure 1.0 | Microsoft Docs"
description: "Cómo administrar registros de DNS inversos o registros PTR para los servicios de Azure mediante la CLI de Azure 1.0 en Resource Manager"
services: DNS
documentationcenter: na
author: s-malone
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c655707e-1156-4893-b163-0b228ffd25d2
ms.service: DNS
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2016
ms.author: smalone
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 227ede3445ced9cca71a3879afb0bb6c8c5c2482
ms.lasthandoff: 02/27/2017


---
# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-using-the-azure-cli-10"></a>Administración de registros de DNS inversos para los servicios de Azure mediante la CLI de Azure 1.0

[!INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]


[!INCLUDE [dns-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versiones de la CLI para completar la tarea

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

* [CLI de Azure 1.0](dns-reverse-dns-record-operations-cli-nodejs.md): la CLI para los modelos de implementación clásica y de administración de recursos.
* [CLI de Azure 2.0](dns-reverse-dns-record-operations-cli.md): la CLI de última generación para el modelo de implementación de administración de recursos.

## <a name="introduction"></a>Introducción

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

Para más información sobre el modelo de implementación clásica, consulte [Administración de registros de DNS inversos para los servicios de Azure (clásicos) mediante Azure PowerShell](dns-reverse-dns-record-operations-classic-ps.md).


## <a name="validation-of-reverse-dns-records"></a>Validación de registros de DNS inversos
Para asegurarse de que un tercero no pueda crear registros de DNS inversos que se asignen a sus dominios DNS, Azure solo permite la creación de un registro de DNS inverso en el que se cumpla una de las siguientes condiciones:

* El valor de "ReverseFqdn" es el mismo que el de "Fqdn" para el recurso de dirección IP pública para el que se ha especificado o el de "Fqdn" para cualquier dirección IP pública dentro de la misma suscripción. Por ejemplo, "ReverseFqdn" es "contosoapp1.northus.cloudapp.azure.com.".
* El valor de "ReverseFqdn" se resuelve directamente en el nombre o la IP de la dirección IP pública para la que se ha especificado, o en el IP o "Fqdn" de cualquier dirección IP pública dentro de la misma suscripción. Por ejemplo, "ReverseFqdn" es "app1.contoso.com.", que es un alias CNAME para "contosoapp1.northus.cloudapp.azure.com.".

Solo se realizan comprobaciones de validación cuando la propiedad DNS inversa de una dirección IP pública se define o modifica. No se llevan a cabo revalidaciones periódicas.

## <a name="add-reverse-dns-to-existing-public-ip-addresses"></a>Adición de DNS inversos a las direcciones IP públicas existentes
Puede agregar DNS inversos a una dirección IP pública existente usando azure network public-ip set:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -f contosoapp1.westus.cloudapp.azure.com.

Si desea agregar un DNS inverso a una dirección IP pública existente que no tenga aún un nombre DNS, debe especificar también un nombre DNS. Puede realizar la adición para conseguir esto con azure network public-ip set:

    azure network public-ip set -n PublicIp -g NRP-DemoRG-PS -d contosoapp1 -f contosoapp1.westus.cloudapp.azure.com.

## <a name="create-a-public-ip-address-with-reverse-dns"></a>Creación de una dirección IP pública con un DNS inverso
Puede agregar una nueva dirección IP pública con la propiedad DNS inversa especificada usando azure network public-ip create:

    azure network public-ip create -n PublicIp3 -g NRP-DemoRG-PS -l westus -d contosoapp3 -f contosoapp3.westus.cloudapp.azure.com.

## <a name="view-reverse-dns-for-existing-public-ip-addresses"></a>Visualización de DNS inversos para las direcciones IP públicas existentes
Puede ver el valor configurado para una dirección IP pública existente usando azure network public-ip show:

    azure network public-ip show -n PublicIp3 -g NRP-DemoRG-PS

## <a name="remove-reverse-dns-from-existing-public-ip-addresses"></a>Eliminación de DNS inversos de direcciones IP públicas existentes
Puede quitar una propiedad DNS inversa de una dirección IP pública existente usando azure network public-ip set. Para ello, se deja en blanco el valor de la propiedad ReverseFqdn:

    azure network public-ip set -n PublicIp3 -g NRP-DemoRG-PS –f “”

[!INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[!INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-arm-include.md)]


