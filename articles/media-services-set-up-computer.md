<properties linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Set Up Computer for Media Services" pageTitle="How to Set Up Computer for Media Services - Azure" metaKeywords="" description="Learn about the prerequisites for Media Services using the Media Services SDK for .NET. Also learn how to create a Visual Studio app." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# <a name="setup-dev"> </a><span class="short header">Configuración de su equipo para el desarrollo de Servicios multimedia</span>

Esta sección contiene los requisitos previos generales para el desarrollo de Servicios multimedia usando el SDK de Servicios multimedia para .NET. Le enseña también a los desarrolladores cómo crear una aplicación de Visual Studio para el desarrollo de SDK de los Servicios multimedia.

### Requisitos previos

-   Una cuenta de Servicios multimedia en una suscripción de Azure nueva o existente. Consulte el tema [Creación de una cuenta de Servicios multimedia][].
-   Sistemas operativos: Windows 7, Windows 2008 R2 o Windows 8.
-   .NET Framework 4.5 o .NET Framework 4.
-   Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).
-   Use el paquete de [Nuget windowsazure.mediaservices][] (en inglés) para instalar el SDK de Azure para .NET. La siguiente sección indica cómo usar **Nuget** para instalar el SDK de Azure.

## <a name="setup-account"></a><span class="short header">Configuración de una cuenta de Azure para Servicios multimedia</span>

Para configurar su cuenta de Servicios multimedia, use el Portal de administración de Azure (recomendado). Consulte el tema [Creación de una cuenta de Servicios multimedia][]. Después de crear la cuenta en el Portal de administración, está listo para configurar el equipo para el desarrollo de Servicios multimedia.

### Creación de una aplicación en Visual Studio

En esta sección se muestra cómo crear un proyecto en Visual Studio y configurarlo para el desarrollo de Servicios multimedia. En este caso, el proyecto es una aplicación de consola de C# Windows, pero los pasos de configuración que se muestran aquí se aplican a otros tipos de proyectos que puede crear para las aplicaciones de Servicios multimedia (por ejemplo, una aplicación de Windows Forms o una aplicación web de ASP.NET).

1.  Cree una **Aplicación de consola** de C# nueva en Visual Studio 2012 o Visual Studio 2010 SP1. Escriba el **Nombre**, la **Ubicación** y el **Nombre de la solución** y, a continuación, haga clic en **OK**.
2.  Agregue una referencia al ensamblado **System.Configuration**. Para agregar una referencia a **System.Configuration**, en el **Explorador de soluciones**, haga clic con el botón secundario en el nodo **Referencias** y seleccione **Add Reference...**. En el cuadro de diálogo **Administrar referencias**, seleccione **System.Configuration** y haga clic en **OK**.
3.  Agregue referencias a **SDK de Azure para .NET** (Microsoft.WindowsAzure.StorageClient.dll), **SDK de los Servicios multimedia de Azure para .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) y bibliotecas de **WCF Data Services 5.0 para OData V3** (Microsoft.Data.OData.dll) que usan el paquete de [Nuget windowsazure.mediaservices][] (en inglés).

    Para agregar referencias usando Nuget, realice lo siguiente. En el **menú principal** de Visual Studio, seleccione **HERRAMIENTAS** -\> **Administrador de paquetes de biblioteca** -\> **Consola del Administrador de paquetes**. En la ventana de la consola, escriba **Install-Package windowsazure.mediaservices** y presione **Entrar**.

4.  Sobrescriba los valores existentes siguiendo las instrucciones al comienzo del archivo Program.cs con el siguiente código.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

En este punto, está listo para iniciar el desarrollo de una aplicación de Servicios multimedia.

## <a name="setup-account"></a><span class="short header">Conexión con los Servicios multimedia</span>

Casi todo lo que hace en la programación de los Servicios multimedia requiere una referencia para el objeto de contexto del servidor. El contexto de servidor le proporciona acceso programático a todos los objetos de programación de los Servicios multimedia.

Para obtener una referencia al contexto de servidor, devuelva una instancia nueva del tipo de concepto como en el siguiente ejemplo de código. Proporcione al constructor el nombre y la clave de la cuenta de Servicios multimedia (que obtuvo durante el proceso de configuración de la cuenta).

    static CloudMediaContext GetContext()
    {
        // Gets the service context. 
        return new CloudMediaContext(_accountName, _accountKey);
    } 

Muchas veces es útil definir una variable en el nivel de módulo del tipo **CloudMediaContext** para mantener una referencia al contexto de servidor que se devuelve mediante un método como **GetContext**. El resto de los ejemplos de código en este tema usan una variable que se llama **\_context** para referirse al contexto de servidor.

## Pasos siguientes

Ahora que ha configurado el equipo y creó una solución de Visual Studio para programar Servicios multimedia, diríjase al tema [Creación de un activo codificado y carga en el almacenamiento][].
[Creación de una cuenta de Servicios multimedia]: <http://go.microsoft.com/fwlink/?linkid=256662>
[Creación de un activo codificado y carga en el almacenamiento]:<http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409>

  [Creación de una cuenta de Servicios multimedia]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-create-a-media-services-account/
  [Nuget windowsazure.mediaservices]: http://nuget.org/packages/windowsazure.mediaservices
