<properties 
	pageTitle="Depuración de una aplicación de API en el Servicio de aplicaciones de Azure" 
	description="Aprenda a crear una aplicación de API mientras se ejecuta en el Servicio de aplicaciones de Azure, con Visual Studio." 
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

# Depuración de una aplicación de API en el Servicio de aplicaciones de Azure 

## Información general

En este tutorial, podrá depurar código de ASP.NET Web API mientras se ejecuta en una [aplicación de API](app-service-api-apps-why-best-platform.md) en [Servicio de aplicaciones de Azure](app-service-value-prop-what-is.md). El tutorial funciona con la aplicación de la API que ha [creado](app-service-dotnet-create-api-app.md) e [implementado](app-service-dotnet-deploy-api-app.md) en los tutoriales anteriores de esta serie.

Ha comenzado por utilizar la característica **Cliente de aplicación de API** para generar código de cliente que llama a la aplicación de API implementada. Después depurará simultáneamente la aplicación cliente y la aplicación de API, con la aplicación API ejecutándose de manera dinámica en la nube.

## Generación de un cliente de aplicación de API 

Las herramientas de aplicaciones de API de Visual Studio facilitan la generación de código C# que llama a las Aplicaciones de API de Azure desde aplicaciones de escritorio, de la tienda y móviles.

1. En Visual Studio, abra la solución que contiene la aplicación de API del tema [Creación de una aplicación de API](app-service-dotnet-create-api-app.md). 

2. En el **Explorador de soluciones**, haga clic con el botón secundario en la solución y seleccione **Agregar** > **Nuevo proyecto**.

	![Incorporación de proyecto nuevo](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. En el cuadro de diálogo **Agregar nuevo proyecto**, realice los pasos siguientes:

	1. Seleccione la categoría **Escritorio de Windows**.
	
	2. Seleccione la plantilla de proyecto **Aplicación de consola**.
	
	3. Asigne el nombre al proyecto.
	
	4. Haga clic en **Aceptar** para generar el nuevo proyecto en la solución existente.
	
	![Incorporación de proyecto nuevo](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. Haga clic en el proyecto de aplicación de consola recientemente creado y seleccione **Agregar** > **Cliente de aplicación de API de Azure**.

	![Incorporación de un nuevo cliente](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. En el cuadro de diálogo **Agregar cliente de aplicación de API de Microsoft Azure**, lleve a cabo los pasos siguientes:

	1. En el cuadro de diálogo, seleccione la opción **Descargar**. 
	
	2. En la lista desplegable, seleccione la aplicación de API que ha creado antes.
	
	3. Haga clic en **Aceptar**.

	![Pantalla de generación](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	El asistente descargará el archivo de metadatos de API y generará una interfaz para llamar a la aplicación de API.

	![Generación en curso](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	Una vez completada la generación de código, verá una nueva carpeta en el Explorador de soluciones, con el nombre de la aplicación de API. Esta carpeta contiene el código que implementa el cliente y los modelos de datos.

	![Generación completa](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

6. Abra el archivo **Program.cs** desde la raíz del proyecto y reemplace el método **Main** por el código siguiente:

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

## Prueba del cliente de aplicación de API

Una vez codificada la aplicación de API, es el momento de probar el código en el explorador.

1. Abra el **Explorador de soluciones**.

2. Haga clic con el botón secundario en la aplicación de consola que creó en la sección anterior.

3. En el menú contextual de la aplicación de consola, seleccione **Depurar > Iniciar nueva instancia**.

4. Se abre una ventana de consola y muestra todos los contactos.

	![Ejecución de la aplicación de consola](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. Presione **Entrar** para cerrar la ventana de consola.

## Implementación de aplicaciones de API 

Ahora que la aplicación de API y el cliente están codificados y probados, veamos cómo depurarlos.

1. En Visual Studio, en el menú **Ver**, seleccione **Explorador de servidores**. 

2. En la ventana **Explorador de servidores**, expanda el nodo **Azure > Servicio de aplicaciones**.

3. Localice el grupo de recursos que ha creado cuando implementaba la aplicación de API.

4. En el grupo de recursos, haga clic con el botón secundario en el nodo de la aplicación de API y seleccione **Asociar depurador**.

	![Asociación del depurador](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	El depurador remoto intentará conectarse. En algunos casos, puede que necesite volver a hacer clic en **Asociar depurador** para establecer una conexión, por lo que si se produce un error, vuelva a intentarlo.

	![Asociación del depurador](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. Una vez establecida la conexión, abra el archivo **ContactsController.cs** en el proyecto de la aplicación de API y agregue puntos de interrupción en los métodos `Get` y `Post`. Es posible que no aparezca como activa al principio, pero si se asocia el depurador remoto, ya estará listo para depurar.

	![Aplicación de puntos de interrupción al controlador](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. Para depurar, haga clic con el botón secundario en la aplicación de consola en el **Explorador de soluciones** y seleccione **Depurar** > **Iniciar nueva instancia**. Ahora puede depurar la aplicación de API de forma remota y la aplicación cliente localmente y consultar el flujo de datos completo.

	En la captura de pantalla siguiente se muestra el depurador cuando alcanza el punto de interrupción para el método `Post`. Puede ver que los datos de contacto del cliente se deserializan en un objeto fuertemente tipado `Contact`.

	![Depuración del cliente local para poder alcanzar la API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Pasos siguientes

La depuración remota de las Aplicaciones de API permite ver fácilmente cómo se ejecuta el código en el Servicio de aplicaciones de Azure. El diagnóstico enriquecido y los datos de depuración están disponibles directamente en el IDE de Visual Studio para las Aplicaciones de API de Azure.

Aplicaciones de API del Servicio de aplicaciones son aplicaciones web del Servicio de aplicaciones que tienen características adicionales para el hospedaje de servicios web, para que pueda utilizar las mismas herramientas de depuración y de solución de problemas para aplicaciones de API que se usan para las aplicaciones web. Para obtener más información, consulte [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

La aplicación de API que creó en esta serie está disponible públicamente para que cualquiera la llame. Para obtener información acerca de cómo proteger la aplicación de API para que solo puedan llamarla usuarios autenticados, consulte [Protección de una aplicación de API: agregar la autenticación de Azure Active Directory o de un proveedor social](app-service-api-dotnet-add-authentication.md).

<!--HONumber=54-->