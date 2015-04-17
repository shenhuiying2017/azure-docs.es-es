<properties 
	pageTitle="Depuración de forma remota de una aplicación de API del Servicio de aplicaciones de Azure" 
	description="Uso de Visual Studio para depurar de forma remota una aplicación de API del Servicio de aplicaciones de Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Depuración de forma remota de una aplicación de API del Servicio de aplicaciones de Azure 

## Información general

Se han ampliado las capacidades de depuración remotas de Visual Studio para incluir compatibilidad con Aplicaciones de API en el Servicio de aplicaciones de Azure. Esto significa que puede utilizar herramientas familiares de depuración para ver el código ejecutándose de manera dinámica en Azure. En este tema se muestra cómo utilizar el **cliente de Aplicaciones de API** de Visual Studio para generar código de cliente que llama a la aplicación de API implementada. Después depurará simultáneamente la aplicación cliente y la aplicación de API, con la aplicación API ejecutándose de manera dinámica en la nube.

Este tutorial es el último de una serie de tres tutoriales:

1. En [Creación de una aplicación de API](app-service-dotnet-create-api-app.md) ha creado un proyecto de aplicación de API. 
* En [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md) ha implementado la aplicación de API para sus suscripción de Azure.
* En este tutorial, va a utilizar Visual Studio para depurar de forma remota el código mientras se ejecuta en Azure.

## Generación de un cliente de aplicación de API 

Las herramientas de aplicaciones de API de Visual Studio facilitan la generación de código C# que llama a las Aplicaciones de API de Azure desde aplicaciones de escritorio, de la tienda y móviles. 

En Visual Studio, abra la solución que contiene la aplicación de API del [primer](app-service-dotnet-create-api-app.md) tutorial. Haga clic con el botón secundario en la solución y seleccione **Agregar** > **Nuevo proyecto**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

Seleccione la categoría **Escritorio de Windows** y la plantilla de proyecto **Aplicación de consola**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

Haga clic en el proyecto de aplicación de consola y seleccione **Agregar** > **Cliente de aplicación de API de Azure**. 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
En el cuadro de diálogo, seleccione la opción **Descargar**. En la lista desplegable, seleccione la aplicación de API que ha creado antes. Haga clic en **Aceptar**. 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

El asistente descargará el archivo de metadatos de API y generará una interfaz para llamar a la aplicación de API.

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

Una vez completada la generación de código, verá una nueva carpeta en el Explorador de soluciones, con el nombre de la aplicación de API. Esta carpeta contiene el código que implementa el cliente y los modelos de datos. 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

Abra el archivo **Program.cs** desde la raíz del proyecto y reemplace el método **Main** por el código siguiente: 

	static void Main(string[] args)
    {
        var client = new ContactsList();

        // Send GET request.
        var contacts = client.Contacts.Get();
        foreach (var c in contacts)
        {
            Console.WriteLine("{0}: {1} {2}",
                c.Id, c.Name, c.EmailAddress);
        }

        // Send POST request.
		client.Contacts.Post(new Models.Contact
	    {
	        EmailAddress = "lkahn@contoso.com",
	        Name = "Loretta Kahn",
	        Id = 4
	    });

        Console.WriteLine("Finished");
        Console.ReadLine();
    }

En el menú **Ver**, seleccione **Explorador de servidores**. En la ventana Explorador de servidores, expanda el nodo *Azure > Servicio de aplicaciones**. Busque el grupo de recursos que ha creado cuando implementaba la aplicación de API. Haga clic con el botón secundario en el nodo de la aplicación de API y seleccione **Asociar depurador**. 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

El depurador remoto intentará conectarse. En algunos casos, puede que necesite volver a hacer clic en **Asociar depurador** para establecer una conexión, por lo que si se produce un error, vuelva a intentarlo.

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

Una vez establecida la conexión, abra el archivo **ContactsController.cs** en el proyecto de la aplicación de API y agregue puntos de interrupción en los métodos  `Get` y  `Post`. Es posible que no aparezca como activa al principio, pero si se asocia el depurador remoto, ya estará listo para depurar. 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

Para depurar, haga clic con el botón secundario en la aplicación de consola en el Explorador de soluciones y seleccione **Depurar** > **Iniciar nueva instancia**. Ahora puede depurar la aplicación de API de forma remota y la aplicación cliente localmente y consultar el flujo de datos completo. 

En la captura de pantalla siguiente se muestra el depurador cuando alcanza el punto de interrupción para el método  `Post`. Puede ver que los datos de contacto del cliente se deserializan en un objeto  `Contact` fuertemente tipado. 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Resumen

La depuración remota de las Aplicaciones de API permite ver fácilmente cómo se ejecuta el código en el Servicio de aplicaciones de Azure. El diagnóstico enriquecido y los datos de depuración están disponibles directamente en el IDE de Visual Studio para que se ejecuten las Aplicaciones de API de Azure de forma remota.


<!--HONumber=49-->