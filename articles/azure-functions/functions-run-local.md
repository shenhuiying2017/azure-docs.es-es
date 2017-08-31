---
title: "Desarrollo y ejecución de funciones de Azure de forma local | Microsoft Docs"
description: "Aprenda a codificar y probar funciones de Azure en la máquina local antes de ejecutarlas en Azure Functions."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/12/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 07ad15c61bd4b3912dfa2f629218deebdebd6dc8
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="code-and-test-azure-functions-locally"></a>Codificación y comprobación de las funciones de Azure en un entorno local

A pesar de que [Azure Portal] proporciona un conjunto completo de herramientas para desarrollar y probar Azure Functions, muchos desarrolladores prefieren desarrollar productos localmente. Azure Functions facilita el uso de su editor de código favorito y de las herramientas de desarrollo locales para desarrollar y probar sus funciones en un equipo local. Las funciones pueden desencadenarse a partir de eventos de Azure, y puede depurar sus funciones en C# y JavaScript en el equipo local. 

Si es un desarrollador de C# de Visual Studio, Azure Functions también [permite la integración con Visual Studio 2017](functions-develop-vs.md).

## <a name="install-the-azure-functions-core-tools"></a>Instalación de Azure Functions Core Tools

Azure Functions Core Tools es una versión local del entorno de ejecución de Azure Functions que puede ejecutar en el equipo Windows local. No se trata de un emulador o simulador. Se trata del mismo entorno de ejecución que se usa en Functions en Azure.

