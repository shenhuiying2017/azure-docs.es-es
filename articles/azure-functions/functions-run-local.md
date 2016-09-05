<properties
	pageTitle="Desarrollo y ejecución de funciones de Azure de forma local | Microsoft Azure"
	description="Aprenda a codificar y probar las funciones de Azure en Visual Studio antes de ejecutarlas en el Servicio de aplicaciones de Azure."
	services="functions"
	documentationCenter="na"
	authors="tdykstra"
	manager="erikre"
	editor=""/>

<tags
	ms.service="functions"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="glenga"/>

# Codificación y comprobación de las funciones de Azure en Visual Studio

## Información general

Este artículo explica cómo ejecutar [Funciones de Azure](functions-overview.md) localmente mediante la descarga del repositorio de GitHub [WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/) y la ejecución de la solución de Visual Studio que contiene.

El tiempo de ejecución de Funciones de Azure es una implementación del proyecto de código abierto WebJobs.Script. Este proyecto a su vez se basa en el [SDK de WebJobs de Azure](../app-service-web/websites-dotnet-webjobs-sdk.md) y ambos marcos se pueden ejecutar localmente. No obstante, es necesario conectarse a una cuenta de Almacenamiento de Azure, ya que el SDK de WebJobs usa unas características de cuenta de almacenamiento que no son compatibles con el emulador de almacenamiento.

