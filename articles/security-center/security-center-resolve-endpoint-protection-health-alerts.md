---
title: "Resolución de alertas de estado de Endpoint Protection en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar la recomendación de Azure Security Center de resolver las alertas de estado de Endpoint Protection."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4050f453-98fc-4314-8438-d476469757fb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5e6b136d6bd3b11fb82126d104fd0cb149255118


---
# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Resolución de alertas de estado de Endpoint Protection en Azure Security Center
Azure Security Center recomendará resolver las alertas de estado detectadas de Endpoint Protection.  Asimismo, permite ver qué máquinas virtuales tienen errores de Endpoint Protection y cuántos.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.
> 
> 

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Resolver alertas de estado de Endpoint Protection**.
   ![Resolver alertas de estado de Endpoint Protection][1]
2. Se abrirá la hoja **Error de Endpoint Protection** que enumera las máquinas virtuales con errores y el número de errores en cada máquina virtual. Seleccione una máquina virtual de la lista.
   ![Endpoint protection failure][2]
3. Se abrirá la hoja **Lista de errores** de la máquina virtual seleccionada mostrando una lista de errores. Seleccione un error en la lista para obtener más información. Se abrirá una hoja con información sobre el error seleccionado.
   ![Lista de errores][3]
   ![Evento de error][4]

## <a name="see-also"></a>Consulte también
Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png



<!--HONumber=Nov16_HO3-->


