---
title: Introducción al SDK de WebJobs - Azure
description: Introducción al SDK de WebJobs para el procesamiento en segundo plano basado en eventos. Aprenda a acceder a los datos de servicios de Azure y de terceros.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: b1de898ec4f661c54b5227367ad416a5edec80ed
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-the-webjobs-sdk"></a>Introducción al SDK de WebJobs

En este artículo se muestra cómo crear un proyecto de SDK de WebJobs, ejecutarlo de forma local e implementarlo en Azure.

Las instrucciones sirven para [Visual Studio 2017](https://www.visualstudio.com/vs/) pero se pueden realizar las mismas tareas con otras herramientas, como [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-webjobs-sdk"></a>Qué es el SDK de WebJobs

El SDK de Azure WebJobs es una plataforma que simplifica la tarea de escribir código de procesamiento en segundo plano que accede a los datos de servicios de Azure. El SDK presenta una sintaxis declarativa para especificar los eventos que deben desencadenar una función, como un mensaje nuevo que se agrega a una cola. Una sintaxis declarativa similar controla la lectura y la escritura de datos una vez que se haya desencadenado una función. Este sistema de desencadenadores y enlaces se encarga de la mayoría de las tareas de codificación de bajo nivel asociadas al acceso a servicios de Azure y de terceros.

### <a name="functions-triggers-and-bindings"></a>Funciones, enlaces y desencadenadores

Un proyecto de SDK de WebJobs define una o varias *funciones*. Una función es un método que tiene un atributo de desencadenador en su signatura del método. Los desencadenadores especifican condiciones para llamar a una función, y los enlaces especifican lo que se debe leer y escribir. Por ejemplo, el atributo de desencadenador en la siguiente función indica el tiempo de ejecución para llamar a la función cada vez que aparece un mensaje de cola en la cola `items`. El atributo `Blob` indica al tiempo de ejecución que utilice la cola de mensajes para leer un blob en el contenedor *workitems*. El contenido del mensaje de cola &mdash;proporcionado en el parámetro `queueTrigger`&mdash; es el nombre del blob.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Versiones 2.x y 3.x

Las instrucciones indican cómo crear un proyecto de la versión 2.x del SDK de WebJobs, con notas acerca de lo que difiere en la versión 3.x (en versión preliminar). El cambio principal que presenta la versión 3.x es el uso de .NET Core en lugar de .NET Framework.

### <a name="azure-functions"></a>Azure Functions

[Azure Functions](../azure-functions/functions-overview.md) se basa en el SDK de WebJobs y es una opción cuando no es necesario usar el SDK de WebJobs directamente. Azure Functions 1.x usa el SDK de WebJobs 2.x. Para más información, consulte la [comparación entre Azure Functions y el SDK de WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>requisitos previos

En este artículo se da por supuesto que dispone de una [cuenta de Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) y tiene experiencia con [aplicaciones de Azure App Service](app-service-web-overview.md). Para completar los pasos de este artículo:

* [Instale Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) con la carga de trabajo de **desarrollo de Azure**. Si ya tiene Visual Studio pero no tiene esa carga de trabajo, agréguela mediante la selección de **Herramientas > Obtener herramientas y características**.
* [Cree una aplicación de App Service](app-service-web-get-started-dotnet-framework.md). Si ya tiene una en la que pueda implementar un WebJob, puede utilizarla en lugar de crear una nueva.

## <a name="create-a-project"></a>Crear un proyecto

1. En Visual Studio, seleccione **Archivo -> Nuevo proyecto**.

2. Seleccione **Escritorio clásico de Windows > Aplicación de consola (.NET Framework)**.

   Para crear un proyecto de 3.x, seleccione **.NET Core > Console App (.NET Core)** [Aplicación de consola (.NET Core)].

3. Llame al proyecto *WebJobsSDKSample* y seleccione **Aceptar**.

   ![Cuadro de diálogo Nuevo proyecto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Incorporación del paquete NuGet de WebJobs

1. Instale la última versión 2.x estable del paquete NuGet `Microsoft.Azure.WebJobs`. (Para el SDK de WebJobs 3.x, deberá elegir la última versión 3.x).
 
   En **Consola del Administrador de paquetes**, escriba el comando para la versión 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>Creación de JobHost

El objeto `JobHost` es el contenedor en tiempo de ejecución de las funciones: escucha a los desencadenadores y llama a las funciones. 

1. En *Program.cs*, agregue una instrucción `using`:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Reemplace el método `Main` por el código siguiente:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Habilitación del registro de consola

Existen varias opciones para el registro en el proyecto de SDK de WebJobs. El que se recomienda es la [plataforma de registro desarrollada para ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging). Esta plataforma ofrece un mejor rendimiento y una mayor flexibilidad en medios de almacenamiento y en filtrado. 

En esta sección, configurará el registro de consola que utiliza la nueva plataforma.

1. Instale la última versión estable de los siguientes paquetes NuGet:

   * `Microsoft.Extensions.Logging` - La plataforma de registro.
   * `Microsoft.Extensions.Logging.Console` -El *proveedor* de consola. Un proveedor envía registros a un destino determinado, en este caso, a la consola. 
 
   Estos son los comandos de la **Consola del Administrador de paquetes** para la versión 2.0.1:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

2. En *Program.cs*, agregue una instrucción `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

3. En el `Main` método, agregue código para actualizar `JobHostConfiguration` antes de crear `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Este código realiza los cambios siguientes:

   * Deshabilita el [registro del panel](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). El panel es una herramienta de supervisión heredada y no se recomienda el registro del panel para escenarios de producción de alto rendimiento.
   * Agrega el proveedor de consola con el [filtrado](webjobs-sdk-how-to.md#log-filtering) predeterminado. 

   El método `Main` tiene el aspecto siguiente:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Creación de una función

1. Cree *Functions.cs* en la carpeta del proyecto y reemplace el código de plantilla por este código:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   El atributo `QueueTrigger` indica el tiempo de ejecución para llamar a esta función cuando se escribe un mensaje nuevo en una cola de Azure Storage denominada `queue`. El contenido del mensaje de cola se proporciona para el código del método en el parámetro `message`. El cuerpo del método es donde se procesan los datos de desencadenador. En este ejemplo, el código solo registra el mensaje.

   El parámetro `message` no tiene que ser una cadena. También puede enlazar a un objeto JSON, una matriz de bytes o un objeto [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage). Consulte [Uso del desencadenador de cola](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Cada tipo de enlace (como colas, blobs o tablas) tiene un conjunto diferente de tipos de parámetros con los que puede enlazar.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento

El emulador de Azure Storage que se ejecuta localmente no tiene todas las características que necesita el SDK de WebJobs. Por lo tanto, en esta sección creará una cuenta de almacenamiento en Azure y configurará el proyecto para utilizarla.

1. Abra el **Explorador de servidores** e inicie sesión en Azure. Haga clic con el botón derecho en el nodo **Azure** y, después, seleccione **Conectar a la suscripción de Microsoft Azure**.

   ![Inicio de sesión en Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. En el nodo **Azure**, en el **Explorador de servidores**, haga clic con el botón derecho en **Almacenamiento** y seleccione luego **Crear cuenta de almacenamiento**.

   ![Menú Crear cuenta de almacenamiento](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

2. En el cuadro de diálogo **Crear cuenta de almacenamiento**, escriba un nombre único para la cuenta de almacenamiento.

3. Elija la misma **Región** en la que ha creado la aplicación de App Service o una región cercana.

1. Seleccione **Crear**.

   ![Crear cuenta de almacenamiento](./media/webjobs-sdk-get-started/create-storage-account.png)

1. En el nodo **Storage**, en el **Explorador de servidores**, seleccione la nueva cuenta de almacenamiento. En la ventana **Propiedades**, seleccione los puntos suspensivos (**...**) a la derecha del campo de valor **Cadena de conexión**.

   ![Puntos suspensivos de Cadena de conexión](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

2. Copie la cadena de conexión y guarde este valor en algún lugar del que pueda copiarlo fácilmente de nuevo.

   ![Copia de la cadena de conexión](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>Configuración del almacenamiento que se ejecuta localmente

El SDK de WebJobs busca la cadena de conexión de almacenamiento en la colección Configuración de la aplicación. En una ejecución local, busca este valor en el archivo *App.config* o las variables de entorno.

1. Agregue el siguiente código XML al archivo *App.config* inmediatamente después de la etiqueta `<configuration>` de apertura.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

2. Reemplace *{storage connection string}* (cadena de conexión de almacenamiento) por la cadena de conexión copiada anteriormente.

   Más adelante usará la cadena de conexión de nuevo, al configurar la aplicación de App Service en Azure.

## <a name="test-locally"></a>Prueba local

En esta sección, va a crear y ejecutar el proyecto localmente, y desencadenar la función mediante la creación de un mensaje de cola.

1. Presione Ctrl+F5 para ejecutar el proyecto.

   La consola muestra que el tiempo de ejecución encuentra la función y está esperando mensajes de cola para desencadenarla.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   Puede ver un mensaje de advertencia acerca de un valor `ServicePointManager`. Para las pruebas que va a realizar con este proyecto, puede omitir la advertencia. Para más información acerca de la advertencia, consulte [Cómo usar el SDK de WebJobs](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

2. Cierre la ventana de la consola.

1. En el **Explorador de servidores**, expanda el nodo de la nueva cuenta de almacenamiento y, después, haga clic con el botón derecho en **Colas**. 

2. Seleccione **Crear cola**. 

3. Escriba *queue* (cola) como nombre de la cola y seleccione luego **Aceptar**.

   ![Crear cola](./media/webjobs-sdk-get-started/create-queue.png)

4. Haga clic con el botón derecho en el nodo de la nueva cola y seleccione **Ver cola**.

5. Seleccione el icono **Agregar mensaje**.

   ![Crear cola](./media/webjobs-sdk-get-started/create-queue-message.png)

6. En el cuadro de diálogo **Agregar mensaje**, escriba *¡Hola mundo!* como **Texto del mensaje**y, a continuación, seleccione **Aceptar**.

   ![Crear cola](./media/webjobs-sdk-get-started/hello-world-text.png)

7. Vuelva a ejecutar el proyecto.

   Debido a que ha utilizado el atributo `QueueTrigger` en la función `ProcessQueueMessage`, el tiempo de ejecución del SDK de WeJobs escucha los mensajes de cola cuando se inicia. Encuentra un nuevo mensaje de cola en la cola denominada *queue* (cola) y llama a la función.

   Debido al [retroceso exponencial del sondeo de la cola](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), el tiempo de ejecución podría tardar hasta 2 minutos en encontrar el mensaje e invocar la función. Este tiempo de espera se puede reducir mediante la ejecución en [modo de desarrollo](webjobs-sdk-how-to.md#jobhost-development-settings).

  La salida de la consola tendrá un aspecto similar al siguiente:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

8. Cierre la ventana de la consola.

## <a name="add-application-insights-logging"></a>Incorporación del registro de Application Insights

Cuando el proyecto se ejecuta en Azure, no puede supervisar la ejecución de la función mediante la visualización de la salida de la consola. La solución de supervisión recomendada es [Application Insights](../application-insights/app-insights-overview.md). Para más información, consulte [Supervisión de Azure Functions](../azure-functions/functions-monitoring.md).

En esta sección, realiza las tareas siguientes para configurar el registro de Application Insights antes de implementarlo en Azure:

* Asegúrese de tener una aplicación de App Service y una instancia de Application Insights para trabajar con ellas.
* Configure la aplicación de App Service para usar la instancia de Application Insights y la cuenta de almacenamiento creadas anteriormente.
* Configure el proyecto para el registro de Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Creación de la aplicación de App Service y la instancia de Application Insights

1. Si aún no tiene una aplicación de App Service que pueda usar, [cree una](app-service-web-get-started-dotnet-framework.md).

2. Si aún no tiene un recurso de Application Insights que pueda utilizar, [cree uno](../application-insights/app-insights-create-new-resource.md). Establezca **Tipo de aplicación** en **General** y omita las secciones que siguen a **Copy the instrumentation key** (Copiar la clave de instrumentación).

3. Si ya tiene un recurso de Application Insights que desee usar, [copie la clave de instrumentación](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Configuración de aplicaciones 

1. En el **Explorador de servidores**, expanda el nodo **App Service** bajo **Azure**.

1. Expanda el grupo de recursos que se encuentra en la aplicación de App Service y, a continuación, haga clic con el botón derecho en la aplicación de App Service.

3. Seleccione **Ver configuración**.

4. En el cuadro de diálogo **Cadenas de conexión**, agregue la entrada siguiente.

   |NOMBRE  |Cadena de conexión  |Tipo de base de datos|
   |---------|---------|------|
   |AzureWebJobsStorage | {cadena de conexión de almacenamiento copiada anteriormente}|Personalizado|
   
6. Si el cuadro de diálogo **Configuración de la aplicación** no tiene una clave de instrumentación de Application Insights, agregue la que ha copiado anteriormente. (Es posible que la clave de instrumentación ya esté allí, en función de cómo haya creado la aplicación de App Service).

   |NOMBRE  |Valor  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {clave de instrumentación} |

2. Reemplace *{clave de instrumentación}* por la clave de instrumentación del recurso de Application Insights que esté utilizando.

2. Seleccione **Guardar**.

1. Agregue el siguiente código XML al archivo *App.config* inmediatamente después de la colección de cadenas de conexión.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

2. Reemplace *{clave de instrumentación}* por la clave de instrumentación del recurso de Application Insights que esté utilizando.

   Al agregar estos datos al archivo *App.config*, puede probar la conexión de Application Insights cuando ejecuta el proyecto localmente. 

3. Guarde los cambios.

### <a name="add-application-insights-logging-provider"></a>Incorporación del proveedor de registro de Application Insights

1. Instale la versión 2.x estable más reciente del paquete NuGet para el proveedor de registro de Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`. (Para el SDK de WebJobs 3.x, elija la última versión 3.x. del paquete)

   En **Consola del Administrador de paquetes**, escriba el comando para la versión 2.2.0:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Instale la versión 4.x estable más reciente del paquete NuGet para el administrador de configuración de .NET: `System.Configuration.ConfigurationManager`.

   En **Consola del Administrador de paquetes**, escriba el comando para la versión 4.4.1:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

2. Abra *Program.cs* y agregue una instrucción `using` para el administrador de configuración:

   ```csharp
   using System.Configuration;
   ```

2. Reemplace el código del método `Main` por el código siguiente:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Este código realiza los cambios siguientes:

   * Agrega un proveedor de registro de Application Insights con un [filtrado](webjobs-sdk-how-to.md#log-filtering) predeterminado; toda la información y los registros de nivel superior se enviarán ahora a la consola y a Application Insights cuando la ejecución se realiza localmente. 
   * Coloca el objeto `LoggerFactory` en un bloque `using` para asegurarse de que la salida del registro se vacíe cuando se cierre el host. 

## <a name="test-application-insights-logging"></a>Prueba del registro de Application Insights

En esta sección vuelve a realizar una ejecución local para verificar que los datos de registro van ahora a Application Insights, así como a la consola.

1. Use el **Explorador de servidores** para crear un mensaje de cola, de la misma manera que lo hizo [anteriormente](#trigger-the-function), excepto en que va a escribir *¡Hola App Insights!* como texto del mensaje.

1. Ejecute el proyecto.

   El SDK de WebJobs procesa el mensaje de cola y ve los registros en la ventana de la consola.

1. Cierre la ventana de la consola.

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al recurso de Application Insights.

2. Seleccione **Buscar**.

   ![Selección de Buscar](./media/webjobs-sdk-get-started/select-search.png)

1. Si no ve el mensaje *¡Hola App Insights!*, seleccione **Actualizar** periódicamente durante varios minutos. (Los registros no aparecen inmediatamente porque el cliente de Application Insights tarda un tiempo en vaciar los registros que procesa).

   ![Registros de Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

3. Cierre la ventana de la consola.

## <a name="deploy-as-a-webjob"></a>Implementación como WebJob

En esta sección se implementa el proyecto como WebJob. Lo implementa en una aplicación de App Service que ha [creado anteriormente](#create-app-service-app-and-application-insights-instance). Para probar el código mientras se ejecuta en Azure, desencadenará una invocación de función mediante la creación de un mensaje de cola.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Publish as Azure WebJob** (Publicar como WebJob de Azure).

1. En el cuadro de diálogo **Agregar Azure WebJob**, seleccione **Aceptar**.

   ![Agregar Azure WebJob](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio instala automáticamente un paquete NuGet para la publicación de WebJob.

1. En el paso **Perfil** del asistente para **Publicar**, haga clic en **Microsoft Azure App Service**.

   ![Cuadro de diálogo Publicar](./media/webjobs-sdk-get-started/publish-dialog.png)

1. En el cuadro de diálogo **App Service**, seleccione **su grupo de recursos > su aplicación de App Service** y, a continuación, seleccione **Aceptar**.

   ![Cuadro de diálogo App Service](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. En el paso **Conexión** del asistente, seleccione **Publicar**.

## <a name="trigger-the-function-in-azure"></a>Desencadenamiento de la función en Azure

1. Asegúrese de que no está en una ejecución local (cierre la ventana la de consola si aún está abierta). En caso contrario, la instancia local podría ser la primera que va a procesar los mensajes de cola que cree.

1. Use el **Explorador de servidores** para crear un mensaje de cola, de la misma manera que lo hizo [anteriormente](#trigger-the-function), excepto en que va a escribir *¡Hola Azure!*

7. Actualice la página **Cola** en Visual Studio y el nuevo mensaje desaparece porque la función que se ejecuta en Azure App Service lo ha procesado.

   > [!TIP]
   > Cuando está realizando pruebas en Azure, utilice el [modo de desarrollo](webjobs-sdk-how-to.md#jobhost-development-settings) para garantizar que se invoca a una función de desencadenador de cola de inmediato y evitar retrasos debido al [retroceso exponencial del sondeo de la cola](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Visualización de registros en Application Insights

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al recurso de Application Insights.

2. Seleccione **Buscar**.

1. Si no ve el mensaje *¡Hola Azure!* seleccione **Actualizar** periódicamente durante varios minutos.

   Ve los registros de la función que se ejecuta en un WebJob, incluido el texto *¡Hola Azure!* que ha escrito en la sección anterior.

## <a name="add-an-input-binding"></a>Incorporación de un enlace de entrada

Los enlaces de entrada simplifican el código que lee los datos. En este ejemplo, el mensaje de cola será un nombre de blob, y usará el nombre del blob para buscar y leer un blob en Azure Storage.

1. En *Functions.cs*, reemplace el método `ProcessQueueMessage` por el código siguiente:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   En este código, `queueTrigger` es una [expresión de enlace](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), lo que significa que se resuelve con un valor diferente en tiempo de ejecución.  En tiempo de ejecución tiene el contenido del mensaje de cola.

2. Agregue `using`:

   ```cs
   using System.IO;
   ```

3. Cree un contenedor de blobs en la cuenta de almacenamiento.

   a. En el **Explorador de servidores**, expanda el nodo de la cuenta de almacenamiento, haga clic con el botón derecho en **Blobs** y, a continuación, seleccione **Crear contenedor de blobs**.

   b. En el cuadro de diálogo **Crear contenedor de blobs**, especifique *contenedor* como nombre para el contenedor y, después, haga clic en **Aceptar**.

4. Cargue el archivo *Program.cs* en el contenedor de blobs. (Este archivo se usa aquí como ejemplo; podría cargar cualquier archivo de texto y crear un mensaje de cola con el nombre del archivo).

   a. En el **Explorador de servidores**, haga doble clic en el nodo para el contenedor que acaba de crear.

   b. En la ventana **Contenedor**, seleccione el botón **Cargar**.

   ![Botón de carga de blobs](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Busque y seleccione *Program.cs* y seleccione luego **Aceptar**.

5. Cree un mensaje de cola en la cola que ha creado anteriormente, con *Program.cs* como texto del mensaje.

   ![Mensaje de cola Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

6. Ejecute el proyecto.

   El mensaje de cola desencadena la función, que lee el blob y registra su longitud. La salida de la consola tendrá un aspecto similar al siguiente:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Adición de un enlace de salida

Los enlaces de salida simplifican el código que escribe los datos. Este ejemplo modifica el ejemplo anterior al escribir una copia del blob en lugar de registrar su tamaño.

1. Reemplace el método `ProcessQueueMessage` por el código siguiente:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

5. Cree otro mensaje de cola con *Program.cs* como texto del mensaje.

6. Ejecute el proyecto.

   El mensaje de cola desencadena la función, que lee el blob, registra su longitud y crea un nuevo blob. La salida de la consola es la misma pero cuando vaya a la ventana del contenedor de blobs y seleccione **Actualizar**, verá un nuevo blob denominado *copy-Program.cs*.

## <a name="next-steps"></a>Pasos siguientes

En esta guía se muestra cómo crear, ejecutar e implementar un proyecto de SDK de WebJobs.

Para mostrar todo lo que entra en un proyecto de SDK de WebJobs, con las instrucciones ha creado un proyecto desde cero. Sin embargo, cuando crea el proyecto siguiente, considere la posibilidad de usar la plantilla **Azure WebJob** en la categoría **Cloud**. Esta plantilla crea un proyecto con paquetes NuGet y un código de ejemplo ya configurado. Tenga en cuenta que quizá deba cambiar el código de ejemplo para utilizar la nueva plataforma de registro.

Para más información, consulte [Cómo usar el SDK de WebJobs](webjobs-sdk-how-to.md).
