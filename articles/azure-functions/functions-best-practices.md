---
title: Procedimientos recomendados de Azure Functions | Microsoft Docs
description: "Información acerca de los procedimientos recomendados y los patrones de Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, patrones, procedimientos recomendados, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8088a8a83bcaefce17ac2756360a46119c8eb27
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimización del rendimiento y confiabilidad de Azure Functions

En este artículo se proporcionan instrucciones para mejorar el rendimiento y la confiabilidad de sus aplicaciones de función [sin servidor](https://azure.microsoft.com/overview/serverless-computing/). 

## <a name="general-best-practices"></a>Procedimientos recomendados generales

Éstos son los procedimientos recomendados para crear y diseñar las soluciones sin servidor mediante Azure Functions.

### <a name="avoid-long-running-functions"></a>Evitar funciones de ejecución prolongada

Las funciones grandes de ejecución prolongada pueden causar problemas de tiempo de espera inesperados. Una función puede ser grande debido a numerosas dependencias de Node.js. La importación de las dependencias también puede provocar mayores tiempos de carga que dan lugar a tiempos de expiración inesperados. Las dependencias se cargan explícita e implícitamente. Un módulo único cargado por el código puede cargar sus propios módulos adicionales.  

Siempre que sea posible, refactorice funciones grandes en conjuntos más pequeños de funciones que trabajen juntos y devuelvan respuestas rápidas. Por ejemplo, un webhook o una función de desencadenador HTTP podría requerir una respuesta de confirmación en un determinado período de tiempo. Es habitual que los webhooks requieran una respuesta inmediata. Puede pasar la carga útil de desencadenador HTTP a una cola para ser procesada por una función de desencadenador de cola. Este enfoque permite aplazar el trabajo real y devolver una respuesta inmediata.


### <a name="cross-function-communication"></a>Comunicación entre funciones

[Durable Functions](durable-functions-overview.md) y [Azure Logic Apps](../logic-apps/logic-apps-overview.md) se han creado para administrar las transiciones de estado y las comunicaciones entre varias funciones.

Si no usa Durable Functions ni Logic Apps para integrar varias funciones, se recomienda usar colas de almacenamiento para la comunicación entre estas.  La razón principal es que las colas de almacenamiento son más baratas y mucho más fáciles de aprovisionar. 

Los mensajes individuales de una cola de almacenamiento tienen un límite de tamaño de 64 KB. Si tiene que pasar mensajes más grandes entre funciones, se podría usar una cola de Azure Service Bus para admitir tamaños de mensaje de hasta 256 KB en el nivel Estándar y hasta 1 MB en el nivel Premium.

Temas de Service Bus son útiles si necesita filtrado de mensajes antes del procesamiento.

Los concentradores de eventos son útiles para admitir comunicaciones de gran volumen.


### <a name="write-functions-to-be-stateless"></a>Escritura de funciones para que no tengan estado 

Si es posible, las funciones no deben tener estado y ser idempotentes. Asociar cualquier información de estado necesaria con los datos. Por ejemplo, un pedido para procesar probablemente tendría un miembro `state` asociado. Una función podría procesar un pedido en función de ese estado mientras que la propia función permanece sin estado. 

Las funciones idempotentes se recomiendan especialmente con desencadenadores de temporizador. Por ejemplo, si tiene algo que debe ejecutarse una vez al día obligatoriamente, escríbalo para poder ejecutarse en cualquier momento durante el día con los mismos resultados. La función puede salir cuando no haya ningún trabajo para un día determinado. Asimismo, si una ejecución anterior no se pudo completar, la siguiente ejecución debe continuar donde se quedó.


### <a name="write-defensive-functions"></a>Escritura de funciones defensivas

Suponga que la función podría encontrarse con una excepción en cualquier momento. Diseñe las funciones con la capacidad de continuar a partir de un punto de error anterior durante la siguiente ejecución. Considere un escenario que requiere las siguientes acciones:

1. Consulta de 10 000 filas en una base de datos.
2. Cree un mensaje de cola para cada una de esas filas para procesar más abajo la línea.
 
Dependiendo de lo complejo que sea el sistema, es posible que haya servicios de bajada implicados con un comportamiento incorrecto, interrupciones de red, límites de cuota alcanzados, etc. Todo esto puede afectar a su función en cualquier momento. Debe diseñar las funciones para que estén preparadas para ello.

¿Cómo reacciona el código si se produce un error después de insertar 5000 de esos elementos en una cola para su procesamiento? Realice un seguimiento de elementos de un conjunto que ha completado. En caso contrario, podría insertarlos la próxima vez. Esto puede tener un impacto grave en el flujo de trabajo. 

Si ya se ha procesado un elemento de la cola, permita que la función sea no operativa.

Aproveche las medidas defensivas ya proporcionadas para los componentes que se usa en la plataforma de Azure Functions. Por ejemplo, vea la información sobre el **tratamiento de mensajes dudosos en la cola** en la documentación de [desencadenadores y enlaces de cola de Azure Storage](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Procedimientos recomendados de escalabilidad

Hay una serie de factores que afectan a cómo se escalan las instancias de la aplicación de función. Se proporcionan más detalles en la documentación sobre [escalado de funciones](functions-scale.md).  A continuación se indican algunos procedimientos recomendados para garantizar una escalabilidad óptima de una aplicación de función.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>No mezclar código de prueba y producción en la misma aplicación de función

Las funciones dentro de una aplicación de función compartan recursos. Por ejemplo, la memoria se comparte. Si usa una aplicación de función en producción, no agregue recursos y funciones relacionados con pruebas a ella. Se podría producir una sobrecarga inesperada durante la ejecución de código de producción.

Asegúrese de cargar en las aplicaciones de función de producción. La memoria se promedia entre cada función de la aplicación.

Si tiene un ensamblado compartido que se hace referencia en varias funciones. NET, colóquelo en una carpeta compartida común. Haga referencia al ensamblado con una instrucción similar al ejemplo siguiente si usa scripts de C# (.csx): 

    #r "..\Shared\MyAssembly.dll". 

En caso contrario, es fácil implementar accidentalmente varias versiones de prueba del mismo binario que se comporten de manera diferente entre funciones.

No utilice el registro detallado en el código de producción. Tiene un impacto negativo en el rendimiento.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Uso del código asincrónico pero evitar las llamadas de bloqueo

La programación asincrónica es una práctica recomendada. Sin embargo, evite siempre las referencias a la propiedad `Result` o las llamadas al método `Wait` en una instancia `Task`. Este enfoque puede provocar el agotamiento de subprocesos.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Recepción de mensajes en lotes siempre que sea posible

Algunos desencadenadores como Event Hubs permiten la recepción de un lote de mensajes en una única invocación.  El procesamiento de mensajes por lotes tiene un rendimiento mucho mejor.  Puede configurar el tamaño de lote máximo en el archivo `functions.json` tal como se detalla en la [documentación de referencia sobre host.json](functions-host-json.md)

Para las funciones de C# puede cambiar el tipo a una matriz fuertemente tipada.  Por ejemplo, en lugar de `EventData sensorEvent` la signatura del método podría ser `EventData[] sensorEvent`.  Para otros idiomas debe establecer explícitamente la propiedad de cardinalidad de `function.json` en `many` para habilitar el procesamiento por lotes [tal y como se muestra aquí](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configuración de los comportamientos de host para controlar mejor la simultaneidad

El archivo `host.json` de la aplicación de función permite la configuración de comportamientos del sistema de tiempo de ejecución y de desencadenadores del host.  Además de los comportamientos del procesamiento por lotes, puede administrar la simultaneidad para varios desencadenadores.  Frecuentemente, el ajustar los valores de estas opciones puede hacer que cada instancia se escale adecuadamente para satisfacer la demanda de las funciones que se invocan.

La configuración de los archivos de los hosts se aplica a todas las funciones de la aplicación, a una *única instancia* de la función. Por ejemplo, si tuviera una aplicación de función con 2 funciones HTTP y un valor de solicitudes simultáneas establecido en 25, una solicitud a cualquiera de los desencadenadores HTTP contaría las 25 solicitudes simultáneas compartidas.  Si esa aplicación de función se escala a 10 instancias, las 2 funciones permitirían eficazmente 250 solicitudes simultáneas (10 instancias * 25 solicitudes simultáneas por cada instancia).

**Opciones de host para simultaneidad de HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Se pueden encontrar otras opciones de configuración de host [en el documento de configuración de host](functions-host-json.md).

## <a name="next-steps"></a>pasos siguientes
Para obtener más información, consulte los siguientes recursos:

Como Azure Functions usa Azure App Service, también debe conocer las guías de App Service.
* [Patterns and Practices HTTP Performance Optimizations](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/) (Patrones y procedimientos de optimización del rendimiento de HTTP)
