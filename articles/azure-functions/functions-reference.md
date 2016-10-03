<properties
	pageTitle="Referencia para desarrolladores de Funciones de Azure | Microsoft Azure"
	description="Comprenda los componentes y conceptos de las funciones de Azure que son comunes en todos los lenguajes y enlaces."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/13/2016"
	ms.author="chrande"/>

# Referencia para desarrolladores de Funciones de Azure

Las funciones de Azure comparten algunos componentes y conceptos técnicos básicos, independientemente del idioma o el enlace que use. Antes de ir a detalles de aprendizaje específicos de un idioma o un enlace determinados, asegúrese de leer al completo esta información general que se aplica a todos ellos.

Este artículo presupone que ya ha leído la [información general de las funciones de Azure](functions-overview.md) y está familiarizado con [conceptos de SDK de WebJobs como desencadenadores, enlaces y el tiempo de ejecución de JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Las funciones de Azure se basan en el SDK de WebJobs.


## Código de función

Una *función* es el concepto principal en las funciones de Azure. Escriba código para una función en el lenguaje que elija y guarde los archivos de código y un archivo de configuración en la misma carpeta. La configuración se encuentra en JSON y el archivo se denomina `function.json`. Se admiten diferentes lenguajes, y cada uno de ellos tiene una experiencia ligeramente diferente optimizada para que funcione mejor para ese lenguaje:

El archivo `function.json` contiene la configuración específica de una función, incluidos sus enlaces. El tiempo de ejecución lee este archivo para determinar qué eventos desencadenar, qué datos incluir cuando se llama a la función y dónde enviar los datos que se transfieren desde la propia función.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Puede impedir que la función se ejecute en tiempo de ejecución si establece la propiedad `disabled` en `true`.

La propiedad `bindings` es donde configura los enlaces y los desencadenadores. Cada enlace comparte unos ajustes de configuración comunes y algunos parámetros que son específicos de un determinado tipo de enlace. Cada enlace requiere la siguiente configuración:

|Propiedad|Valores/tipos|Comentarios|
|---|-----|------|
|`type`|string|Tipo de enlace. Por ejemplo: `queueTrigger`.
|`direction`|'in', 'out'| Indica si el enlace está disponible para recibir datos en la función o enviar datos de la función.
| `name` | string | El nombre que se usará para los datos enlazados en la función. Para C# será un nombre de argumento; para JavaScript será la clave en una lista de clave-valor.

## Aplicación de función

Una aplicación de función se compone de una o varias funciones individuales que se administran conjuntamente en el Servicio de aplicaciones de Azure. Todas las funciones de una aplicación de función comparten el mismo plan de precios, la misma implementación continua y la misma versión en tiempo de ejecución. Las funciones escritas en varios lenguajes pueden compartir la misma aplicación de función. Una aplicación de función es como una forma de organizar y administrar las funciones de manera colectiva.

## Tiempo de ejecución (host de script y host web)

El tiempo de ejecución, o host de script, es el host del SDK de WebJobs subyacente que escucha eventos, recopila y envía datos y, finalmente, ejecuta el código.

Para facilitar los desencadenadores HTTP, también hay un host web que se ha diseñado para colocarse delante el host de script en escenarios de producción. Esto ayuda a aislar el host de script del tráfico front-end administrado por el host web.

## Estructura de carpetas

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Al configurar un proyecto para la implementación de funciones en una aplicación de función en el Servicio de aplicaciones de Azure, puede tratar esta estructura de carpetas como el código del sitio. Puede utilizar herramientas existentes como scripts de implementación personalizados o implementación e integración continuas para realizar la transpilación de código o la instalación del paquete de tiempo de implementación.

## <a id="fileupdate"></a> Actualización de los archivos de aplicación de función

El editor de funciones integrado en el Portal de Azure le permite actualizar el archivo *function.json* y el archivo de código de una función. Para cargar o actualizar otros archivos como *package.json* o *project.json* u otras dependencias, tendrá que usar otros métodos de implementación.

Las aplicaciones de función se integran en el Servicio de aplicaciones, por lo que todas las [opciones de implementación disponibles para las aplicaciones web estándar](../app-service-web/web-sites-deploy.md) están también disponibles para las aplicaciones de función. Estos son algunos métodos que puede utilizar para cargar o actualizar los archivos del contenedor de funciones.

#### Uso del Editor de App Service

1. En el portal de Funciones de Azure, haga clic en **Function app settings** (Configuración de Function App).

2. En la sección **Configuración avanzada**, haga clic en **Go to App Service Settings** (Ir a la configuración del Servicio de aplicaciones).

3. Haga clic en **Editor de App Service** en el panel de navegación del menú de aplicaciones, que está debajo de **HERRAMIENTAS DE DESARROLLO**.

4.  Haga clic en **Ir**.

	Después de que se cargue el Editor de App Service, verá el archivo *host.json* y las carpetas de funciones en *wwwroot*.

5. Abra los archivos para editarlos, o arrástrelos y colóquelos desde el equipo de desarrollo para cargar los archivos.

#### Para usar el punto de conexión de SCM (Kudu) del contenedor de funciones

1. Vaya a `https://<function_app_name>.scm.azurewebsites.net`.

2. Haga clic en **Consola de depuración > CMD**.

3. Vaya a `D:\home\site\wwwroot` para actualizar *host.json* o `D:\home\site\wwwroot<function_name>` para actualizar los archivos de la función.

4. Arrastre y coloque el archivo que desea cargar en la carpeta adecuada en la cuadrícula de archivos. Hay dos áreas en la cuadrícula de archivos donde puede colocar un archivo. Para los archivos *.zip*, aparece un cuadro con la etiqueta "Drag here to upload and unzip" (Arrastre aquí para cargar y descomprimir). Para otros tipos de archivo, colóquelos en la cuadrícula de archivos, pero fuera del cuadro para "descomprimir".

#### Para usar FTP

1. Siga las instrucciones descritas [aquí](../app-service-web/web-sites-deploy.md#ftp) para configurar el FTP.

2. Cuando esté conectado al sitio de aplicaciones de función, copie el archivo *host.json* actualizado en `/site/wwwroot` o copie los archivos de funciones en `/site/wwwroot/<function_name>`.

#### Para usar la implementación continua

Siga las instrucciones que se indican en el tema [Continuous deployment for Azure Functions](functions-continuous-deployment.md) (Implementación continua para Funciones de Azure).

## Ejecución en paralelo

Cuando se producen varios eventos de desencadenado más rápido de lo que un tiempo de ejecución de función de un solo subproceso pueda procesarlos, el tiempo de ejecución puede invocar la función varias veces en paralelo. Si una aplicación de función usa el [plan de servicio dinámico](functions-scale.md#dynamic-service-plan), esta aplicación podría escalarse horizontalmente de forma automática. Cada instancia de la aplicación de función, tanto si la aplicación se ejecuta en el plan de servicio dinámico como en el [plan del Servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) normal, puede procesar invocaciones de función simultáneas en paralelo mediante varios subprocesos. El número máximo de invocaciones de función simultáneas en cada instancia de aplicación de función varía según el tamaño de la memoria del contenedor de funciones.

## Impulso de funciones de Azure  

El impulso es una secuencia de eventos en directo que muestra con qué frecuencia se ejecuta la función, así como las operaciones correctas y erróneas. También puede supervisar el tiempo medio de ejecución. Iremos agregando más características y personalización con el paso del tiempo. Puede acceder a la página **Impulso** desde la pestaña **Supervisión**.

## Repositorios

El código de Funciones de Azure es código abierto y está almacenado en repositorios de GitHub:

* [Tiempo de ejecución de Funciones de Azure](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portal de Funciones de Azure](https://github.com/projectkudu/AzureFunctionsPortal)
* [Plantillas de Funciones de Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [SDK de WebJobs de Azure](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensiones del SDK de WebJobs de Azure](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## Enlaces

Esta es una tabla de todos los enlaces admitidos.

[AZURE.INCLUDE [proceso dinámico](../../includes/functions-bindings.md)]

## Problemas de informes

[AZURE.INCLUDE [Problemas de informes](../../includes/functions-reporting-issues.md)]

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Referencia para desarrolladores de F# de Azure Functions](functions-reference-fsharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)
* [Azure Functions: The Journey](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) (Funciones de Azure: trayectoria) en el blog del equipo del Servicio de aplicaciones de Azure. Esta es la historia de cómo se desarrolló Funciones de Azure.

<!---HONumber=AcomDC_0921_2016-->