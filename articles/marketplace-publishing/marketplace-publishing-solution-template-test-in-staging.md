---
title: "Prueba de la oferta de plantilla de solución en Marketplace | Microsoft Docs"
description: "Conozca cómo probar la oferta de plantilla de solución en Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: hascipio; v-divte
ms.openlocfilehash: da1fc4713fd1d832c7ba91226f72cbef63b241bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="test-your-solution-template-offer-in-staging"></a>Prueba de la oferta de plantilla de solución en ensayo
En la etapa de ensayo se implementa la oferta en un “espacio aislado” privado, donde puede probar y comprobar su funcionalidad antes pasarla a producción. La oferta aparece en un entorno de ensayo tal y como lo haría para un cliente que la ha implementado. La oferta debe estar certificada para pasar a un entorno de ensayo.

Después de preconfigurar la oferta, puede verla y probarla en el [Portal de Azure](https://portal.azure.com/).

Siga los pasos indicados a continuación para insertar la oferta en el entorno de ensayo y probarla en el [Portal de Azure](https://portal.azure.com/).

1. Vaya al [Portal de publicación](https://publish.windowsazure.com) >  pestaña **Plantillas de solución** > su oferta > **Publicar** > **Push to Staging** (Pasar a ensayo).
2. Proporcione la lista de suscripciones de Azure que va a usar para obtener la vista previa de la oferta y probarla.
3. Inicie sesión en el Portal de vista previa de Azure mediante el identificador de suscripción que usó en el paso anterior.
4. Realice al menos una ronda de pruebas en el Portal de vista previa de Azure sobre los puntos que se mencionan a continuación:
   * Asegúrese de que el contenido de marketing se muestra correctamente en el Azure Marketplace.
   * Compruebe la implementación completa de la topología.
   * Realice pruebas de rendimiento y pruebas de esfuerzo.
   * Asegúrese de que la topología se ajusta a las prácticas recomendadas.

## <a name="next-steps"></a>Pasos siguientes
Si está satisfecho con los resultados, puede continuar en la fase de publicación de la oferta final, **paso 4**: [Implementación de la oferta en Marketplace](marketplace-publishing-push-to-production.md). Si no, realice cambios en la oferta y vuelva a solicitar la certificación.

> [!NOTE]
> Para los cambios de contenido de marketing, no se requiere certificación.
> 
> 

Vea [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md) para obtener una guía de todas las tareas del publicador.

