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
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Referencia para desarrolladores de Funciones de Azure

Las funciones de Azure comparten algunos componentes y conceptos técnicos básicos, independientemente del idioma o el enlace que use. Antes de ir a detalles de aprendizaje específicos de un idioma o un enlace determinados, asegúrese de leer al completo esta información general que se aplica a todos ellos.

Este artículo presupone que ya ha leído la [información general de las funciones de Azure](functions-overview.md) y está familiarizado con [conceptos de SDK de WebJobs como desencadenadores, enlaces y el tiempo de ejecución de JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Las funciones de Azure se basan en el SDK de WebJobs.

## function

Una *función* es el concepto principal en las funciones de Azure. Escriba código para una función en el lenguaje que elija y guarde los archivos de código y un archivo de configuración en la misma carpeta. La configuración se encuentra en JSON y el archivo se denomina `function.json`. Se admiten diferentes lenguajes, y cada uno de ellos tiene una experiencia ligeramente diferente optimizada para que funcione mejor para ese lenguaje: Estructura de carpetas de ejemplo:

```
mynodefunction
| - function.json
| - index.js
| - node_modules
| | - ... packages ...
| - package.json
mycsharpfunction
| - function.json
| - run.csx
```

## function.json y enlaces

El `function.json` archivo contiene la configuración específica de una función, incluidos sus enlaces. El tiempo de ejecución lee este archivo para determinar qué eventos desencadenar, qué datos incluir cuando se llama a la función y dónde enviar los datos que se transfieren desde la propia función.

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

Puede impedir que el tiempo de ejecución ejecute la función estableciendo la propiedad `disabled` en `true`.

La propiedad `bindings` es donde configura los enlaces y los desencadenadores. Cada enlace comparte unos ajustes de configuración comunes y algunos parámetros que son específicos de un determinado tipo de enlace. Cada enlace requiere la siguiente configuración:

|Propiedad|Valores/tipos|Comentarios|
|---|-----|------|
|`type`|cadena|Tipo de enlace. Por ejemplo: `queueTrigger`.
|`direction`|'in', 'out'| Indica si el enlace está disponible para recibir datos en la función o enviar datos de la función.
| `name` | cadena | El nombre que se usará para los datos enlazados en la función. Para C# será un nombre de argumento; para JavaScript será la clave en una lista de clave-valor.

## Tiempo de ejecución (host de script y host web)

El tiempo de ejecución, conocido también como el host de script, es el host de SDK de WebJobs subyacente que escucha los eventos, recopila y envía los datos y, finalmente, ejecuta el código.

Para facilitar los desencadenadores HTTP, también hay un host web que se ha diseñado para colocarse delante el host de script en escenarios de producción. Esto ayuda a aislar el host de script del tráfico front-end administrado por el host web.

## Estructura de carpetas

Un host de script apunta a una carpeta que contiene un archivo de configuración y una o más funciones.

```
parentFolder (for example, wwwroot)
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

El archivo *host.json* contiene alguna configuración específica del host de script y se coloca en la carpeta principal.

Cada función tiene una carpeta que contiene los archivos de código, *function.json* y otras dependencias.

Al configurar un proyecto para la implementación de funciones en un contenedor de funciones en Servicio de aplicaciones de Azure, puede tratar esta estructura de carpetas como el código del sitio. Puede utilizar herramientas existentes como scripts de implementación personalizados o implementación e integración continuas para realizar la transpilación de código o la instalación del paquete de tiempo de implementación.

## Ejecución en paralelo

Cuando se producen varios eventos de desencadenado más rápido de lo que un tiempo de ejecución de función de un solo subproceso pueda procesarlos, el tiempo de ejecución puede invocar la función varias veces en paralelo. Si un contenedor de funciones usa el [Plan de servicio dinámico](functions-scale.md#dynamic-service-plan), el contenedor de funciones podría escalar horizontalmente hasta 10 instancias simultáneas de forma automática. Cada instancia del contenedor de funciones, si la aplicación se ejecuta en el Plan de servicio dinámico o el [Plan de servicio de aplicaciones](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) normal, puede procesar invocaciones de función simultáneas en paralelo mediante varios subprocesos. El número máximo de invocaciones de función simultáneas en cada instancia de aplicación de función varía según el tamaño de la memoria del contenedor de funciones.

## Impulso de funciones de Azure  

El impulso es una secuencia de eventos en directo que muestra con qué frecuencia se ejecuta la función, así como las operaciones correctas y erróneas. También puede supervisar el tiempo medio de ejecución. Iremos agregando más características y personalización con el paso del tiempo. Puede obtener acceso a la página **Impulso** desde la pestaña **Supervisión**.

## Enlaces

Esta es una tabla de todos los enlaces admitidos.

[AZURE.INCLUDE [proceso dinámico](../../includes/functions-bindings.md)]

## Problemas de informes

[AZURE.INCLUDE [Problemas de informes](../../includes/functions-reporting-issues.md)]

## Pasos siguientes

Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)
* [Enlaces y desencadenadores de las Funciones de azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->