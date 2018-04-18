---
title: Generación de perfiles de aplicaciones web de Azure en Linux de ASP.NET Core con Application Insights Profiler | Microsoft Docs
description: Información general conceptual y tutorial paso a paso sobre cómo usar Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 5596c4efeba14e9d2bfdadd7ce92bb6b2c9fcbf0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Generación de perfiles de aplicaciones web de Azure en Linux de ASP.NET Core con Application Insights Profiler

Esta funcionalidad actualmente está en su versión preliminar.

Averigüe cuánto tiempo se invierte en cada método de la aplicación web activa con [Application Insights](app-insights-overview.md). Application Insights Profiler ahora está disponible para las aplicaciones web de ASP.NET Core hospedadas en Linux en Azure App Service. Esta guía proporciona instrucciones paso a paso sobre cómo se pueden recopilar los seguimientos de Profiler para las aplicaciones web de ASP.NET Core en Linux.

Después de completar este tutorial, la aplicación puede recopilar los seguimientos de Profiler igual que los que se muestran en la imagen. En este ejemplo, el seguimiento de Profiler indica que una solicitud web en particular es lenta porque la mayor parte del tiempo se dedica a esperar. La *ruta de acceso activa* en el código que está ralentizando la aplicación se marca con un icono de llama. El método **About** de la sección **HomeController** está ralentizando la aplicación web porque llama a la función **Thread.Sleep**.

![Seguimientos del generador de perfiles](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>requisitos previos
Las instrucciones siguientes se aplican a todos los entornos de desarrollo Windows, Linux y Mac:

* Instale [SDK 2.1.2 de .NET Core o posterior](https://www.microsoft.com/net/download/windows/build).
* Instale Git siguiendo las instrucciones que encontrará en [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Introducción: instalación de Git).

## <a name="set-up-the-project-locally"></a>Configuración del proyecto de forma local

1. Abra una ventana del símbolo del sistema en la máquina. Las instrucciones siguientes se aplican a todos los entornos de desarrollo Windows, Linux y Mac.

2. Cree una aplicación web MVC de ASP.NET Core:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. Cambie el directorio de trabajo a la carpeta raíz para el proyecto.

4. Agregue el paquete NuGet para recopilar los seguimientos de Profiler:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Agregue una línea de código a la sección **HomeController.cs** para retardar de manera aleatoria unos segundos:

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

6. Guarde y confirme los cambios en el repositorio local:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Creación de la aplicación web en Linux para hospedar el proyecto

1. Cree el entorno de aplicación web mediante App Service en Linux:

    ![Creación de la aplicación web en Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Cree las credenciales de implementación:

    > [!NOTE]
    > Anote la contraseña que se utilizará más adelante al implementar la aplicación web.

    ![Creación de las credenciales de implementación](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Elija las opciones de implementación. Para configurar un repositorio de Git local en la aplicación web, siga las instrucciones de Azure Portal. Automáticamente se crea un repositorio de Git.

    ![Configuración del repositorio de Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Para obtener más opciones de implementación, consulte [este artículo](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Implementación del proyecto

1. En la ventana del símbolo del sistema, vaya a la carpeta raíz para el proyecto. Agregue el repositorio de Git remoto para que apunte a una de las instancias de App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Use el **nombre de usuario** que empleó para crear las credenciales de implementación.
    * Use el **nombre de aplicación** que usó para crear la aplicación web mediante App Service en Linux.

2. Implemente el proyecto insertando los cambios en Azure:

    ```
    git push azure master
    ```

Debería ver una salida similar a la del siguiente ejemplo:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Adición de Application Insights para supervisar las aplicaciones web

1. [Cree un recurso de Application Insights](./app-insights-create-new-resource.md).

2. Copie el valor de **iKey** del recurso de Application Insights y configure las opciones siguientes en sus aplicaciones web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Configuración de las opciones de la aplicación](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Cuando se cambia la configuración de la aplicación, el sitio se reinicia automáticamente. Tras aplicar la nueva configuración, Profiler se ejecuta inmediatamente durante dos minutos. Profiler se ejecuta entonces dos minutos cada hora.

3. Genere cierto tráfico a su sitio web. Puede generar tráfico actualizando la página **About** (Acerca de) varias veces.

4. Espere de dos a cinco minutos para que los eventos se agreguen a Application Insights.

5. Vaya al panel **Performance** (Rendimiento) de Application Insights en Azure Portal. Puede ver los seguimientos de Profiler en la parte inferior derecha del panel.

    ![Visualización de los seguimientos de Profiler](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Problemas conocidos

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>La acción de activación en el panel de configuración de Profiler no funciona

> [!NOTE]
> Si hospeda la aplicación con App Service en Linux, no es necesario volver a habilitar Profiler en el panel **Performance** (Rendimiento) en el portal de Application Insights. Puede incluir el paquete NuGet en el proyecto y establecer el valor de **iKey** de Application Insights en la configuración de la aplicación web para habilitar Profiler.

Si sigue el flujo de trabajo de habilitación de [Application Insights Profiler para Windows](./app-insights-profiler.md) y selecciona **Enable** (Habilitar) en el panel **Configure Profiler** (Configurar Profiler), recibirá un error. La acción de habilitación intenta instalar la versión de Windows del agente de Profiler en el entorno de Linux.

Estamos trabajando para solucionar el problema.

![No intente volver a habilitar Profiler en el panel de rendimiento](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Pasos siguientes
Si usa contenedores personalizados hospedados por Azure App Service, siga las instrucciones que se describen en [ Enable Service Profiler for a containerized ASP.NET Core application](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) (Habilitación de Profiler de servicio para una aplicación ASP.NET Core en contenedor) para habilitar Application Insights Profiler.

Si tiene algún problema o sugerencias, notifíquelos en el repositorio de GitHub de Application Insights: [ApplicationInsights-Profiler-AspNetCore: problemas](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
