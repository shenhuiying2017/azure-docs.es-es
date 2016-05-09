<properties
	pageTitle="Referencia para desarrolladores de Funciones de Azure | Microsoft Azure"
	description="Cómo desarrollar funciones de Azure con C#."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="dotnet"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/14/2016"
	ms.author="chrande"/>

# Referencia para desarrolladores de C# de Funciones de Azure

La experiencia en C# para las Funciones de Azure se basa en el SDK de WebJobs de Azure. Los datos fluyen en la función de C# a través de los argumentos de método. Los nombres de los argumentos se especifican en `function.json` y hay nombres predefinidos para acceder a cosas como el registrador de funciones y los tokens de cancelación.

En este artículo se supone que ya ha leído la [referencia para desarrolladores de Funciones de Azure](functions-reference.md).

## Funcionamiento de .csx

El formato `.csx` permite escribir menos "texto reutilizable" y centrarse en escribir solo una función de C#. En Funciones de Azure, incluya solo las referencias a ensamblados y espacios de nombres que necesite, como de costumbre, y en lugar de ajustar todo en un espacio de nombres y una clase, simplemente defina el método `Run`. Si necesita incluir todas las clases, por ejemplo para definir objetos POCO, puede incluir una clase dentro del mismo archivo.

## Enlace a argumentos

Los distintos enlaces se enlazan a una función de C# mediante la propiedad `name` de la configuración de *function.json*. Cada enlace tiene sus propios tipos admitidos que se documentan por enlace. Por ejemplo, un desencadenador de blob puede admitir una cadena, un objeto POCO o algunos otros tipos. Puede utilizar el tipo que mejor se adapte a sus necesidades.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

## Registro

Para registrar la salida en sus registros de streaming en C#, puede incluir un argumento con tipo `TraceWriter`. Es recomendable que lo denomine `log`. Le recomendamos que evite `Console.Write` en Funciones de Azure.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
}
```

## Async

Para hacer que una función sea asincrónica, utilice la palabra clave `async` y devuelva un objeto `Task`.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName, 
        Stream blobInput,
        Stream blobOutput)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## Token de cancelación

En algunos casos, puede que tenga operaciones que son sensibles al cierre. Aunque siempre es mejor escribir código para controlar los bloqueos, en los casos donde desee controlar las solicitudes de cierre estable, defina un argumento con tipo [`CancellationToken`](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx). Se proporcionará un `CancellationToken` si se desencadena un cierre del host.

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
        string blobName, 
        Stream blobInput,
        Stream blobOutput,
        CancellationToken token)
    {
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
```

## Importación de espacios de nombres

Si necesita importar espacios de nombres, puede hacerlo de la manera habitual, con la cláusula `using`.

```csharp
using System.Net;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

Los siguientes espacios de nombres se importan automáticamente y, por tanto, son opcionales:

* `System`
* `System.Collections.Generic`
* `System.Linq`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## Referencia a ensamblados externos

Para los ensamblados de marco, agregue referencias mediante la directiva `#r "AssemblyName"`.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
```

El entorno de hospedaje de las Funciones de Azure agrega automáticamente los siguientes ensamblados:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Además, los siguientes ensamblados hacen un uso especial de las mayúsculas y minúsculas y simplename puede hacer referencia a ellos (por ejemplo, `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Si necesita hacer referencia a un ensamblado privado, puede cargar el archivo de ensamblado en una carpeta `bin` relacionada con la función y hacer referencia a él mediante el nombre de archivo (por ejemplo, `#r "MyAssembly.dll"`).

## Administración de paquetes

Para utilizar paquetes NuGet en una función de C#, cargue un archivo *project.json* en la carpeta de la función del sistema de archivos de la aplicación de la función. Este es un ejemplo de archivo *project.json* que agrega una referencia a la versión 1.1.0 de Microsoft.ProjectOxford.Face:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

Al cargar un archivo *project.json*, el tiempo de ejecución obtiene los paquetes y agrega automáticamente las referencias a los ensamblados del mismo. No es necesario agregar directivas `#r "AssemblyName"`. Simplemente agregue las instrucciones `using` necesarias para que el archivo *run.csx* use los tipos definidos en los paquetes NuGet.

### Cómo cargar un archivo project.json

En primer lugar, asegúrese de que la aplicación de la función se está ejecutando, lo que puede hacer abriéndola en el Portal de Azure. Esto también proporciona acceso a los registros de streaming donde se mostrará la salida de la instalación del paquete.

Las aplicaciones de la función se integran en el Servicio de aplicaciones, por lo que todas las [opciones de implementación disponibles para las aplicaciones web estándar](../app-service-web/web-sites-deploy.md) están también disponibles para las aplicaciones de la función. Estos son algunos métodos que puede utilizar.

#### Para cargar project.json mediante Visual Studio Online (Mónaco)

1. En el portal de Funciones de Azure, haga clic en **Configuración del contenedor de funciones**.

2. En la sección **Configuración avanzada**, haga clic en **Ir a la configuración del Servicio de aplicaciones**.

3. Haga clic en **Herramientas**.

4. En **Desarrollar**, haga clic en **Visual Studio Online**.

5. **Actívelo** si no está ya habilitado y haga clic en **Ir**.

6. Una vez cargado Visual Studio Online, arrastre y coloque el archivo *project.json* en la carpeta de la función (es decir, la carpeta con el nombre de la función).

#### Carga del archivo project.json con el punto de conexión SCM (Kudu) de la aplicación de la función

1. Vaya a: `https://<function_app_name>.scm.azurewebsites.net`.

2. Haga clic en **Consola de depuración > CMD**.

3. Vaya a *D:\\home\\site\\wwwroot<nombre\_de\_función>*.

4. Arrastre y coloque el archivo *project.json* en la carpeta (en la cuadrícula de archivos).

#### Carga del archivo project.json mediante FTP

1. Siga las instrucciones descritas [aquí](../app-service-web/web-sites-deploy.md#ftp) para configurar el FTP.

2. Cuando esté conectado al sitio del contenedor de funciones, copie el archivo *project.json* en */site/wwwroot/<function_name>*.

#### Registro de instalación del paquete 

Una vez cargado el archivo *project.json*, verá un resultado similar al del ejemplo siguiente en el registro de streaming de la función:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261 
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.189 
2016-04-04T19:02:57.455 Packages restored.
```

## Reutilización del código .csx

Puede utilizar las clases y métodos definidos en otros archivos *.csx* con el archivo *run.csx*. Para ello, utilice directivas `#load` en el archivo *run.csx*, como se muestra en el ejemplo siguiente.

Archivo *run.csx* de ejemplo:

```csharp
#load "mylogger.csx"

public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"Log by run.csx: {DateTime.Now}"); 
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Archivo *mylogger.csx* de ejemplo:

```csharp
public static void MyLogger(TraceWriter log, string logtext)
{
    log.Verbose(logtext); 
}
```

Puede utilizar una ruta de acceso relativa con la directiva `#load`:

* `#load "mylogger.csx"` carga un archivo que se encuentra en la carpeta de la función.

* `#load "loadedfiles\mylogger.csx"` carga un archivo ubicado en una carpeta que a su vez está en la carpeta de la función.

* `#load "..\shared\mylogger.csx"` carga un archivo ubicado en una carpeta que está en el mismo nivel que la carpeta de la función, es decir, directamente en *wwwroot*.
 
La directiva `#load` solo funciona con archivos (script de C#) *.csx*, no con archivos *.cs*.

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->