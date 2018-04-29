---
title: Desarrollo y ejecución de funciones de Azure de forma local | Microsoft Docs
description: Aprenda a codificar y probar funciones de Azure en la máquina local antes de ejecutarlas en Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 2273a39f1c9da57072ca027e34c4acd6d86ea61a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="code-and-test-azure-functions-locally"></a>Codificación y comprobación de las funciones de Azure Functions en un entorno local

A pesar de que [Azure Portal] proporciona un conjunto completo de herramientas para desarrollar y probar Azure Functions, muchos desarrolladores prefieren desarrollar productos localmente. Azure Functions facilita el uso de su editor de código favorito y de las herramientas de desarrollo locales para desarrollar y probar sus funciones en un equipo local. Las funciones pueden desencadenarse a partir de eventos de Azure, y puede depurar sus funciones en C# y JavaScript en el equipo local. 

Si es un desarrollador de C# de Visual Studio, Azure Functions también [permite la integración con Visual Studio 2017](functions-develop-vs.md).

>[!IMPORTANT]  
> No mezcle el desarrollo local con el desarrollo del portal en la misma aplicación de función. Cuando cree y publique funciones desde un proyecto local, no debe intentar mantener o modificar el código del proyecto en el portal.

## <a name="install-the-azure-functions-core-tools"></a>Instalación de Azure Functions Core Tools

[Azure Functions Core Tools] es una versión local del entorno en tiempo de ejecución de Azure Functions que puede ejecutar en el equipo de desarrollo local. No se trata de un emulador o simulador. Se trata del mismo entorno de ejecución que se usa en Functions en Azure. Hay dos versiones de Azure Functions Core Tools:

