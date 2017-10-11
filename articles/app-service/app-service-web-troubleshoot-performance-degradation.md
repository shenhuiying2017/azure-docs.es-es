---
title: Rendimiento reducido de las aplicaciones web en App Service | Microsoft Docs
description: "Este artículo lo ayuda a solucionar los problemas de rendimiento reducido en las aplicaciones web de Azure App Service."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "rendimiento de aplicaciones web, aplicación lenta, aplicaciones lentas"
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.openlocfilehash: 1cfe7ec37ad8b24a8bd9ab2bf67e95675a57b675
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Solucionar los problemas de rendimiento reducido de aplicaciones web en Azure App Service
Este artículo lo ayuda a solucionar los problemas de rendimiento reducido en las aplicaciones web de [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Si necesita más ayuda en cualquier momento con este artículo, puede ponerse en contacto con los expertos de Azure en [los foros de MSDN Azure y de desbordamiento de pila](https://azure.microsoft.com/support/forums/). Como alternativa, también puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y haga clic en **Obtener soporte técnico**.

## <a name="symptom"></a>Síntoma
Al examinar la aplicación web, las páginas se cargan lentamente y, en ocasiones, se agota el tiempo de espera.

## <a name="cause"></a>Causa
Con frecuencia este problema se debe a problemas en el nivel de la aplicación, como, por ejemplo:

* Solicitudes de redes que tardan mucho tiempo
* Consultas de código de aplicación o base de datos que no son eficaces
* Aplicaciones que hacen un uso elevado de memoria y CPU
* Aplicaciones que se bloquean debido a una excepción

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas
El procedimiento de solución de problemas se puede dividir en tres tareas distintas, en orden secuencial:

1. [Observación y supervisión del comportamiento de la aplicación](#observe)
2. [Recopilación de datos](#collect)
3. [Mitigación del problema](#mitigate)

[Azure App Service Web Apps](/services/app-service/web/) ofrece diversas opciones en cada paso.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Observación y supervisión del comportamiento de la aplicación
#### <a name="track-service-health"></a>Seguimiento del estado del servicio
Cada vez que hay una interrupción del servicio o una degradación del rendimiento, Microsoft Azure lo anuncia. Puede realizar un seguimiento del estado del servicio en [Azure Portal](https://portal.azure.com/). Para más información, consulte [Track service health](../monitoring-and-diagnostics/insights-service-health.md) (Seguimiento del estado del servicio).

#### <a name="monitor-your-web-app"></a>Supervisión de la aplicación web
Esta opción le permite averiguar si la aplicación tiene problemas. En la hoja de la aplicación web, haga clic en el icono **Solicitudes y errores** . La hoja **Métrica** muestra todas las métricas que puede agregar.

Algunas de las métricas que podría querer supervisar para su aplicación web son:

* Espacio de trabajo de memoria promedio
* Tiempo medio de respuesta
* Tiempo de CPU
* Espacio de trabajo de memoria
* Solicitudes

![supervisar el rendimiento de aplicaciones web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para más información, consulte:

* [Supervisión de aplicaciones web en Azure App Service](web-sites-monitor.md)
* [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>estado de extremo web
Si ejecuta la aplicación web en el plan de tarifa **Estándar**, Web Apps le permite supervisar dos puntos de conexión de tres ubicaciones geográficas.

La supervisión de extremo configura pruebas web desde ubicaciones distribuidas geográficamente que prueban el tiempo de respuesta y el tiempo activo de direcciones URL web. La prueba realiza una operación HTTP Get en la dirección URL web para determinar el tiempo de respuesta y el tiempo de actividad desde cada ubicación. Cada ubicación configurada ejecuta una prueba cada cinco minutos.

El tiempo activo se supervisa a través de códigos de respuesta de HTTP y el tiempo de respuesta se mide en milisegundos. Una prueba de supervisión da error si el código de respuesta HTTP es mayor o igual que 400 o si la respuesta demora más de 30 segundos. Un extremo se considera disponible si sus pruebas de supervisión se realizan correctamente desde todas las ubicaciones especificadas.

Para configurarlo, consulte [Supervisión de Aplicaciones en Azure App Service](web-sites-monitor.md).

Consulte también [Mantenimiento de Sitios web de Azure activos y supervisión de puntos de conexión: con Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) para ver un vídeo sobre la supervisión de los puntos de conexión.

#### <a name="application-performance-monitoring-using-extensions"></a>Supervisión del rendimiento de la aplicación mediante el uso de extensiones
También puede supervisar el rendimiento de la aplicación mediante las *extensiones de sitio*.

Cada aplicación web de App Service proporciona un punto de conexión de administración extensible que le permite usar un eficaz conjunto de herramientas implementadas como extensiones del sitio. Entre las extensiones se incluyen: 

- Editores de código fuente como [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Las herramientas de administración de los recursos conectados como, por ejemplo, una base de datos de MySQL conectada a una aplicación web.

[Azure Application Insights](/services/application-insights/) y [New Relic](/marketplace/partners/newrelic/newrelic/) son dos de las extensiones de sitio de supervisión del rendimiento que se encuentran disponibles. Para usar New Relic, instale a un agente en tiempo de ejecución. Para usar Application Insights de Azure, vuelva a compilar el código con un SDK; también puede instalar una extensión que proporciona acceso a datos adicionales. El SDK permite escribir código para supervisar el uso y el rendimiento de la aplicación con más detalle.

Para usar Application Insights, consulte [Supervisión del rendimiento en las aplicaciones web](../application-insights/app-insights-web-monitor-performance.md).

Para usar New Relic, consulte [Administración del rendimiento de las aplicaciones de New Relic en Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Recopilación de datos
El entorno Web Apps proporciona funciones de diagnóstico para registrar información del servidor web y de la aplicación web. La información se divide en diagnósticos del servidor web y diagnósticos de aplicaciones.

#### <a name="enable-web-server-diagnostics"></a>Habilitar diagnósticos del servidor web
Puede habilitar o deshabilitar los siguientes tipos de registros:

* **Registro de errores detallado** : registra información detallada de errores para códigos de estado HTTP que indican un problema (código de estado 400 o superior). Puede contener información que puede ayudar a determinar por qué el servidor ha devuelto el código de error.
* **Seguimiento de solicitudes con error** : registra información detallada acerca de solicitudes con error, incluido un seguimiento de los componentes de IIS usados para procesar la solicitud y el tiempo dedicado a cada componente. Esto puede resultar útil si está intentando mejorar el rendimiento de las aplicaciones web o de aislar la causa de un error HTTP determinado.
* **Registro del servidor web** : registra todas las transacciones HTTP con el formato de archivo de registro extendido de W3C. Este informe resulta útil para determinar las métricas totales de la aplicación web, como el número de solicitudes tramitadas o cuántas solicitudes proceden de una dirección IP específica.

#### <a name="enable-application-diagnostics"></a>Habilitar diagnósticos de la aplicación
Existen varias opciones para recopilar los datos de rendimiento de Web Apps, generar el perfil de la aplicación en directo desde Visual Studio, o modificar el código de la aplicación para registrar más información y seguimientos. Puede elegir las opciones en función de cuántos accesos tiene a la aplicación y de lo que ha observado con las herramientas de supervisión.

##### <a name="use-application-insights-profiler"></a>Uso de Application Insights Profiler
Puede habilitar Application Insights Profiler para empezar a capturar seguimientos detallados de rendimiento. Puede acceder a los seguimientos capturados de hace cinco días como máximo cuando lo necesite para investigar los problemas que se produjeron en el pasado. Puede elegir esta opción siempre que tenga acceso al recurso de Application Insights de la aplicación web en Azure Portal.

Application Insights Profiler proporciona estadísticas sobre el tiempo de respuesta de cada llamada web y seguimiento que indican cuál es la línea de código que provocó las respuestas lentas. En ocasiones, la aplicación de App Service es lenta porque determinado código no está escrito pensando en el rendimiento. Por ejemplo, código secuencial que se puede ejecutar en paralelo y contenciones de bloqueo de base de datos no deseadas. La eliminación de estos cuellos de botella en el código aumenta el rendimiento de la aplicación, pero son difíciles de detectar sin configurar elaborados registros y seguimientos. Los seguimientos recopilados por Application Insights Profiler ayudan a identificar las líneas de código que ralentizan la aplicación y a superar este desafío para las aplicaciones de App Service.

 Para más información, consulte [Generación de perfiles de aplicaciones web activas de Azure con Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Uso de la generación remota de perfiles
En Azure App Service, se pueden generar perfiles de forma remota de aplicaciones web, aplicaciones de API y WebJobs. Elija esta opción si tiene acceso al recurso de la aplicación web y sabe cómo reproducir el problema o si conoce el intervalo de tiempo exacto en que ocurre el problema de rendimiento.

La generación de perfiles remota puede resultar útil si el uso de CPU del proceso es elevado y el proceso se ejecuta más lento de lo esperado o la latencia de las solicitudes HTTP es superior a la normal. En estos casos, puede generar un perfil para su proceso de forma remota y obtener las pilas de llamada de muestreo de CPU para analizar la actividad del proceso y las rutas de acceso activas del código.

Para más información, consulte [Compatibilidad con la generación remota de perfiles en Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Establecimiento manual de los seguimientos de diagnóstico
Si tiene acceso al código fuente de la aplicación web, el diagnóstico de aplicaciones le permite capturar información generada por una aplicación web. Las aplicaciones de ASP.NET pueden usar la clase `System.Diagnostics.Trace` para registrar información en el registro de diagnóstico de aplicaciones. Sin embargo, debe cambiar el código y volver a implementar la aplicación. Este método es recomendable si la aplicación se ejecuta en un entorno de prueba.

Para obtener instrucciones detalladas sobre cómo configurar su aplicación para el registro, consulte [Habilitación del registro de diagnóstico para aplicaciones web en Azure App Service](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Uso del portal de soporte técnico de Azure App Service
El servicio Web Apps ofrece la posibilidad de solucionar los problemas relacionados con su aplicación web con solo examinar los registros HTTP, los registros de eventos, los volcados de proceso, etc. Puede tener acceso a toda esta información con nuestro Portal de soporte técnico en **http://&lt;Nombre de la aplicación>.scm.azurewebsites.net/Support**.

El portal de soporte técnico de Azure App Service le proporciona tres pestañas distintas correspondientes a los tres pasos de un escenario común de solución de problemas:

1. Observación del comportamiento actual
2. Análisis mediante la recopilación de información de diagnóstico y la ejecución de los analizadores integrados
3. Mitigación

Si el problema está sucediendo justo ahora, haga clic en **Analizar** > **Diagnósticos** > **Diagnosticar ahora** para crear una sesión de diagnóstico, que recopilará registros HTTP, registros del visor de eventos, volcados de memoria, registros de errores PHP e informes de procesos PHP.

Después de la recopilación de los datos, el portal de soporte técnico también ejecuta un análisis de ellos y se proporciona un informe HTML.

En caso de que quiera descargar los datos, de forma predeterminada se almacenarían en la carpeta D:\home\data\DaaS.

Para más información sobre el portal de soporte técnico de Azure App Service, consulte [Nuevas actualizaciones de la extensión de sitios de soporte técnico para Azure Websites](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Uso de la consola de depuración Kudu
El servicio Web Apps incluye una consola de depuración que puede usar para depurar, explorar o cargar archivos, e incluye también puntos de conexión JSON para obtener información sobre su entorno. A esta consola se le denomina *Consola Kudu* o *Panel SCM* para la aplicación web.

Puede tener acceso a este panel en el vínculo **https://&lt;Nombre de la aplicación>.scm.azurewebsites.net/**.

Algunas de las cosas que proporciona Kudu son:

* Configuración del entorno de la aplicación
* transmisión de registro
* Volcado de diagnóstico
* Depuración de la consola en la que puede ejecutar cmdlets de Powershell y comandos básicos de DOS.

Otra característica útil de Kudu es que, en caso de que la aplicación inicie excepciones de primera oportunidad, puede usar Kudu y la herramienta Procdump de SysInternals para crear volcados de memoria. Estos volcados de memoria son instantáneas del proceso y a menudo pueden ayudarle solucionar problemas más complicados de su aplicación web.

Para obtener más información sobre las características disponibles en Kudu, consulte [Herramientas de Azure Websites Team Services que debe conocer](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. Mitigación del problema
#### <a name="scale-the-web-app"></a>Escalado de la aplicación web
En Azure App Service, puede ajustar la escala en la que se ejecuta la aplicación para aumentar el rendimiento y la capacidad de proceso. El escalado vertical de una aplicación web implica dos acciones relacionadas: cambiar el plan de App Service por un plan de tarifa más alto y configurar determinados valores después de haber cambiado a ese plan de tarifa más alto.

Para obtener más información sobre el escalado, consulte [Escalado de una aplicación web en Azure App Service](web-sites-scale.md).

Además, puede elegir ejecutar la aplicación en más de una instancia. El escalado no solo le proporciona una mayor capacidad de procesamiento, sino también algo de tolerancia a errores. Si el proceso se interrumpe en una instancia, las otras instancias seguirán atendiendo las solicitudes.

Puede establecer el escalado en Manual o Automático.

#### <a name="use-autoheal"></a>Uso de AutoHeal
AutoHeal recicla el proceso de trabajo para su aplicación en función de la configuración que elija (como cambios de configuración, solicitudes, límites de memoria o el tiempo necesario para ejecutar una solicitud). Casi siempre, el proceso de reciclaje es la forma más rápida de recuperarse de un problema. Aunque siempre puede reiniciar la aplicación web directamente en Azure Portal, AutoHeal lo hará automáticamente por usted. Todo lo que debe hacer es agregar algunos desencadenadores en web.config raíz de la aplicación web. Esta configuración funcionaría igual incluso si la aplicación no fuera .Net.

Para obtener más información, consulte [Recuperación automática de Sitios web de Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Reinicio de la aplicación web
Reiniciar suele ser la manera más sencilla de recuperarse de problemas que solo tienen lugar una vez. En [Azure Portal](https://portal.azure.com/), en la hoja de la aplicación web, tiene las opciones para detener o reiniciar la aplicación.

 ![reiniciar las aplicaciones web para resolver los problemas de rendimiento](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

También puede administrar la aplicación web con Azure Powershell. Para obtener más información, vea [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md).
