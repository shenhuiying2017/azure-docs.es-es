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
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63bfc6922de224f56186003991f4a51d8f99ed35
ms.lasthandoff: 04/15/2017

---
# <a name="monitor-app-service"></a>Supervisión de App Service
En este tutorial se muestra cómo usar las herramientas de la plataforma integradas para supervisar y diagnosticar la aplicación hospedada en App Service. 

## <a name="in-this-tutorial"></a>En este tutorial

1. [Explorador de procesos](#explorer)
    - Obtenga información detallada sobre la ejecución de aplicaciones entre instancias del plan de App Service.
1. [Métricas de App Service](#metrics) 
   - Aprenda a supervisar la aplicación mediante los gráficos integrados.
   - Configure gráficos para satisfacer sus necesidades.
   - Cree un panel personalizado anclando sus gráficos personalizados.
1. [Configuración de alertas](#alerts)
    - Aprenda a configurar alertas para su aplicación y el plan de App Service.
1. [App Service Companion](#Companion)
    - Supervise y solucione problemas de la aplicación mediante un dispositivo móvil.
1. [Configuración del registro](#logging)
    - Aprenda a recopilar registros de aplicación y del servidor.
    - Conozca los diferentes lugares para almacenar sus registros y cómo encontrarlos.
1. [Secuencias de registro](#streaming)
    - Use secuencias de registro para ver la aplicación y los registros wc3 a medida que se emiten.
1. [Depuración remota](#remote)
    - Use Visual Studio para depurar de forma remota su proyecto que se ejecuta en App Service.
1. [Diagnóstico y solución de problemas](#diagnose)
    - Identifique los problemas de la aplicación y obtenga información acerca de cómo solucionarlos.
1. [Application Insights](#insights)
    - Generación de perfiles y supervisión avanzadas para la aplicación

## <a name="before-you-begin"></a>Antes de empezar

- Necesita una aplicación web para supervisar y seguir estos pasos. 
    - Para crear una aplicación, siga los pasos que se describen en el tutorial [Create an ASP.NET app in Azure with SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md) (Creación de una aplicación ASP.NET en Azure con SQL Database).

- Si quiere probar la **depuración remota** de la aplicación, necesita Visual Studio. 
    - Si aún no tiene Visual Studio de 2017 instalado, puede descargar y usar la versión [gratuita de Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). 
    - Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.

## <a name="explorer"></a> Paso 1: Explorador de procesos

El Explorador de procesos es una herramienta que le permite obtener información detallada sobre el funcionamiento interno de su plan de App Service.

Use el **Explorador de procesos** para:

- Enumerar todos los procesos entre distintas instancias de su plan de App Service
- Explorar en profundidad y ver los identificadores y módulos asociados con cada proceso 
- Ver el recuento de CPU, espacio de trabajo y subproceso en el nivel de proceso para ayudarle a identificar los procesos descontrolados
- Encontrar identificadores de archivos abiertos e incluso eliminar una instancia de proceso específica.

El Explorador de procesos puede estar en **Supervisión** > **Explorador de procesos**.

![Explorador de procesos](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)

## <a name="metrics"></a>Paso 2: Visualización de las métricas de App Service
**Las métricas** proporcionan información detallada sobre la aplicación web y las interacciones con sus usuarios y la plataforma.

Las métricas se pueden usar para obtener información sobre:
- el número de recursos que usa su aplicación
- el volumen de tráfico de la aplicación
- solicitudes/errores generales
- volumen de entrada y salida de datos.

Para cualquier aplicación hospedada en App Service, debe supervisar la aplicación web y el plan de App Service.

- Las métricas de **aplicación** proporcionan información sobre las solicitudes/errores HTTP y el tiempo medio de respuesta.
- Las métricas del **plan de App Service** proporcionan información sobre el uso de recursos.

El portal de Azure cuenta con una forma rápida de inspeccionar visualmente las métricas de su aplicación mediante **Azure Monitor**.

- Vaya a la hoja de **información general** de la aplicación que quiere supervisar.

![Supervisión de la aplicación](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

- Puede ver las métricas de la aplicación como un **icono de Supervisión**.
- Haga clic en el icono para editar y configurar qué métricas ver y el intervalo de tiempo durante el cual se muestran.

![Configuración de gráficos](media/app-service-web-tutorial-monitoring/app-service-monitor-configure.png)

- Puede anclar gráficos personalizados al panel para facilitar el acceso y como referencia rápida.

![Anclaje del gráfico](media/app-service-web-tutorial-monitoring/app-service-monitor-pin.png)

> [!TIP]
> Más información sobre Azure Monitor con los siguientes vínculos:
> - [Introducción a Azure Monitor](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Métricas de Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Métricas compatibles con Azure Monitor](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Paneles del portal de Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a>Paso 3: Configuración de alertas

Las **alertas** le permiten automatizar la supervisión de la aplicación.

Use alertas para recibir notificaciones cuando se detectan condiciones interesantes que afectan a la aplicación.

Para crear una alerta:
- Vaya a la hoja de **información general** de la aplicación que quiere supervisar.
- En el menú, vaya a **Supervisión** > **Alertas**
- Seleccione **[+] Agregar alerta**.
- Configure la alerta según sea necesario.

![Alertas](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

> [!TIP]
> Más información sobre las alertas de Azure con los siguientes vínculos:
> - [¿Qué son las alertas en Microsoft Azure?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Realización de acciones en las métricas](..\monitoring-and-diagnostics\monitoring-overview.md)

## <a name="companion"></a> Paso 4: App Service Companion
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

## <a name="logging"></a> Paso 5: Registro
El registro le permite recopilar registros de **Diagnósticos de aplicaciones** y de **Diagnóstico del servidor web** para su aplicación web.

Use registros de diagnóstico para comprender el comportamiento de su aplicación, solucionar problemas de la aplicación y entender las condiciones de error.

### <a name="application-diagnostics"></a>Diagnósticos de aplicaciones
Diagnósticos de aplicaciones le permite capturar seguimientos generados por la aplicación en tiempo de ejecución. 

Para habilitar el registro de aplicaciones:

- Vaya a **Supervisión** > **Registros de diagnóstico**. 
- Habilite el registro de la aplicación mediante lo botones de alternancia.

Los registros de aplicación se pueden almacenar en el sistema de archivos de la aplicación web o se pueden insertar en el almacenamiento de blobs.

> [!TIP]
> En los escenarios de producción se recomienda usar el almacenamiento de blobs.

![Supervisión de la aplicación](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

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

> [!IMPORTANT]
> La habilitación del registro tiene un impacto sobre el rendimiento de su aplicación y la utilización de recursos. En escenarios de producción, se recomiendan habilitar los registros de errores. Habilite únicamente un registro más detallado al investigar problemas.

 ### <a name="web-server-diagnostics"></a>Diagnósticos del servidor web
App Service puede recopilar tres tipos diferentes de registros de servidor:

- **Registro del servidor web**. 
    - Información sobre las transacciones HTTP mediante el [formato de archivo de registro extendido W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 
    - Resulta útil para determinar las métricas totales del sitio, como el número de solicitudes tramitadas o cuántas solicitudes proceden de una dirección IP específica.
- **Registro de error detallado** 
    - Información de error detallada de los códigos de estado HTTP que indican un error (código de estado 400 o superior). 
- **Seguimiento de solicitudes erróneas**. 
    - Información detallada sobre solicitudes erróneas, lo que incluye un seguimiento de los componentes de IIS usados para procesar la solicitud y el tiempo dedicado a cada componente. 
    - Los registros de solicitudes erróneas son útiles al intentar aislar lo que está provocando un error HTTP específico.

Para habilitar el registro del servidor:
- Vaya a **Supervisión** > **Registros de diagnóstico**. 
- Habilite los diferentes tipos de diagnósticos del servidor web mediante los botones de alternancia.

![Supervisión de la aplicación](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> La habilitación del registro tiene un impacto sobre el rendimiento de su aplicación y la utilización de recursos. En escenarios de producción, se recomienda habilitar los registros de errores. Habilite únicamente un registro más detallado al investigar problemas.

### <a name="accessing-logs"></a>Acceso a los registros
El acceso a los registros almacenados en Blob Storage se realiza mediante el Explorador de Azure Storage.

El acceso a los registros almacenados en el sistema de archivos de la aplicación web se realiza mediante FTP en las siguientes rutas de acceso:

- **Registros de aplicaciones** : /LogFiles/Application/. 
    - Esta carpeta contiene uno o varios archivos de texto con información generada por el registro de aplicaciones.
- **Seguimientos de solicitudes con error** : /LogFiles/W3SVC#########/. 
    - Esta carpeta contiene un archivo XSL y uno o varios archivos XML. 
- **Registros detallados de errores** : /LogFiles/DetailedErrors/. 
    - Esta carpeta contiene uno o varios archivos .htm con información extensa sobre los errores HTTP generados por la aplicación.
- **Registros de servidor web** : /LogFiles/http/RawLogs. 
    - Esta carpeta contiene uno o varios archivos de texto con formato de archivo de registro extendido W3C.

## <a name="streaming"></a>Paso 6: Secuencias de registro
App Service puede transmitir **registros de la aplicación** y **registros del servidor web** a medida que se generan. 

Las secuencias de registro resultan prácticas al depurar una aplicación dado que ahorran tiempo en comparación con el acceso a los registros mediante FTP u otros métodos.

> [!TIP]
> Antes de intentar transmitir registros, asegúrese de que ha habilitado la recopilación de registros como se describe en la sección [Registro](#logging).

Para transmitir registros:
- Vaya a **Supervisión**> **Secuencia de registro**.
- Seleccione **Registros de aplicación** o **Registros del servidor web** según la información que busque.
- Desde aquí, también puede pausar, reiniciar y borrar el búfer.

![Registros de transmisión](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> Solo se generan registros cuando hay tráfico en la aplicación; también puede aumentar el nivel de detalle de los registros para obtener más eventos o información.

## <a name="remote"></a>Paso 7: Depuración remota
La **depuración remota** le permite asociar un depurador a la aplicación web que se ejecuta en la nube. Puede establecer puntos de interrupción, manipular la memoria directamente, recorrer el código e incluso cambiar la ruta de acceso del código, al igual que haría con una aplicación que se ejecuta localmente.

Use la depuración remota junto con los registros de diagnóstico para buscar y corregir problemas de la aplicación.

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
> No se recomienda ejecutar el modo de depuración en producción. Si su aplicación de producción no está escalada horizontalmente a varias instancias de servidor, la depuración impide que el servidor web responda a otras solicitudes. Para solucionar problemas de producción, el mejor recurso es [configurar el registro](#logging) y [Application Insights](#insights).

## <a name="diagnose"></a> Paso 8: Diagnóstico y solución de problemas
**Diagnosticar y resolver problemas** es una herramienta integrada que examina las últimas 24 horas de actividad de la aplicación web. La UX presenta una vista resumida de los problemas identificados.

Use esta característica para ayudarle a distinguir problemas de la aplicación de problemas de la plataforma y encontrar los posibles remedios para devolver la aplicación web a un estado correcto.

![Diagnosticar y resolver problemas](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

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
