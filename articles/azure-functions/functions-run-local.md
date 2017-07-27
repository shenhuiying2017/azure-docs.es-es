---
title: "Desarrollo y ejecución de funciones de Azure de forma local | Microsoft Docs"
description: "Aprenda a codificar y probar funciones de Azure en la máquina local antes de ejecutarlas en Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: erikre
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/08/2016
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 478c2ffbb0bfe5b1595bc6ea1bebb2144aebc728
ms.contentlocale: es-es
ms.lasthandoff: 06/09/2017


---
# <a name="code-and-test-azure-functions-locally"></a>Codificación y comprobación de las funciones de Azure en un entorno local

Puede usar su editor de código favorito y herramientas de desarrollo local para ejecutar el tiempo de ejecución de Azure Functions localmente. Se desencadenan eventos de Azure y depuran funciones de JavaScript y C#.

Para empezar, instale [Azure Functions Core Tools] de npm. Azure Functions Core Tools es una versión local del tiempo de ejecución de Azure Functions que puede ejecutar en el equipo Windows local. No se trata de un emulador o simulador. Se trata del mismo tiempo de ejecución que se ejecuta en Azure.

[Azure Functions Core Tools] agrega los siguientes alias de comando:
- `func`
- `azfun`
- `azurefunctions`

Azure Functions Core Tools es [código abierto que se hospeda en GitHub](https://github.com/azure/azure-functions-cli). Para notificar un error o realizar una solicitud de característica, [abra un problema de GitHub](https://github.com/azure/azure-functions-cli/issues).

## <a name="create-a-local-functions-project"></a>Creación de un proyecto local de Functions

Cuando se ejecuta localmente, un proyecto de Functions es un directorio que tiene los archivos host.json y local.settings.json. Este directorio es el equivalente a una aplicación de función en Azure. Para obtener más información sobre la estructura de carpetas de Azure Functions, vea la [Guía para desarrolladores de Azure Functions](functions-reference.md#folder-structure).

En el símbolo del sistema, ejecute el siguiente comando:

```
func init MyFunctionProj
```

La salida tendrá un aspecto similar al siguiente:

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

Para no participar en la creación de un repositorio Git, use la opción `--no-source-control [-n]`.

<a name="local-settings"></a>

## <a name="local-settings-file"></a>Archivo de configuración local

El archivo local.settings.json almacena la configuración de la aplicación, las cadenas de conexión y la configuración de Azure Functions Core Tools. Tiene la siguiente estructura:

```json
{
  "IsEncrypted": false,   // If set to true, all values are encrypted by using a local machine key. Use with "func settings" commands.
  "Values": {
    "AzureWebJobsStorage": "<connection string>",   // This is required for all triggers except HTTP.
    "AzureWebJobsDashboard": "<connection string>", // Optional, controls whether to log to the Monitor tab in the portal.
  },
  "Host": {
    "LocalHttpPort": 7071, // If specified, this is the default port for "host start" and "run". Can be overridden by using the --port command-line option.
    "CORS": "*"            // Origins to allow in the CORS setting.
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```

Especifique la configuración de la aplicación en la colección **Valores**. Estas opciones de configuración pueden leerse después como variables de entorno. En C#, use `System.Environment.GetEnvironmentVariable` o `ConfigurationManager.AppSettings`. En JavaScript, use `process.env`. Si se especifica la misma configuración como variable de entorno, tiene prioridad sobre los valores de local.settings.json.

La configuración de la aplicación **AzureWebJobsStorage** es una configuración especial que se requiere en el tiempo de ejecución de Azure Functions para todos los desencadenadores que no sean HTTP. Internamente, el tiempo de ejecución crea colas para administrar desencadenadores en esta cuenta de almacenamiento. Si no se especifica un valor de **AzureWebJobsStorage** y se usan desencadenadores que no sean HTTP, verá el siguiente mensaje:

*Missing value for AzureWebJobsStorage in local.settings.json. This is required for all triggers other than HTTP. You can run 'func azure functionapp fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json* (Falta un valor para AzureWebJobsStorage en local.settings.json. Este valor es necesario para todos los desencadenadores que no sean HTTP. Puede ejecutar "func azure functionapp fetch-app-settings " o especificar una cadena de conexión en local.settings.json).

> [!NOTE]
> Es posible usar el emulador de almacenamiento local, a través de la cadena de conexión "AzureWebJobsStorage": "UseDevelopmentStorage=true". Pero, puede que haya diferencias de comportamiento en comparación con el servicio Azure Storage.

La siguiente configuración personaliza el host local de Functions:
- `LocalHttpPort`. Puerto predeterminado que se usa para `func host start` `func run`. La opción de línea de comandos `--port` tiene prioridad sobre este valor.
- `CORS`. Orígenes permitidos de CORS, como una lista separada por comas sin espacios en blanco. Use "*" para permitirlos todos.

Puede especificar cadenas de conexión en el objeto `ConnectionStrings`. Se agregan al entorno con el nombre del proveedor **System.Data.SqlClient**.

La mayoría de los desencadenadores y enlaces tienen una propiedad **Connection** que es el nombre de una variable de entorno o la configuración de la aplicación en local.settings.json. Si falta el valor de configuración de la aplicación, ve el siguiente mensaje:

*Warning: Cannot find value named 'MyStorageConnection' in local.settings.json that matches 'connection' property set on 'blobTrigger' in 'BlobTriggerCSharp\function.json'. You can run 'func azure functionapp fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json* (Falta un valor para AzureWebJobsStorage en local.settings.json. Este valor es necesario para todos los desencadenadores que no sean HTTP. Puede ejecutar "func azure functionapp fetch-app-settings " o especificar una cadena de conexión en local.settings.json).

El archivo local.settings.json solo se usa en Azure Functions Core Tools. Para establecer la configuración de la aplicación y las cadenas de conexión en Azure, use la hoja **Application Settings**.

### <a name="configure-app-settings"></a>Configuración de aplicaciones

Para establecer un valor para las cadenas de conexión, puede realizar alguna de las siguientes acciones:
- Escriba manualmente una cadena de conexión desde el [Explorador de Azure Storage](http://storageexplorer.com/).
- Use **func azure functionapp fetch-app-settings \<FunctionAppName\>**. Requiere **inicio de sesión de Azure**.
- Use **func azure functionapp storage fetch-connection-string \<StorageAccountName\>**. Requiere **inicio de sesión de Azure**.

## <a name="create-a-function"></a>Creación de una función

Para crear una función, ejecute `func new`. Este comando tiene los siguientes argumentos opcionales:

- `--language [-l]`. Lenguaje de programación de la plantilla, como C#, F# o JavaScript.
- `--template [-t]`. Nombre de la plantilla.
- `--name [-n]`. Nombre de la función.

Por ejemplo, para crear un desencadenador HTTP de JavaScript, ejecute:

```
func new --language JavaScript --template HttpTrigger --name MyHttpTrigger
```

Para crear una función desencadenada por la cola, ejecute:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```

## <a name="run-functions-locally"></a>Ejecución local de funciones

Para ejecutar un proyecto de Functions, ejecute el host de Functions. El host habilita desencadenadores para todas las funciones del proyecto:

```
func host start
```

Puede usar las siguientes opciones con `func host start`:

- `--port [-p]`. Puerto local en el que se escucha. Valor predeterminado: 7071.
- `--debug <type>`. Las opciones son VSCode y VS.
- `--cors`. Lista separada por comas de orígenes CORS, sin espacios en blanco.
- `--nodeDebugPort [-n]`. Puerto del depurador de nodo que se va a usar. Valor predeterminado: un valor de launch.json o 5858.
- `--debugLevel [-d]`. Nivel de seguimiento de la consola (desactivado, detallado, información, advertencia o error). Valor predeterminado: información.
- `--timeout [-t]`. Tiempo de espera en segundos para que se inicie el host de Functions. Valor predeterminado: 20 segundos.
- `--useHttps`. Se enlaza a https://localhost:{port} en lugar de a http://localhost:{port}. De forma predeterminada, esta opción crea un certificado de confianza en el equipo.
- `--pause-on-error`. Se pone en pausa en espera de entrada adicional antes de salir del proceso. Resulta útil cuando se inicia Azure Functions Core Tools desde un entorno de desarrollo integrado (IDE).

Cuando se inicia el host de Functions, devuelve la dirección URL de las funciones desencadenadas por HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug"></a>Depurar

Para asociar un depurador, pase el argumento `--debug`. Para depurar funciones de JavaScript, use Visual Studio Code. Para funciones de C#, use Visual Studio.

Para depurar funciones de C#, use `--debug vs`. También puede usar [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Para iniciar el host y configurar la depuración de JavaScript, ejecute:

```
func host start --debug vscode
```

Luego, en la vista de **depuración** de Visual Studio Code, seleccione **Attach to Azure Functions** (Asociar a Azure Functions). Puede asociar puntos de interrupción, inspeccionar variables y recorrer el código.

![Depuración de JavaScript con Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="call-a-function-by-using-test-data"></a>Llamada a una función con datos de prueba

También puede invocar una función directamente con `func run <FunctionName>`. Este comando funciona de manera similar a la pestaña **Prueba** de Azure Portal, donde se pueden especificar datos de entrada para la función. Este comando inicia el host de Functions completo.

Puede usar las siguientes opciones con `func run`:

- `--content [-c]`. Contenido alineado.
- `--debug [-d]`. Se asocia un depurador al proceso de host antes de ejecutar la función.
- `--timeout [-t]`. Tiempo de espera (en segundos) hasta que el host local de Functions está listo.
- `--file [-f]`. Nombre del archivo que se usa como contenido.
- `--no-interactive`. No pide entrada. Resulta útil en escenarios de automatización.

Por ejemplo, para llamar a una función desencadenada por HTTP y pasar cuerpo del contenido, ejecute el siguiente comando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-a-function-app"></a>Publicación de una aplicación de función

Para publicar un proyecto de Functions en una aplicación de función en Azure, use el comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

Puede usar las siguientes opciones:

- `--publish-local-settings [-i]`.  Se publica la configuración de local.settings.json en Azure, se pide que se sobrescriba si la configuración ya existe.
- `--overwrite-settings [-y]`. Debe usarse con `-i`. Sobrescribe AppSettings en Azure con el valor local si es distinto. El valor predeterminado es Preguntar.

El comando `publish` carga el contenido del directorio del proyecto de Functions. Si se eliminan archivos localmente, este comando no los elimina de Azure. Para eliminar estos archivos, use Kudu en el portal de Azure Functions. Para iniciar Kudu, en el portal de Azure Functions, seleccione **Características de la plataforma** > **Herramientas avanzadas (Kudu)**. 


<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
