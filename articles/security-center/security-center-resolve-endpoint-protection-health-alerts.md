---
title: Resolución de alertas de estado de Endpoint Protection en Azure Security Center | Microsoft Docs
description: En este documento se muestra cómo implementar la recomendación de Azure Security Center de resolver las alertas de estado de Endpoint Protection.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''

ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2016
ms.author: terrylan

---
# Resolución de alertas de estado de Endpoint Protection en Azure Security Center
Azure Security Center recomendará resolver las alertas de estado detectadas de Endpoint Protection. Asimismo, permite ver qué máquinas virtuales tienen errores de Endpoint Protection y cuántos.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.
> 
> 

## Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Resolver alertas de estado de Endpoint Protection**. ![Resolver alertas de estado de Endpoint Protection][1]
2. Se abrirá la hoja **Error de Endpoint Protection** que enumera las máquinas virtuales con errores y el número de errores en cada máquina virtual. Seleccione una máquina virtual de la lista. ![Error de Endpoint Protection][2]
3. Se abrirá la hoja **Lista de errores** de la máquina virtual seleccionada mostrando una lista de errores. Seleccione un error en la lista para obtener más información. Se abrirá una hoja con información sobre el error seleccionado. ![Lista de errores][3] ![Eventos de error][4]

## Consulte también
Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png

<!---HONumber=AcomDC_0803_2016-->