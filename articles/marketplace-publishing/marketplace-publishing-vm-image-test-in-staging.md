---
title: Probar su oferta de VM para Marketplace | Microsoft Docs
description: "Entienda cómo probar su imagen de VM para Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ms.openlocfilehash: 26f856059b381be91b9cdd1f98a11dc90813c0c5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Probar su oferta de VM para Azure Marketplace en un entorno de ensayo
En la etapa de ensayo, se implementa la SKU en un “espacio aislado” privado, donde puede probar y validar su funcionalidad antes de implementarla en Marketplace. La SKU aparece en un entorno de ensayo tal y como lo haría para un cliente que la ha implementado. Su imagen de VM debe estar certificada para trasladarse a un entorno de ensayo.

## <a name="step-1-push-your-offer-to-staging"></a>Paso 1: trasladar su oferta a un entorno de ensayo
1. En la pestaña **Publicar**, haga clic en **Push to Staging** (Pasar a ensayo).
   
    ![dibujo](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Si el Portal de publicación le informa errores, corríjalos.
3. En el cuadro de diálogo **¿Quién puede obtener acceso a su oferta de ensayo?** , escriba la lista de suscripciones de Azure que usará para obtener una vista previa de su oferta en el [portal de vista previa de Azure](https://portal.azure.com).
   
   > [!NOTE]
   > En el caso de las plantillas de soluciones y máquinas virtuales, **no** agregue a la lista blanca suscripciones de tipo CSP, DreamSpark o Azure bajo licencia Open.
   > 
   > 

    > En el caso de las máquinas virtuales, al hacer clic en el botón **PUSH TO STAGING**(INSERTAR EN ENTORNO DE ENSAYO) para enviar la oferta a producción, los siguientes pasos se realizan automáticamente. Podrá ver el progreso de cada paso en la pestaña Publicar en el Portal de publicación. Debe comprobar esta página de forma periódica (hasta que el estado muestre que está en el entorno provisional) para consultar cualquier información de error que tenga que solucionar.

    > - En primer lugar, la solicitud de ensayo pasa al equipo de certificación que valida el VHD. Sin embargo, si la solicitud tiene solo incluye un cambio de marketing, se omite el paso de certificación.
    > - Cuando se complete la certificación, se inicia la replicación de la oferta en todos los centros de datos de Azure. Normalmente, el proceso de replicación de la oferta tarda en completarse entre 24 y 48 horas, aunque puede extenderse hasta una semana en función del tamaño del VHD. Sin embargo, si la solicitud tiene solo incluye un cambio de marketing, la replicación se realiza más rápidamente.
    > - Cuando se complete la replicación, la oferta se mostrará en el [Portal de Azure](http:/portal.azure.com). En este momento, el estado muestra que la oferta está en el entorno de ensayo en el Portal de publicación. Una oferta de ensayo está visible en el [Portal de Azure](http:/portal.azure.com) solo con los identificadores de correo electrónico asociados con la suscripción con la que se envío la oferta al entorno de ensayo.

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com) mediante una de las suscripciones de Azure anteriores que se enumeran en el paso anterior.
2. Busque su oferta y valide los puntos de su imagen de VM:
   
   * Asegúrese de que el contenido de marketing se muestra correctamente en el Marketplace.
   * Implementación completa de la imagen de VM.
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> Su oferta permanecerá en un entorno de ensayo hasta que notifique a Microsoft a través del Portal de publicación [pestaña **Publicar** > **"Solicitar aprobación para enviar a producción"**] que está listo para enviar a producción. Este es un momento ideal para que todos los miembros de su equipo revisen todo antes de hacer efectiva la oferta.
> 
> La plataforma de almacenamiento provisional está diseñada para probar la oferta en un modo de versión preliminar del publicador. Se desaconseja encarecidamente usar esta plataforma con fines comerciales.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Ahora que el estado de su oferta es "de ensayo" y que ha probado su funcionalidad y contenido de marketing, puede continuar con la fase de publicación final, el **Paso 4**: [Implementación de su oferta en Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Consulte también
* [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

