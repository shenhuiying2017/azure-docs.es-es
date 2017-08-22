---
title: Uso de .NET Core en Web App en Linux | Microsoft Docs
description: Use .NET Core en Web App en Linux.
keywords: "azure app service, aplic. web, dotnet, núcleo, linux, oss"
services: app-service
documentationCenter: 
authors: michimune, rachelappel
manager: erikre
editor: 
ms.assetid: c02959e6-7220-496a-a417-9b2147638e2e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: aelnably;wesmc;mikono;rachelap
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: cbcc43e04e58894b6505fdf6c48c7792343049b9
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---

# <a name="use-net-core-in-an-azure-web-app-on-linux"></a>Uso de .NET Core en una instancia de Azure Web App en Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

[Web App](https://docs.microsoft.com/azure/app-service-web/app-service-linux-intro) en Linux proporciona un servicio de hospedaje web muy escalable y con aplicación automática de revisiones mediante el sistema operativo Linux. Este tutorial contiene instrucciones paso a paso que demuestran cómo crear una aplicación [.NET Core](https://docs.microsoft.com/aspnet/core/) en una aplicación web de Azure en Linux. 

![Aplicación web en Linux][10]

Estos pasos se pueden realizar con un equipo Mac, Windows o Linux.

## <a name="prerequisites"></a>Requisitos previos ##

Para completar este tutorial: 

* Instale el [SDK de .NET Core](https://www.microsoft.com/net/download/core).
* Instale [Git](https://git-scm.com/downloads).

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-local-net-core-application"></a>Creación de una aplicación .NET Core local ##

Inicie una nueva sesión de terminal. Cree un directorio denominado `hellodotnetcore` y haga que sea el directorio actual. A continuación, escriba lo siguiente: 

```
dotnet new web
``` 

  Este comando crea tres archivos (*hellodotnetcore.csproj*, *Program.cs* y *Startup.cs*) y una carpeta vacía (*wwwroot /*) en el directorio actual. El contenido de `.csproj` debería tener esta apariencia: 

```xml
  <!-- Empty lines are omitted. -->

  <Project Sdk="Microsoft.NET.Sdk.Web">
        <PropertyGroup>
        <TargetFramework>netcoreapp1.1</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
        <Folder Include="wwwroot\" />
        </ItemGroup>
        <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore" Version="1.1.2" />
        </ItemGroup>
  </Project>
```

Puesto que esta aplicación es una aplicación web, una referencia a un paquete de ASP.NET Core se agregó automáticamente al archivo *hellodotnetcore.csproj*. El número de versión del paquete se establece según el marco de trabajo seleccionado. Este ejemplo hace referencia a ASP.NET Core versión 1.1.2 porque se usa .NET Core 1.1.

## <a name="build-and-test-the-application-locally"></a>Compilación y prueba de la aplicación localmente ##

Puede compilar y ejecutar la aplicación .NET Core con el comando `dotnet restore` seguido del comando `dotnet run`, como se muestra aquí:

```
dotnet restore
dotnet run
```


Cuando se inicia la aplicación, muestra un mensaje que indica que está atendiendo las solicitudes entrantes en un puerto. 

```bash
Hosting environment: Production
Content root path: C:\hellodotnetcore
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

Examine `http://localhost:5000/` con el explorador para probarla. Si todo funciona correctamente, verá "Hello World!" (¡Hola mundo!) como texto del resultado.

![Prueba con un explorador][7]

## <a name="create-a-net-core-app-in-the-azure-portal"></a>Creación de una aplicación .NET Core en Azure Portal ##

En primer lugar debe crear una aplicación web vacía. Inicie sesión en el [Azure Portal](https://portal.azure.com/) y cree una nueva [Web App en Linux](https://portal.azure.com/#create/Microsoft.AppSvcLinux).

![Creación de una aplicación web][1]

Cuando se abra la página **Crear**, proporcione detalles acerca de la aplicación web:

![Elección de una pila de tiempo de ejecución .NET Core][2]

Utilice la tabla siguiente como guía para rellenar la página **Crear**, a continuación, seleccione **Aceptar** y **Crear** para crear la aplicación.

| Configuración      | Valor sugerido  | Descripción                                        |
| ------------ | ---------------- | -------------------------------------------------- |
| Nombre de la aplicación | hellodotnetcore  | Nombre de la aplicación. Este nombre debe ser único. |
| La suscripción | Elección de una suscripción actual | Suscripción a Azure. |
| Grupo de recursos | myResourceGroup |  Grupo de recursos de Azure que contiene la aplicación web. |
| Plan de servicio de aplicación | Nombre del plan de App Service existente |  Plan de App Service.  |
| Configuración del contenedor | .NET Core 1.1 | Tipo de contenedor de esta aplicación web: registro integrado, Docker o Private. |
| Origen de la imagen  | Característica integrada  |  Origen de la imagen. |
| Pila de tiempo de ejecución  | .NET Core 1.1  | Pila de tiempo de ejecución y versión.  |

## <a name="deploy-your-application-via-git"></a>Implementación de la aplicación a través de Git ##

Use Git para implementar la aplicación .NET Core para la aplicación web de Azure App Service en Linux.

La nueva aplicación web de Azure ya tiene configurada una implementación de Git. Encontrará la URL de implementación de Git accediendo a la siguiente dirección URL después de insertar el nombre de la aplicación web:

```https://{your web app name}.scm.azurewebsites.net/api/scm/info```

La URL de Git tiene el formato siguiente en función del nombre de la aplicación web:

```https://{your web app name}.scm.azurewebsites.net/{your web app name}.git```

Ejecute los comandos siguientes para implementar la aplicación local en la aplicación web de Azure: 
 
```bash
git init
git remote add azure <Git deployment URL from above>
git add *.csproj *.cs
git commit -m "Initial deployment commit"
git push azure master
```

No es necesario insertar los archivos en los directorios *bin/* u *obj/* dado que la aplicación se compila en la nube cuando se insertan los archivos de código fuente de la aplicación en Azure. Una vez completado el proceso de compilación, los archivos binarios se copian en el directorio de la aplicación en */home/site/wwwroot/*.

Confirme que las operaciones de implementación remota se han realizado correctamente. Las operaciones de inserción pueden tardar bastante tiempo desde que se ejecuta el proceso de compilación y resolución del paquete en la nube. Verá varios mensajes de estado, y algunos indican que se han copiado los archivos. La salida debe tener una apariencia similar a la siguiente:

```bash
/* some output has been removed for brevity */
remote: Copying file: 'System.Net.Websockets.dll' 
remote: Copying file: 'System.Runtime.CompilerServices.Unsafe.dll' 
remote: Copying file: 'System.Runtime.Serialization.Primitives.dll' 
remote: Copying file: 'System.Text.Encodings.Web.dll' 
remote: Copying file: 'hellodotnetcore.deps.json' 
remote: Copying file: 'hellodotnetcore.dll' 
remote: Omitting next output lines...
remote: Finished successfully.
remote: Running post deployment commands...
remote: Deployment successful.
To https://hellodotnetcore.scm.azurewebsites.net/
 * [new branch]           master -> master

```

Una vez que la implementación haya finalizado, reinicie la aplicación web para que la implementación surta efecto. Para ello, vaya a Azure Portal y a la página **Introducción** de su aplicación web. Seleccione el botón **Reinicio** en la página. Cuando aparezca una ventana emergente, seleccione **Sí** para confirmar. A continuación, puede examinar la aplicación web, como se muestra aquí:

![Exploración de la aplicación .NET Core implementada en la instancia de Azure App Service en Linux][10]

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Pasos siguientes
* [Preguntas más frecuentes sobre Web App on Linux de Azure App Service](./app-service-linux-faq.md)

[1]: ./media/app-service-linux-using-dotnetcore/top-level-create.png
[2]: ./media/app-service-linux-using-dotnetcore/dotnet-new-webapp.png
[7]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-local.png
[10]: ./media/app-service-linux-using-dotnetcore/dotnet-browse-azure.png

