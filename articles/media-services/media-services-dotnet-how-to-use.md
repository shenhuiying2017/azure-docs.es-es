---
title: "Configuración del equipo para el desarrollo de Servicios multimedia con .NET"
description: "Conozca los requisitos previos de Servicios multimedia usando el SDK de Servicios multimedia para .NET. Aprenda también a crear una aplicación de Visual Studio."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/07/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 612b58db48e160cb1b4cfef1f8f4c2b203061064
ms.lasthandoff: 03/10/2017


---
# <a name="media-services-development-with-net"></a>Desarrollo de Servicios multimedia con .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

En este tema se explica cómo empezar a desarrollar aplicaciones de Servicios multimedia con .NET.

La **biblioteca del SDK de Azure Media Services para .NET** le permite programar en los Media Services mediante. NET. Para que el desarrollo con .NET sea aún más fácil, se proporciona la biblioteca **Extensiones del SDK de Azure Media Services para .NET**. Esta biblioteca contiene un conjunto de métodos de extensión y funciones auxiliares que simplifican el código de .NET. Las dos bibliotecas están disponibles a través de **NuGet** y **GitHub**.

## <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Servicios multimedia en una suscripción de Azure nueva o existente. Vea el tema [Cómo crear una cuenta de Media Services](media-services-portal-create-account.md).
* Sistemas operativos: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
* .NET Framework 4.5.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Creación y configuración de un proyecto de Visual Studio
En esta sección se muestra cómo crear un proyecto en Visual Studio y configurarlo para el desarrollo de Servicios multimedia.  En este caso, el proyecto es una aplicación de consola Windows de C#, pero los pasos de configuración que se muestran aquí se aplican a otros tipos de proyectos que puede crear para las aplicaciones de Media Services (por ejemplo, una aplicación de Windows Forms o una aplicación web ASP.NET).

En esta sección se muestra cómo usar **NuGet** para agregar el SDK de Servicios multimedia para .NET y otras bibliotecas dependientes.

También puede obtener los bits más recientes del SDK de Media Services para .NET en GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) o [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), compilar la solución y agregar las referencias al proyecto de cliente. Todas las dependencias necesarias se descargan y extraen automáticamente.

1. Cree una aplicación de consola en C# mediante Visual Studio. Escriba el **Nombre**, la **Ubicación** y el **Nombre de la solución** y, a continuación, haga clic en Aceptar.
2. Compile la solución.
3. Use **NuGet** para instalar y agregar **Extensiones del SDK de Azure Media Services para .NET**. Al instalar este paquete, también se instala el **SDK de Servicios multimedia para .NET** y agrega todas las demás dependencias necesarias.
   
    Asegúrese de que tiene instalada la versión más reciente de NuGet. Para más información e instrucciones de instalación, consulte [NuGet](http://nuget.codeplex.com/).
4. En el Explorador de soluciones, haga clic con el botón derecho en el nombre del proyecto y elija Administrar paquetes NuGet.
   
    Aparecerá el cuadro de diálogo Administrar paquetes de NuGet.
5. En la galería en línea, busque Extensiones de Servicios multimedia de Azure, elija Extensiones del SDK de Servicios multimedia de Azure para .NET y luego haga clic en el botón Instalar.
   
    El proyecto se modifica y se agregan referencias a Extensiones del SDK de Media Services para .NET, al SDK de .NET de Media Services y a otros ensamblados dependientes.
6. Para promover un entorno de desarrollo más limpio, considere la posibilidad de habilitar la restauración de paquetes de NuGet. Para obtener más información, consulte [Restauración de paquetes de NuGet](http://docs.nuget.org/consume/package-restore).
7. Agregue una referencia al ensamblado **System.Configuration** . Este ensamblado contiene la clase de Configuración del sistema.**Administrador de configuración** que se usa para tener acceso a los archivos de configuración (por ejemplo, App.config).
   
    Para agregar referencias usando el cuadro de diálogo de administración de referencias, haga clic con el botón derecho en el nombre del proyecto en el Explorador de soluciones. A continuación, seleccione Agregar y Referencias.
   
    Aparecerá el cuadro de diálogo Administrar referencias.
8. En los ensamblados de .NET Framework, busque, seleccione el ensamblado System.Configuration y haga clic en Aceptar.
9. Abra el archivo App.config (agregue el archivo al proyecto si no se ha agregado de forma predeterminada) y agregue una sección *appSettings* al archivo.     
   Establezca los valores de la clave de nombre y la cuenta de cuenta de Servicios multimedia de Azure, tal como se muestra en el ejemplo siguiente.
   
    Para buscar el nombre y la clave de valores, vaya a Azure Portal y seleccione la cuenta. Aparecerá la ventana Configuración a la derecha. En la ventana Configuración, seleccione Claves. Al hacer clic en el icono junto a cada cuadro de texto, se copia el valor al Portapapeles del sistema.

        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

1. Sobrescriba las instrucciones **using** existentes siguiendo las instrucciones al comienzo del archivo Program.cs con el siguiente código.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

En este punto, está listo para iniciar el desarrollo de una aplicación de Servicios multimedia.    

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


