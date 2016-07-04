<properties
   pageTitle="Depuración de aplicaciones en un contenedor de Docker local | Microsoft Azure"
   description="Obtenga información acerca de cómo modificar una aplicación que se ejecuta en un contenedor de Docker local, a actualizar el contenedor mediante la edición y actualización, y a establecer puntos de interrupción para la depuración"
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# Depuración de aplicaciones en un contenedor de Docker local

## Información general
Visual Studio Tools para Docker ofrece una forma coherente de desarrollar y validar una aplicación localmente en un contenedor de Docker de Linux sin tener que reiniciar el contenedor cada vez que se realiza un cambio en el código. En este artículo se explica cómo utilizar la característica "Editar y actualizar" para iniciar una aplicación web de ASP.NET Core en un contenedor de Docker local, realizar los cambios necesarios y actualizar el explorador para ver dichos cambios. También se muestra cómo establecer puntos de interrupción para la depuración.

> [AZURE.NOTE] La compatibilidad con el contenedor de Windows estará disponible en una versión futura

## Requisitos previos
Es necesario instalar las siguientes herramientas.

- [Visual Studio 2015 Update 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Visual Studio 2015 Tools para Docker](https://aka.ms/DockerToolsForVS)

Para ejecutar los contenedores de Docker de forma local, se necesita a un cliente Docker local. Puede usar la versión de [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox) que requiere que Hyper-V esté deshabilitado, o bien puede usar la [versión beta de Docker para Windows](https://beta.docker.com), que usa Hyper-V y requiere Windows 10.

Si usa Docker Toolbox, tendrá que [configurar el cliente Docker](./vs-azure-tools-docker-setup.md)

## 1\. Creación de una aplicación web

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2\. Agregue compatibilidad con Docker

[AZURE.INCLUDE [Incorporación de compatibilidad con Docker](../includes/vs-azure-tools-docker-add-docker-support.md)]


## 3\. Edición del código y actualización

Para iterar rápidamente los cambios, puede iniciar la aplicación dentro de un contenedor, seguir realizando cambios y verlos como lo haría con IIS Express.

1. Establezca la Configuración de soluciones en `Debug` y presione **&lt;CTRL + F5 >** para generar la imagen de Docker y ejecutarla localmente.

    Cuando se haya creado la imagen del contenedor y se esté ejecutando en un contenedor de Docker, Visual Studio iniciará la aplicación web en el explorador predeterminado. Si utiliza el explorador Microsoft Edge o aparece algún error, consulte la sección [Solución de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Vaya a la página Acerca de, que es donde vamos a realizar los cambios.

1. Vuelva a Visual Studio y abra `Views\Home\About.cshtml`.

1. Agregue el siguiente contenido HTML al final del archivo y guarde los cambios.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	Viendo la ventana de salida, cuando se haya completado la compilación de .NET y vea estas líneas, vuelva al explorador y actualice la página Acerca de.

    ```
    Now listening on: http://*:80
    Application started. Press Ctrl+C to shut down
    ```

1.	Se han aplicado los cambios.

## 4\. Depuración con puntos de interrupción

A menudo, será preciso inspeccionar más detalladamente los cambios realizados, para lo que se puede sacar provecho de las características de depuración de Visual Studio.

1.	Vuelva a Visual Studio y abra `Controllers\HomeController.cs`.

1.  Reemplace el contenido del método About () por el siguiente:

	```
	string message = "Your application description page from wthin a Container";
	ViewData["Message"] = message;
    ````

1.  Establezca un punto de interrupción a la izquierda de la línea `string message`...

1.  Presione **& lt; F5 >** para iniciar la depuración.

1.  Navegue hasta la página About para llegar al punto de interrupción.

1.  Cambie a Visual Studio para ver el punto de interrupción e inspeccione el valor del mensaje.

	![][2]

##Resumen

Con [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS) puede conseguir una productividad similar a la de trabajar de forma local, con el realismo que produce desarrollar en un contenedor de Docker.

## Solución de problemas

[Solución de problemas de desarrollo de Docker en Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Más información acerca de Docker con Visual Studio, Windows y Azure

- [Docker Tools for Visual Studio](http://aka.ms/dockertoolsforvs): desarrollo de código de .NET Core en un contenedor
- [Docker Tools for Visual Studio Team Services](http://aka.ms/dockertoolsforvsts): compilación e implementación de contenedores de Docker
- [Docker Tools for Visual Studio Code](http://aka.ms/dockertoolsforvscode): servicios de lenguaje para editar archivos de Docker, a los que próximamente se incorporarán más escenarios de E2E
- [Documentación acerca de los contenedores de Windows](http://aka.ms/containers): información de Windows Server y Nano Server
- [Servicio de contenedores de Azure](https://azure.microsoft.com/services/container-service/): [Presentación del servicio Contenedor de Azure](http://aka.ms/AzureContainerService)

## Varias herramientas de Docker

[Some great docker tools (Steve Lasker's blog) (Herramientas excelentes de Docker [blog de Steve])](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## Buenos artículos

[Introduction to Microservices (Introducción a Microservicios) de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## Presentaciones

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo (Introducción a ASP.NET Core @ compilación 2016)](https://channel9.msdn.com/Events/Build/2016/B810)
- [Developing .NET apps in containers, Channel 9 (Desarrollo de aplicaciones .NET en contenedores, Channel 9)](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0622_2016-->