---
title: "Corrección de configuraciones de seguridad en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar la recomendación de Azure Security Center **Corregir las configuraciones de seguridad**."
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
ms.openlocfilehash: 412234b1486fa15cbc399bcf43be8ce90aac252a
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Corregir las configuraciones de seguridad en Azure Security Center
Azure Security Center analiza diariamente el sistema operativo (SO) de sus máquinas virtuales (VM) y equipos en busca de alguna configuración que pueda hacer que estos sean más vulnerables frente a los ataques. Security Center recomienda que resuelva las vulnerabilidades cuando la configuración del SO no coincida con las reglas de configuración de seguridad recomendadas y recomienda la realización de cambios en la configuración para hacer frente a estas vulnerabilidades.

Para obtener más información sobre las configuraciones específicas que se están supervisando, [consulte la lista de reglas de configuración recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Para saber cómo personalizar las evaluaciones de configuración de seguridad, consulte [Customizing OS Security Configurations](security-center-customize-os-security-config.md) (Personalización de las configuraciones de seguridad del sistema operativo en Security Center).

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
La corrección de los errores de coincidencia de las configuraciones de seguridad se presenta como una recomendación en Security Center. Esta recomendación se muestra en **Recomendaciones** y en **Proceso**.

En este ejemplo, se examinará la recomendación **Remediate security configurations** (Corregir las configuraciones de seguridad) que hay en **Compute**.
1. Seleccione **Proceso** en el menú principal de Security Center.

   ![Corrección de configuraciones de seguridad][1]

2. En **Compute**, seleccione **Remediate security configurations** (Corregir las configuraciones de seguridad). Se abre la página **Configuraciones de seguridad**.

   ![Configuraciones de seguridad][2]

  En la parte superior del panel se muestra lo siguiente:

  - El número total de reglas según su gravedad en las que se produjo un error en la configuración del sistema operativo en sus máquinas virtuales y equipos.
  - El número total de reglas según su tipo en las que se produjo un error en la configuración del sistema operativo en sus máquinas virtuales y equipos.
  - El número total de reglas en las que se produjo algún error por las configuraciones del sistema operativo Windows o las de Linux.

  En la parte inferior del panel se muestran todas las reglas con errores en todas las máquinas virtuales y equipos, así como la gravedad de la actualización que falta. La lista incluye:

  - **CCEID**: identificador único de CCE para la regla. Azure Security Center utiliza Common Configuration Enumeration (CCE) con el fin de asignar identificadores únicos para las reglas de configuración.
  - **NOMBRE**: nombre de la regla con error.
  - **TIPO DE REGLA**: clave del Registro, directiva de seguridad o directiva de auditoría.
  - **NÚMERO DE MÁQUINAS VIRTUALES Y EQUIPOS**: número total de máquinas virtuales y equipos en los que se aplica esa regla con errores.
  - **GRAVEDAD DE LA REGLA**: valor de gravedad de CCE, que puede ser Crítico, Importante o Advertencia.
  - **ESTADO**: el estado actual de la recomendación:

    - **Abierta**: la recomendación aún no se ha abordado.
    - **En curso**: la recomendación se está aplicando actualmente a esos recursos y no se requiere ninguna acción por su parte.
    - **Resuelta**: la recomendación ya terminó. (Si se ha resuelto el problema, la entrada aparecerá atenuada).

3. En la lista, seleccione una regla con errores para ver los detalles.

   ![Reglas de configuración con error][3]

  En esta hoja, se proporciona la siguiente información:

  - NOMBRE: nombre de la regla.
  - CCIED: identificador único de CCE para la regla.
  - Versión del sistema operativo: versión del sistema operativo de la máquina virtual o el equipo
  - GRAVEDAD DE LA REGLA: valor de gravedad de CCE, que puede ser Crítico, Importante o Advertencia.
  - DESCRIPCIÓN COMPLETA: descripción de la regla.
  - VULNERABILIDAD: explicación de la vulnerabilidad o el riesgo si no se aplica la regla.
  - POSIBLE IMPACTO: impacto empresarial cuando se aplica la regla.
  - CONTRAMEDIDA: pasos de corrección
  - VALOR ESPERADO: valor esperado cuando Azure Security Center analiza la configuración del sistema operativo de la máquina virtual comparándola con la regla.
  - VALOR REAL: valor devuelto después de analizar la configuración del sistema operativo de la máquina virtual comparándola con la regla.
  - FUNCIONAMIENTO DE LA REGLA: cómo utiliza Azure Security Center la regla durante el análisis de la configuración del sistema operativo de la máquina virtual comparándola con la regla.

4. Seleccione el icono **Buscar** de la cinta de opciones superior. La búsqueda se abre con una lista de áreas de trabajo que contienen máquinas virtuales y equipos con el error de coincidencia de configuraciones de seguridad. Esta hoja de selección del área de trabajo solo aparece si la regla seleccionada se aplica a varias máquinas virtuales que están conectadas a diferentes áreas de trabajo.

  ![Lista de áreas de trabajo][4]

5. Seleccione un área de trabajo. Se abre una consulta de búsqueda de Log Analytics filtrada con el área de trabajo con el error de coincidencia de las configuraciones de seguridad.

  ![Área de trabajo con una vulnerabilidad del sistema operativo][5]

6. Seleccione un equipo de la lista para más información. Se abre otro resultado de la búsqueda con la información filtrada solo para ese equipo.

  ![Información filtrada para ese equipo][6]

## <a name="next-steps"></a>pasos siguientes
En este artículo se muestra cómo implementar la recomendación de Security Center de corregir las configuraciones de seguridad. Para saber cómo personalizar las evaluaciones de configuración de seguridad, consulte [Customizing OS Security Configurations](security-center-customize-os-security-config.md) (Personalización de las configuraciones de seguridad del sistema operativo en Security Center).

Puede revisar el conjunto de reglas de configuración [aquí](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Azure Security Center utiliza Common Configuration Enumeration (CCE) con el fin de asignar identificadores únicos para las reglas de configuración. Visite el sitio de [CCE](https://nvd.nist.gov/cce/index.cfm) para obtener más información.

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Plataformas compatibles con Azure Security Center](security-center-os-coverage.md): proporciona una lista de máquinas virtuales Windows y Linux compatibles.
* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
