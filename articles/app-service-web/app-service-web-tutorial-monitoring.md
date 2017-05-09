---
title: "Supervisión de una aplicación web | Microsoft Docs"
description: "Aprenda a configurar la supervisión en su aplicación web."
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
ms.translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 8d9b4a4fa3b62659fc7e2aa1c6329fdc5e01fe39
ms.contentlocale: es-es
ms.lasthandoff: 04/21/2017

---
# <a name="monitor-app-service"></a>Supervisión de App Service
Este tutorial le guiará a través de la supervisión de la aplicación y el uso de las herramientas integradas en la plataforma para solucionar problemas cuando se produzcan.

En cada sección de este documento se abarca una característica específica. El uso conjunto de las características le permite:
- Identificar problemas en la aplicación.
- Determinar si el problema se debe al código o a la plataforma.
- Restringir el origen del problema en el código.
- Depurar y corregir el problema.

## <a name="before-you-begin"></a>Antes de empezar
- Necesita una aplicación web para supervisar y seguir estos pasos. 
    - Para crear una aplicación, siga los pasos que se describen en el tutorial [Create an ASP.NET app in Azure with SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) (Creación de una aplicación ASP.NET en Azure con SQL Database).

- Si quiere probar la **depuración remota** de la aplicación, necesita Visual Studio. 
    - Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión [gratuita de Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 
    - Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

## <a name="metrics"></a>Paso 1: Visualización de métricas
Las **métricas** son útiles para comprender lo siguiente: 
- Estado de la aplicación
- Rendimiento de la aplicación
- Consumo de recursos

Cuando se investiga un problema de aplicación, revisar las métricas es un buen comienzo. El portal de Azure cuenta con una forma rápida de inspeccionar visualmente las métricas de su aplicación mediante **Azure Monitor**.

Las métricas proporcionan una vista histórica de varias agregaciones clave para la aplicación. Para cualquier aplicación hospedada en App Service, debe supervisar tanto la aplicación web como el plan de App Service.

> [!NOTE]
> Un plan de App Service representa la colección de recursos físicos que se utiliza para hospedar las aplicaciones. Todas las aplicaciones asignadas a un plan de App Service comparten los recursos definidos por él, lo que permite ahorrar costos al hospedar varias aplicaciones.
>
> Los planes de App Service definen lo siguiente:
> * Región: Europa del Norte, este de EE. UU., Sudeste Asiático, etc.
> * Tamaño de la instancia: pequeño, mediano, grande, etc.
> * Recuento de escala: una, dos, tres instancias, etc.
> * SKU: Gratis, Compartido, Básico, Estándar, Premium, etc.

Para revisar las métricas de la aplicación web, vaya a la hoja **Overview** (Información general) de la aplicación que quiera supervisar. En ella, puede ver un gráfico de las métricas de la aplicación en forma de **icono Monitoring** (Supervisión). Haga clic en el icono para editar y configurar qué métricas ver y el intervalo de tiempo durante el cual se muestran. 

De forma predeterminada, la hoja de recursos proporciona una vista de las solicitudes de la aplicación y los errores de la última hora.
![Supervisión de la aplicación](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

Como puede ver en el ejemplo, tenemos una aplicación que genera muchos **errores del servidor HTTP**. El gran volumen de errores es el primer indicio de que necesitamos investigar esta aplicación.

> [!TIP]
> Más información sobre Azure Monitor con los siguientes vínculos:
> - [Introducción a Azure Monitor](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Métricas de Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Métricas compatibles con Azure Monitor](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Paneles de Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a>Paso 2: Configuración de alertas
Las **alertas** se pueden configurar para que se desencadenen cuando se produzcan condiciones específicas de la aplicación.

En el [Paso 1: Visualización de métricas](#metrics), hemos visto que la aplicación tenía un gran número de errores. 

Vamos a configurar una alerta de notificación automática cuando se produzcan errores. En este caso, queremos que con la alerta se envíe un correo electrónico cada vez que el número de errores HTTP 50X supere un umbral determinado.

Para crear una alerta, vaya a **Supervisión** > **Alertas** y haga clic en **[+] Agregar alerta**.

![Alertas](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

Proporcione valores para la configuración de la Alerta:
- **Recurso:** sitio para supervisar con la alerta. 
- **Nombre:** nombre de la alerta, en este caso: *High HTTP 50X*.
- **Descripción:** texto sin formato que explica lo que observa esta alerta.
- **Alerta activada:** las alertas pueden mirar métricas o eventos, en este ejemplo, examinamos las métricas.
- **Métrica:** métrica que se va a supervisar, en este caso: *Errores del servidor HTTP*.
- **Condición:** cuándo se activa la alerta, en este caso, seleccione la opción *mayor que*.
- **Umbral:** valor que se desea buscar, en este caso: *400*.
- **Período:** las alertas funcionan sobre el valor medio de una métrica. Los períodos de tiempo menores suspenden las alertas más sensibles. En este caso examinamos *5 minutos*. 
- **Propietarios y colaboradores de correo electrónico:** en este caso: *Habilitado*.

Ahora que se creó la alerta, se enviará un correo electrónico cada vez que la aplicación supere el umbral configurado. Las alertas activas también se pueden revisar en Azure Portal.

![Alertas desencadenadas](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> Más información sobre las alertas de Azure con los siguientes vínculos:
> - [¿Qué son las alertas en Microsoft Azure?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Realización de acciones en las métricas](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Creación de alertas de métrica](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a> Paso 3: App Service Companion
**App Service Companion** ofrece un método cómodo de supervisar la aplicación con una experiencia nativa en su dispositivo móvil (iOS o Android).

Use Azure App Service Companion para:
- Revisar las métricas de aplicación
- Revisar y realizar acciones sobre alertas y recomendaciones de aplicación
- Realizar solución de problemas básicos (examinar, iniciar, detener, reiniciar la aplicación)
- Obtener notificaciones push de eventos críticos.

![App Service Companion](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![App Service Companion en App Store](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![App Service Companion en Google Play](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

Puede instalar App Service Companion desde [App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260) o [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps).

## <a name="diagnose"></a> Paso 4: Diagnóstico y solución de problemas
**Diagnosticar y resolver problemas** le ayudará a diferenciar los problemas de la aplicación de los de la plataforma. También puede sugerir mitigaciones para que la aplicación web se recupere.
 
![Diagnosticar y resolver problemas](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

Siguiendo con los pasos anteriores del formulario de ejemplo, podemos ver que la aplicación ha tenido problemas de disponibilidad. En cambio, la disponibilidad de la plataforma no ha cambiado del 100 %.

Cuando la aplicación tenga un problema y la plataforma no, es evidente que se trata de un problema de aplicación.

## <a name="logging"></a> Paso 5: Registro
Ahora que nos hemos reducido los errores a un problema de aplicación, podemos observar los registros de aplicación y servidor para más información.

El registro le permite recopilar registros de **Diagnósticos de aplicaciones** y de **Diagnóstico del servidor web** para su aplicación web.

### <a name="application-diagnostics"></a>Diagnósticos de aplicaciones
Diagnósticos de aplicaciones le permite capturar seguimientos generados por la aplicación en tiempo de ejecución. 

Agregar la funcionalidad de seguimiento a la aplicación mejora en gran medida la capacidad de depuración y e identificación de problemas.

En ASP.NET, puede registrar los seguimientos de aplicación mediante la [clase System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) para generar eventos que se capturan mediante la infraestructura de registro. También puede especificar la gravedad del seguimiento para facilitar el filtrado.

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```
Para habilitar el registro de la aplicación, vaya a **Supervisión** > **Registros de diagnóstico** y habilite los registros de la aplicación con los conmutadores.

![Supervisión de la aplicación](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

Los registros de aplicación se pueden almacenar en el sistema de archivos de la aplicación web o se pueden insertar en el almacenamiento de blobs. En los escenarios de producción se recomienda usar una instancia de Blob Storage.

> [!IMPORTANT]
> La habilitación del registro tiene un impacto sobre el rendimiento de su aplicación y la utilización de recursos. En escenarios de producción, se recomiendan habilitar los registros de errores. Habilite únicamente un registro más detallado al investigar problemas.

 ### <a name="web-server-diagnostics"></a>Diagnósticos del servidor web
Los registros del servidor web se generan aunque la aplicación no se haya instrumentado. App Service puede recopilar tres tipos diferentes de registros de servidor:

- **Registro del servidor web**. 
    - Información sobre las transacciones HTTP mediante el [formato de archivo de registro extendido W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 
    - Resulta útil para determinar las métricas totales del sitio, como el número de solicitudes tramitadas o cuántas solicitudes proceden de una dirección IP específica.
- **Registro de error detallado** 
    - Información de error detallada de los códigos de estado HTTP que indican un error (código de estado 400 o superior). 
    - [Más información sobre el registro de errores detallado](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **Seguimiento de solicitudes erróneas**. 
    - Información detallada sobre solicitudes erróneas, lo que incluye un seguimiento de los componentes de IIS usados para procesar la solicitud y el tiempo dedicado a cada componente. 
    - Los registros de solicitudes erróneas son útiles al intentar aislar lo que está provocando un error HTTP específico.
    - [Más información acerca del seguimiento de solicitudes con error](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

Para habilitar el registro del servidor:
- Vaya a **Supervisión** > **Registros de diagnóstico**. 
- Habilite los diferentes tipos de diagnósticos del servidor web mediante los botones de alternancia.

![Supervisión de la aplicación](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> La habilitación del registro tiene un impacto sobre el rendimiento de su aplicación y la utilización de recursos. En escenarios de producción, se recomienda habilitar los registros de errores. Habilite únicamente un registro más detallado al investigar problemas.

### <a name="accessing-logs"></a>Acceso a los registros
El acceso a los registros almacenados en Blob Storage se realiza mediante el Explorador de Azure Storage. El acceso a los registros almacenados en el sistema de archivos de la aplicación web se realiza mediante FTP en las siguientes rutas de acceso:

- **Registros de la aplicación** - `%HOME%/LogFiles/Application/`.
    - Esta carpeta contiene uno o varios archivos de texto con información generada por el registro de aplicaciones.
- **Seguimiento de solicitudes con error** - `%HOME%/LogFiles/W3SVC#########/`. 
    - Esta carpeta contiene un archivo XSL y uno o varios archivos XML. 
- **Registros de errores detallados** - `%HOME%/LogFiles/DetailedErrors/`. 
    - Esta carpeta contiene uno o varios archivos .htm con información extensa sobre los errores HTTP generados por la aplicación.
- **Registros del servidor web** - `%HOME%/LogFiles/http/RawLogs`. 
    - Esta carpeta contiene uno o varios archivos de texto con formato de archivo de registro extendido W3C.

## <a name="streaming"></a>Paso 6: Registros de streaming
Los registros de streaming resultan prácticos cuando se depurar una aplicación, dado que ahorran tiempo en comparación con el [acceso a los registros](#Accessing-Logs) desde el FTP.

App Service puede transmitir **registros de la aplicación** y **registros del servidor web** a medida que se generan. 

> [!TIP]
> Antes de intentar transmitir registros, asegúrese de que ha habilitado la recopilación de registros como se describe en la sección [Registro](#logging).

Para transmitir registros, vaya a **Supervisión**> **Secuencia de registro**. Seleccione **Registros de aplicación** o **Registros del servidor web** según la información que busque. Desde aquí, también puede pausar, reiniciar y borrar el búfer.

![Registros de transmisión](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> Solo se generan registros cuando hay tráfico en la aplicación; también puede aumentar el nivel de detalle de los registros para obtener más eventos o información.

## <a name="remote"></a>Paso 7: Depuración remota
Una vez que se haya identificado el origen de los problemas de aplicación, use **Depuración remota** para desplazarse por el código.

Depuración remota le permite asociar un depurador a la aplicación web que se ejecuta en la nube. Puede establecer puntos de interrupción, manipular la memoria directamente, recorrer el código e incluso cambiar la ruta de acceso del código, al igual que haría con una aplicación que se ejecuta localmente.

Para asociar el depurador a la aplicación que se ejecuta en la nube:

- Con Visual Studio de 2017, abra la solución para la aplicación que desea depurar. 
- Establezca algunos puntos de interrupción, igual que haría con el desarrollo local.
- Abra **Cloud Explorer** (Ctr + /, Ctrl + x).
- Inicie sesión con sus credenciales de Azure según sea necesario.
- Busque la aplicación que quiere depurar.
- Seleccione el formulario **Asociar depurador** en el panel **Acciones**.

![Depuración remota](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

Visual Studio configura su aplicación para la depuración remota y abre una ventana de explorador que lleva a su aplicación. Examine la aplicación para desencadenar puntos de interrupción y recorrer el código.

> [!WARNING]
> No se recomienda ejecutar el modo de depuración en producción. Si su aplicación de producción no está escalada horizontalmente a varias instancias de servidor, la depuración impide que el servidor web responda a otras solicitudes. Para solucionar problemas de producción, los mejores recursos son [configurar el registro](#logging) y [Application Insights](#insights).



## <a name="explorer"></a> Paso 8: Explorador de procesos
Cuando la aplicación se escala horizontalmente a más de una instancia, el **explorador de procesos** puede ayudarle a identificar problemas específicos de instancia.

Use el **Explorador de procesos** para:

- Enumerar todos los procesos entre distintas instancias de su plan de App Service
- Explorar en profundidad y ver los identificadores y módulos asociados con cada proceso 
- Ver el recuento de CPU, espacio de trabajo y subproceso en el nivel de proceso para ayudarle a identificar los procesos descontrolados
- Encontrar identificadores de archivos abiertos e incluso eliminar una instancia de proceso específica.

El explorador de procesos puede estar en **Supervisión** > **Explorador de procesos**.

![Explorador de procesos](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


## <a name="insights"></a> Paso 9: Application Insights
**Application Insights** proporciona funcionalidades avanzadas de supervisión y generación de perfiles de aplicaciones para su aplicación. 

Use Application Insights para detectar y diagnosticar excepciones y problemas de rendimiento en su aplicación web.

Puede habilitar Application Insights para su aplicación web en **Supervisión** > **Application Insights** 

> [!NOTE]
> Application Insights podría solicitarle instalar las extensiones del sitio de Application Insights para iniciar la recopilación de datos. La instalación de la extensión del sitio provoca un reinicio de la aplicación.

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

Application Insights contiene un conjunto completo de características; para aprender más, siga los vínculos que se incluyen en la sección [Pasos siguientes](#next).

## <a name="next"></a> Pasos siguientes

 - [¿Qué es Application Insights?](..\application-insights\app-insights-overview.md)
 - [Supervisión del rendimiento de aplicaciones web de Azure con Application Insights](..\application-insights\app-insights-azure-web-apps.md)
 - [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sito web con Application Insights](..\application-insights\app-insights-monitor-web-app-availability.md)
