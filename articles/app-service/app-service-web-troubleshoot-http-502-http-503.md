---
title: "Solución de los errores 502 Puerta de enlace no válida y 503 Servicio no disponible | Microsoft Docs"
description: "Solucionar los errores 502 Puerta de enlace no válida y 503 Servicio no disponible en su aplicación web hospedada en Servicio de aplicaciones de Azure."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "502 Puerta de enlace no válida, 503 Servicio no disponible, error 503, error 502"
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 397a6aaf7dc27adfa0fc0e722b8a2be5cc1d75f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Solucionar los errores HTTP de "502 Puerta de enlace no válida" y "503 Servicio no disponible" en las aplicaciones web de Azure
"502 Puerta de enlace no válida" y "503 Servicio no disponible" son errores comunes de su aplicación web hospedada en [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Este artículo le ayuda a solucionar estos errores.

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure y de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

## <a name="symptom"></a>Síntoma
Cuando se dirige a la aplicación web, se devuelve un error HTTP "502 Puerta de enlace no válida" o un error HTTP "503 Servicio no disponible".

## <a name="cause"></a>Causa
Con frecuencia este problema se debe a problemas en el nivel de la aplicación, como, por ejemplo:

* Solicitudes que tardan mucho tiempo
* Aplicaciones que hacen un uso elevado de memoria y CPU
* Aplicaciones que se bloquean debido a una excepción.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Pasos de solución de problemas para resolver los errores "502 Puerta de enlace no válida" y "503 Servicio no disponible"
El procedimiento de solución de problemas se puede dividir en tres tareas distintas, en orden secuencial:

1. [Observación y supervisión del comportamiento de la aplicación](#observe)
2. [Recopilación de datos](#collect)
3. [Mitigación del problema](#mitigate)

[Azure App Service Web Apps](/services/app-service/web/) ofrece diversas opciones en cada paso.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observación y supervisión del comportamiento de la aplicación
#### <a name="track-service-health"></a>Seguimiento del estado del servicio
Cada vez que hay una interrupción del servicio o una degradación del rendimiento, Microsoft Azure lo anuncia. Puede realizar un seguimiento del estado del servicio en el [Portal de Azure](https://portal.azure.com/). Para más información, consulte [Track service health](../monitoring-and-diagnostics/insights-service-health.md) (Seguimiento del estado del servicio).

#### <a name="monitor-your-web-app"></a>Supervisión de la aplicación web
Esta opción le permite averiguar si la aplicación tiene problemas. En la hoja de la aplicación web, haga clic en el icono **Solicitudes y errores** . La hoja **Métrica** mostrará todas las métricas que puede agregar.

Algunas de las métricas que podría querer supervisar para su aplicación web son:

* Espacio de trabajo de memoria promedio
* Tiempo medio de respuesta
* Tiempo de CPU
* Espacio de trabajo de memoria
* Solicitudes

![supervisar la aplicación web para solucionar los errores HTTP de 502 Puerta de enlace no válida y 503 Servicio no disponible](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para más información, consulte:

* [Supervisión de aplicaciones web en Azure App Service](web-sites-monitor.md)
* [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2. Recopilación de datos
#### <a name="use-the-azure-app-service-support-portal"></a>Uso del Portal de soporte técnico del Servicio de aplicaciones de Azure
El servicio Aplicaciones web ofrece la posibilidad de solucionar los problemas relacionados con su aplicación web con solo examinar los registros HTTP, los registros de eventos, los volcados de proceso, etc. Puede tener acceso a toda esta información con nuestro Portal de soporte técnico en **http://&lt;Nombre de la aplicación>.scm.azurewebsites.net/Support**.

El Portal de soporte técnico del Servicio de aplicaciones de Azure le proporciona tres pestañas distintas correspondientes a los tres pasos de un escenario común de solución de problemas:

1. Observación del comportamiento actual
2. Análisis mediante la recopilación de información de diagnóstico y la ejecución de los analizadores integrados
3. Mitigación

Si el problema está sucediendo justo ahora, haga clic en **Analizar** > **Diagnósticos** > **Diagnosticar ahora** para crear una sesión de diagnóstico, que recopilará registros HTTP, registros del visor de eventos, volcados de memoria, registros de errores PHP e informes de procesos PHP.

Después de la recopilación de los datos, también se ejecuta un análisis de ellos y se proporciona un informe HTML.

En caso de que quiera descargar los datos, de forma predeterminada se almacenarían en la carpeta D:\home\data\DaaS.

Para obtener más información sobre el Portal de soporte técnico del Servicio de aplicaciones de Azure, consulte [Nuevas actualizaciones de la extensión de sitios de soporte técnico para Sitios web de Azure](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Uso de la consola de depuración Kudu
El servicio Aplicaciones web incluye una consola de depuración que puede usar para depurar, explorar o cargar archivos, e incluye también puntos de conexión JSON para obtener información sobre su entorno. A esto se le denomina *Consola Kudu* o *Panel SCM* para la aplicación web.

Puede tener acceso a este panel en el vínculo **https://&lt;Nombre de la aplicación>.scm.azurewebsites.net/**.

Algunas de las cosas que proporciona Kudu son:

* Configuración del entorno de la aplicación
* transmisión de registro
* Volcado de diagnóstico
* Depuración de la consola en la que puede ejecutar cmdlets de Powershell y comandos básicos de DOS.

Otra característica útil de Kudu es que, en caso de que la aplicación inicie excepciones de primera oportunidad, puede usar Kudu y la herramienta Procdump de SysInternals para crear volcados de memoria. Estos volcados de memoria son instantáneas del proceso y a menudo pueden ayudarle solucionar problemas más complicados de su aplicación web.

Para obtener más información sobre las características disponibles en Kudu, consulte [Herramientas en línea de Sitios web de Azure que debe conocer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Mitigación del problema
#### <a name="scale-the-web-app"></a>Escalado de la aplicación web
En Azure App Service, puede ajustar la escala en la que se ejecuta la aplicación para aumentar el rendimiento y la capacidad de proceso. El escalado vertical de una aplicación web implica dos acciones relacionadas: cambiar el plan de App Service por un plan de tarifa más alto y configurar determinados valores después de haber cambiado a ese plan de tarifa más alto.

Para obtener más información sobre el escalado, consulte [Escalado de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-scale.md).

Además, puede elegir ejecutar la aplicación en más de una instancia. Esto no solo le proporciona una mayor capacidad de procesamiento, sino también algo de tolerancia a errores. Si el proceso se interrumpe en una instancia, la otra instancia seguirá atendiendo las solicitudes.

Puede establecer el escalado en Manual o Automático.

#### <a name="use-autoheal"></a>Uso de AutoHeal
AutoHeal recicla el proceso de trabajo para su aplicación en función de la configuración que elija (como cambios de configuración, solicitudes, límites de memoria o el tiempo necesario para ejecutar una solicitud). Casi siempre, el proceso de reciclaje es la forma más rápida de recuperarse de un problema. Aunque siempre puede reiniciar la aplicación web directamente en el Portal de Azure, AutoHeal lo hará automáticamente por usted. Todo lo que debe hacer es agregar algunos desencadenadores en web.config raíz de la aplicación web. Tenga en cuenta que esta configuración funcionaría igual incluso si la aplicación no fuera .Net.

Para obtener más información, consulte [Recuperación automática de Sitios web de Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Reinicio de la aplicación web
Suele ser la manera más sencilla de recuperarse de problemas que solo tienen lugar una vez. En el [Portal de Azure](https://portal.azure.com/), en la hoja de la aplicación web, tiene las opciones para detener o reiniciar la aplicación.

 ![reiniciar la aplicación para solucionar los errores HTTP de 502 Puerta de enlace no válida y 503 Servicio no disponible](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

También puede administrar la aplicación web con Azure Powershell. Para obtener más información, vea [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

