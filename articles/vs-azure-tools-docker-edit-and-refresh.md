<properties
   pageTitle="Depuración de aplicaciones en un contenedor de Docker local | Microsoft Azure"
   description="Obtenga información acerca de cómo modificar una aplicación que se ejecuta en un contenedor de Docker local, a actualizar el contenedor mediante la edición y actualización, y a establecer puntos de interrupción para la depuración"
   services="visual-studio-online"
   documentationCenter="na"
   authors="AllenClark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="05/13/2016"
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

Para ejecutar los contenedores de Docker de forma local, se necesita a un cliente Docker local. Puede usar la versión de [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox) que requiere que Hyper-V que esté deshabilitado, o bien puede usar la [versión beta de Docker para Windows](https://beta.docker.com), que usa Hyper-V y requiere Windows 10.

Si usa Docker Toolbox, tendrá que [configurar el cliente Docker](./vs-azure-tools-docker-setup.md)

## Edición de una aplicación que se ejecuta en un contenedor de Docker local
Visual Studio 2015 Tools para Docker permite a los desarrolladores de aplicaciones web de ASP .NET Core RC2 probar y ejecutar aplicaciones en un contenedor de Docker, realizar cambios en la aplicación en Visual Studio y actualizar el explorador para que refleje los cambios realizados a la aplicación que se ejecuta dentro del contenedor. Con .NET Core y Visual Studio Tools for Docker, versión 0.20, también se pueden establecer puntos de interrupción para el código que se ejecuta con el contenedor de Docker.

1. En el menú de Visual Studio, seleccione **Archivo > Nuevo > Proyecto**.

1. En la sección **Plantillas** del cuadro de diálogo **Nuevo proyecto**, seleccione **Visual C# > Web**.

1. Select **ASP.NET Core Web Application (.NET Core)** (Aplicación web de ASP.NET Core [.NET Core]).

1. Asigne un nombre a la nueva aplicación (o utilice el predeterminado) y pulse **Aceptar**.

1. En **ASP.NET Core Templates** (Plantillas de ASP.NET Core), select **Aplicación web** y pulse **Aceptar**.

1. Anule la selección de **Host en la nube**, ya que va a usar Docker como solución de implementación.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y seleccione **Agregar > Docker Support** (Compatibilidad con Docker).

	![][0]

1. En el nodo del proyecto se crean los siguientes archivos:

	![][1]

> [AZURE.NOTE] Si utiliza la [versión beta de Docker para Windows](https://beta.docker.com), abra Properties\\Docker.props, elimine el valor predeterminado y reinicie Visual Studio para que el valor surta efecto. ![][2]

##Edición y actualización
Para iterar rápidamente los cambios, puede iniciar la aplicación dentro de un contenedor, seguir realizando cambios y verlos como lo haría con IIS Express.

1. Establezca la Configuración de soluciones en `Debug` y presione **&lt;CTRL + F5 >** para generar la imagen de Docker y ejecutarla localmente. Vea la ventana de salida mediante la compilación.

1. Una vez que se haya creado la imagen del contenedor y se esté ejecutando en un contenedor de Docker, Visual Studio intentará iniciar la aplicación web en el explorador predeterminado. Si utiliza el explorador Microsoft Edge o aparece algún error, consulte la sección [Solución de problemas](vs-azure-tools-docker-troubleshooting-docker-errors.md).

1. Vuelva a Visual Studio y abra `Views\Home\About.cshtml`.

1. Anexe el siguiente contenido HTML al final del archivo y guarde los cambios.

	```
	<h1>Hello from a Docker Container!</h1>
	```

1.	Viendo la ventana de salida, cuando se haya completado la compilación de .NET y vea `Application started. Press Ctrl+C to shut down`, vuelva al explorador y actualice la página.

1.	Debería ver que se han aplicado los cambios.

##Puntos de interrupción para depuración  

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

	![][3]

##Resumen
Con [Visual Studio 2015 Tools para Docker](https://aka.ms/DockerToolsForVS) puede obtener la productividad de trabajar de forma local, con el realismo de producción de desarrollar en un contenedor de Docker.

## Solución de problemas
[Solución de problemas de desarrollo de Docker en Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Más información acerca de Docker con Visual Studio, Windows y Azure

- [Docker Tools para Visual Studio](http://aka.ms/dockertoolsforvs): desarrollo de código de .NET Core en un contenedor
- [Docker Tools para Visual Studio Team Services](http://aka.ms/dockertoolsforvsts): compilación e implementación de contenedores de Docker
- [Docker Tools para Visual Studio Code](http://aka.ms/dockertoolsforvscode): servicios del lenguaje para editar archivos de Docker, a los que próximamente se incorporarán más escenarios de E2E
- [Información del contenedor de Windows](http://aka.ms/containers): información de Windows Server y Nano Server
- [Servicio de contenedores de Azure](https://azure.microsoft.com/services/container-service/): [Contenido del servicio Contenedor de Azure](http://aka.ms/AzureContainerService)

## Varias herramientas de Docker

[Some great docker tools (Steve Lasker's blog)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/) (Herramientas excelentes de Docker [blog de Steve])

## Buenos artículos

[Introduction to Microservices](https://www.nginx.com/blog/introduction-to-microservices/) (Introducción a Microservicios) de NGINX

## Presentaciones

- [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
- [Introduction to ASP.NET Core @ build 2016 - Where You At Demo](https://channel9.msdn.com/Events/Build/2016/B810) (Introducción a ASP.NET Core @ compilación 2016)
- [Developing .NET apps in containers, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/) (Desarrollo de aplicaciones .NET en contenedores, Channel 9)

[0]: ./media/vs-azure-tools-docker-edit-and-refresh/add-docker-support.png
[1]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-files-added.png
[2]: ./media/vs-azure-tools-docker-edit-and-refresh/docker-props.png
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

<!---HONumber=AcomDC_0518_2016-->