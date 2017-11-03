---
title: "Administración de soluciones conectadas de asociados en Azure Security Center | Microsoft Docs"
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
ms.date: 10/16/2017
ms.author: terrylan
ms.openlocfilehash: 181e1e00716987732ee809df6171c2f71087f3e1
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2017
---
# <a name="managing-connected-partner-solutions-with-azure-security-center"></a>Administración de las soluciones conectadas de asociados con Azure Security Center
Este artículo le explica la administración y supervisión de soluciones de seguridad conectadas en Azure Security Center.

## <a name="monitoring-partner-solutions"></a>Supervisión de soluciones de asociados
Para supervisar el estado de mantenimiento de las soluciones de seguridad conectadas y realizar la administración básica:

1. En **Security Center: Introducción**, seleccione **Soluciones de seguridad**.

  ![Seleccionar soluciones de seguridad][1]

  La sección **Soluciones conectadas** incluye las soluciones de seguridad que están conectadas a Security Center e información acerca del estado de mantenimiento de cada solución.

  ![Soluciones de socios][2]

   El estado de una solución de asociado puede ser:

   * Correcto (verde): no hay ningún problema de mantenimiento.
   * Incorrecto (rojo): hay un problema de estado que requiere atención inmediata.
   * Problemas de mantenimiento (naranja): la solución ha dejado de informar sobre su estado.
   * Sin información (gris): la solución no ha notificado nada todavía; es posible que el estado de una solución no se notifique si acaba de conectarse y todavía se está implementando, o no hay datos de mantenimiento disponibles.

   > [!NOTE]
   > Si los datos sobre el estado de mantenimiento no están disponibles, Security Center muestra la fecha y hora del último evento recibido para indicar si la solución está enviando notificaciones o no. Si no hay disponible ningún dato de mantenimiento y no ha recibido ninguna alerta en los últimos 14 días, Security Center indica que la solución es incorrecta o que no informa.
   >
   >

2. Seleccione **VER** para obtener información y opciones adicionales, entre las que se incluyen:

  - **Consola de solución**. Se abre la experiencia de administración de esta solución.
  - **Vincular VM**. Se abre la hoja Vincular aplicaciones. Aquí puede conectar recursos a la solución de asociados.
  - **Eliminar solución**.
  - **Configurar**.

   ![Detalle de solución de asociado][3]

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a administrar y supervisar las soluciones de seguridad conectadas en Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Introducción a las soluciones de seguridad](security-center-partner-integration.md): aprenda a conectar y administrar soluciones de seguridad.
* [Connecting Microsoft Advanced Threat Analytics (ATA)](security-center-ata-integration.md) (Conexión de Microsoft Advanced Threat Analytics (ATA)): aprenda a conectarse a las alertas desde ATA.
* [Conexión de Azure Active Directory (AD) Identity Protection ](security-center-aadip-integration.md): aprenda a conectarse a las alertas desde Azure AD Identity Protection.
* [Integración de asociados y soluciones](security-center-partner-integration.md): obtenga una idea general sobre la integración de otras soluciones de seguridad.
* [Administración y respuesta a las alertas de seguridad](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/partner-solutions.png
[3]: ./media/security-center-partner-solutions/partner-solutions-detail.png
