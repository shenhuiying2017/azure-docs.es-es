---
title: Guía de inicio rápido para aprender a usar Azure SignalR Service| Microsoft Docs
description: Una guía de inicio rápido para usar Azure SignalR Service para crear un salón de chat con aplicaciones de ASP.NET Core MVC.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: 78e164b566194fcfe952e3ad59dd3d228f90d193
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-a-chat-room-with-signalr-service"></a>Guía de inicio rápido: Creación de un salón de chat con SignalR Service

Azure SignalR Service es un servicio de Azure que ayuda a los desarrolladores a compilar fácilmente aplicaciones web con características en tiempo real. Este servicio se basa en [SignalR para ASP.NET Core 2.0](https://docs.microsoft.com/aspnet/core/signalr/introduction).

En este artículo se muestra cómo empezar a trabajar con Azure SignalR Service. En esta guía de inicio rápido, creará una aplicación de chat con una aplicación web de ASP.NET Core MVC. Esta aplicación establecerá una conexión con el recurso de Azure SignalR Service para habilitar las actualizaciones de contenido en tiempo real. Hospedará la aplicación web localmente y se conectará con varios clientes de explorador. Cada cliente podrá insertar actualizaciones de contenido en todos los demás clientes. 


Puede usar cualquier editor de código para realizar los pasos de esta guía de inicio rápido. Sin embargo, [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción disponible en las plataformas Windows, macOS y Linux.

El código de este tutorial está disponible para su descarga en el [repositorio de GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom).  Además, la creación de los recursos de Azure que se usan en esta guía de inicio rápido puede lograrse con el [script de Create a SignalR Service](scripts/signalr-cli-create-service.md) (Creación de un recurso de SignalR Service).

![Guía de inicio rápido completo local](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>requisitos previos

* Instalar el [SDK de .NET Core](https://www.microsoft.com/net/download/windows)
* Descargar o clonar el repositorio de github [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

## <a name="create-an-azure-signalr-resource"></a>Creación de un recurso de Azure SignalR

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>Cree una aplicación web ASP.NET Core

En esta sección, utilizará la [interfaz de la línea de comandos (CLI) de .NET Core](https://docs.microsoft.com/dotnet/core/tools/) para crear un nuevo proyecto de aplicación web de ASP.NET Core MVC. La ventaja de usar la CLI de .NET Core frente a Visual Studio es que está disponible en las plataformas Windows, macOS y Linux. 

1. Cree una carpeta nueva para su proyecto. En esta guía de inicio rápido, se usa la carpeta *E:\Testing\chattest*.

2. En la nueva carpeta, ejecute el siguiente comando para crear un nuevo proyecto de aplicación web de ASP.NET Core MVC:

        dotnet new mvc


## <a name="add-secret-manager-to-the-project"></a>Adición de Secret Manager al proyecto

En esta sección, agregará la [herramienta Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) al proyecto. La herramienta Secret Manager almacena información confidencial para el trabajo de desarrollo fuera de su árbol de proyecto. Este enfoque ayuda a evitar el uso compartido accidental de secretos de la aplicación en el código fuente.

1. Abra el archivo *.csproj*. Agregue un elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Además, agregue un elemento `UserSecretsId` como se muestra a continuación y guarde el archivo.

    *chattest.csproj:*

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```

## <a name="add-azure-signalr-to-the-web-app"></a>Adición de Azure SignalR a la aplicación web

1. Para agregar una referencia al paquete de NuGet `Microsoft.Azure.SignalR`, ejecute el comando siguiente:

        dotnet add package Microsoft.Azure.SignalR -v 1.0.0-preview-10007

2. Ejecute el siguiente comando para restaurar los paquetes para el proyecto.

        dotnet restore

3. Agregue un secreto llamado *Azure:SignalR:ConnectionString* a Secret Manager. Este secreto contendrá la cadena de conexión para tener acceso al recurso de SignalR Service. *Azure: SignalR:ConnectionString* es la clave de configuración predeterminada que busca SignalR con el fin de establecer una conexión. Sustituya el valor en el comando siguiente por la cadena de conexión para el recurso de SignalR Service.

    Este comando debe ejecutarse en el mismo directorio que el archivo *.csproj*.

    ```
    dotnet user-secrets set Azure:SignalR:ConnectionString Endpoint=<Your endpoint>;AccessKey=<Your access key>;    
    ```

    Secret Manager solo se usará para probar la aplicación web mientras se hospeda localmente. En un tutorial posterior, se implementará la aplicación web de chat en Azure. Una vez que la aplicación web se haya implementado en Azure, usará una configuración de la aplicación en lugar de almacenar la cadena de conexión con Secret Manager.

4. Abra *Startup.cs* y actualice el método `ConfigureServices` para usar Azure SignalR Service mediante una llamada al método `services.AddSignalR().AddAzureSignalR()`:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    Al no pasar un parámetro a `AddAzureSignalR()`, este código usa la clave de configuración predeterminada, *Azure:SignalR:ConnectionString*, para la cadena de conexión del recurso de SignalR Service.

5. También en *Startup.cs*, actualice el método `Configure` sustituyendo la llamada a `app.UseStaticFiles()` con el código siguiente y guarde el archivo.

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            

### <a name="add-a-hub-class"></a>Adición de una clase de hub

En SignalR, un hub es un componente básico que expone un conjunto de métodos al que se puede llamar desde el cliente. En esta sección, define una clase de hub con dos métodos: 

* `Broadcast`: Este método difunde un mensaje a todos los clientes.
* `Echo`: Este método devuelve un mensaje al autor de la llamada.

Los dos métodos utilizan la interfaz `Clients` proporcionada por el SDK de ASP.NET Core SignalR. Esta interfaz le da acceso a todos los clientes conectados, lo que le permite insertar contenido en los clientes.

1. En el directorio del proyecto, agregue una nueva carpeta denominada *Hub*. Agregue un nuevo archivo de código de hub denominado *Chat.cs* a la nueva carpeta.

2. Agregue el código siguiente a *Chat.cs* para definir la clase de hub y guarde el archivo. 

    Actualice el espacio de nombres para esta clase si ha usado un nombre de proyecto que difiere de *chattest*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-web-app-client-interface"></a>Adición de la interfaz de cliente de aplicación web

La interfaz de usuario del cliente para esta aplicación de salón de chat se compondrá de HTML y JavaScript en un archivo denominado *index.html* en el directorio *wwwroot*.

Copie el archivo *index.html* y las carpetas *css* y *scripts* desde la carpeta *wwwroot* del [repositorio de ejemplos](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot) en la carpeta *wwwroot* del proyecto.

El código principal de *index.html*: 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

El código en *index.html* llama a `HubConnectionBuilder.build()` para realizar una conexión HTTP para el recurso de Azure SignalR.

Si la conexión es correcta, esa conexión se pasa a `bindConnectionMessage`, que agrega controladores de eventos para las inserciones de contenido entrante al cliente. 

`HubConnection.start()` inicia la comunicación con el hub. Una vez que se inicia la comunicación, `onConnected()` agrega los controladores de eventos de botones. Estos controladores utilizan la conexión para permitir que este cliente inserte las actualizaciones de contenido en todos los clientes conectados.

## <a name="add-a-development-runtime-profile"></a>Adición de un perfil de desarrollo en tiempo de ejecución

En esta sección, agregará un entorno de desarrollo en tiempo de ejecución para ASP.NET Core. Para obtener más información sobre el entorno en tiempo de ejecución para ASP.NET Core, consulte [Trabajar con varios entornos](https://docs.microsoft.com/aspnet/core/fundamentals/environments).

1. Cree una nueva carpeta en el proyecto denominada *Properties*.

2. Agregue un nuevo archivo denominado *launchSettings.json* a la carpeta, con el siguiente contenido y guarde el archivo.

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```


## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación localmente

1. Para compilar la aplicación mediante la CLI de .NET Core, ejecute el siguiente comando en el shell de comandos:

        dotnet build

2. Una vez que la compilación se complete correctamente, ejecute el siguiente comando para ejecutar la aplicación web localmente:

        dotnet run

    La aplicación se hospedará localmente en el puerto 5000 como está configurado en el perfil de desarrollo en tiempo de ejecución:

        E:\Testing\chattest>dotnet run
        Hosting environment: Development
        Content root path: E:\Testing\chattest
        Now listening on: http://localhost:5000
        Application started. Press Ctrl+C to shut down.    

3. Inicie dos ventanas del explorador y navegue por cada explorador hasta `http://localhost:5000`. Se le pedirá que escriba su nombre. Escriba un nombre de cliente para ambos clientes y pruebe a inserta el contenido del mensaje entre ambos clientes con el botón **Enviar**.

    ![Guía de inicio rápido completo local](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)



## <a name="clean-up-resources"></a>Limpieza de recursos

Si va a seguir con el tutorial siguiente, puede mantener los recursos creados en esta guía de inicio rápido y volverlos a utilizar con el tutorial siguiente.

En caso contrario, si ya ha terminado con la aplicación de ejemplo de la guía de inicio rápido, puede eliminar los recursos de Azure creados en este tutorial para evitar cargos. 

> [!IMPORTANT]
> La eliminación de un grupo de recursos es irreversible y el grupo de recursos y todos los recursos que contiene se eliminarán de forma permanente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. Si ha creado los recursos para hospedar este ejemplo dentro de un grupo de recursos existente que contiene recursos que desea mantener, puede eliminar cada recurso individualmente de sus hojas respectivas, en lugar de eliminar el grupo de recursos.
> 
> 

Inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Grupos de recursos**.

Escriba el nombre del grupo de recursos en el cuadro de texto **Filtrar por nombre…**. Las instrucciones de esta guía de inicio rápido usan un grupo de recursos llamado *SignalRTestResources*. En el grupo de recursos en la lista de resultados, haga clic en **…** y, a continuación, en **Eliminar grupo de recursos**.

   
![Eliminar](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


Se le pedirá que confirme la eliminación del grupo de recursos. Escriba el nombre del grupo de recursos para confirmar y haga clic en **Eliminar**.
   
Transcurridos unos instantes, el grupo de recursos y todos los recursos que contiene se eliminan.



## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado un nuevo recurso de Azure SignalR Service y lo ha usado con una aplicación web de ASP.NET Core para incrustar actualizaciones de contenido en tiempo real en varios clientes conectados. Para obtener más información sobre cómo utilizar Azure SignalR Service, continúe con el siguiente tutorial que muestra cómo realizar la autenticación.

> [!div class="nextstepaction"]
> [Autenticación de Azure SignalR Service](./signalr-authenticate-oauth.md)


