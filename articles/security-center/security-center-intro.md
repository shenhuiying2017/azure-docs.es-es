---
title: "Introducción a Azure Security Center | Microsoft Docs"
description: "Obtenga información sobre el Centro de seguridad de Azure, sus capacidades clave y cómo funciona."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 8951167213da6ab5341c1ca420353ec625ef5424
ms.contentlocale: es-es
ms.lasthandoff: 06/17/2017


---
# <a name="introduction-to-azure-security-center"></a>Introducción al Centro de seguridad de Azure
Obtenga información sobre el Centro de seguridad de Azure, sus capacidades clave y cómo funciona.

> [!NOTE]
> Desde primeros de junio de 2017, Security Center usará Microsoft Monitoring Agent para recopilar y almacenar datos. Consulte [Migración de la plataforma de Azure Security Center](security-center-platform-migration.md) para más información. La información de este artículo representa la funcionalidad de Security Center después de la transición a Microsoft Monitoring Agent.
>
>

## <a name="what-is-azure-security-center"></a>¿Qué es el Centro de seguridad de Azure?
 El Centro de seguridad ayuda a evitar, detectar y responder a amenazas con más visibilidad y control de la seguridad en los recursos de Azure. Proporciona administración de directivas y supervisión de la seguridad integrada en las suscripciones de Azure, ayuda a detectar las amenazas que podrían pasar desapercibidas y funciona con un amplio ecosistema de soluciones de seguridad.

## <a name="key-capabilities"></a>Principales capacidades
 El Centro de seguridad ofrece las funcionalidades sencillas y eficaces de prevención, detección y respuesta ante amenazas integradas en Azure. Principales capacidades:

| Fase | Capacidad |
| --- | --- |
| Prevención |Supervisa el estado de seguridad de los recursos de Azure |
| Prevención | Define las directivas de las suscripciones de Azure en función de los requisitos de seguridad de la compañía, los tipos de aplicaciones utilizados y la confidencialidad de los datos. |
| Prevención | Usa recomendaciones de seguridad controladas por directivas para guiar a los propietarios de los servicios a través del proceso de implementación de los controles necesarios |
| Prevención | Implementa rápidamente servicios y dispositivos de seguridad de Microsoft y asociados |
| Detección |Recopila y analiza automáticamente los datos de seguridad de los recursos de Azure, la red y las soluciones de asociados como firewalls y programas antimalware |
| Detección | Utiliza la inteligencia de amenazas globales de los productos y servicios de Microsoft, Microsoft Digital Crimes Unit (DCU), Microsoft Security Response Center (MSRC) y fuentes externas. |
| Detección | Aplica análisis avanzados, incluido el aprendizaje automático y el análisis de comportamientos |
| Respuesta |Proporciona seguridad prioritaria ante incidentes y alertas |
| Respuesta | Proporciona información sobre el origen del ataque y los recursos afectados |
| Respuesta | Sugiere formas de detener el ataque actual y de ayudar a impedir ataques futuros |

