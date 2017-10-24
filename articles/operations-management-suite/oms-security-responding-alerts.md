---
title: "Supervisión de las alertas de seguridad y su respuesta en la solución Seguridad y auditoría de Operations Management Suite | Microsoft Docs"
description: "Este documento le ayuda a utilizar la opción de información sobre amenazas disponible en Seguridad y auditoría de OMS para supervisar las alertas de seguridad y dar una respuesta a ellas."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 7d45a32b-1341-4bb5-a436-1f42a8a2590a
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2017
ms.author: yurid
ms.openlocfilehash: df82afab2c38431e134146143524edc080ee38f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Supervisión de las alertas de seguridad y su respuesta en la solución Seguridad y auditoría de Operations Management Suite
Este documento le ayuda a utilizar la opción de información sobre amenazas disponible en Seguridad y auditoría de OMS para supervisar las alertas de seguridad y dar una respuesta a ellas.

## <a name="what-is-oms"></a>¿Qué es OMS?
Microsoft Operations Management Suite (OMS) es la solución de administración de TI basada en la nube de Microsoft que le ayuda a administrar y proteger su infraestructura en la nube y local. Para más información sobre OMS, lea el artículo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Información sobre amenazas
En un entorno empresarial en el que los usuarios tienen un amplio acceso a la red y usan una variedad de dispositivos para conectarse a los datos corporativos, es fundamental que pueda supervisar activamente los recursos y responder rápidamente ante los incidentes de seguridad. Esto es especialmente importante desde la perspectiva del ciclo de vida de seguridad porque algunas amenazas de ciberseguridad pueden no generar alertas o actividades sospechosas que puedan identificarse mediante los controles técnicos de seguridad tradicionales. 

Mediante el uso de la opción **Información sobre amenazas** disponible en Seguridad y auditoría de OMS, los administradores de TI pueden identificar las amenazas de seguridad en el entorno, por ejemplo, identificar si un determinado equipo forma parte de un [botnet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Los equipos pueden ser nodos en un botnet cuando los atacantes instalan de forma ilegal malware que se conecta en secreto al equipo para hacerse con el control. También puede identificar posibles amenazas procedentes de canales de comunicación de tipo underground, como [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Para crear esta información sobre amenazas, la solución Security and Audit de OMS utiliza datos procedentes de varios orígenes en Microsoft. Seguridad y auditoría de OMS aprovechará estos datos para identificar posibles amenazas en su entorno.

El panel de Información sobre amenazas está compuesto por tres opciones principales:

* Servidores con tráfico malintencionado saliente
* Tipos de amenazas detectadas
* Mapa de información sobre amenazas

> [!NOTE]
> Para obtener una descripción general de todas estas opciones, lea [Introducción a la solución Security and Audit de Operations Management Suite](oms-security-getting-started.md).
> 
> 

### <a name="responding-to-security-alerts"></a>Responder a alertas de seguridad
Uno de los pasos de un proceso de [respuesta a incidentes seguridad](https://technet.microsoft.com/library/cc512623.aspx) consiste en identificar la gravedad de los sistemas en peligro. En esta fase debe realizar las siguientes tareas:

* Determinar la naturaleza del ataque
* Determinar el punto de ataque de origen
* Determinar la intención del ataque ¿Estaba el ataque dirigido específicamente a su organización para conseguir información concreta o fue aleatorio?
* Identificar los sistemas que se han visto comprometidos
* Identificar los archivos a los que se ha obtenido acceso y determinar la confidencialidad de los archivos

Puede aprovechar **Información sobre amenazas** de la solución Security and Audit de OMS para que le ayude con estas tareas. Siga los pasos siguientes para acceder a estas opciones de **Información sobre amenazas**:

1. En el panel principal de **Microsoft Operations Management Suite**, haga clic en el icono **Seguridad y auditoría**.
   
    ![Seguridad y auditoría](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)
2. En el panel de **Seguridad y auditoría**, verá las opciones **Información sobre amenazas** a la derecha, como se muestra a continuación:
   
    ![Información sobre amenazas](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Estos tres iconos le darán una visión general de las amenazas actuales. En el **Servidor con el tráfico malintencionado saliente** podrá identificar si hay un equipo que se está supervisando (dentro o fuera de la red) que envíe tráfico malintencionado a Internet. 

El icono **Tipos de amenazas detectados** muestra un resumen de las amenazas actuales "en estado salvaje". Si hace clic en este icono verá más detalles sobre estas amenazas como se muestra a continuación:

![Tipos de amenazas detectadas](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Para obtener más información sobre cada amenaza, haga clic en ella. El ejemplo siguiente muestra más información sobre botnet:

![más información sobre una amenaza](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Como se describe al principio de esta sección, esta información puede ser muy útil durante un caso de respuesta ante incidentes. También puede ser importante durante una investigación forense, donde debe buscar el origen del ataque, qué sistema se ha puesto en peligro y la escala de tiempo. En este informe se puede identificar fácilmente información clave sobre el ataque, como el origen del ataque, la dirección IP local que se ha puesto en peligro y el estado de la sesión actual de la conexión. 

El **mapa de información sobre amenazas** le ayudará a identificar las ubicaciones actuales de todo el mundo que cuentan con tráfico malintencionado. Existen flechas naranjas (entrante) y rojas (saliente) en este mapa que identifican la dirección del tráfico. Si hace clic en una de estas flechas, mostrará el tipo de amenaza y la dirección del tráfico como se muestra a continuación:

![mapa de información sobre amenazas](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [!NOTE]
> Puede ver una demostración sobre cómo utilizar esta capacidad durante un proceso de respuesta a incidentes observando la presentación [Mitigate datacenter security threats with guided investigation using Operations Management Suite](https://myignite.microsoft.com/videos/5000) (Mitigar las amenazas de seguridad del centro de datos con la investigación guiada mediante Operations Management Suite) proporcionada en Microsoft Ignite.
> 

### <a name="responding-to-distinct-malicious-ip-accessed"></a>Respuesta a diversos accesos a direcciones IP malintencionadas
En algunos escenarios, puede que observe una posible dirección IP malintencionada a la que se accedió desde un equipo supervisado:

![mapa de información sobre amenazas](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.PNG)

Esta alerta y otras de la misma categoría, se generan a través de la seguridad de OMS mediante el uso de [Inteligencia de amenazas de Microsoft](https://youtu.be/O4WtxgUrDc8). Microsoft recopila los datos de inteligencia de amenazas, así como también los adquiere a través de los principales proveedores. Estos datos se actualizan con frecuencia y se adaptan a amenazas que cambian rápidamente. Debido a su naturaleza, debe combinarse con otras fuentes de información de seguridad mientras se [investiga](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) una alerta de seguridad. 

## <a name="customize-alerts-received-via-e-mail"></a>Personalización de las alertas que se reciben por correo electrónico

Puede personalizar los usuarios de la organización que recibirán notificación cuando Seguridad de OMS desencadena alertas de seguridad. Esta opción se encuentra disponible en la sección de información general y configuración del panel de OMS:

![Email](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig7.png)

## <a name="see-also"></a>Otras referencias
En este documento ha aprendido a usar la opción **Información sobre amenazas** de la solución Seguridad y auditoría de OMS para responder a las alertas de seguridad. Para obtener más información sobre Seguridad de OMS, consulte los siguientes artículos:

* [Información general de Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Introducción a la solución Seguridad y auditoría de Operations Management Suite](oms-security-getting-started.md)
* [Supervisión de los recursos en la solución Seguridad y auditoría de Operations Management Suite](oms-security-monitoring-resources.md)

