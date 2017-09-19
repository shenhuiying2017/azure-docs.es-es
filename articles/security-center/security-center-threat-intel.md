---
title: "Información sobre amenazas de Azure Security Center | Microsoft Docs"
description: "Este documento le ayuda a utilizar la funcionalidad de información sobre amenazas de Azure Security Center para identificar posibles amenazas en las máquinas virtuales y los equipos."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c492662aa3ee444347c55d9771790573ad969150
ms.contentlocale: es-es
ms.lasthandoff: 09/14/2017

---
# <a name="threat-intelligence-in-azure-security-center"></a>Información sobre amenazas de Azure Security Center
Este documento le ayuda a utilizar Información sobre amenazas de Azure Security Center para tratar problemas relacionados con la seguridad.

## <a name="what-is-threat-intelligence"></a>¿Qué es la información sobre amenazas?
Con la opción Información sobre amenazas, disponible en Security Center, los administradores de TI pueden identificar las amenazas de seguridad en el entorno, por ejemplo, identificar si un determinado equipo forma parte de una botnet. Los equipos pueden ser nodos en un botnet cuando los atacantes instalan de forma ilegal malware que se conecta en secreto al equipo para hacerse con el control. También puede identificar posibles amenazas procedentes de canales de comunicación de tipo underground, como darknet.

Para generar esta información sobre amenazas, Security Center utiliza datos procedentes de varios orígenes en Microsoft. Security Center aprovecha estos datos para identificar posibles amenazas en su entorno. El panel de Información sobre amenazas está compuesto por tres opciones principales:

- Tipos de amenazas detectadas
- Origen de la amenaza
- Mapa de información sobre amenazas


## <a name="when-should-i-use-threat-intelligence"></a>¿Cuándo debo utilizar información sobre amenazas?
Uno de los pasos de un [proceso de respuesta a incidentes seguridad](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) consiste en identificar la gravedad de los sistemas en peligro. En esta fase debe realizar las siguientes tareas:

- Determinar la naturaleza del ataque
- Determinar el punto de ataque de origen
- Determinar la intención del ataque ¿Estaba el ataque dirigido específicamente a su organización para conseguir información concreta o fue aleatorio?
- Identificar los sistemas que se han visto comprometidos
- Identificar los archivos a los que se ha tenido acceso y determinar la confidencialidad de dichos archivos. Puede aprovechar la información sobre amenazas de Security Center para ayudarle con estas tareas. 

## <a name="how-to-access-the-threat-intelligence"></a>¿Cómo se tiene acceso a la información sobre amenazas?
Para visualizar la información sobre amenazas actual para su entorno, primero debe seleccionar el área de trabajo en el que se encuentra la información. Si no dispone de varias áreas de trabajo no se verá el selector de área de trabajo y pasará directamente al panel **Información sobre amenazas**. Siga los pasos siguientes para tener acceso al panel Información sobre amenazas:

1.  Abra el panel **Security Center**.
2.  En el panel izquierdo, bajo **Detección** haga clic en **Información sobre amenazas**. Aparecerá el panel **Información sobre amenazas**.

    ![Información sobre amenazas](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Si la última columna muestra **ACTUALIZAR PLAN** se debe a que este área de trabajo está usando la suscripción gratuita y debe actualizar a la estándar para utilizar esta característica. Si se muestra REQUIERE ACTUALIZACIÓN, debe actualizar [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esta característica. Para más información sobre el plan de precios, consulte Precios de Azure Security Center. 
    > 
3. Si tiene más de un área de trabajo para investigar, puede dar prioridad a la investigación de acuerdo con la columna **DIRECCIONES IP MALINTENCIONADAS**, que muestra el número actual de direcciones IP malintencionadas en este área de trabajo. Seleccione el área de trabajo que desea usar y, a continuación, se muestra el panel **Información sobre amenazas**.

    ![Información sobre amenazas](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Este panel se divide en cuatro iconos:
    * **Tipos de amenaza**: resume el tipo de amenazas detectadas en el área de trabajo seleccionada.
    * **País de origen**: agrega la cantidad de tráfico en función de su ubicación de origen.
    * **Ubicación de amenazas**: le ayuda a identificar las ubicaciones actuales de todo el mundo que se comunican con su entorno. Existen flechas naranjas (entrante) y rojas (saliente) en este mapa que identifican la dirección del tráfico. Si hace clic en una de estas flechas, mostrará el tipo de amenaza y la dirección del tráfico.
    * **Detalles de la amenaza**: muestra más detalles sobre la amenaza que seleccionó en el mapa.

Independientemente de qué icono se seleccione, el panel que aparece se basa en la [consulta de búsqueda de registros](https://docs.microsoft.com/azure/security-center/security-center-search) y la única diferencia es el tipo de consulta y el resultado.

### <a name="threat-types"></a>Tipos de amenaza
Tras hacer clic en el icono **Tipos de amenaza**, aparecerá el panel **Búsqueda de registros**, que tiene las opciones de filtro a la izquierda y los resultados de la consulta a la derecha.

![Búsqueda de información sobre amenazas](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

El resultado de la consulta muestra las amenazas agregadas por nombre. Puede usar el panel izquierdo para seleccionar el atributo que desea filtrar. Por ejemplo, si desea ver solo las amenazas actuales conectadas a las máquinas, seleccione **Conectado** en **SESSIONSTATE** y haga clic en el botón **Aplicar**.

![estado de la sesión](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

Para las máquinas virtuales de Azure, solo aparecerán en el panel de información sobre amenazas los datos de red que se transmiten a través del agente. Información sobre amenazas también utiliza los siguientes tipos de datos:

- Datos CEF (Tipo = CommonSecurityLog)
- WireData (Tipo = WireData)
- Registros de IIS (Tipo = W3CIISLog)
- Firewall de Windows (Tipo = WindowsFirewall)
- Eventos de DNS (Tipo = DnsEvents)


## <a name="see-also"></a>Otras referencias
En este documento ha aprendido a utilizar Información sobre amenazas de Security Center para ayudarle a identificar cualquier actividad sospechosa. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Aprenda a administrar las alertas y responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.


