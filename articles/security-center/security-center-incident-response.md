---
title: Respuesta a los incidentes de seguridad con Azure Security Center | Microsoft Docs
description: "En este documento se explica cómo usar Azure Security Center en un escenario de respuesta a incidentes."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 8af12f1c-4dce-4212-8ac4-170d4313492d
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 6cd6c822eb255893feac2536d7bae034380094b2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="using-azure-security-center-for-an-incident-response"></a>Uso de Azure Security Center para dar respuesta a incidentes
Muchas organizaciones aprenden a responder a incidentes de seguridad solo después de sufrir un ataque. Para reducir los costos y los daños, es importante tener un plan de respuesta a incidentes implantado antes de que se produzca un ataque. Azure Security Center puede usarse en distintas fases de una respuesta a incidentes.

## <a name="incident-response-planning"></a>Planeamiento de respuesta a incidentes
Un plan eficaz depende de tres funcionalidades básicas: proteger, detectar y responder a las amenazas. La protección consiste en prevenir incidentes, la detección consiste en identificar las amenazas pronto y la respuesta consiste en expulsar el atacante y restaurar los sistemas para mitigar los efectos de una infracción.

Este artículo usará las fases de la respuesta a incidentes de seguridad del artículo [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) (Respuesta de seguridad de Microsoft Azure en la nube), tal como se muestra en el diagrama siguiente:

