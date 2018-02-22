---
title: "Configuración de zonas de búsqueda inversa para la comprobación del banner de SMTP en Azure | Microsoft Docs"
description: "Describe cómo configurar zonas de búsqueda inversa para la comprobación de un banner de SMTP en Azure"
services: virtual-network
documentationcenter: virtual-network
author: genlin
manager: WillChen
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/06/2018
ms.author: genli
ms.custom: 
ms.openlocfilehash: 1e95b00ea08105238a860265e46275c24ed7bfbd
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/11/2018
---
#  <a name="configure-reverse-lookup-zones-for-an-smtp-banner-check"></a>Configuración de zonas de búsqueda inversa para la comprobación de un banner de SMTP

Este artículo describe cómo usar una zona inversa en Azure DNS y crear un registro DNS inverso (PTR) para la comprobación del banner de SMTP. 

## <a name="symptom"></a>Síntoma

Si hospeda un servidor SMTP en Microsoft Azure, puede que reciba el siguiente mensaje de error al enviar o recibir un mensaje de servidores de correo remotos:

**554: no hay ningún registro PTR** 

## <a name="solution"></a>Solución

Para una dirección IP virtual de Azure, los registros inversos se crean en zonas de dominio propiedad de Microsoft y no en las zonas de dominios personalizados.

Para configurar registros PTR en zonas propiedad de Microsoft, use la propiedad -ReverseFqdn en el recurso PublicIpAddress. Para obtener más información, consulte [Configuración de DNS inversos para servicios hospedados en Azure](../dns/dns-reverse-dns-for-azure-services.md). 

Al configurar registros PTR, asegúrese de que la dirección IP y el FQDN inverso pertenecen a la suscripción. Si intenta establecer un FQDN inverso que no pertenece a la suscripción, recibirá el siguiente mensaje de error:

    Set-AzureRmPublicIpAddress : ReverseFqdn mail.contoso.com that PublicIPAddress ip01 is trying to use does not belong to subscription <Subscription ID>. One of the following conditions need to be met to establish ownership: 
                        
    1) ReverseFqdn debe coincidir con el FQDN de cualquier recurso de IP pública de la suscripción; 
    2) ReverseFqdn debe resolverse en el FQDN (a través de la cadena de registros CName) de cualquier recurso de IP pública de la suscripción; 
    3) Debe resolverse en la dirección IP (a través de la cadena de registros CNAME y D) de un recurso de IP pública estática de la suscripción. 

Si cambia manualmente el banner de SMTP para que coincida con el FQDN inverso predeterminado, el servidor de correo remoto puede seguir generando errores porque es posible que espere que el host del banner de SMTP coincida con el registro MX para el dominio.