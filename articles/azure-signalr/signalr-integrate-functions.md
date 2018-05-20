---
title: Tutorial para la integración de Azure Functions con Azure SignalR Service | Microsoft Docs
description: En este tutorial, aprenderá a usar Azure Functions con Azure SignalR Service.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Tutorial: Integración de Azure Functions con Azure SignalR Service

Este tutorial se basa en la aplicación de salón de chat creada en tutoriales anteriores. Si no ha realizado los ejercicios [Create a chat room with SignalR Service](signalr-quickstart-dotnet-core.md) (Creación de un salón de chat con SignalR Service) y [Azure SignalR Service authentication](./signalr-authenticate-oauth.md) (Autenticación de Azure SignalR Service), hágalos primero. 

Un escenario común con las aplicaciones en tiempo real es el de las actualizaciones de contenido que se originan desde un servidor para publicarse en los clientes web. [Azure Functions](../azure-functions/functions-overview.md) es un excelente candidato para generar estas actualizaciones de contenido. Una de las ventajas principales del uso de Azure Functions es que puede ejecutar su código a petición sin preocuparse por la arquitectura de una aplicación completa o de la infraestructura para ejecutarla. Solo paga por el tiempo que el código se ejecuta realmente.  

Normalmente, este escenario podría suponer un problema al intentar usar SignalR porque SignalR intenta mantener una conexión entre el cliente y el servidor para insertar las actualizaciones de contenido. Puesto que el código solo se ejecuta a petición, no se puede mantener una conexión. Sin embargo, Azure SignalR Service puede admitir este escenario, ya que administra las conexiones automáticamente en tiempo de ejecución.

En este tutorial, usará Azure Functions para generar mensajes mediante una función de temporizador al comienzo de cada minuto. La función publicará los mensajes para todos los clientes de salón de chat creados en los tutoriales anteriores. Para más información sobre las funciones de temporizador, consulte [Función Timer](../azure-functions/functions-create-scheduled-function.md).