![Ciclo de vida de respuesta a incidentes](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Puede usar Security Center durante las fases de detección, evaluación y diagnóstico. A continuación se proporcionan ejemplos de cómo Security Center puede ser útil durante las tres fases iniciales de respuesta a incidentes:

* **Detectar**: revisión de la primera indicación de una investigación de eventos.
  * Por ejemplo, revisar la comprobación inicial de que se ha generado una alerta de seguridad de alta prioridad en el panel de Security Center.
* **Evaluar**: realice la evaluación inicial para más información acerca de la actividad sospechosa.
  * Por ejemplo, obtener más información acerca de la alerta de seguridad.
* **Diagnosticar**: realizar una investigación técnica e identificar las estrategias de contención, mitigación y solución.
  * Ejemplo: siga los pasos de corrección descritos por Security Center para esa alerta de seguridad.

El siguiente escenario muestra cómo aprovechar Security Center durante las fases de detección, evaluación y diagnóstico/respuesta ante un incidente de seguridad. En Security Center, un [incidente de seguridad](security-center-incident.md) es la suma de todas las alertas de un recurso que se alinean con patrones de [cadenas de eliminación](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Los incidentes aparecen en el icono y en la hoja [Alertas de seguridad](security-center-managing-and-responding-alerts.md). El incidente muestra la lista de alertas relacionadas, lo que permite obtener más información sobre cada repetición. Security Center también presenta alertas de seguridad independientes que pueden utilizarse para realizar un seguimiento de una actividad sospechosa.

## <a name="scenario"></a>Escenario
Contoso migró recientemente algunos de sus recursos locales a Azure, incluidas algunas cargas de trabajo empresariales basadas en máquinas virtuales y bases de datos SQL. Actualmente el equipo de respuesta a incidentes de seguridad informática central de Contoso (CSIRT) tiene un problema para investigar asuntos de seguridad, debido a la falta de inteligencia de seguridad integrada en sus herramientas actuales de respuesta a incidentes. Esta falta de integración presenta un problema durante las fases de detección (demasiados falsos positivos), evaluación y diagnóstico. Como parte de esta migración, decidió participar en Security Center para ayudar a solucionar el problema.

La primera fase de migración terminó después de incorporar todos los recursos y abordar todas las recomendaciones de seguridad de Security Center. El equipo CSIRT de Contoso es crucial para tratar los incidentes de seguridad informática. El equipo está formado por un grupo de personas encargadas de manejar los incidentes de seguridad. Los miembros del equipo tienen tareas claramente definidas para asegurarse de que no quede sin cubrir ninguna área de respuesta.

En este escenario, nos vamos a centrar en los roles de las siguientes personas que forman parte del equipo CSIRT de Contoso:

![Ciclo de vida de respuesta a incidentes](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Judy se encarga de las operaciones de seguridad. Entre sus responsabilidades están:

* Supervisar y responder a amenazas de seguridad durante las 24 horas.
* Remitirlas al propietario de la carga de trabajo en la nube o al analista de seguridad, según sea necesario.

Sam es un analista de seguridad y sus responsabilidades incluyen:

* Investigar los ataques.
* Solucionar las alertas.
* Trabajar con los propietarios de la carga de trabajo para determinar y aplicar mitigaciones.

Como puede ver, Judy y Sam tienen responsabilidades diferentes, y deben trabajar juntos para compartir la información de Security Center.

## <a name="recommended-solution"></a>Solución recomendada
Puesto que Judy y Sam tienen diferentes roles, usarán diferentes áreas de Security Center para obtener la información pertinente para sus actividades diarias. Judy usará las **alertas de seguridad** como parte de su supervisión diaria.

![Alertas de seguridad](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Judy usará las alertas de seguridad durante las fases de evaluación y detección. Una vez que Judy finalice la evaluación inicial, puede remitir el problema a Sam si requiere una investigación adicional. En este punto, Sam usará la información proporcionada por Security Center, a veces junto con otros orígenes de datos, para pasar a la fase de diagnóstico.

## <a name="how-to-implement-this-solution"></a>Implemento de esta solución
Para ver cómo se utilizaría Azure Security Center en un escenario de respuesta a incidentes, seguiremos los pasos de Judy en las fases de detección y evaluación y, a continuación, veremos lo que hace Sam para diagnosticar el problema.

### <a name="detect-and-assess-incident-response-stages"></a>Etapas de detección y evaluación de respuesta a incidentes
Judy ha iniciado sesión en Azure Portal y está trabajando en la consola de Security Center. Como parte de sus actividades de supervisión diarias comienza a revisar las alertas de seguridad con alta prioridad mediante los pasos siguientes:

1. Hace clic en el icono **Alertas de seguridad** y accede a la hoja **Alertas de seguridad**.
    ![Hoja Alertas de seguridad](./media/security-center-incident-response/security-center-incident-response-fig4.png)

   > [!NOTE]
   > En este escenario, Judy va a realizar una evaluación de la alerta de actividad SQL malintencionada, como se muestra en la ilustración anterior.
   >
   >
2. Haga clic en el **actividad SQL malintencionado** de alerta y revise los recursos atacados en el **actividad SQL malintencionado** hoja: ![detalles de incidentes](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    En esta hoja, Judy puede tomar notas relativas a los recursos atacados, cuántas veces ocurrió este ataque y cuándo se detectó.
3. Hace clic en el **recurso atacado** para más información acerca de este ataque.

Después de leer la descripción, Judy está convencida de que no es un falso positivo y que debe remitir este caso a Sam.

### <a name="diagnose-incident-response-stage"></a>Fases de diagnóstico de la respuesta a incidentes
Sam recibe el caso de Judy y empieza a revisar los pasos de corrección sugeridos por Security Center.

![Ciclo de vida de respuesta a incidentes](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Recursos adicionales
El equipo de respuesta a incidentes también puede aprovechar la funcionalidad [Power BI de Security Center](security-center-powerbi.md) para ver distintos tipos de informes. Estos informes pueden ayudarle durante la investigación posterior para visualizar, analizar y filtrar recomendaciones y alertas de seguridad. Las empresas que usan su solución de información de seguridad y administración de eventos (SIEM) durante el proceso de investigación, también pueden [integrar Security Center en su solución](security-center-integrating-alerts-with-log-integration.md). También se pueden integrar los registros de auditoría de Azure y los eventos de seguridad de máquina virtual (VM) mediante la [herramienta de integración de registros de Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Para investigar un ataque, puede usar esta información junto con la información que proporciona Security Center.

## <a name="conclusion"></a>Conclusión
Es muy importante que su organización forme un equipo antes de que se produzca un incidente, ya que influirá positivamente en cómo se manejan los incidentes. Tener las herramientas adecuadas para supervisar los recursos puede ayudar a este equipo a adoptar los pasos exactos para solucionar un incidente de seguridad. Las [funcionalidades de detección](security-center-detection-capabilities.md) de Security Center pueden ayudar a TI a responder a los incidentes de seguridad y corregir los problemas de seguridad rápidamente.