+ [Versión 1.x](#v1): admite la versión 1.x del entorno en tiempo de ejecución. Esta versión solo se admite en equipos con Windows y se instala desde un [paquete npm](https://docs.npmjs.com/getting-started/what-is-npm).
+ [Versión 2.x](#v2): admite la versión 2.x del entorno en tiempo de ejecución. Esta versión admite [Windows](#windows-npm), [macOS](#brew) y [Linux](#linux). Usa administradores de paquetes específicos de la plataforma o npm para la instalación. 

### <a name="v1"></a>Versión 1.x

La versión original de las herramientas usa el entorno en tiempo de ejecución versión 1.x de Functions. Esta versión usa .NET Framework (4.7.1) y solo se admite en equipos con Windows. Antes de instalar las herramientas de la versión 1.x, debe [instalar NodeJS](https://docs.npmjs.com/getting-started/installing-node), que incluye npm.

Use el siguiente comando para instalar las herramientas de la versión 1.x:

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Versión 2.x

>[!NOTE]
> El entorno en tiempo de ejecución 2.0 de Azure Functions está en versión preliminar y actualmente no se admiten todas las características de Azure Functions. Para más información, consulte las [versiones de Azure Functions](functions-versions.md). 

La versión 2.x de las herramientas usa el entorno en tiempo de ejecución 2.x de Azure Functions, que se basa en .NET Core. Esta versión se admite en todas las plataformas que admiten .NET Core 2.x, incluidas [Windows](#windows-npm), [macOS](#brew) y [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Los pasos siguientes utilizan npm para instalar Core Tools en Windows. También puede usar [Chocolatey](https://chocolatey.org/). Para más información, consulte el [archivo Léame de Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Instale [.NET Core 2.0 para Windows](https://www.microsoft.com/net/download/windows).

2. Instale [Node.js], que incluye npm. Para la versión 2.x de las herramientas, solo se admite Node.js 8.5 y versiones posteriores.

3. Instale el paquete de Core Tools:

  ```bash
  npm install -g azure-functions-core-tools@core
  ```

#### <a name="brew"></a>MacOS con Homebrew

Los pasos siguientes utilizan Homebrew para instalar Core Tools en macOS.

1. Instale [.NET Core 2.0 para macOS](https://www.microsoft.com/net/download/macos).

1. Instale [Homebrew](https://brew.sh/), si aún no está instalado.

2. Instale el paquete de Core Tools:

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) con APT

Los siguientes pasos usan [APT](https://wiki.debian.org/Apt) para instalar Core Tools en la distribución de Ubuntu/Debian Linux. Para otras distribuciones de Linux, consulte el [archivo Léame de Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Instale [.NET Core 2.0 para Linux](https://www.microsoft.com/net/download/linux).

1. Registre la clave de producto de Microsoft como de confianza:

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
  ```

2.  Configure la fuente del paquete, reemplazando `<version>` en el siguiente comando por el nombre de la versión adecuada de la tabla:

  ```bash
  sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-<version>-prod <version> main" > /etc/apt/sources.list.d/dotnetdev.list'
  sudo apt-get update
  ```

  | Distribución de Linux | `<version>` |
  | --------------- | ----------- |
  | Ubuntu 17.10    | `artful`    |
  | Ubuntu 17.04    | `zesty`     |
  | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

3. Instale el paquete de Core Tools:

  ```bash
  sudo apt-get install azure-functions-core-tools
  ```

## <a name="run-azure-functions-core-tools"></a>Ejecución de Azure Functions Core Tools
 
Azure Functions Core Tools agrega los siguientes alias de comando:
* **func**
* **azfun**
* **azurefunctions**

Se puede utilizar cualquiera de estos alias donde se muestra `func` en los ejemplos.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>Creación de un proyecto local de Functions

Cuando se ejecuta localmente, un proyecto de Functions es un directorio que tiene los archivos [host.json](functions-host-json.md) y [local.settings.json](#local-settings-file). Este directorio es el equivalente a una aplicación de función en Azure. Para obtener más información sobre la estructura de carpetas de Azure Functions, vea la [Guía para desarrolladores de Azure Functions](functions-reference.md#folder-structure).

En la ventana de terminal o desde un símbolo del sistema, ejecute el siguiente comando para crear el proyecto y el repositorio de Git local:

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

Para crear el proyecto sin un repositorio Git local, use la opción `--no-source-control [-n]`.

## <a name="register-extensions"></a>Registro de las extensiones

En la versión 2.x del entorno de ejecución de Azure Functions, debe registrar explícitamente las [extensiones de enlace](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md) que utilice en la aplicación de función. 

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Para más información, consulte [Conceptos básicos sobre los enlaces y desencadenadores de Azure Functions](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Archivo de configuración local

El archivo local.settings.json almacena la configuración de la aplicación, las cadenas de conexión y la configuración de Azure Functions Core Tools. Tiene la siguiente estructura:

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| Configuración      | DESCRIPCIÓN                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Cuando se establece en **true**, todos los valores se cifran con una clave de máquina local. Se usa con los comandos `func settings`. El valor predeterminado es **false**. |
| **Valores** | Colección de opciones de configuración de la aplicación que se usa en la ejecución local. **AzureWebJobsStorage** y **AzureWebJobsDashboard** son ejemplos; para obtener una lista completa, consulte la [referencia de las opciones de configuración de aplicaciones](functions-app-settings.md). Muchos desencadenadores y enlaces tienen una propiedad que hace referencia a una configuración de la aplicación como, por ejemplo, **Conexión** para el desencadenador de Blob Storage. Para estas propiedades, se necesita una configuración de la aplicación definida en la matriz **Valores**. Esto también se aplica a cualquier propiedad de enlace que establezca en un nombre de la configuración de la aplicación mediante la adición del valor entre signos de porcentaje, por ejemplo `%AppSettingName%`. |
| **Host** | La configuración que se muestra esta sección permite personalizar el proceso de host de Functions cuando se ejecuta localmente. | 
| **LocalHttpPort** | Establece el puerto predeterminado que se usa cuando al ejecutar el host de Functions local (`func host start` y `func run`). La opción de línea de comandos `--port` tiene prioridad sobre este valor. |
| **CORS** | Define los orígenes permitidos para el [uso compartido de recursos entre orígenes (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Los orígenes se proporcionan en una lista de valores separados por comas y sin espacios. Se admite el valor comodín (\*), lo que permite realizar solicitudes desde cualquier origen. |
| **ConnectionStrings** | Contiene las cadenas de conexión de la base de datos para las funciones. Las cadenas de conexión de este objeto se agregan al entorno con el tipo de proveedor de **System.Data.SqlClient**.  | 

Esta configuración también se puede leer en el código como variables de entorno. Para más información, consulte la sección Variables de entorno de estos temas de referencia específicos del lenguaje:

+ [C# precompilado](functions-dotnet-class-library.md#environment-variables)
+ [Script de C# (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Las herramientas de Functions solo usan las opciones de configuración de dicho archivo cuando las herramientas se ejecutan localmente. De manera predeterminada, estas opciones de configuración no se migran automáticamente cuando el proyecto se publica en Azure. Use el conmutador `--publish-local-settings` [al publicarlo](#publish) para asegurarse de que la configuración se agregue a la aplicación de función en Azure.

Cuando no se establece ninguna cadena de conexión de almacenamiento válida para **AzureWebJobsStorage**, se muestra el siguiente mensaje de error:  

>Missing value for AzureWebJobsStorage in local.settings.json. This is required for all triggers other than HTTP. You can run 'func azure functionapp fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json (Puede ejecutar "func azure functionapp fetch-app-settings" o especificar una cadena de conexión en local settings.json).
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Configuración de aplicaciones

Para establecer un valor para las cadenas de conexión, puede realizar alguna de las siguientes acciones:
* Indique la cadena de conexión desde el [Explorador de Azure Storage](http://storageexplorer.com/).
* Use uno de los siguientes comandos:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    Ambos comandos requieren que primero inicie sesión en Azure.

<a name="create-func"></a>
## <a name="create-a-function"></a>Creación de una función

Para crear una función, ejecute el siguiente comando:

```
func new
``` 
`func new` admite los siguientes argumentos opcionales:

| Argumento     | DESCRIPCIÓN                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Lenguaje de programación de la plantilla, como C#, F# o JavaScript. |
| **`--template -t`** | Nombre de la plantilla. |
| **`--name -n`** | Nombre de la función. |

Por ejemplo, para crear un desencadenador HTTP de JavaScript, ejecute:

```
func new --language JavaScript --template "HttpTrigger" --name MyHttpTrigger
```

Para crear una función desencadenada por la cola, ejecute:

```
func new --language JavaScript --template QueueTrigger --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>Ejecución local de funciones

Para ejecutar un proyecto de Functions, ejecute el host de Functions. El host habilita desencadenadores para todas las funciones del proyecto:

```
func host start
```

`func host start` admite las siguientes opciones:

| Opción     | DESCRIPCIÓN                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Puerto local en el que se escucha. Valor predeterminado: 7071. |
| **`--debug <type>`** | Las opciones son `VSCode` y `VS`. |
| **`--cors`** | Lista separada por comas de orígenes CORS, sin espacios en blanco. |
| **`--nodeDebugPort -n`** | Puerto del depurador de nodo que se va a usar. Valor predeterminado: un valor de launch.json o 5858. |
| **`--debugLevel -d`** | Nivel de seguimiento de la consola (desactivado, detallado, información, advertencia o error). Valor predeterminado: información.|
| **`--timeout -t`** | Tiempo de espera en segundos para que se inicie el host de Functions. Valor predeterminado: 20 segundos.|
| **`--useHttps`** | Enlace con https://localhost:{port} en lugar de con http://localhost:{port}. De forma predeterminada, esta opción crea un certificado de confianza en el equipo.|
| **`--pause-on-error`** | Se pone en pausa en espera de entrada adicional antes de salir del proceso. Resulta útil cuando se inicia Azure Functions Core Tools desde un entorno de desarrollo integrado (IDE).|

Cuando se inicia el host de Functions, devuelve la dirección URL de las funciones desencadenadas por HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Depuración en VS Code o Visual Studio

Para asociar un depurador, pase el argumento `--debug`. Para depurar funciones de JavaScript, use Visual Studio Code. Para funciones de C#, use Visual Studio.

Para depurar funciones de C#, use `--debug vs`. También puede usar [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Para iniciar el host y configurar la depuración de JavaScript, ejecute:

```
func host start --debug vscode
```

> [!IMPORTANT]
> Para la depuración, solo se admite Node.js 8.x. Node.js 9.x no es compatible. 

Luego, en la vista de **depuración** de Visual Studio Code, seleccione **Attach to Azure Functions** (Asociar a Azure Functions). Puede asociar puntos de interrupción, inspeccionar variables y recorrer el código.

![Depuración de JavaScript con Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Paso de datos de prueba a una función

Para probar sus funciones localmente, [inicie el host de Functions](#start) y llame a puntos de conexión del servidor local mediante solicitudes HTTP. El punto de conexión al que llama depende del tipo de función. 

>[!NOTE]  
> En los ejemplos de este tema se usa la herramienta cURL para enviar solicitudes HTTP desde el terminal o un símbolo del sistema. Puede usar una herramienta de su elección para enviar solicitudes HTTP al servidor local. La herramienta cURL está disponible de forma predeterminada en los sistemas basados en Linux. En Windows, primero debe descargar e instalar la [herramienta cURL](https://curl.haxx.se/).

Para obtener información más general sobre cómo probar funciones, consulte [Estrategias para probar el código en Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Funciones desencadenadas por HTTP y webhook

Llama al siguiente punto de conexión para ejecutar de forma local funciones desencadenadas por HTTP y webhook:

    http://localhost:{port}/api/{function_name}

Asegúrese de usar el mismo nombre del servidor y puerto en el que escucha el host de Functions. Puede ver esto en la salida generada al iniciar el host de Functions. Puede llamar a esta dirección URL mediante cualquier método HTTP admitido por el desencadenador. 

El siguiente comando cURL desencadena la función de inicio rápido `MyHttpTrigger` desde una solicitud GET con el parámetro _name_ transferido en la cadena de consulta. 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
En el siguiente ejemplo está la misma función a la que se llama desde una solicitud POST que transfiere _name_ en el cuerpo de la solicitud:

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Puede realizar solicitudes GET desde un explorador que transfiere datos en la cadena de consulta. Para todos los demás métodos HTTP, debe usar cURL, Fiddler, Postman o una herramienta de pruebas HTTP similar.  

#### <a name="non-http-triggered-functions"></a>Funciones no desencadenadas por HTTP
En el caso de todos los tipos de funciones distintas de los desencadenadores HTTP y HTTP webhooks, puede probar sus funciones localmente llamando a un punto de conexión de administración. Al llamar a este punto de conexión con una solicitud HTTP POST en el servidor local se desencadena esta función. Opcionalmente, puede transferir los datos de prueba a la ejecución en el cuerpo de la solicitud POST. Esta funcionalidad es similar a la pestaña **Prueba** de Azure Portal.  

Se llama al siguiente punto de conexión de administrador para desencadenar funciones ajenas a HTTP:

    http://localhost:{port}/admin/functions/{function_name}

Para transferir datos de prueba al punto de conexión de administrador de una función, debe proporcionar los datos en el cuerpo de un mensaje de solicitud POST. Es necesario que el cuerpo del mensaje tenga el siguiente formato JSON:

```JSON
{
    "input": "<trigger_input>"
}
```` 
El valor `<trigger_input>` contiene datos en un formato esperado por la función. El siguiente ejemplo de cURL es una solicitud POST dirigida a una función `QueueTriggerJS`. En este caso, la entrada es una cadena que equivale al mensaje que se espera encontrar en la cola.      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Uso del comando `func run` en la versión 1.x

>[!IMPORTANT]  
> El comando `func run` no se admite en la versión 2.x de las herramientas. Para obtener más información, consulte el tema [How to target Azure Functions runtime versions](set-runtime-version.md) (Cómo seleccionar un destino para versiones en tiempo de ejecución de Azure Functions).

También puede invocar una función directamente con `func run <FunctionName>` y proporcionar datos de entrada para la función. Este comando es similar a la ejecución de una función con la pestaña **Prueba** de Azure Portal. 

`func run` admite las siguientes opciones:

| Opción     | DESCRIPCIÓN                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Contenido alineado. |
| **`--debug -d`** | Se asocia un depurador al proceso de host antes de ejecutar la función.|
| **`--timeout -t`** | Tiempo de espera (en segundos) hasta que el host local de Functions está listo.|
| **`--file -f`** | Nombre del archivo que se usa como contenido.|
| **`--no-interactive`** | No pide entrada. Resulta útil en escenarios de automatización.|

Por ejemplo, para llamar a una función desencadenada por HTTP y pasar cuerpo del contenido, ejecute el siguiente comando:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>Consulta de los archivos de registro en el entorno local

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publicación en Azure

Para publicar un proyecto de Functions en una aplicación de función en Azure, use el comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

Puede usar las siguientes opciones:

| Opción     | DESCRIPCIÓN                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Se publica la configuración de local.settings.json en Azure, se pide que se sobrescriba si la configuración ya existe.|
| **`--overwrite-settings -y`** | Debe usarse con `-i`. Sobrescribe AppSettings en Azure con el valor local si es distinto. El valor predeterminado es Preguntar.|

Este comando se publica en una aplicación de función existente en Azure. Se produce un error cuando `<FunctionAppName>` no existe en la suscripción. Para obtener información sobre cómo crear una aplicación de función desde el símbolo del sistema o la ventana de Terminal mediante la CLI de Azure, consulte [Creación de una instancia de Function App para la ejecución sin servidor](./scripts/functions-cli-create-serverless.md).

El comando `publish` carga el contenido del directorio del proyecto de Functions. Si elimina archivos localmente, el comando `publish` no los eliminará de Azure. Puede eliminar archivos de Azure con la [herramienta Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) de [Azure Portal].  

>[!IMPORTANT]  
> Cuando se crea una aplicación de función en Azure, se usa la versión 1.x del entorno en tiempo de ejecución de Functions de forma predeterminada. Para hacer que la aplicación de función use la versión 2.x del entorno en tiempo de ejecución, agregue la opción de configuración de aplicación `FUNCTIONS_EXTENSION_VERSION=beta`.  
Use el siguiente código de la CLI de Azure para agregar esta opción de configuración a la aplicación de función: 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Pasos siguientes

Azure Functions Core Tools es [código abierto que se hospeda en GitHub](https://github.com/azure/azure-functions-cli).  
Para notificar un error o realizar una solicitud de característica, [abra un problema de GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