Puede usar cualquier editor de código para realizar los pasos de esta guía de inicio rápido. Sin embargo, [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción disponible en las plataformas Windows, macOS y Linux.

El código de este tutorial está disponible para su descarga en el [repositorio de GitHub AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Aplicación de chat con mensajes del servidor](./media/signalr-integrate-functions/signalr-functions-complete.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una nueva función Timer con Azure Functions mediante la CLI de Azure.
> * Configurar la función de temporizador para la implementación del repositorio de Git local
> * Conectar el temporizador a SignalR Service para insertar actualizaciones cada minuto

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>requisitos previos

Para realizar este tutorial, debe disponer de los siguientes requisitos previos:

* [Git](https://git-scm.com/)
* [SDK de .NET Core](https://www.microsoft.com/net/download/windows) 
* [Azure Cloud Shell configurado](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Descargar o clonar el repositorio de GitHub [AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Creación de una aplicación de función

Debe crear una aplicación de función para definir el entorno de ejecución de las funciones. La aplicación de función también le permite agrupar varias funciones como una unidad lógica para facilitar la administración, la implementación y el uso compartido de recursos. Para más información, consulte [Creación de su primera función con la CLI de Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md).

En esta sección, usará Azure Cloud Shell para crear una nueva aplicación de Azure Functions configurada para la implementación desde un repositorio de Git local. 

Al crear los recursos de la aplicación de función, lo hace en el mismo grupo de recursos que usó en los tutoriales anteriores. Este enfoque simplifica la administración de los recursos de todos los tutoriales.

Copie el script siguiente en el editor de texto y reemplace los valores de los parámetros de variable por los valores de los recursos. Copie y pegue el script actualizado en Azure Cloud Shell y presione **Entrar** para crear una cuenta de almacenamiento y una aplicación de función.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| . | DESCRIPCIÓN |
| -------------------- | --------------- |
| ResourceGroupName | Este nombre de grupo de recursos se sugirió en tutoriales anteriores. Es una buena idea para mantener agrupados todos juntos los recursos de los tutoriales. Use el mismo grupo de recursos que utilizó en los tutoriales anteriores. | 
| location | Actualice esta variable con la misma ubicación que usó para crear el grupo de recursos en los tutoriales anteriores. | 
| functionappName | Actualice esta variable con un nombre único para la nueva aplicación de función. Por ejemplo, signalrfunctionapp22665120. | 
| storageAccountName | Escriba un nombre para la nueva cuenta de almacenamiento que contendrá la configuración y el código de la aplicación de función. | 



## <a name="configure-the-function-app"></a>Configuración de la Function App

En esta sección, configurará la aplicación de función con una configuración de aplicación que contiene la cadena de conexión para el recurso de Azure SignalR Service. El código de función usará este valor para conectarse al salón de chat y publicar mensajes en él. También configurará la aplicación de función para la implementación desde un repositorio de Git local.

Copie el script siguiente y reemplace los valores de los parámetros. Pegue el script actualizado en Azure Cloud Shell y presione **Entrar**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| . | DESCRIPCIÓN |
| -------------------- | --------------- |
| ResourceGroupName | Este nombre de grupo de recursos se sugirió en tutoriales anteriores. Es una buena idea para mantener agrupados todos juntos los recursos de los tutoriales. Use el mismo grupo de recursos que utilizó en los tutoriales anteriores. | 
| functionappName | Actualice esta variable con un nombre único para la nueva aplicación de función. Por ejemplo, signalrfunctionapp22665120. | 
| connstring | Escriba la cadena de conexión del recurso de SignalR Service. Puede recuperar esta cadena de conexión de la página de recursos de SignalR Service en Azure Portal; para ello, haga clic en **Claves** en **Configuración**. | 



Anote la dirección URL de implementación de Git que devolvió el último comando. La usará para implementar el código de función.


## <a name="the-timer-function"></a>La función de temporizador

El ejemplo de la función de temporizador se encuentra en el directorio */samples/Timer* de su descarga; también puede clonar el repositorio de GitHub [AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer). El código de la función de temporizador se encuentra en *TimerFunction.cs*. Este código usa la cadena de conexión almacenada con la clave de configuración predeterminada (*Azure: SignalR:ConnectionString*) para crear un proxy para la central. Dado que el código de función se ejecuta en el servidor, no hay ninguna razón para exigir que se autentique como un cliente normal. El código es de confianza para usar la cadena de conexión. Mediante este proxy de central, el código de función puede llamar a cualquiera de los métodos que ha definido en la central. El código llama al método `BroadcastMessage` para publicar un mensaje que contiene la hora actual de cuando se activó el desencadenador.

El desencadenador del código de función es un objeto *timerTrigger*, definido en los enlaces en *TimerFunction/function.json*. Incluye una [expresión CRON](https://wikipedia.org/wiki/Cron#CRON_expression) para establecer el desencadenador de temporizador para que se active al comienzo de cada minuto.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Compilación de la función de temporizador

Use la [interfaz de la línea de comandos de .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) en los pasos siguientes para compilar la función y prepararla para la implementación:

1. Vaya al subdirectorio */samples/Timer* de la descarga o clone el repositorio de GitHub [AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples).

2. Restaure los paquetes de NuGet mediante el comando siguiente:

        dotnet restore

3. Compile la aplicación de función *Timer* con el comando siguiente:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Creación e implementación del repositorio de Git local

1. En un shell de Git, desplácese hasta el subdirectorio de compilación, */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Inicialice el directorio como un nuevo repositorio de Git con el comando siguiente:

        git init

3. Agregue una nueva confirmación para todos los archivos del directorio de compilación.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Agregue un punto de conexión remoto para la dirección URL de implementación de Git que anotó durante la configuración de la aplicación de función:

        git remote add Azure <enter your Git deployment URL>

5. Implementación de la aplicación de función

        git push Azure master

   Una vez implementado el código, el temporizador comienza a activarse inmediatamente cada minuto para ejecutar el código.

## <a name="test-the-chat-app"></a>Prueba de la aplicación de chat

Vaya a la aplicación de chat; la función Timer que acaba de crear notifica ahora el tiempo al comienzo de cada minuto.

![Aplicación de chat con mensajes del servidor](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere seguir probando la aplicación, puede mantener los recursos que ha creado.

En caso contrario, si ha terminado con la aplicación de ejemplo, puede eliminar los recursos de Azure para evitar cargos. 

> [!IMPORTANT]
> La eliminación de un grupo de recursos es irreversible y el grupo de recursos y todos los recursos que contiene se eliminarán de forma permanente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. Si ha creado los recursos para hospedar este ejemplo dentro de un grupo de recursos existente que contiene recursos que desea mantener, puede eliminar cada recurso individualmente de sus hojas respectivas, en lugar de eliminar el grupo de recursos.
> 
> 

Inicie sesión en el [Portal de Azure](https://portal.azure.com) y haga clic en **Grupos de recursos**.

Escriba el nombre del grupo de recursos en el cuadro de texto **Filtrar por nombre...**. En las instrucciones de este artículo se usa un grupo de recursos llamado *SignalRTestResources*. En el grupo de recursos de la lista de resultados, haga clic en **...** y, a continuación, en **Eliminar grupo de recursos**.

   
![Eliminar](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Se le pedirá que confirme la eliminación del grupo de recursos. Escriba el nombre del grupo de recursos para confirmar y haga clic en **Eliminar**.
   
Transcurridos unos instantes, el grupo de recursos y todos los recursos que contiene se eliminan.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió sobre la integración con Azure Functions para insertar actualizaciones en clientes basados en desencadenadores de Azure Functions. Para más información sobre el uso de Azure SignalR Server, continúe con los ejemplos de la CLI de Azure para SignalR Service.

> [!div class="nextstepaction"]
> [Ejemplos de la CLI de Azure SignalR](./signalr-cli-samples.md)