Las funciones son fáciles de codificar y comprobar en el Portal de Azure pero, a veces, resulta útil trabajar con ellas de forma local antes de ejecutarlas en Azure. Por ejemplo, algunos de los idiomas compatibles con Funciones de Azure son fáciles de codificar en Visual Studio porque este proporciona [IntelliSense](https://msdn.microsoft.com/library/hcw1s69b.aspx). Y aunque puede depurar una función de forma remota, puede ser más rápido y fácil depurarla localmente. Cuando se ejecuta localmente, puede depurar y establecer puntos de interrupción en el código de función así como en el código de host de WebJobs.Script.

>[AZURE.NOTE] Funciones de Azure está actualmente en vista previa y, tanto la experiencia global, como las herramientas todavía están experimentando un rápido desarrollo. Los procedimientos descritos en este artículo no reflejan la experiencia de desarrollo local final y sería estupendo que nos [proporcionara sus comentarios](https://feedback.azure.com/forums/355860-azure-functions).

## Requisitos previos

### Una cuenta de Azure con una aplicación de función

Este artículo presupone que ha trabajado con [Funciones de Azure](functions-overview.md) en el portal y está familiarizado con conceptos de las mismas como [desencadenadores, enlaces y JobHost](functions-reference.md).

Cuando se ejecutan localmente las funciones, se obtienen algunas salidas en la ventana de consola, pero también deberá utilizar el panel que está hospedado en una aplicación de función en vivo para ver las invocaciones y registros a las funciones.

### Visual Studio 2015 con el SDK más reciente de Azure para .NET

Si no tiene Visual Studio 2015 o no tiene el SDK de Azure actual, [descargue el SDK de Azure para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Si aún no lo tiene, Visual Studio 2015 se instala automáticamente con el SDK.

### Condiciones previas

Algunos recursos de Azure e instalaciones de software solo son necesarias si va a ejecutar funciones que los utilicen, por ejemplo:

* Recursos de Azure
	* Bus de servicio
	* Tablas fáciles
	* DocumentDB
	* Centros de eventos
	* Centros de notificaciones

* Motores de scripts y compiladores
	* F#
	* BASH
	* Python
	* PHP

Para más información acerca de estos requisitos, incluidas las variables de entorno que se deben establecer para ellas, consulte las [páginas wiki para el repositorio de WebJobs.Script](https://github.com/Azure/azure-webjobs-sdk-script/wiki/home)

Si su objetivo es contribuir al proyecto WebJobs.SDK, necesitará todas las condiciones previas para ejecutar pruebas completas.

## Ejecución en modo local

1. [Clone](https://github.com/Azure/azure-webjobs-sdk-script/) o [descargar](https://github.com/Azure/azure-webjobs-sdk-script/archive/master.zip) el repositorio de Webjobs.Script.

2. Establezca las variables de entorno para las cadenas de conexión de almacenamiento.

	* AzureWebJobsStorage
	* AzureWebJobsDashboard

	Puede obtener estos valores de la hoja del portal de **configuración de la aplicación** del Servicio de aplicaciones de una aplicación de la función.

	a. En la hoja **Contenedor de funciones** hoja, haga clic en **Configuración del contenedor de funciones**.

	![Haga clic en Configuración del contenedor de funciones](./media/functions-run-local/clickfuncappsettings.png)
 
	b. En la hoja **Configuración del contenedor de funciones**, haga clic en **Ir a la configuración del Servicio de aplicaciones**.

	![Haga clic en Configuración del Servicio de aplicaciones](./media/functions-run-local/clickappsvcsettings.png)
 
	c. En la hoja **Configuración**, haga clic en **Configuración de la aplicación**.

	![Haga clic en Configuración de la aplicación](./media/functions-run-local/clickappsettings.png)
 
	d. En la hoja **Configuración de la aplicación**, desplácese hacia abajo hasta la sección **Configuración de la aplicación** y busque la configuración del SDK de WebJobs.

	![Configuración de WebJobs](./media/functions-run-local/wjsettings.png)

	e. Establezca una variable de entorno con el mismo nombre y valor que el de la configuración de la aplicación `AzureWebJobsStorage`.

	f. Haga lo mismo para la configuración de la aplicación`AzureWebJobsDashboard`.

2. Cree una variable de entorno denominada AzureWebJobsServiceBus y establézcala en la cadena de conexión del bus de servicio.

	Esta variable de entorno es necesaria para los enlaces del bus de servicio y es recomendable que la establezca incluso si no utiliza tales enlaces. En algunos escenarios, puede que vea excepciones si la cadena de conexión del bus de servicio no está establecida, independientemente de los enlaces en uso.

3. Asegúrese de que se establecen las variables de entorno que necesita. (Consulte la sección anterior [Condiciones previas](#conditional-prerequisites)).

4. Inicie Visual Studio y, a continuación, abra la solución WebJobs.Script.

6. Establezca el proyecto de inicio. Si desea ejecutar las funciones que utilizan los desencadenadores HTTP o WebHook, elija **WebJobs.Script.WebHost**; en caso contrario, elija **WebJobs.Script.Host**.

4. Si su proyecto de inicio es WebJobs.Script.Host:

	a. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto WebJobs.Script.Host y, a continuación, haga clic en **Propiedades**.

	b. En la pestaña **Depurar** de la ventana **Propiedades del proyecto**, establezca **Argumentos de la línea de comandos** en `..\..\..\..\sample`.

	![Argumentos de la línea de comandos](./media/functions-run-local/cmdlineargs.png)

	Se trata de una ruta de acceso relativa a la carpeta de *ejemplo* del repositorio. La carpeta de *ejemplo* contiene un archivo *host.json* que incluye la configuración global y una carpeta para cada función de ejemplo.

	Para empezar, lo más fácil es utilizar la carpeta de *ejemplo* que se proporciona. Más adelante puede agregar sus propias funciones a la carpeta de *ejemplo* o utilizar cualquier carpeta que contenga un archivo *host.json* y carpetas de función.

5. Si su proyecto de inicio es WebJobs.Script.WebHost:

	a. Establezca una variable de entorno AzureWebJobsScriptRoot en la ruta de acceso completa a la carpeta `sample`.

	b. Reinicie Visual Studio para que recoja el nuevo valor de la variable de entorno.

	Consulte la sección [Claves de API](#api-keys) para más información acerca de cómo ejecutar funciones de desencadenador HTTP.

5. Abra el archivo *sample\\host.json* y agregue una propiedad `functions` para especificar qué funciones desea ejecutar.

	Por ejemplo, el siguiente JSON hará que el objeto JobHost del SDK de WebJobs busque solo dos funciones.

		{
		  "functions": [ "TimerTrigger-CSharp", "QueueTrigger-CSharp"],
		  "id": "5a709861cab44e68bfed5d2c2fe7fc0c"
		}

	Si usa su propia carpeta en lugar de la carpeta de *ejemplo*, incluya en ella solo las funciones que desea ejecutar. Después, puede omitir la propiedad `functions` en *host.json*.
 
6. Compile y ejecute la solución.

	La ventana de consola muestra que el objeto JobHost busca solo las funciones especificadas en el archivo `host.json`.

		Found the following functions:
		Host.Functions.QueueTrigger-CSharp
		Host.Functions.TimerTrigger-CSharp
		Job host started

	Si inició el proyecto de WebHost, se mostrará una página en blanco en el explorador porque no hay contenido al que acceder en la dirección URL base del proyecto. Consulte la sección [Claves de API](#apikeys) para obtener información acerca de cómo utilizar direcciones URL con las funciones de desencadenador HTTP.

## Visualización de la salida de función

Vaya al panel de la aplicación de la función para ver las invocaciones de funciones y registre la salida para ellas.

El panel se encuentra en la siguiente dirección URL:

	https://{function app name}.scm.azurewebsites.net/azurejobs/#/functions

La página **Funciones** muestra una lista de funciones que se ejecutaron y una lista de invocaciones de funciones.

![Detalles de invocación](./media/functions-run-local/invocationdetail.png)

Haga clic en una invocación para ver la página **Invocation Details** (Detalles de la invocación), que indica cuando se desencadenó la función, el tiempo de ejecución aproximado y si finalizó correctamente. Haga clic en el botón **Toggle Output** (Alternar salida) para ver los registros escritos por el código de función.

![Detalles de invocación](./media/functions-run-local/invocationdetail.png)

## <a id="apikeys"></a> Claves de API para desencadenadores HTTP

Para ejecutar una función HTTP o WebHook, necesitará una clave de API a menos que incluya `"authLevel": "anonymous"` en el archivo *function.json*.

Por ejemplo, si la clave de API es `12345`, puede desencadenar la función *HttpTrigger* con la siguiente dirección URL durante la ejecución del proyecto WebJobs.Script.WebHost.

	http://localhost:28549/api/httptrigger?code=12345

(Como alternativa, puede colocar la clave de API en el encabezado HTTP `x-functions-key`).

Las claves de API se almacenan en archivos `.json` en la carpeta [App\_Data/secrets](https://github.com/Azure/azure-webjobs-sdk-script/tree/master/src/WebJobs.Script.WebHost/App_Data/secrets) del proyecto WebJobs.Script.WebHost.

### Claves de API que se aplican a todas las funciones de HTTP y WebHook

El archivo *host.json* de la carpeta *App\_Data/secrets* contiene dos claves:

```json
{
  "masterKey": "hyexydhln844f2mb7hyexydhln844f2mb7",
  "functionKey": "7hyexydhn844f2mb7hyexydhln844f2mb7"
}
```

La propiedad `functionKey` almacena una clave que se puede usar para cualquier función de HTTP o WebHook si no se definió ningún reemplazo para esa función determinada. Esta característica elimina la necesidad de definir siempre nuevas claves de API para cada función que se crea.

La propiedad `masterKey` almacena una clave que es útil en algunos escenarios de prueba:

* Si llama a una función de WebHook con una clave maestra, el SDK de WebJobs omite la validación de la firma del proveedor de WebHook.

* Si llama a una función de HTTP o WebHook con una clave maestra, la función se desencadena incluso si está deshabilitada en el archivo *function.json*. Se utiliza en el Portal de Azure para hacer que el botón **Ejecutar** funcione incluso para las funciones deshabilitadas.
 
### Claves de API que se aplican a funciones individuales

Los archivos que se denominan *{nombre de la función}.json* contienen la clave de API para una función determinada. Por ejemplo, el siguiente ejemplo de contenido de JSON en *App\_Data/secrets/HttpTrigger.json* establece la clave de API para la función `HttpTrigger`.

```json
{
  "key": "844f2mdhn844f2mb7hyexydhln844f2mb7"
}
```

## Uso de referencias de paquetes de NuGet en funciones  

Debido al modo en que actualmente se procesan las referencias de NuGet, asegúrese de "tocar" el *project.json* archivo mientras se está ejecutando el host. El host busca modificaciones en el archivo e inicia una restauración cuando detecta cambios. Además, *NuGet.exe* (se recomienda la versión 3.3.0) debe estar ubicado en la ruta de acceso o bien debe tener una variable de entorno con el nombre de conjunto de AzureWebJobs\_NuGetPath, con la ruta de acceso *NuGet.exe*.

## Solución de problemas

Los cambios de variables de entorno realizados mientras se ejecuta Visual Studio no se recogen automáticamente. Si agrega o cambia una variable de entorno después de iniciar Visual Studio, cierre Visual Studio y reinícielo para asegurarse de que recoge los valores actuales.

Al depurar, puede obtener más información sobre excepciones seleccionando la opción **Common Language Runtime Exceptions** (Excepciones de Common Language Runtime) en la ventana **Configuración de excepciones** (pulse Ctrl+Alt+E para abrir la ventana).

Otra manera de obtener más información sobre las excepciones durante la depuración consiste en establecer un punto de interrupción en el bloque `catch` del bucle principal para el host de script. Lo encontrará en el proyecto WebJobs.Script, en *Host/ScriptHostManager.cs*, en el método `RunAndBlock`.

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0824_2016-->