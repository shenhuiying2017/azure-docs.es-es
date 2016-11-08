---
title: Supervisión del rendimiento de aplicaciones web de Azure | Microsoft Docs
description: Supervisión del rendimiento de aplicaciones web de Azure Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge

ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: awills

---
# Supervisión del rendimiento de aplicaciones web de Azure
En el [Portal de Azure](https://portal.azure.com), puede configurar la supervisión de rendimiento de aplicaciones para sus [aplicaciones web de Azure](../app-service-web/app-service-web-overview.md). [Application Insights para Visual Studio](app-insights-overview.md) instrumenta la aplicación para enviar datos de telemetría sobre las actividades al servicio Application Insights, donde se almacenan y analizan. En esta plataforma, se pueden usar los gráficos de métricas y las herramientas de búsqueda para ayudar a diagnosticar problemas, mejorar el rendimiento y evaluar el uso.

## Tiempo de ejecución o de compilación
Puede configurar la supervisión mediante la instrumentación de la aplicación de dos maneras:

* **Tiempo de ejecución**. puede seleccionar una extensión de supervisión de rendimiento cuando la aplicación web ya esté activa. No es necesario volver a compilar o instalar la aplicación. Tendrá a disposición un conjunto estándar de paquetes que supervisan tiempos de respuesta, tasas de éxito, excepciones, dependencias, etc.
  
    **Application Insights** y **New Relic** son dos de las extensiones de supervisión de rendimiento en tiempo de ejecución que hay disponibles.
* **Tiempo de compilación**: puede instalar un paquete en la aplicación que esté en desarrollo. Esta opción es más versátil. Además de los mismos paquetes estándares, puede escribir código para personalizar los datos de telemetría o enviar los suyos propios. Puede registrar las actividades específicas o grabar eventos según la semántica del dominio de la aplicación.
  
    **Application Insights** proporciona paquetes de tiempo de compilación.

## Compilación de la aplicación con el paquete de Application Insights
Application Insights puede proporcionar una telemetría más detallada instalando un SDK en la aplicación.

En Visual Studio (2013 Update 2 o posteriores), agregue el SDK de Application Insights al proyecto.

![Haga clic con el botón derecho y elija Agregar Application Insights.](./media/app-insights-azure-web-apps/03-add.png)

Cuando se la pide que inicie sesión, use las credenciales de su cuenta de Azure.

La operación tiene dos efectos:

1. Crea un recurso de Application Insights en Azure, donde se almacenan, analizan y muestran los datos de telemetría.
2. Agrega el paquete NuGet de Application Insights al código y lo configura para enviar los datos de telemetría al recurso de Azure.

Para probar la telemetría, ejecute la aplicación en el equipo de desarrollo (F5) o vuelva a publicarla directamente.

El SDK proporciona una API para que pueda [escribir telemetría personalizada](app-insights-api-custom-events-metrics.md) para realizar un seguimiento del uso.

### ...o configurar un recurso manualmente
Si no agregó el SDK a Visual Studio, debe configurar el recurso de Application Insights en Azure, donde se almacenan, analizan y muestran los datos de telemetría.

![Haga clic en Agregar, Servicios para desarrolladores, Application Insights. Elija el tipo de aplicación ASP.NET.](./media/app-insights-azure-web-apps/01-new.png)

## Habilitación de extensiones
1. Vaya a la hoja de control de la aplicación web o la máquina virtual que quiere instrumentar.
2. Agregue toda la extensión de Application Insights o New Relic.
   
    Si va a instrumentar una aplicación web:

![Configuración, Extensiones, Agregar, Application Insights](./media/app-insights-azure-web-apps/05-extend.png)

O bien, si utiliza una máquina virtual:

![Haga clic en el icono Análisis](./media/app-insights-azure-web-apps/10-vm1.png)

## Exploración de los datos
1. Abra el recurso de Application Insights (directamente mediante Examinar o con la herramienta de supervisión de rendimiento de la aplicación web).
2. Haga clic en cualquier gráfico para obtener información más detallada:
   
    ![En la hoja de información general de Application Insights, haga clic en un gráfico.](./media/app-insights-azure-web-apps/07-dependency.png)
   
    También puede [personalizar las hojas de métricas](app-insights-metrics-explorer.md).
3. Siga haciendo clic para ver los eventos individuales y sus propiedades:
   
    ![Haga clic en un tipo de evento para abrir una búsqueda filtrada en ese tipo.](./media/app-insights-azure-web-apps/08-requests.png)
   
    Observe el vínculo "..." para abrir todas las propiedades.
   
    También puede [personalizar las búsquedas](app-insights-diagnostic-search.md).

Para realizar búsquedas más eficaces sobre los datos de telemetría, use el [lenguaje de consulta Analytics](app-insights-analytics-tour.md).

## Preguntas y respuestas
¿Cómo cambio para enviar datos a un recurso de Application Insights diferente?

* *Si agregó Application Insights a su código en Visual Studio:* haga clic con el botón derecho en el proyecto, elija **Application Insights > Configurar** y elija el recurso que quiera. Tiene la opción de crear un nuevo recurso. Vuelva a compilar e implementar.
* *En caso contrario:* en Azure, abra la hoja de control de la aplicación web y abra **Herramientas > Extensiones**. Elimine la extensión de Application Insights. Después, abra **Herramientas > Rendimiento**, haga clic aquí, elija Application Insights y el recurso que quiera. (Si quiere crear un nuevo recurso de Application Insights, haga esto primero).

## Pasos siguientes
* Habilite [Diagnósticos de Microsoft Azure](app-insights-azure-diagnostics.md) para enviar este tipo de información a Application Insights.
* [Supervise las métricas del estado del servicio](../azure-portal/insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y responde adecuadamente.
* [Reciba notificaciones de alerta](../azure-portal/insights-receive-alert-notifications.md) cada vez que se produzcan eventos de operaciones o las métricas traspasen un umbral.
* Use [aplicaciones y páginas web de Application Insights para JavaScript](app-insights-web-track-usage.md) para obtener la telemetría del cliente de los exploradores que visitan una página web.
* [Configure pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md) para recibir una alerta si el sitio deja de estar activo.

<!---HONumber=AcomDC_0907_2016-->