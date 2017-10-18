---
title: Inteligencia sobre amenazas de Azure Security Center | Microsoft Docs
description: "Aprenda a usar la funcionalidad de inteligencia sobre amenazas de Azure Security Center para identificar posibles amenazas en las máquinas virtuales y los equipos."
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: b5bdeb1c70893c2477413df02c7b1a4a07999e93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="threat-intelligence-in-azure-security-center"></a>Inteligencia sobre amenazas de Azure Security Center
Este artículo le ayuda a utilizar la inteligencia sobre amenazas de Azure Security Center para tratar problemas relacionados con la seguridad.

## <a name="what-is-threat-intelligence"></a>¿Qué es la información sobre amenazas?
Mediante la opción de inteligencia sobre amenazas disponible en Security Center, los administradores de TI pueden identificar las amenazas de seguridad en el entorno. Por ejemplo, pueden identificar si un determinado equipo forma parte de una red de robots (botnet). Los equipos pueden convertirse en nodos en una de estas redes cuando los atacantes instalan de forma ilegal malware que se conecta en secreto al equipo para hacerse con el control. La inteligencia sobre amenazas también puede identificar posibles amenazas procedentes de canales de comunicación clandestinos, como la Internet oscura (dark web).

Para crear esta inteligencia sobre amenazas, Security Center utiliza datos procedentes de varios orígenes dentro de Microsoft y aprovecha estos datos para identificar posibles amenazas en su entorno. El panel **Información sobre amenazas** está compuesto por tres opciones principales:

- Tipos de amenazas detectadas
- Origen de la amenaza
- Mapa de información sobre amenazas


## <a name="when-should-you-use-threat-intelligence"></a>¿Cuándo debo utilizar información de amenazas?
Uno de los pasos de un [proceso de respuesta a incidentes de seguridad](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) consiste en identificar la gravedad de los sistemas en peligro. En esta fase debe realizar las siguientes tareas:

- Determinar la naturaleza del ataque
- Determinar el punto de ataque de origen
- Determinar la intención del ataque ¿Estaba el ataque dirigido a su organización para conseguir información concreta o fue aleatorio?
- Identificar los sistemas que se pusieron en peligro
- Identificar los archivos a los que se ha obtenido acceso y determinar la confidencialidad de esos archivos

Puede usar la información de inteligencia sobre amenazas de Security Center para ayudarle con estas tareas. 

## <a name="access-the-threat-intelligence"></a>Acceso a la información sobre amenazas
Para visualizar la información sobre amenazas actual en su entorno, primero debe seleccionar el área de trabajo en el que se encuentra la información. Si no dispone de varias áreas de trabajo no se verá el selector de áreas de trabajo y pasará directamente al panel **Inteligencia sobre amenazas**. Para acceder al panel:

1. Abra el panel **Security Center**.

2. En el panel izquierdo, en **Detección**, haga clic en **Información sobre amenazas**. Aparecerá el panel **Información sobre amenazas**.

    ![Panel Inteligencia sobre amenazas](./media/security-center-threat-intel/security-center-threat-intel-fig1.png)

    > [!NOTE]
    > Si en la columna de la derecha se muestra **UPGRADE PLAN** (ACTUALIZAR PLAN), esta área de trabajo está usando la suscripción gratuita. Para usar esta característica, actualice a la versión Estándar. Si en columna de la derecha se muestra **REQUIRES UPDATE** (REQUIERE ACTUALIZACIÓN), actualice [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para usar esta característica. Para más información sobre el plan de precios, consulte Precios de Azure Security Center. 
    > 
3. Si tiene más de un área de trabajo para investigar, priorice la investigación de acuerdo con la columna **Direcciones IP malintencionadas**. Muestra el número actual de direcciones IP malintencionadas en esta área de trabajo. Seleccione el área de trabajo que desea usar, y aparecerá el panel **Información sobre amenazas**.

    ![Información de inteligencia sobre amenazas](./media/security-center-threat-intel/security-center-threat-intel-fig5.png)

4. Este panel se divide en cuatro iconos:

    a.  **Tipos de amenaza**. Resume el tipo de amenaza detectada en el área de trabajo seleccionada.

    b.  **País de origen**. Agrega la cantidad de tráfico en función de su ubicación de origen.

    c.  **Ubicación de la amenaza**. Le ayuda a identificar las ubicaciones actuales de todo el mundo que se comunican con su entorno. En el mapa que se muestra, las flechas naranja (entrante) y roja (saliente) identifican las direcciones del tráfico. Al seleccionar una de estas flechas, aparece el tipo de amenaza y la dirección del tráfico.

    d.  **Detalles de la amenaza**. Muestra más detalles sobre la amenaza que seleccionó en el mapa.

Con independencia del icono de opción que seleccione, el panel que aparece se basa en la consulta de búsqueda de registros. La única diferencia es el tipo de consulta y el resultado.

### <a name="threat-types"></a>Tipos de amenaza
Seleccione el icono **Tipos de amenazas** para abrir el panel **Búsqueda de registros**. A la izquierda aparecen las opciones de filtro y a la derecha los resultados de consulta.

![Búsqueda de registros](./media/security-center-threat-intel/security-center-threat-intel-fig3.png)

El resultado de la consulta muestra las amenazas por nombre. Puede usar el panel izquierdo para seleccionar el atributo por el que quiere filtrar. Por ejemplo, para ver solo las amenazas que están conectadas actualmente a las máquinas en **SESSIONSTATE**, seleccione **Conectada** > **Aplicar**.

![Estado de sesión](./media/security-center-threat-intel/security-center-threat-intel-fig4.png)

En las máquinas virtuales de Azure, solo aparecen en el panel **Inteligencia sobre amenazas** los datos de red que pasan por el agente. Los siguientes tipos de datos también se usan en la inteligencia sobre amenazas:

- Datos CEF (Tipo = CommonSecurityLog)
- WireData (Tipo = WireData)
- Registros de IIS (Tipo = W3CIISLog)
- Firewall de Windows (Tipo = WindowsFirewall)
- Eventos de DNS (Tipo = DnsEvents)


## <a name="see-also"></a>Otras referencias
En este artículo, ha aprendido a utilizar la inteligencia sobre amenazas de Security Center para ayudarle a identificar cualquier actividad sospechosa. Para más información sobre Security Center, consulte los siguientes artículos:

* [Administración y respuesta a las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Aprenda a administrar las alertas y a responder a incidentes de seguridad en Security Center.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md). Aprenda a supervisar el estado de los recursos de Azure.
* [Comprensión de las alertas de seguridad en Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Obtenga información acerca de los distintos tipos de alertas de seguridad.
* [Guía de solución de problemas de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Obtenga información acerca de cómo solucionar problemas comunes en Security Center. 
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md). Obtenga respuestas a preguntas frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/). Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.

