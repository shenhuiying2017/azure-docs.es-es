---
title: "Corrección de vulnerabilidades del SO en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar la recomendación de Azure Security Center **Corregir las vulnerabilidades del sistema operativo**."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c88dc19cdacc94b9b81adcdbf69f22aac8f6eabe
ms.lasthandoff: 03/22/2017


---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Corrección de vulnerabilidades del SO en Azure Security Center
Azure Security Center analiza a diario las configuraciones del sistema operativo (SO) de la máquina virtual que podrían provocar que esta fuera más vulnerable a ataques. Asimismo, recomienda cambios de configuración para solucionar estas vulnerabilidades. Azure Security Center recomienda que solucione las vulnerabilidades cuando la configuración del SO de la máquina virtual no coincida con las reglas de configuración recomendadas.

> [!NOTE]
> Para obtener más información sobre las configuraciones específicas que se están supervisando, [consulte la lista de reglas de configuración recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). En este momento, Windows Server 2016 no es totalmente compatible.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
>
>

1. En la hoja **Recomendaciones**, seleccione **Corregir las vulnerabilidades del sistema operativo**.
   ![Corrección de vulnerabilidades del SO][1]

    La hoja **Corregir las vulnerabilidades del sistema operativo** abre y enumera las máquinas virtuales con configuraciones del sistema operativo que no coinciden con las reglas de configuración recomendadas.  En cada máquina virtual, la hoja identifica lo siguiente:

   * **REGLAS CON ERROR** : número de reglas con errores de configuración del sistema operativo de la máquina virtual.
   * **HORA DE LA ÚLTIMA DETECCIÓN** : fecha y hora en que Azure Security Center realizó el último examen de la configuración del sistema operativo de la máquina virtual.
   * **ESTADO** : estado actual de la vulnerabilidad.

     * Abierta: la vulnerabilidad aún no se ha solucionado.
     * En curso: se está aplicando la corrección en la vulnerabilidad; no se requiere ninguna acción de su parte.
     * Resuelta: la vulnerabilidad ya se solucionó (cuando el problema se ha resuelto, la entrada aparece atenuada).
   * **GRAVEDAD** : todas las vulnerabilidades se establecen en un nivel de gravedad Baja; es decir, debe solucionarse, pero no se requiere atención inmediata.

2. Seleccione una máquina virtual. Una hoja de esa VM se abre y muestra las reglas con error.
   ![Reglas de configuración con error][2]

3. Seleccione una regla. En este ejemplo, seleccionamos **La contraseña debe cumplir los requisitos de complejidad**. Se abre una hoja que describe la regla con errores y el impacto. Revise los detalles y tenga en cuenta cómo se aplicarán las configuraciones del sistema operativo.
  ![Descripción de la regla con error][3]

  Azure Security Center utiliza Common Configuration Enumeration (CCE) con el fin de asignar identificadores únicos para las reglas de configuración. En esta hoja, se proporciona la siguiente información:

  - NOMBRE: nombre de la regla.
  - GRAVEDAD: valor de gravedad de CCE, que puede ser Crítico, Importante o Advertencia.
  - CCIED: identificador único de CCE para la regla.
  - DESCRIPCIÓN: descripción de la regla.
  - VULNERABILIDAD: explicación de la vulnerabilidad o el riesgo si no se aplica la regla.
  - IMPACTO: impacto de negocio cuando se aplica la regla.
  - VALOR ESPERADO: valor esperado cuando Azure Security Center analiza la configuración del sistema operativo de la máquina virtual comparándola con la regla.
  - FUNCIONAMIENTO DE LA REGLA: cómo utiliza Azure Security Center la regla durante el análisis de la configuración del sistema operativo de la máquina virtual comparándola con la regla.
  - VALOR REAL: valor devuelto después de analizar la configuración del sistema operativo de la máquina virtual comparándola con la regla.
  - RESULTADO DE LA EVALUACIÓN: resultado del análisis, que puede ser Sin errores o Con errores.

## <a name="see-also"></a>Consulte también
En este artículo se muestra cómo implementar la recomendación de Azure Security Center de corregir vulnerabilidades del sistema operativo. Puede revisar el conjunto de reglas de configuración [aquí](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Azure Security Center utiliza Common Configuration Enumeration (CCE) con el fin de asignar identificadores únicos para las reglas de configuración. Visite el sitio de [CCE](https://nvd.nist.gov/cce/index.cfm) para obtener más información.

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/recommendation.png
[2]:./media/security-center-remediate-os-vulnerabilities/vm-remediate-os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png

