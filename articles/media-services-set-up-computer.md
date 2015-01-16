<properties urlDisplayName="Set Up Computer for Media Services" pageTitle="Configuración del equipo para Servicios multimedia de Azure" metaKeywords="" description="Conozca los requisitos previos de Servicios multimedia usando el SDK de Servicios multimedia para .NET. Aprenda también a crear una aplicación de Visual Studio." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1><a name="setup-dev"> </a><span class="short header">Configuración de su equipo para el desarrollo de Servicios multimedia</span></h1> 

Esta sección contiene los requisitos previos generales para el desarrollo de Servicios multimedia usando el SDK de Servicios multimedia para .NET. Le enseña también a los desarrolladores cómo crear una aplicación de Visual Studio para el desarrollo de SDK de los Servicios multimedia. 

###Requisitos previos

-   Una cuenta de Servicios multimedia en una suscripción de Azure nueva o existente. Consulte el tema [Creación de una cuenta de Servicios multimedia](../media-services-create-account/).
-   Sistemas operativos: Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.
-   Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express). 
-   Use el paquete [Nuget windowsazure.mediaservices](http://nuget.org/packages/windowsazure.mediaservices) para instalar el SDK de Azure para .NET. La siguiente sección indica cómo usar **Nuget** para instalar el SDK de Azure.
  
<h2><a name="setup-account"></a><span class="short header">Configuración de una cuenta de Azure para Servicios multimedia</span></h2>

Para configurar su cuenta de Servicios multimedia, use el Portal de administración de Azure (recomendado). Consulte el tema [Creación de una cuenta de Servicios multimedia][]. Después de crear la cuenta en el Portal de administración, está listo para configurar el equipo para el desarrollo de Servicios multimedia. 

###Creación de una aplicación en Visual Studio

En esta sección se muestra cómo crear un proyecto en Visual Studio y configurarlo para el desarrollo de Servicios multimedia.  En este caso, el proyecto es una aplicación de consola de C# Windows, pero los pasos de configuración que se muestran aquí se aplican a otros tipos de proyectos que puede crear para las aplicaciones de Servicios multimedia (por ejemplo, una aplicación de Windows Forms o una aplicación web de ASP.NET).

   1. Cree una nueva **Aplicación de consola** de C# en Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **nombre**, la **ubicación** y el **nombre de la solución** y, a continuación, haga clic en **Aceptar**.
   2. Asegúrese de establecer el marco de destino en .NET Framework 4. Para ello, haga clic con el botón secundario del mouse en el proyecto de Visual Studio y seleccione Propiedades. En la pestaña Aplicación, establezca .NET Framework 4 como marco de destino.   
   3. Agregue una referencia al ensamblado **System.Configuration**. Para agregar una referencia a **System.Configuration**, en el **Explorador de soluciones**, haga clic con el botón secundario en el nodo **References** y seleccione **Agregar referencia...**. En el cuadro de diálogo **Administrar referencias**, seleccione **System.Configuration** y haga clic en **Aceptar**.
   4. Use el paquete [Nuget windowsazure.mediaservices](http://nuget.org/packages/windowsazure.mediaservices) para agregar referencias al **SDK de Azure para .NET.** (todos los demás ensamblados dependientes se instalarán también).  

	Para agregar referencias usando Nuget, realice lo siguiente. En el **menú principal** de Visual Studio, seleccione **HERRAMIENTAS** -> **Administrador de paquetes de la biblioteca** -> **Consola del administrador de paquetes**. En la ventana de la consola escriba **Install-Package windowsazure.mediaservices** y presione **Entrar**..
   4. Sobrescriba los valores existentes siguiendo las instrucciones al comienzo del archivo Program.cs con el siguiente código.

   		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.MediaServices.Client;

En este punto, está listo para iniciar el desarrollo de una aplicación de Servicios multimedia.    
   
<h2><a name="setup-account"></a><span class="short header">Conexión con los Servicios multimedia</span></h2> 

Casi todo lo que hace en la programación de los Servicios multimedia requiere una referencia para el objeto de contexto del servidor. El contexto de servidor le proporciona acceso programático a todos los objetos de programación de los Servicios multimedia.

Para obtener una referencia al contexto de servidor, cree una instancia nueva del tipo de contexto como en el siguiente ejemplo de código. Proporcione al constructor el nombre y la clave de la cuenta de Servicios multimedia (que obtuvo durante el proceso de configuración de la cuenta). 

    // Create and cache the Media Services credentials in a static class variable.
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);

	// Use the cached credentials to create CloudMediaContext.
	_context = new CloudMediaContext(_cachedCredentials);


Con frecuencia resulta útil definir una variable de nivel de módulo de tipo **CloudMediaContext** para mantener una referencia al contexto de servidor. Para obtener más información, consulte [Conectar con Servicios multimedia mediante el SDK de Servicios multimedia](http://msdn.microsoft.com/en-us/library/azure/jj129571.aspx).

El resto de los ejemplos de código en este tema usan una variable que se llama **_context** para referirse al contexto de servidor. 

<h2>Pasos siguientes</h2>
Ahora que ha configurado el equipo y creó una solución de Visual Studio para programar Servicios multimedia, diríjase al tema [Creación de un recurso codificado y carga en el almacenamiento][]..
[Creación de una cuenta de Servicios multimedia]: ../media-services-create-account/
[Creación de un recurso cifrado y carga en el almacenamiento]: ../media-services-create-encrypted-asset-upload-storage/

<!--HONumber=35.1-->
