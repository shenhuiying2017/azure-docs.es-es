---
title: Azure Application Insights para ASP.NET Core | Microsoft Docs
description: Supervise la disponibilidad, el rendimiento y el uso de las aplicaciones web.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: e9fb3e68db66449d9ca3b43e6974910cb9477e62
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2018
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para ASP.NET Core

Azure Application Insights proporciona una supervisión detallada de la aplicación web hasta el nivel de código. Puede supervisar fácilmente la disponibilidad, el rendimiento y el uso de su aplicación web. También puede identificar y diagnosticar errores en la aplicación rápidamente sin tener que esperar a que un usuario informe de ellos.

En este artículo se explica cómo crear una aplicación de las [páginas de Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) de ASP.NET Core de ejemplo en Visual Studio y cómo empezar la supervisión con Azure Application Insights.

## <a name="prerequisites"></a>requisitos previos

- SDK de .NET Core 2.0.0 o posterior.
- Versión 15.3 o posterior de [Visual Studio 2017](https://www.visualstudio.com/downloads/) con la carga de trabajo de desarrollo web y ASP.NET.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Creación de un proyecto de ASP.NET Core en Visual Studio

1. Haga clic con el botón derecho en **Visual Studio 2017** e inícielo como administrador.
2. Seleccione **Archivo** > **Nuevo** > **Proyecto** (Ctrl-Mayús-N).

   ![Captura de pantalla del menú Archivo > Nuevo > Proyecto de Visual Studio](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Expanda **Visual C#** > seleccione **.NET Core** > **Aplicación web ASP.NET Core**. Escriba un **Nombre** > **Nombre de la solución** > active **Crear nuevo repositorio Git**.

   ![Captura de pantalla del Asistente Archivo > Nuevo > Proyecto de Visual Studio](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Seleccione **.Net Core** > **ASP.NET Core 2.0** **Web Application** > **Aceptar**.

    ![Captura de pantalla del menú de selección Archivo > Nuevo > Proyecto de Visual Studio](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Incorporación de los datos de telemetría de Application Insights

1. Seleccione **Proyecto** > **Agregar Telemetría de Application Insights...** (También puede hacer clic con el botón derecho en **Servicios conectados** > Agregar servicio conectado).

    ![Captura de pantalla del menú de selección Archivo > Nuevo > Proyecto de Visual Studio](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Seleccione **Comenzar gratis**.

    ![Captura de pantalla del menú de selección Archivo > Nuevo > Proyecto de Visual Studio](./media/app-insights-asp-net-core/0005-start-free.png)

3. Seleccione **Suscripción** > **Recurso** > correspondiente y si permitir o no la recopilación por sobre 1 GB por mes > **Registrar**.

    ![Captura de pantalla del menú de selección Archivo > Nuevo > Proyecto de Visual Studio](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Cambios en el proyecto

Application Insights tiene muy poca sobrecarga. Para revisar las modificaciones que se realizaron en el proyecto al agregar la telemetría de Application Insights:

Seleccione **Ver** > **Team Explorer** (Ctrl+\, Ctrl+M) > **Proyecto** > **Cambios**

- Cuatro cambios en total:

  ![Captura de pantalla de los archivos que se modificaron al agregar Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- Se crea un archivo nuevo:

   **ConnectedService.json**

  ![Captura de pantalla de los archivos que se modificaron al agregar Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Se modifican tres archivos:

  **appsettings.json**

   ![Captura de pantalla de los archivos que se modificaron al agregar Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Captura de pantalla de los archivos que se modificaron al agregar Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Captura de pantalla de los archivos que se modificaron al agregar Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Transacciones sintéticas con PowerShell

Iniciar la aplicación y luego hacer clic en los vínculos de manera manual se puede usar para generar tráfico de prueba. Sin embargo, a menudo resulta útil crear una transacción sintética sencilla en PowerShell.

1. Ejecute la aplicación mediante un clic en IIS Express ![Captura de pantalla del icono de IIS Express de Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Copie la dirección URL de la barra de direcciones del explorador. Tiene el formato http://localhost:{número de puerto aleatorio}

   ![Captura de pantalla de la barra de direcciones URL del explorador](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Ejecute el bucle de PowerShell siguiente para crear 100 transacciones sintéticas en la aplicación de prueba. Modifique el número de puerto que aparece después de **localhost:** para que coincida con la dirección URL que copió en el paso anterior.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Apertura del portal de Application Insights

Después de ejecutar la instancia de PowerShell de la sección anterior, inicie Application Insights para ver las transacciones y confirmar que se recopilan los datos. 

En el menú de Visual Studio, seleccione **Proyecto** > **Application Insights** > **Abrir portal de Application Insights**

   ![Captura de pantalla de la información general de Application Insights](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> En la captura de pantalla de ejemplo anterior, **Live Stream**, **Tiempo de carga de la vista de página** y **Solicitudes con error** no se recopilan actualmente. En la sección siguiente se explicará cómo agregar cada una de ellas. Si ya recopila **Live Stream** y **Tiempo de carga de la vista de página**, solo siga los pasos para **Solicitudes con error**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Recopilación de Solicitudes con error, Live Stream y Tiempo de carga de la vista de página

### <a name="failed-requests"></a>Solicitudes con error

Técnica, las **solicitudes con error** se están recopilando, pero todavía no ocurre ninguna. Para acelerar el proceso, se puede agregar una excepción personalizada al proyecto existente para simular una excepción real. Si la aplicación sigue ejecutándose en Visual Studio, antes de continuar debe **detener la depuración** (Mayús+F5)

1. En el **Explorador de soluciones** > expanda **Páginas** > **About.cshtml** > abra **About.cshtml.cs**.

   ![Captura de pantalla del Explorador de soluciones de Visual Studio](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Agregue una excepción bajo ``Message=`` > guarde el cambio en el archivo.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Captura de pantalla del código de excepción](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Live Stream

Para acceder a la funcionalidad Live Stream de Application Insights con la actualización de ASP.NET Core a los paquetes NuGet **Microsoft.ApplicationInsights.AspNetCore 2.2.0**.

En Visual Studio, seleccione **Proyecto** > **Administrar paquetes NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > Versión **2.2.0** > **Actualizar**.

  ![Captura de pantalla del Administrador de paquetes NuGet](./media/app-insights-asp-net-core/0017-update-nuget.png)

Aparecerán varios mensajes de confirmación. Léalos y acéptelos si está de acuerdo con los cambios.

### <a name="page-view-load-time"></a>Tiempo de carga de la vista de página

1. En Visual Studio, vaya al **Explorador de soluciones** > **Páginas** > se deben modificar dos archivos: **_Layout.cshtml** y **_ViewImports.cshtml**

2. En **_ViewImports.cshtml**, agregue:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Captura de pantalla del cambio de código en _ViewImports.cshtml](./media/app-insights-asp-net-core/00018-view-imports.png)

3. En **Layout.cshtml**, agregue la línea siguiente antes de la etiqueta ``</head>`` y antes de cualquier otro script.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Captura de pantalla del cambio de código en layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Prueba de Solicitudes con error, Tiempo de carga de la vista de página, Live Stream

Ahora que completó los pasos anteriores, puede probar y confirmar que todo funciona correctamente.

1. Ejecute la aplicación mediante un clic en IIS Express ![Captura de pantalla del icono de IIS Express de Visual Studio](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Vaya a la página **About** (Información) para desencadenar la excepción de prueba. (Si se está ejecutando en el modo de depuración, deberá hacer clic en **Continuar** en Visual Studio antes de que Application Insights recoja la excepción).

3. Vuelva a ejecutar el script de transacción de PowerShell simulado anterior (es posible que tenga que ajustar el número de puerto en el script).

4. Si todavía no está abierta la ventana de información general, en el menú de Visual Studio seleccione **Proyecto** > **Application Insights** > **Abrir portal de Application Insights**. 

   > [!TIP]
   > Si todavía no ve el tráfico nuevo, compruebe el **Intervalo de tiempo** y haga clic en **Actualizar**.

   ![Captura de pantalla de la ventana de información general](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Seleccione Live Stream

   ![Captura de pantalla de Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Si el script de PowerShell todavía está en ejecución, debería ver Live Metrics. Si se detuvo, vuelva a ejecutar el script con Live Stream abierto).

## <a name="app-insights-sdk-comparison"></a>Comparación de los SDK de App Insights

El grupo de productos de Application Insights se ha esforzado para lograr una paridad de características lo más estrecha posible entre el [SDK de .NET Framework completo](https://github.com/Microsoft/ApplicationInsights-dotnet) y el SDK de .NET Core. La versión 2.2.0 del [SDK de ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore) para Application Insights ha permitido estrechar considerablemente las distancias entre las características.

Para conocer mejor las diferencias y similitudes entre [.NET y .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server).

   | Comparación de los SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Live Metrics**      | **+** |**-** | **+** |
   | **Canal de telemetría del servidor** | **+** |**-** | **+**|
   |**Muestreo adaptable**| **+** | **-** | **+**|
   | **Llamadas de dependencia de SQL**     | **+** |**-** | **+**|
   | **Contadores de rendimiento*** | **+** | **-**| **-**|

En este contexto, _Contadores de rendimiento_ hace referencia a los [contenedores de rendimiento del lado servidor](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters), como el uso de disco, la memoria y el procesador.

## <a name="open-source-sdk"></a>SDK de código abierto
[Lectura y contribución al código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>pasos siguientes
* [Explore los flujos de usuarios](app-insights-usage-flows.md) para saber cómo los usuarios navegan por la aplicación.
* [Use la API](app-insights-api-custom-events-metrics.md) para enviar sus propios eventos y métricas para obtener una vista más detallada del rendimiento y el uso de la aplicación.
* [Las pruebas de disponibilidad](app-insights-monitor-web-app-availability.md) comprueban su aplicación constantemente desde cualquier parte del mundo.