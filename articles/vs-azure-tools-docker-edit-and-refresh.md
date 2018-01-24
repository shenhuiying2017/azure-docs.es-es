---
title: "Depuración de aplicaciones en un contenedor de Docker local | Microsoft Docs"
description: "Obtenga información acerca de cómo modificar una aplicación que se ejecuta en un contenedor de Docker local, a actualizar el contenedor mediante la edición y actualización, y a establecer puntos de interrupción para la depuración"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: 7df8bb2dd61466fd9af6492277d54cf0ffdfeb0b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Depuración de aplicaciones en un contenedor de Docker local
## <a name="overview"></a>Información general
Visual Studio Tools para Docker ofrece una forma coherente de desarrollar y validar una aplicación localmente en un contenedor de Docker de Linux.
sin tener que reiniciar el contenedor cada vez que se realiza un cambio en el código.
En este artículo se explica cómo utilizar la característica "Editar y actualizar" para iniciar una aplicación web de ASP.NET Core en un contenedor de Docker local, realizar los cambios necesarios y actualizar el explorador para ver dichos cambios.
También se muestra cómo establecer puntos de interrupción para la depuración.

> [!NOTE]
> La compatibilidad con el contenedor de Windows estará disponible en una versión futura
>
>

## <a name="prerequisites"></a>requisitos previos
Se deben instalar las siguientes herramientas.

* [Versión más reciente de Visual Studio](https://www.visualstudio.com/downloads/)
* [SDK de Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Para ejecutar los contenedores de Docker de forma local, se necesita a un cliente Docker local.
Puede usar la versión de [Docker Toolbox](https://www.docker.com/products/docker-toolbox) que requiere que Hyper-V esté deshabilitado, o bien puede usar [Docker para Windows](https://www.docker.com/get-docker), que usa Hyper-V y requiere Windows 10.

Si usa Docker Toolbox, tendrá que [configurar el cliente Docker](vs-azure-tools-docker-setup.md).

## <a name="1-create-a-web-app"></a>1. Creación de una aplicación web
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Agregue compatibilidad con Docker
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Edición del código y actualización
Para iterar rápidamente los cambios, puede iniciar la aplicación dentro de un contenedor, seguir realizando cambios y verlos como lo haría con IIS Express.

1. Establezca la Configuración de soluciones en `Debug` y presione **&lt;CTRL + F5 >** para generar la imagen de Docker y ejecutarla localmente.

    Cuando se haya creado la imagen del contenedor y se esté ejecutando en un contenedor de Docker, Visual Studio iniciará la aplicación web en el explorador predeterminado.
    Si utiliza el explorador Microsoft Edge o aparece algún error, consulte la sección [Solución de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md) .
2. Vaya a la página Acerca de, que es donde vamos a realizar los cambios.
3. Vuelva a Visual Studio y abra `Views\Home\About.cshtml`.
4. Agregue el siguiente contenido HTML al final del archivo y guarde los cambios.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Viendo la ventana de salida, cuando se haya completado la compilación de .NET y vea estas líneas, vuelva al explorador y actualice la página Acerca de.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Se han aplicado los cambios.

## <a name="4-debug-with-breakpoints"></a>4. Depuración con puntos de interrupción
A menudo, será preciso inspeccionar más detalladamente los cambios realizados, para lo que se puede sacar provecho de las características de depuración de Visual Studio.

1. Vuelva a Visual Studio y abra `Controllers\HomeController.cs`
2. Reemplace el contenido del método About () por el siguiente:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Establezca un punto de interrupción a la izquierda de la línea `string message`...
4. Presione **&lt;F5 >** para iniciar la depuración.
5. Navegue hasta la página About para llegar al punto de interrupción.
6. Cambie a Visual Studio para ver el punto de interrupción e inspeccione el valor del mensaje.

   ![][2]

## <a name="summary"></a>Resumen
Con [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)puede conseguir una productividad similar a la de trabajar de forma local, con el realismo que produce desarrollar en un contenedor de Docker.

## <a name="troubleshooting"></a>solución de problemas
[Solución de problemas de desarrollo de Docker en Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Más información acerca de Docker con Visual Studio, Windows y Azure
* [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs) : desarrollo de código de .NET Core en un contenedor
* [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) : compilación e implementación de contenedores de Docker
* [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode) : servicios de lenguaje para editar archivos de Docker, a los que próximamente se incorporarán más escenarios de E2E
* [Documentación acerca de los contenedores de Windows](http://aka.ms/containers): información de Windows Server y Nano Server
* [Azure Container Service](https://azure.microsoft.com/services/container-service/) - [Contenido de Azure Container Service](http://aka.ms/AzureContainerService)
* Para ver más ejemplos de cómo trabajar con Docker, consulte [cómo trabajar con Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) en la [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) realizada en Connect 2015. Para ver más guías rápidas de la demostración de HealthClinic.biz, consulte las [guías rápidas de las herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Varias herramientas de Docker
[Some great docker tools (Steve Lasker's blog) (Herramientas excelentes de Docker [blog de Steve])](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Buenos artículos
[Introduction to Microservices (Introducción a Microservicios) de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Presentaciones
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction to ASP.NET Core build 2016 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810) (Introducción a ASP.NET Core @ compilación 2016)
* [Developing .NET apps in containers, Channel 9 (Desarrollo de aplicaciones .NET en contenedores, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
