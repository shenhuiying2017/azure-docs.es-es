---
title: "Protección de datos personales con Azure Security Center | Microsoft Docs"
description: "protección de datos personales mediante Azure Security Center"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Protección de datos personales frente a infracciones de seguridad y ataques: Azure Security Center

En este artículo se le enseñará a usar Azure Security Center para proteger datos personales frente a infracciones de seguridad y ataques.

## <a name="scenario"></a>Escenario 

Una gran empresa de cruceros, con sede en Estados Unidos, se encuentra en proceso de expansión de sus operaciones para ofrecer itinerarios en los mares Mediterráneo y Báltico, así como en las Islas Británicas. Para contribuir a esos esfuerzos, ha adquirido varias líneas de cruceros más pequeñas establecidas en Italia, Alemania, Dinamarca y Reino Unido.

La empresa usa Microsoft Azure para almacenar datos corporativos en la nube. Esto incluye información de identificación personal como nombres, direcciones, números de teléfono e información de la tarjeta de crédito. También incluye información de recursos humanos como:

- Direcciones
- Números de teléfono
- Números de identificación fiscal
- Información médica

La línea de cruceros también conserva una gran base de datos de miembros del programa de recompensa y lealtad. Los empleados corporativos tienen acceso a la red de las oficinas remotas de la empresa, mientras que los agentes de viajes repartidos por todo el mundo tienen acceso a algunos recursos empresariales.
Los datos personales circulan a través de la red entre estas ubicaciones y el centro de datos de Microsoft.

## <a name="problem-statement"></a>Declaración del problema

A la empresa le preocupa la amenaza de ataques contra sus recursos de Azure. Desea evitar que personas no autorizadas puedan ver los datos personales de los clientes y los empleados. Le gustaría recibir orientación tanto sobre prevención como sobre respuesta o corrección. Asimismo, desea encontrar una forma eficaz de supervisar la seguridad continua de sus recursos de nube.
Necesita una línea de defensa sólida contra los atacantes sofisticados y organizados que hay en la actualidad.

## <a name="company-goal"></a>Objetivo de la empresa

Uno de los objetivos de la empresa es garantizar la privacidad de los datos personales de los clientes y los empleados protegiéndolos frente a amenazas. Uno de sus objetivos es responder de forma inmediata a cualquier indicio de infracción de seguridad para mitigar el impacto. Requiere una forma de evaluar el estado de seguridad actual, identificar configuraciones vulnerables y corregirlas.

## <a name="solutions"></a>Soluciones

Microsoft Azure Security Center (ASC) proporciona una solución integrada de supervisión de la seguridad y administración de directivas. Ofrece las funcionalidades sencillas y eficaces de prevención, detección y respuesta ante amenazas.

### <a name="prevention"></a>Prevención

ASC ayuda a evitar infracciones de seguridad permitiendo establecer directivas de seguridad, proporcionar acceso Just-In-Time e implementar recomendaciones de seguridad.

Una directiva de seguridad define el conjunto de controles recomendados para los recursos de la suscripción específica. El acceso Just-In-Time se puede usar para bloquear el tráfico entrante a las máquinas virtuales de Azure, lo que reduce la exposición a posibles ataques. ASC crea las recomendaciones de seguridad después de analizar el estado de seguridad de los recursos de Azure.

#### <a name="how-do-i-set-security-policies-in-asc"></a>¿Cómo se pueden establecer directivas de seguridad en ASC?

Puede configurar directivas de seguridad para cada suscripción. Para modificar una directiva de seguridad, debe ser propietario o colaborador de esa suscripción. En Azure Portal, realice lo siguiente:

1. Seleccione **Directiva** en el panel de ASC.

2. Seleccione la suscripción en la que desea habilitar la directiva.

3. Elija **Directiva de prevención** para configurar directivas por suscripción. **Recopilar datos de máquinas virtuales** debe establecerse en **Activar.**

