---
title: "Administración de soluciones de asociados en Azure Security Center | Microsoft Docs"
description: "En este documento se explica cómo Azure Security Center permite supervisar de un vistazo el estado de mantenimiento de las soluciones de asociados integradas con su suscripción de Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 3ff38892f198ab5dfb9e08c1c01b942810a40260
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Supervisión de las soluciones de asociados con Azure Security Center
En este documento se explica cómo supervisar el estado de mantenimiento de las soluciones de asociados en Azure Security Center.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. Este documento no es una guía paso a paso.
>
>

## <a name="monitoring-partner-solutions"></a>Supervisión de soluciones de asociados
El icono de **Soluciones de seguridad** de la hoja **Información general** permite supervisar a simple vista el estado de mantenimiento de las soluciones de asociados que se integran con su suscripción de Azure.

![Icono Partner solutions (Soluciones de asociados)][1]

Para ver el estado de sus soluciones de asociados:

1. Seleccione el icono de **Soluciones de seguridad**. **Soluciones de seguridad** abre una hoja con una lista de todas las soluciones de asociados conectadas a Security Center.

   ![Soluciones de socios][3]

   El estado de una solución de asociado puede ser:

   * Protegido (verde): no hay ningún problema de estado.
   * Incorrecto (rojo): hay un problema de estado que requiere atención inmediata.
   * Información detenida (naranja): la solución ha dejado de informar sobre su estado.
   * Estado de protección desconocido (naranja): el estado de la solución se desconoce en este momento debido a un error en el proceso de agregación de un nuevo recurso a la solución existente.
   * Sin información (gris): la solución no ha notificado nada todavía; es posible que el estado de una solución no se notifique si acaba de conectarse y todavía se está implementando.

2. Seleccione una solución de asociado. Se abre una hoja que muestra que el estado de la solución de asociado y de sus recursos asociados. Seleccione **Consola de soluciones** para abrir la experiencia de administración de asociados de esta solución.

   ![Detalle de solución de asociado][4]
3. Vuelva a la hoja **Qualys** y seleccione **Vincular VM**. Se abre la hoja **Link Applications** (Vincular aplicaciones). Aquí puede conectar recursos a la solución de asociados.

   ![Vinculación de recursos a soluciones de asociados][5]

## <a name="next-steps"></a>Pasos siguientes
En este documento, se ha presentado el icono de **Soluciones de asociados** de Security Center. Para obtener más información sobre la integración con asociados para mejorar la seguridad general, consulte el artículo sobre [integración de asociados y soluciones](security-center-partner-integration.md).

Para más información sobre Security Center, consulte los siguientes artículos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png
