---
title: Generación de perfiles de aplicaciones web de Azure Linux de ASP.NET Core con Application Insights Profiler | Microsoft Docs
description: Información general del concepto y tutorial paso a paso sobre cómo habilitarlo
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
ms.openlocfilehash: 2d7405baee84b53311f01e748ca7975147c107d8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Generación de perfiles de aplicaciones web de Azure Linux de ASP.NET Core con Application Insights Profiler

Esta características actualmente está en su versión preliminar.

Averigüe cuánto tiempo se invierte en cada método de la aplicación web activa con [Application Insights](app-insights-overview.md). El generador de perfiles ahora está disponible para las aplicaciones web de ASP.NET Core hospedadas en Linux en App Services. Esta guía proporciona instrucciones paso a paso sobre cómo los seguimientos del generador de perfiles se pueden recopilar para las aplicaciones web Linux de ASP.NET Core.

Después de completar este tutorial, la aplicación recopilará seguimientos del generador de perfiles similares a la captura de pantalla siguiente. En este ejemplo, el seguimiento del generador de perfiles indica que una solicitud web en particular es lenta porque la mayor parte del tiempo se dedica a esperar. La ruta de acceso activa del código que ralentizaba la aplicación está precedida por un icono de llamas. En este ejemplo muestra el método `About` en `HomeController` que ralentiza la aplicación web porque estaba llamando a `Thread.Sleep`.

![Seguimientos del generador de perfiles](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="pre-requisites"></a>Requisitos previos
Las instrucciones siguientes se aplican a todos los entornos de desarrollo Windows, Linux y Mac:

* Instalación de [SDK 2.1.2 de .NET Core o posterior](https://www.microsoft.com/net/download/windows/build)
* Instale Git siguiendo las instrucciones que encontrará en [Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) (Introducción: instalación de Git).

## <a name="setup-project-locally"></a>Configuración del proyecto de forma local

1. Abra un símbolo del sistema en la máquina. Las instrucciones siguientes funcionan en todos los entornos de desarrollo Windows, Linux y Mac.

2. Creación de una aplicación web MVC de ASP.NET Core
    ```
    dotnet new mvc -n LinuxProfilerTest
    ```
3. Cambie el directorio del símbolo del sistema a la carpeta raíz del proyecto

4. Agregue un paquete Nuget para recopilar seguimientos del generador de perfiles.
    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```
5. Agregue una línea de código para retardar de manera aleatoria unos segundos en HomeController.cs.

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
6. Guarde y confirme los cambios en el repositorio local.

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-azure-app-service-for-hosting-your-project"></a>Creación de una instancia de Azure App Service para hospedar el proyecto
1. Cree un entorno Linux de App Services.

    ![Creación de App Services de Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Cree las credenciales de implementación. Tome nota de su contraseña, ya que la necesitará más tarde al implementar su aplicación.

    ![Creación de credenciales de implementación](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Elija la opción de implementación. Para configurar un repositorio de Git local en la aplicación web, siga las instrucciones de Azure Portal. Se creará automáticamente un repositorio de Git.

    ![Configuración de un repositorio de Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

[Aquí](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type) hay disponibles más opciones de implementación.

## <a name="deploy-your-project"></a>Implementación del proyecto

1. En el símbolo del sistema, vaya a la carpeta raíz del proyecto. Agregue el repositorio de Git remoto para que apunte a una de las instancias de App Services:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```
    * Utilice el nombre de usuario del paso de creación de credenciales de implementación.
    * Utilice el 'nombre de la aplicación' del paso de creación del servicio de aplicaciones.

2. Implementación del proyecto mediante la inserción de los cambios en Azure

    ```
    git push azure master
    ```
Verá un resultado similar al siguiente:

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
1. [Creación de recursos en Application Insights](./app-insights-create-new-resource.md)
2. Copie la iKey del recurso de Application Insights y configure las opciones siguientes en las instancias de App Service.

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Configuración de aplicación](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Si se cambia la configuración de la aplicación, se reiniciará automáticamente el sitio. Cuando se ha aplicado la nueva configuración, el generador de perfiles empezará a ejecutarse durante dos minutos inmediatamente, y después se ejecutará durante dos minutos cada hora.

3. Genere cierto tráfico a su sitio web. Puede actualizar la página ```About``` del sitio varias veces.

4. Espere de 2 a 5 minutos para que los eventos se puedan agregar a Application Insights.

5. Vaya al panel de rendimiento de Application Insights en Azure Portal. Podrá ver los seguimientos del generador de perfiles disponibles en la esquina inferior derecha.

    ![Visualización de seguimientos](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="next-steps"></a>Pasos siguientes
Si usa contenedores personalizados hospedados por App Services, siga las instrucciones que se describen en [Habilitación de Profiler de servicio para aplicaciones ASP.NET en contenedor](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) para habilitar App Insights Profiler.

Si tiene algún problema o sugerencias, notifique a nuestro repositorio de Github: [ApplicationInsights-Profiler-AspNetCore: problemas](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues)