4. En las opciones **Política de prevención**, seleccione **Activar** para habilitar las recomendaciones de seguridad que sean pertinentes para la suscripción.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Para obtener instrucciones más detalladas y una explicación de cada una de las recomendaciones de la directiva que se pueden habilitar, consulte [Establecimiento de directivas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>¿Cómo se puede configurar el acceso Just-In-Time (JIT)?

Cuando se habilita JIT, Security Center bloquea el tráfico entrante a las máquinas virtuales de Azure mediante la creación de una regla de NSG. Se deben seleccionar los puertos de la máquina virtual para la que se bloqueará el tráfico entrante. Para usar el acceso JIT, realice lo siguiente:

1. Seleccione el **icono Acceso a VM Just-In-Time** en la hoja ASC.

2. Seleccione la pestaña **Recomendado**.

3. En **Máquinas virtuales**, seleccione las máquinas virtuales que quiere habilitar. De este modo, se coloca una marca de verificación junto a una máquina virtual. 
4. Seleccione **Habilitar JIT** en VM.
5. Seleccione **Guardar**.

A continuación, podrá ver los puertos predeterminados cuya habilitación para JIT recomienda ASC. También puede agregar y configurar un puerto nuevo en el que habilitar la solución Just-In-Time. El icono **Acceso a VM Just-In-Time** de Security Center muestra el número de máquinas virtuales configuradas para el acceso JIT. También muestra el número de solicitudes de acceso aprobadas realizadas en la última semana.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Para obtener instrucciones sobre cómo hacerlo, así como información adicional sobre el acceso Just-In-Time, consulte [Administrar el acceso a máquina virtual mediante Just-In-Time.](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

#### <a name="how-do-i-implement-asc-security-recommendations"></a>¿Cómo se pueden implementar recomendaciones de seguridad de ASC?

Cuando el Centro de seguridad identifica vulnerabilidades de seguridad potenciales, crea recomendaciones. Las recomendaciones le guían en el proceso de configuración de los controles necesarios. 
1. Seleccione el icono **Recomendaciones** del panel de ASC.

2. Consulte las recomendaciones, que aparecen en un formato de tabla donde cada línea representa una recomendación.

3. Para filtrar recomendaciones, seleccione **Filtrar** y los valores de gravedad y estado que desee ver.

4. Para descartar una recomendación no aplicable, puede hacer clic con el botón derecho y seleccionar **Descartar.**

5. Evalúe qué recomendación debería aplicarse primero.

6. Aplique las recomendaciones en orden de prioridad.

Para obtener una lista de posibles recomendaciones y tutoriales sobre cómo aplicar cada una, consulte [Administración de recomendaciones de seguridad en Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

### <a name="detection-and-response"></a>Detección y respuesta

Detección y respuesta van unidas, ya que desea responder lo más rápido posible tras detectarse una amenaza.
La detección de amenazas de ASC recopila automáticamente información de seguridad de sus recursos de Azure, de la red y de soluciones de asociados relacionadas. ASC es capaz de actualizar rápidamente los algoritmos de detección a medida que los atacantes idean nuevos y más sofisticados ataques de seguridad. Para obtener información más detallada sobre cómo funciona la detección de amenazas de ASC, consulte [Funcionalidades de detección de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>¿Cómo se pueden administrar las alertas de seguridad y responder a ellas?

En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente el problema. Security Center también incluye recomendaciones para la forma de poner solución a un ataque. Para administrar las alertas de seguridad, realice lo siguiente:

1. Seleccione el icono **Alertas de seguridad** del panel de ASC. Aquí verá detalles de cada alerta.

2. Para filtrar alertas en función de la fecha, el estado y la gravedad, seleccione **Filtrar** y, a continuación, seleccione los valores que desee ver.

3. Para responder a una alerta, selecciónela y revise la información. A continuación, seleccione el recurso atacado.

4. En el campo **Descripción**, verá detalles, incluida la corrección recomendada.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Para obtener instrucciones más detalladas sobre cómo responder a las alertas de seguridad, consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

Para obtener ayuda adicional en la investigación de las alertas de seguridad, la empresa puede integrar alertas de ASC con su propia solución SIEM, mediante [Integración de registro de Azure](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>¿Cómo se pueden administrar incidentes de seguridad?

En ASC, un incidente de seguridad es la suma de todas las alertas de un recurso que se alinean con patrones de cadenas de eliminación. El incidente mostrará la lista de alertas relacionadas, lo que permite obtener más información sobre cada repetición. Los incidentes aparecen en el icono y en la hoja Alertas de seguridad.

Para revisar y administrar incidentes de seguridad, realice lo siguiente:

1. Seleccione el icono **Alertas de seguridad**. si se detecta un incidente de seguridad, aparecerá en el gráfico de alertas de seguridad. Tendrá un icono distinto de otras alertas.

2. Seleccione el incidente para ver más detalles sobre este incidente de seguridad. Entre los detalles adicionales se incluyen su descripción completa, su gravedad, su estado actual, el recurso atacado, los pasos de corrección del incidente y las alertas que contenía este incidente.

Puede filtrar para ver **solo incidentes**, **solo alertas** o **ambos**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>¿Cómo se puede tener acceso al informe de inteligencia de amenazas?

ASC analiza la información de diferentes orígenes para identificar las amenazas. Para ayudar a los equipos de respuesta a incidentes a investigar y solucionar las amenazas, Security Center incluye un informe de inteligencia de amenazas que contiene información sobre la amenaza detectada.

Security Center tiene tres tipos de informes de amenazas, que pueden variar por ataque.
Los informes disponibles son:

- Informe de grupo de actividad: proporciona información detallada sobre los atacantes, sus objetivos y las tácticas que emplean.

- Informe de campaña: se centra en los detalles de campañas de ataque específicas.

- Informe de resumen de amenazas: cubre todos los elementos de los dos informes anteriores.

Este tipo de información es muy útil durante los procesos de respuesta a incidentes, donde hay una investigación en curso para comprender el origen del ataque, las motivaciones del atacante y lo que se debe hacer para mitigar este problema más adelante.

1. Para tener acceso al informe de inteligencia de amenazas, realice lo siguiente:

2. Seleccione el icono **Alertas de seguridad** del panel de ASC.

3. Seleccione la alerta de seguridad para la que desea obtener más información.

4. En el campo **Informes**, haga clic en el vínculo al informe de inteligencia de amenazas.

5. Se abrirá el archivo PDF, que puede descargar.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

Para obtener información adicional sobre el informe de inteligencia de amenazas de ASC, consulte [Informe de inteligencia frente a amenazas de Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

### <a name="assessment"></a>Evaluación

Para ayudar con la prueba, valoración y evaluación de su posición de seguridad, ASC contempla la evaluación de vulnerabilidades integrada con agentes en la nube de Qualys, como parte de su componente Recomendaciones sobre máquinas virtuales.

El agente de Qualys informa a la plataforma de administración Qualys sobre los datos de vulnerabilidad. Dicha plataforma, a continuación, envía de nuevo los datos de supervisión del estado y vulnerabilidad a ASC. La recomendación para agregar una solución de evaluación de vulnerabilidades se muestra en la hoja **Recomendaciones** del panel de ASC.

Una vez instalada la solución de evaluación de vulnerabilidades en la máquina virtual de destino, Security Center examina la máquina virtual para detectar e identificar las vulnerabilidades tanto del sistema como de las aplicaciones. Los problemas detectados se muestran en la opción **Recomendaciones de máquinas virtuales**.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>¿Cómo se puede implementar una solución de evaluación de vulnerabilidades? 

Si una máquina virtual aún no tiene implementada una solución de evaluación de vulnerabilidades integrada, Security Center recomienda su instalación.

1. En el panel de ASC, en la hoja **Recomendaciones**, seleccione **Agregar una solución de evaluación de vulnerabilidad.**

2. Seleccione las máquinas virtuales donde desea instalar la solución de evaluación de vulnerabilidades.

3. Haga clic en **Instalar en las máquinas virtuales de [número de].**

4. Seleccione una solución de asociado en Azure Marketplace, o bien, en **Usar solución existente,** seleccione **Qualys.**

5. Puede activar o desactivar la configuración de actualización automática en la hoja **Soluciones de asociados**.

Para obtener instrucciones adicionales sobre cómo implementar una solución de evaluación de vulnerabilidades, consulte [Evaluación de vulnerabilidades en Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

## <a name="next-steps"></a>Pasos siguientes

- [Guía de inicio rápido de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Introducción al Centro de seguridad de Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Integración de las alertas de Azure Security Center con la integración de registro de Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Boost Azure Security Center with Integrated Vulnerability Assessment](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/) (Potenciación de Azure Security Center con la evaluación de vulnerabilidades integrada)
