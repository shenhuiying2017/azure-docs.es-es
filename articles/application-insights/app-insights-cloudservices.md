---
title: Application Insights para Azure Cloud Services | Microsoft Docs
description: "Supervisión de los roles web y de trabajo de manera eficaz con Application Insights"
services: application-insights
documentationcenter: 
keywords: WAD2AI, Azure Diagnostics
author: mrbullwinkle
manager: carmonm
editor: alancameronwills
ms.assetid: 5c7a5b34-329e-42b7-9330-9dcbb9ff1f88
ms.service: application-insights
ms.devlang: na
ms.tgt_pltfrm: ibiza
ms.topic: get-started-article
ms.workload: tbd
ms.date: 05/05/2017
ms.author: mbullwin
ms.openlocfilehash: dd35ef2239469be6ecf478f44a7e97389e5c5dd3
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="application-insights-for-azure-cloud-services"></a>Application Insights para Azure Cloud Services
La disponibilidad, el rendimiento, los errores y el uso de las [aplicaciones de servicio Microsoft Azure Cloud](https://azure.microsoft.com/services/cloud-services/) se pueden supervisar con [Application Insights][start] mediante la combinación de datos de los SDK de Application Insights y datos de [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) de Cloud Services. Con los comentarios que obtendrá sobre el rendimiento y la eficacia de la aplicación en su entorno natural, pueda tomar decisiones meditadas sobre la dirección del diseño en cada ciclo de vida de desarrollo.

![Ejemplo](./media/app-insights-cloudservices/sample.png)

## <a name="before-you-start"></a>Antes de comenzar
Necesitará:

* Una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, XBox Live u otros servicios en la nube de Microsoft. 
* Herramientas de Microsoft Azure 2.9 o superior
* Herramientas de análisis del desarrollador 7.10 o posterior

## <a name="quick-start"></a>Inicio rápido
La manera más rápida y sencilla de supervisar su servicio en la nube con Application Insights es elegir esa opción al publicar su servicio en Azure.

![Ejemplo](./media/app-insights-cloudservices/azure-cloud-application-insights.png)

Esta opción instrumenta su aplicación en tiempo de ejecución, lo que le proporciona la telemetría que necesita para supervisar solicitudes, excepciones y dependencias en el rol web, así como los contadores de rendimiento de los roles de trabajo. Los seguimientos de diagnóstico generados por la aplicación también se envían a Application Insights.

Si eso es todo lo que necesita, ha terminado. Los siguientes pasos son [ver las métricas de la aplicación](app-insights-metrics-explorer.md), [consultar los datos con Analytics](app-insights-analytics.md) y, quizás, configurar un [panel](app-insights-dashboards.md). Puede que quiera configurar [pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) y [agregar código a las páginas web](app-insights-javascript.md) para supervisar el rendimiento en el explorador.

Sin embargo, también puede obtener más opciones:

* Enviar datos de diferentes componentes y creación de configuraciones para separar recursos.
* Agregar telemetría personalizada desde su aplicación.

Si estas opciones le interesan, siga leyendo.

## <a name="sample-application-instrumented-with-application-insights"></a>Aplicación de ejemplo instrumentado con Application Insights
Eche un vistazo a esta [aplicación de ejemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) , en la que Application Insights se agrega a un servicio en la nube con dos roles de trabajo hospedados en Azure. 

A continuación se explica cómo adaptar su propio proyecto de servicio en la nube de la misma manera.

## <a name="plan-resources-and-resource-groups"></a>Planeamiento de recursos y grupos de recursos
La telemetría de la aplicación se almacena, analiza y muestra en un recurso de Azure de tipo Application Insights. 

Cada recurso pertenece a un grupo de recursos. Los grupos de recursos se usan para administrar los costos, para conceder acceso a los miembros del equipo y para implementar las actualizaciones en una transacción coordinada única. Por ejemplo, podría [escribir un script para implementar](../azure-resource-manager/resource-group-template-deploy.md) un servicio en la nube de Azure y sus recursos de supervisión de Application Insights en una operación todo en uno.

### <a name="resources-for-components"></a>Recursos para componentes
El esquema recomendado es crear un recurso independiente para cada componente de la aplicación, es decir, cada rol web y rol de trabajo. Puede analizar cada componente por separado, pero puede crear un [panel](app-insights-dashboards.md) que reúna los gráficos de clave de todos los componentes, por lo que puede compararlos y supervisarlos conjuntamente. 

Un esquema alternativo consiste en enviar la telemetría de más de un rol al mismo recurso, pero [agregar una propiedad de dimensión para cada elemento de telemetría](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer) que identifique su rol de origen. En este esquema, los gráficos de métricas, como las excepciones, normalmente muestran una agregación de las cuentas de diferentes roles, pero puede segmentar el gráfico por el identificador de rol cuando sea necesario. También se pueden filtrar las búsquedas por la misma dimensión. Esta alternativa hace un poco más fácil el poder ver todo al mismo tiempo, pero también puede causar confusión entre los roles.

La telemetría del explorador suele incluirse en el mismo recurso que su rol web de lado servidor.

Coloque los recursos de Application Insights para los diferentes componentes en un grupo de recursos. Esto facilita administrarlos juntos. 

### <a name="separating-development-test-and-production"></a>Separación de desarrollo, prueba y producción
Si va a desarrollar eventos personalizados para la característica siguiente mientras la versión anterior está activa, querrá enviar la telemetría de desarrollo a un recurso de Application Insights independiente. De lo contrario, será difícil encontrar su telemetría de prueba entre todo el tráfico desde el sitio activo.

Para evitar esta situación, cree recursos independientes para cada configuración de compilación o 'sello' (desarrollo, prueba, producción,...) del sistema. Coloque los recursos para cada configuración de compilación en un grupo de recursos independiente. 

Para enviar la telemetría a los recursos adecuados, puede configurar el SDK de Application Insights para que seleccione una clave de instrumentación diferente dependiendo de la configuración de compilación. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Creación de un recurso de Application Insights para cada rol
Si ha decidido crear un recurso independiente para cada rol, y quizás otro conjunto distinto para cada configuración de compilación, es más fácil crearlos en el portal de Application Insights. (Si crea muchos recursos, puede [automatizar el proceso](app-insights-powershell.md).

1. En [Azure Portal][portal], cree un nuevo recurso de Application Insights. Para el tipo de aplicación, elija la aplicación ASP.NET. 

    ![Haga clic en Nuevo, Application Insights.](./media/app-insights-cloudservices/01-new.png)
2. Observe que cada recurso se identifica por una clave de instrumentación. Podría necesitar esto más adelante si quisiera configurar o comprobar manualmente la configuración del SDK.

    ![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-cloudservices/02-props.png) 

## <a name="set-up-azure-diagnostics-for-each-role"></a>Configuración de Diagnósticos de Azure para cada rol
Establezca esta opción para supervisar la aplicación con Application Insights. Para roles web, proporciona supervisión del rendimiento, alertas y diagnósticos, así como análisis de uso. Para otros roles, puede buscar y supervisar diagnósticos de Azure, como reinicio, contadores de rendimiento y llamadas a System.Diagnostics.Trace. 

1. En el Explorador de soluciones de Visual Studio, en &lt;YourCloudService&gt;, Roles, abra las propiedades de cada rol.
2. En **Configuración**, establezca **Enviar datos de diagnóstico a Application Insights** y seleccione el recurso de Application Insights adecuado que creó anteriormente.

Si ha decidido usar un recurso de Application Insights distinto para cada configuración de compilación, seleccione primero la configuración.

![En las propiedades de cada rol de Azure, configure Application Insights.](./media/app-insights-cloudservices/configure-azure-diagnostics.png)

Esto tiene el efecto de insertar las claves de instrumentación de Application Insights en los archivos denominados `ServiceConfiguration.*.cscfg`. ([Código de ejemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)).

Si quiere modificar el nivel de información de diagnóstico enviado a Application Insights, puede hacerlo [editando directamente los archivos `.cscfg`](app-insights-azure-diagnostics.md).

## <a name="sdk"></a>Instalación del SDK en cada proyecto
Esta opción ofrece la posibilidad de agregar telemetría de empresa personalizada a cualquier rol, para un análisis más detallado de cómo se usa y se ejecuta su aplicación.

En Visual Studio, configure el SDK de Application Insights para cada proyecto de aplicación en la nube.

1. **Roles web**: haga clic con el botón derecho en el proyecto y elija **Configurar Application Insights** o **Agregar &gt; Telemetría de Application Insights**.

2. **Roles de trabajo**: 
 * Haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
 * Agregue [Application Insights para servidores de Windows](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/).

    ![Busque "Application Insights"](./media/app-insights-cloudservices/04-ai-nuget.png)

3. Configure el SDK para enviar datos al recurso de Application Insights.

    En una función de inicio adecuado, establezca la clave de instrumentación del ajuste de configuración en el archivo .cscfg:
 
    ```csharp
   
     TelemetryConfiguration.Active.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
    ```
   
    Realice esto para cada rol de la aplicación. Consulte los ejemplos:
   
   * [Rol web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
   * [Rol de trabajo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
   * [Para páginas web](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 
4. Establezca el archivo ApplicationInsights.config para que se copie siempre en el directorio de salida. 
   
    (En el archivo .config, verá mensajes pidiéndole colocar allí la clave de instrumentación. Sin embargo, para las aplicaciones de nube es mejor establecerla desde el archivo .cscfg. De esta forma se garantiza que el rol se identifica correctamente en el portal).

#### <a name="run-and-publish-the-app"></a>Ejecución y publicación de la aplicación
Ejecute la aplicación e inicie sesión en Azure. Abra los recursos de Application Insights que ha creado y verá que aparecen puntos de datos individuales en [Búsqueda](app-insights-diagnostic-search.md), y datos agregados en [Explorador de métricas](app-insights-metrics-explorer.md). 

Agregue más telemetría (consulte las secciones siguientes) y luego publique la aplicación para obtener diagnósticos en vivo y comentarios sobre el uso. 

#### <a name="no-data"></a>¿No hay datos?
* Abra el icono [Búsqueda][diagnostic] para ver los eventos individuales.
* Use la aplicación y abra varias páginas para generar telemetría.
* Espere unos segundos y haga clic en Actualizar.
* Consulte [Solución de problemas][qna].

## <a name="view-azure-diagnostic-events"></a>Visualización de eventos de Azure Diagnostic
Dónde encontrar la información de [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) en Application Insights:

* Los contadores de rendimiento se muestran como métricas personalizadas. 
* Los registros de eventos de Windows se muestran como seguimientos y eventos personalizados.
* Los registros de aplicación, los registros ETW y los registros de infraestructura de diagnóstico aparecen como seguimientos.

Para ver los recuentos de eventos y contadores de rendimiento, abra [Explorador de métricas](app-insights-metrics-explorer.md) y agregue un nuevo gráfico:

![Datos de Diagnósticos de Azure](./media/app-insights-cloudservices/23-wad.png)

Use [Búsqueda](app-insights-diagnostic-search.md) o una [consulta de Analytics](app-insights-analytics-tour.md) para buscar en los distintos registros de seguimiento enviados por Azure Diagnostics. Por ejemplo, suponga que tiene una excepción no controlada que provocó el bloqueo y reciclaje de un rol. Esa información aparecería en el canal Aplicación del registro de eventos de Windows. Puede utilizar la funcionalidad Buscar para ver el error del registro de eventos de Windows y obtener el seguimiento de la pila completo para la excepción. Eso le permitirá encontrar la causa raíz del problema.

![Búsqueda de Diagnósticos de Azure](./media/app-insights-cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Más telemetría
En las secciones siguientes se muestra cómo obtener telemetría adicional de diferentes aspectos de la aplicación.

## <a name="track-requests-from-worker-roles"></a>Seguimiento de solicitudes de roles de trabajo
En los roles web, el módulo de solicitudes recopila automáticamente datos acerca de las solicitudes HTTP. Consulte el [MVCWebRole de ejemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole) para obtener ejemplos de cómo puede invalidar el comportamiento de la colección predeterminada. 

Puede capturar el rendimiento de las llamadas a los roles de trabajo realizando su seguimiento de la misma manera que con las solicitudes HTTP. En Application Insights, el tipo de telemetría Solicitud mide una unidad de trabajo del lado servidor con nombre que se puede programar y puede realizarse correctamente o producir un error de forma independiente. Si bien el SDK captura automáticamente las solicitudes HTTP, puede insertar su propio código para realizar el seguimiento de las solicitudes a los roles de trabajo.

Consulte los dos roles de trabajo de ejemplo instrumentados en las solicitudes de informes: [WorkerRoleA](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA) y [WorkerRoleB](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Excepciones
Consulte [Supervisión de excepciones en Application Insights](app-insights-asp-net-exceptions.md) para obtener información acerca de cómo puede recopilar excepciones no controladas de tipos de aplicaciones web diferentes.

El rol de web ejemplo tiene los controladores MVC5 y Web API 2. Las excepciones no controladas de los dos se capturan con los siguientes controladores:

* Configure [AiHandleErrorAttribute](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) [aquí](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) para los controladores MVC5
* Configure [AiWebApiExceptionLogger](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) [aquí](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) para los controladores Web API 2

Para los roles de trabajo, hay dos maneras de realizar el seguimiento de las excepciones:

* TrackException(ex)
* Si ha agregado el paquete de NuGet del agente de escucha de seguimiento de Application Insights, puede usar **System.Diagnostics.Trace** para registrar excepciones. [Ejemplo de código](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)

## <a name="performance-counters"></a>Contadores de rendimiento
Los siguientes contadores se recopilan de forma predeterminada:

    * \Process(??APP_WIN32_PROC??)\% Tiempo de procesador
    * \Memoria\Bytes disponibles
    * \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
    * \Process(??APP_WIN32_PROC??)\Private Bytes
    * \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
    * Procesador(_Total)\% Hora del procesador

Además, también se recopilan los siguientes contadores para los roles web:

    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
    * \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

Puede especificar otros contadores de rendimiento de Windows o personalizados editando ApplicationInsights.config [como en este ejemplo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14).

  ![contadores de rendimiento](./media/app-insights-cloudservices/OLfMo2f.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Telemetría correlacionada para roles de trabajo
Es una sofisticada experiencia de diagnóstico, donde puede ver qué provocó una solicitud de latencia alta o con errores. Con los roles web, el SDK se establece automáticamente la correlación entre la telemetría relacionada. Para los roles de trabajo, puede utilizar un inicializador de telemetría personalizado para establecer un atributo de contexto Operation.Id común para todas la telemetrías con el fin de lograrlo. Esto permite saber de un vistazo si se produjo el problema de latencia o con errores debido a una dependencia o el código. 

Este es el procedimiento:

* Establezca el identificador de la correlación en una CallContext como se muestra [aquí](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36). En este caso, usamos el identificador de solicitud como identificador de correlación
* Agregue una implementación personalizada de TelemetryInitializer para establecer el Operation.Id al correlationId indicado. Hay un ejemplo aquí: [ItemCorrelationTelemetryInitializer](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13)
* Agregue el inicializador de telemetría personalizado. Puede hacerlo en el archivo ApplicationInsights.config o en el código, como se muestra [aquí](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233)

Eso es todo. La experiencia del portal ya está dispuesta para ayudarle a ver la telemetría asociada de un vistazo:

![Telemetría correlacionada](./media/app-insights-cloudservices/bHxuUhd.png)

## <a name="client-telemetry"></a>Telemetría de cliente
[Agregue el SDK de JavaScript a sus páginas web][client] para obtener telemetría basada en el explorador, como recuentos de vista de página, tiempos de carga de página y excepciones de script, y para poder editar telemetría personalizada en los scripts de página.

## <a name="availability-tests"></a>Pruebas de disponibilidad
[Configure las pruebas web][availability] para comprobar que la aplicación efectivamente está activa y responde adecuadamente.

## <a name="display-everything-together"></a>Mostrar todos los elementos juntos
Para obtener una imagen general del sistema, puede reunir los gráficos de supervisión clave en un [panel](app-insights-dashboards.md). Por ejemplo, puede anclar la solicitud y el número de errores de cada rol. 

Si el sistema usa otros servicios de Azure, como Stream Analytics, incluya también sus gráficos de supervisión. 

Si tiene una aplicación móvil de cliente, inserte algún código para enviar eventos personalizados en operaciones de usuario claves y cree un [puente de HockeyApp](app-insights-hockeyapp-bridge-app.md). Cree consultas en [Analytics](app-insights-analytics.md) para mostrar los recuentos de eventos y anclarlos al panel.

## <a name="example"></a>Ejemplo
[El ejemplo](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService) supervisa un servicio que tiene un rol web y dos roles de trabajo.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Excepción "Método no encontrado" en la ejecución en Azure Cloud Services
¿Ha realizado la compilación para .NET 4.6? 4.6 no se admite automáticamente en los roles de Azure Cloud Service. [Instale 4.6 en cada rol](../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de ejecutar la aplicación.

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>pasos siguientes
* [Configuración del envío de diagnósticos de Azure a Application Insights](app-insights-azure-diagnostics.md)
* [Automatizar la creación de recursos de Application Insights](app-insights-powershell.md)
* [Automatizar los diagnósticos de Azure](app-insights-powershell-azure-diagnostics.md)
* [Funciones de Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[azure]: app-insights-azure.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[netlogs]: app-insights-asp-net-trace-logs.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md 