## <a name="introductory-walkthrough"></a>Tutorial de introducción

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo. Este documento no es una guía paso a paso.
>
>

 Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). [Inicie sesión en el portal](https://portal.azure.com). En el menú principal del portal, desplácese hasta la opción **Security Center** o seleccione el icono **Security Center** que ancló anteriormente al panel del portal.

![Icono Seguridad en el Portal de Azure][1]

En el Centro de seguridad, puede establecer directivas de seguridad, supervisar configuraciones de seguridad y ver alertas de seguridad.

### <a name="security-policies"></a>Directivas de seguridad
Puede definir directivas para las suscripciones de Azure según los requisitos de seguridad de la compañía. También puede personalizarlas según los tipos de aplicaciones que use o la confidencialidad de los datos en cada suscripción. Por ejemplo, es posible que los recursos usados para el desarrollo o las pruebas tengan requisitos de seguridad distintos a los empleados para las aplicaciones de producción. Del mismo modo, es posible que las aplicaciones con datos regulados como PII requieran un mayor nivel de seguridad.

> [!NOTE]
> Para modificar una directiva de seguridad, debe ser administrador de seguridad o el propietario/colaborador de la suscripción. Consulte [Permisos en Azure Security Center](security-center-permissions.md) para obtener más información sobre los roles y las acciones permitidas en Security Center.
>
>

En la hoja **Security Center**, seleccione el icono **Directiva** para obtener una lista de las suscripciones y los grupos de recursos.   

![Hoja de centro de seguridad][2]

En la hoja **Directiva de seguridad**, seleccione una suscripción para ver los detalles de la directiva.

**Recopilación de datos** habilita la recopilación de datos en una directiva de seguridad. Con la habilitación se consigue lo siguiente:

* Examen diario de todas las máquinas virtuales (VM) admitidas para las recomendaciones y la supervisión de seguridad.
* Colección de eventos de seguridad para el análisis y la detección de amenazas.

> [!NOTE]
> Recopilación de datos se configura en el nivel de suscripción.
>
>

Seleccione **Directiva de prevención** para abrir la hoja **Directiva de prevención**. **Mostrar recomendaciones para** permite elegir los controles de seguridad que desea supervisar y las recomendaciones que desea ver según las necesidades de seguridad de los recursos de la suscripción.

### <a name="security-recommendations"></a>Recomendaciones de seguridad
 El Centro de seguridad analiza el estado de seguridad de los recursos de Azure para identificar posibles vulnerabilidades de seguridad. Una lista de recomendaciones le guiará por el proceso de configuración de los controles necesarios. Algunos ejemplos son:

* Aprovisionamiento de antimalware para ayudar a identificar y eliminar el software malintencionado.
* Configuración de reglas y grupos de seguridad de red para controlar el tráfico a las VM
* Aprovisionamiento de firewalls de aplicaciones web para ayudar a defenderse contra ataques dirigidos a las aplicaciones web.
* Implementación de actualizaciones del sistema que faltan.
* Resolución de las configuraciones de sistema operativo que no coinciden con las líneas base recomendadas.

Haga clic en el icono **Recomendaciones** para obtener una lista de recomendaciones. Haga clic en cada recomendación para ver más información o para tomar medidas para solucionar el problema.

![Recomendaciones de seguridad en el Centro de seguridad de Azure][5]

### <a name="security-state-of-azure-resources"></a>Estado de seguridad de recursos de Azure
En la sección **Prevención** del panel se muestra la posición de seguridad general del entorno por tipo de recurso, lo que incluye máquinas virtuales, aplicaciones web y otros recursos.   

Seleccione un tipo de recurso en **Prevención** para ver más información, incluida una lista de las posibles vulnerabilidades de seguridad que se hayan identificado. (En el siguiente ejemplo, se selecciona la opción **Compute**).

![Icono Estado de los recursos][6]

### <a name="security-alerts"></a>Alertas de seguridad
 El Centro de seguridad recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y soluciones de asociados como firewalls y programas antimalware. Cuando se detecten amenazas, se creará una alerta de seguridad. Como ejemplos se incluye la detección de:

* VM en peligro que se comunican con direcciones IP malintencionadas conocidas
* Malware avanzado detectado mediante la generación de informes de errores de Windows.
* Ataques de fuerza bruta contra VM
* Alertas de seguridad de programas antimalware y firewalls integrados

Al hacer clic en el icono **Alertas de seguridad** se muestra una lista de alertas por prioridad.

![Alertas de seguridad][7]

Al seleccionar una alerta, se muestra más información sobre el ataque y sugerencias para resolverlo.

![Detalles de alertas de seguridad][8]

### <a name="partner-solutions"></a>Soluciones de socios
El icono de **Soluciones de asociados** permite supervisar de un solo vistazo el estado de seguridad de las soluciones de asociados integradas en la suscripción de Azure. Security Center muestra las alertas procedentes de las soluciones.

Seleccione el icono **Soluciones de asociados** . Se muestra una hoja con una lista de todas las soluciones de asociados conectadas.

![Soluciones de socios][9]

## <a name="get-started"></a>Primeros pasos
Para empezar a trabajar con el Centro de seguridad, necesita una suscripción a Microsoft Azure. El Centro de seguridad se habilita con su suscripción de Azure. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

 Se accede al Centro de seguridad desde el [Portal de Azure](https://azure.microsoft.com/features/azure-portal/). Para obtener más información, consulte la [documentación del portal](https://azure.microsoft.com/documentation/services/azure-portal/) .

[Introducción a Azure Security Center](security-center-get-started.md) le guía rápidamente por los componentes de administración de directivas y supervisión de seguridad de Azure Security Center.

## <a name="next-steps"></a>Pasos siguientes
En este documento, se ha presentado el Centro de seguridad, sus funcionalidades clave y cómo empezar a usarlo. Para obtener más información, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que lo ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el estado de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md) : aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Seguridad de datos de Azure Security Center](security-center-data-security.md): aprenda cómo se administran y protegen los datos en Security Center.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