[Azure Functions Core Tools] se proporciona como un paquete npm. Primero debe [instalar NodeJS](https://docs.npmjs.com/getting-started/installing-node), que incluye npm.  

>[!NOTE]
>En este momento el paquete Azure Functions Core Tools solo se puede instalar en equipos Windows. Esta restricción se debe a una limitación temporal del host de Functions.

[Azure Functions Core Tools] agrega los siguientes alias de comando:
* **func**
* **azfun**
* **azurefunctions**

Es posible utilizar cualquier de estos alias en lugar del alias `func` de este tema.

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
| Configuración      | Descripción                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Cuando se establece en **true**, todos los valores se cifran con una clave de máquina local. Se usa con los comandos `func settings`. El valor predeterminado es **false**. |
| **Valores** | Colección de opciones de configuración de la aplicación que se usa en la ejecución local. Agregue la configuración de la aplicación a este objeto.  |
| **AzureWebJobsStorage** | Establece la cadena de conexión para la cuenta de Azure Storage que el entorno de ejecución de Azure Functions usa internamente. La cuenta de almacenamiento admite los desencadenadores de su función. Esta configuración de conexión de la cuenta de almacenamiento es necesaria para todas las funciones, salvo en el caso de las funciones desencadenadas de HTTP.  |
| **AzureWebJobsDashboard** | Establece la cadena de conexión a la cuenta de Azure Storage que se usa para almacenar los registros de funciones. Este valor opcional hace que se pueda acceder a los registros en el portal.|
| **Host** | La configuración que se muestra esta sección permite personalizar el proceso de host de Functions cuando se ejecuta localmente. | 
| **LocalHttpPort** | Establece el puerto predeterminado que se usa cuando al ejecutar el host de Functions local (`func host start` y `func run`). La opción de línea de comandos `--port` tiene prioridad sobre este valor. |
| **CORS** | Define los orígenes permitidos para el [uso compartido de recursos entre orígenes (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Los orígenes se proporcionan en una lista de valores separados por comas y sin espacios. Se admite el valor comodín (**\***), lo que permite realizar solicitudes desde cualquier origen. |
| **ConnectionStrings** | Contiene las cadenas de conexión de la base de datos para las funciones. Las cadenas de conexión de este objeto se agregan al entorno con el tipo de proveedor de **System.Data.SqlClient**.  | 

La mayoría de los desencadenadores y los enlaces tienen una propiedad **Connection** que se asigna al nombre de una variable de entorno o a una configuración de aplicación. Por cada propiedad de conexión debe haber una configuración de aplicación definida en el archivo local.settings.json. 

Esta configuración también se puede leer en el código como variables de entorno. En C#, use [System.Environment.GetEnvironmentVariable](https://msdn.microsoft.com/library/system.environment.getenvironmentvariable(v=vs.110).aspx) o [ConfigurationManager.AppSettings](https://msdn.microsoft.com/library/system.configuration.configurationmanager.appsettings%28v=vs.110%29.aspx). En JavaScript, use `process.env`. Las opciones de configuración especificadas como variable de entorno del sistema tienen prioridad sobre otros valores del archivo local.settings.json. 

Las herramientas de Functions solo usan las opciones de configuración de dicho archivo cuando las herramientas se ejecutan localmente. De manera predeterminada, estas opciones de configuración no se migran automáticamente cuando el proyecto se publica en Azure. Use el conmutador `--publish-local-settings` [al publicarlo](#publish) para asegurarse de que la configuración se agregue a la aplicación de función en Azure.

Cuando no se establece ninguna cadena de conexión de almacenamiento válida para **AzureWebJobsStorage**, se muestra el siguiente mensaje de error:  

>Missing value for AzureWebJobsStorage in local.settings.json. This is required for all triggers other than HTTP. You can run 'func azure functionary fetch-app-settings <functionAppName>' or specify a connection string in local.settings.json. (Falta un valor para AzureWebJobsStorage en local.settings.json. Este valor es necesario para todos los desencadenadores que no sean HTTP. Puede ejecutar "func azure functionary fetch-app-settings " o especificar una cadena de conexión en local.settings.json).
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>Configuración de aplicaciones

Para establecer un valor para las cadenas de conexión, puede realizar alguna de las siguientes acciones:
* Indique la cadena de conexión desde el [Explorador de Azure Storage](http://storageexplorer.com/).
* Use uno de los siguientes comandos:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure functionapp storage fetch-connection-string <StorageAccountName>
    ```
    Ambos comandos requieren que primero inicie sesión en Azure.

## <a name="create-a-function"></a>Creación de una función

Para crear una función, ejecute el siguiente comando:

```
func new
``` 
`func new` admite los siguientes argumentos opcionales:

| Argumento     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | Lenguaje de programación de la plantilla, como C#, F# o JavaScript. |
| **`--template -t`** | Nombre de la plantilla. |
| **`--name -n`** | Nombre de la función. |

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

`func host start` admite las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Puerto local en el que se escucha. Valor predeterminado: 7071. |
| **`--debug <type>`** | Las opciones son `VSCode` y `VS`. |
| **`--cors`** | Lista separada por comas de orígenes CORS, sin espacios en blanco. |
| **`--nodeDebugPort -n`** | Puerto del depurador de nodo que se va a usar. Valor predeterminado: un valor de launch.json o 5858. |
| **`--debugLevel -d`** | Nivel de seguimiento de la consola (desactivado, detallado, información, advertencia o error). Valor predeterminado: información.|
| **`--timeout -t`** | Tiempo de espera en segundos para iniciar el host de Functions. Valor predeterminado: 20 segundos.|
| **`--useHttps`** | Se enlaza a https://localhost:{port} en lugar de a http://localhost:{port}. De forma predeterminada, esta opción crea un certificado de confianza en el equipo.|
| **`--pause-on-error`** | Se pone en pausa en espera de entrada adicional antes de salir del proceso. Resulta útil cuando se inicia Azure Functions Core Tools desde un entorno de desarrollo integrado (IDE).|

Cuando se inicia el host de Functions, devuelve la dirección URL de las funciones desencadenadas por HTTP:

```
Found the following functions:
Host.Functions.MyHttpTrigger

ob host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>Depuración en VS Code o Visual Studio

Para asociar un depurador, pase el argumento `--debug`. Para depurar funciones de JavaScript, use Visual Studio Code. Para funciones de C#, use Visual Studio.

Para depurar funciones de C#, use `--debug vs`. También puede usar [Azure Functions Visual Studio 2017 Tools](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/). 

Para iniciar el host y configurar la depuración de JavaScript, ejecute:

```
func host start --debug vscode
```

Luego, en la vista de **depuración** de Visual Studio Code, seleccione **Attach to Azure Functions** (Asociar a Azure Functions). Puede asociar puntos de interrupción, inspeccionar variables y recorrer el código.

![Depuración de JavaScript con Visual Studio Code](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>Paso de datos de prueba a una función

También puede invocar una función directamente con `func run <FunctionName>` y proporcionar datos de entrada para la función. Este comando es similar a la ejecución de una función con la pestaña **Prueba** de Azure Portal. Este comando inicia el host de Functions completo.

`func run` admite las siguientes opciones:

| Opción     | Descripción                            |
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

## <a name="publish"></a>Publicación en Azure

Para publicar un proyecto de Functions en una aplicación de función en Azure, use el comando `publish`:

```
func azure functionapp publish <FunctionAppName>
```

Puede usar las siguientes opciones:

| Opción     | Descripción                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Se publica la configuración de local.settings.json en Azure, se pide que se sobrescriba si la configuración ya existe.|
| **`--overwrite-settings -y`** | Debe usarse con `-i`. Sobrescribe AppSettings en Azure con el valor local si es distinto. El valor predeterminado es Preguntar.|

El comando `publish` carga el contenido del directorio del proyecto de Functions. Si elimina archivos localmente, el comando `publish` no los eliminará de Azure. Puede eliminar archivos de Azure con la [herramienta Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) de [Azure Portal].

## <a name="next-steps"></a>Pasos siguientes

Azure Functions Core Tools es [código abierto que se hospeda en GitHub](https://github.com/azure/azure-functions-cli).  
Para notificar un error o realizar una solicitud de característica, [abra un problema de GitHub](https://github.com/azure/azure-functions-cli/issues). 

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 

