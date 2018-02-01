---
title: "Corrección de configuraciones de seguridad en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar la recomendación de Azure Security Center \"Corregir las configuraciones de seguridad\"."
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
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 477973298d8cc9d99da78e36274933e0bb737c4f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Corregir las configuraciones de seguridad en Azure Security Center
Azure Security Center analiza diariamente el sistema operativo (SO) de sus máquinas virtuales (VM) y equipos en busca de alguna configuración que pueda hacer que estos sean más vulnerables frente a los ataques. Security Center recomienda resolver las vulnerabilidades cuando la configuración del sistema operativo no coincida con las reglas de configuración de seguridad recomendadas y realizar cambios en la configuración para hacer frente a estas vulnerabilidades.

Consulte la [lista de reglas de configuración recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obtener más información sobre las configuraciones específicas que se están supervisando. Para saber cómo personalizar las valoraciones de configuración de seguridad, consulte [Personalización de las configuraciones de seguridad del sistema operativo en Azure Security Center (versión preliminar)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
La opción de "Corregir las configuraciones de seguridad" se presenta como una recomendación en Security Center. La recomendación se muestra en **Recomendaciones** > **Compute**.

En este ejemplo, se examinará la recomendación "Corregir las configuraciones de seguridad" que hay en **Compute**.
1. En el panel izquierdo de Security Center, seleccione **Compute**.  
  Se abrirá la ventana **Compute**.

   ![Corrección de configuraciones de seguridad][1]

2. Seleccione **Corregir las configuraciones de seguridad**.  
  Se abre la ventana **Configuraciones de seguridad**.

   ![Ventana "Configuraciones de seguridad".][2]

  La sección superior del panel muestra lo siguiente:

  - **Reglas con errores según su gravedad**: número total de reglas en las que se produjo un error en la configuración del sistema operativo en las máquinas virtuales y equipos, distribuidas según su gravedad.
  - **Reglas con errores según el tipo**: número total de reglas en las que se produjo un error en la configuración del sistema operativo en las máquinas virtuales y equipos, distribuidas según el tipo.
  - **Reglas de Windows con errores**: número total de reglas en las que se produjo un error debido a las configuraciones del sistema operativo Windows.
  - **Reglas de Linux con errores**: número total de reglas en las que se produjo un error debido a las configuraciones del sistema operativo Linux.

  En la parte inferior del panel se muestran todas las reglas con errores en las máquinas virtuales y equipos, así como la gravedad de la actualización que falta. La lista contiene los siguientes elementos:

  - **CCEID**: identificador único de CCE para la regla. Security Center utiliza Common Configuration Enumeration (CCE) con el fin de asignar identificadores únicos a las reglas de configuración.
  - **Nombre**: nombre de la regla con error.
  - **Tipo de regla**: tipo de regla de la *clave del Registro*, *directiva de seguridad* o *directiva de auditoría*.
  - **Número de máquinas virtuales y equipos**: número total de máquinas virtuales y equipos en los que se aplica esa regla con errores.
  - **Gravedad de la regla**: valor de CCE, que puede ser *Crítico*, *Importante* o *Advertencia*.
  - **Estado**: el estado actual de la recomendación:

    - **Abierta**: la recomendación aún no se ha abordado.
    - **En curso**: la recomendación se está aplicando actualmente a los recursos y no se requiere ninguna acción por su parte.
    - **Resuelta**: se ha aplicado la recomendación. Si se ha resuelto el problema, la entrada aparecerá atenuada.

3. Para ver los detalles de una regla con errores, selecciónela en la lista.

   ![Vista detallada de una regla de configuración con errores][3]

   La vista detallada muestra la siguiente información:

   - **Nombre**: nombre de la regla.
   - **CCIED**: identificador único de CCE para la regla.
   - **Versión del sistema operativo**: versión del sistema operativo de la máquina virtual o el equipo.
   - **Gravedad de la regla**: valor de CCE, que puede ser *Crítico*, *Importante* o *Advertencia*.
   - **Descripción completa**: descripción de la regla.
   - **Vulnerabilidad**: explicación de la vulnerabilidad o el riesgo si no se aplica la regla.
   - **Posible impacto**: impacto empresarial cuando se aplica la regla.
   - **Contramedida**: pasos de corrección.
   - **Valor esperado**: valor esperado cuando Security Center analiza la configuración del sistema operativo de la máquina virtual comparándola con la regla.
   - **Valor real**: valor devuelto después de analizar la configuración del sistema operativo de la máquina virtual comparándola con la regla.
   - **Funcionamiento de la regla**: cómo utiliza Security Center la regla durante el análisis de la configuración del sistema operativo de la máquina virtual en comparación con la regla.

4. En la parte superior de la ventana de la vista detallada, seleccione **Buscar**.  
  La búsqueda se abre con una lista de áreas de trabajo que contienen máquinas virtuales y equipos con el error de coincidencia de configuraciones de seguridad. Esta selección del área de trabajo solo aparece si la regla seleccionada se aplica a varias máquinas virtuales que están conectadas a diferentes áreas de trabajo.

   ![Lista de áreas de trabajo][4]

5. Seleccione un área de trabajo.  
  Se abre una consulta de búsqueda de Log Analytics filtrada con el área de trabajo con el error de coincidencia de las configuraciones de seguridad.

   ![Área de trabajo con una vulnerabilidad del sistema operativo][5]

6. Seleccione un equipo en la lista.  
  Se abre otro resultado de la búsqueda con la información filtrada solo para ese equipo.

   ![Información detallada sobre el equipo seleccionado][6]

## <a name="next-steps"></a>pasos siguientes
En este artículo se muestra cómo implementar la recomendación de Security Center de corregir las configuraciones de seguridad. Para saber cómo personalizar las valoraciones de configuración de seguridad, consulte [Personalización de las configuraciones de seguridad del sistema operativo en Azure Security Center (versión preliminar)](security-center-customize-os-security-config.md).

Consulte la [lista de reglas de configuración recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obtener más información sobre las configuraciones específicas que se están supervisando. Security Center utiliza Common Configuration Enumeration (CCE) con el fin de asignar identificadores únicos a las reglas de configuración. Visite el sitio de [CCE](https://nvd.nist.gov/cce/index.cfm) para obtener más información.

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* Para obtener una lista de máquinas virtuales de Windows y Linux compatibles, consulte [Plataformas compatibles con Azure Security Center](security-center-os-coverage.md). 
* Para aprender a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure, consulte [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md). 
* Para saber qué recomendaciones pueden ayudarle a proteger los recursos de Azure, consulte [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md). 
* Para saber cómo supervisar el mantenimiento de los recursos de Azure, consulte [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). 
* Para aprender a administrar y responder a alertas de seguridad, consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md).
* Para aprender a supervisar el estado de mantenimiento de las soluciones de asociados, consulte [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md).
* Para obtener respuestas a las preguntas más frecuentes sobre cómo usar el servicio, consulte [Preguntas frecuentes acerca de Azure Security Center](security-center-faq.md).
* Para leer entradas de blog sobre el cumplimiento y la seguridad de Azure, consulte [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
