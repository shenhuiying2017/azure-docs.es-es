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
ms.date: 06/13/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e0d4a29f59e48cac675e567fd84384b1b60d35f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimización del rendimiento y confiabilidad de Azure Functions

En este artículo se proporcionan instrucciones para mejorar el rendimiento y la confiabilidad de sus aplicaciones de función. 


## <a name="avoid-long-running-functions"></a>Evitar funciones de ejecución prolongada

Las funciones grandes de ejecución prolongada pueden causar problemas de tiempo de espera inesperados. Una función puede ser grande debido a numerosas dependencias de Node.js. La importación de las dependencias también puede provocar mayores tiempos de carga que dan lugar a tiempos de expiración inesperados. Las dependencias se cargan explícita e implícitamente. Un módulo único cargado por el código puede cargar sus propios módulos adicionales.  

Siempre que sea posible, refactorice funciones grandes en conjuntos más pequeños de funciones que trabajen juntos y devuelvan respuestas rápidas. Por ejemplo, un webhook o una función de desencadenador HTTP podría requerir una respuesta de confirmación en un determinado período de tiempo. Es habitual que los webhooks requieran una respuesta inmediata. Puede pasar la carga útil de desencadenador HTTP a una cola para ser procesada por una función de desencadenador de cola. Este enfoque permite aplazar el trabajo real y devolver una respuesta inmediata.


## <a name="cross-function-communication"></a>Comunicación entre funciones

Cuando se integra varias funciones, normalmente es un procedimiento recomendado usar colas de almacenamiento para comunicación entre funciones.  La razón principal es que las colas de almacenamiento son más baratas y mucho más fáciles de aprovisionar. 

Los mensajes individuales de una cola de almacenamiento tienen un límite de tamaño de 64 KB. Si tiene que pasar mensajes más grandes entre funciones, se podría usar una cola de Azure Service Bus para admitir tamaños de mensaje de hasta 256 KB.

Temas de Service Bus son útiles si necesita filtrado de mensajes antes del procesamiento.

Los concentradores de eventos son útiles para admitir comunicaciones de gran volumen.


## <a name="write-functions-to-be-stateless"></a>Escritura de funciones para que no tengan estado 

Si es posible, las funciones no deben tener estado y ser idempotentes. Asociar cualquier información de estado necesaria con los datos. Por ejemplo, un pedido para procesar probablemente tendría un miembro `state` asociado. Una función podría procesar un pedido en función de ese estado mientras que la propia función permanece sin estado. 

Las funciones idempotentes se recomiendan especialmente con desencadenadores de temporizador. Por ejemplo, si tiene algo que debe ejecutarse una vez al día obligatoriamente, escríbalo para poder ejecutarse en cualquier momento durante el día con los mismos resultados. La función puede salir cuando no haya ningún trabajo para un día determinado. Asimismo, si una ejecución anterior no se pudo completar, la siguiente ejecución debe continuar donde se quedó.


## <a name="write-defensive-functions"></a>Escritura de funciones defensivas

Suponga que la función podría encontrarse con una excepción en cualquier momento. Diseñe las funciones con la capacidad de continuar a partir de un punto de error anterior durante la siguiente ejecución. Considere un escenario que requiere las siguientes acciones:

1. Consulta de 10 000 filas en una base de datos.
2. Cree un mensaje de cola para cada una de esas filas para procesar más abajo la línea.
 
Dependiendo de lo complejo que sea el sistema, es posible que haya servicios de bajada implicados con un comportamiento incorrecto, interrupciones de red, límites de cuota alcanzados, etc. Todo esto puede afectar a su función en cualquier momento. Debe diseñar las funciones para que estén preparadas para ello.

¿Cómo reacciona el código si se produce un error después de insertar 5000 de esos elementos en una cola para su procesamiento? Realice un seguimiento de elementos de un conjunto que ha completado. En caso contrario, podría insertarlos la próxima vez. Esto puede tener un impacto grave en el flujo de trabajo. 

Si ya se ha procesado un elemento de la cola, permita que la función sea no operativa.

Aproveche las medidas defensivas ya proporcionadas para los componentes que se usa en la plataforma de Azure Functions. Por ejemplo, vea la información sobre el **tratamiento de mensajes dudosos en la cola** en la documentación del [desencadenador de cola de Azure Storage](functions-bindings-storage-queue.md#trigger).
 

## <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>No mezclar código de prueba y producción en la misma aplicación de función

Las funciones dentro de una aplicación de función compartan recursos. Por ejemplo, la memoria se comparte. Si usa una aplicación de función en producción, no agregue recursos y funciones relacionados con pruebas a ella. Se podría producir una sobrecarga inesperada durante la ejecución de código de producción.

Asegúrese de cargar en las aplicaciones de función de producción. La memoria se promedia entre cada función de la aplicación.

Si tiene un ensamblado compartido que se hace referencia en varias funciones. NET, colóquelo en una carpeta compartida común. Haga referencia al ensamblado con una instrucción similar al ejemplo siguiente: 

    #r "..\Shared\MyAssembly.dll". 

En caso contrario, es fácil implementar accidentalmente varias versiones de prueba del mismo binario que se comporten de manera diferente entre funciones.

No utilice el registro detallado en el código de producción. Tiene un impacto negativo en el rendimiento.



## <a name="use-async-code-but-avoid-blocking-calls"></a>Uso del código asincrónico pero evitar las llamadas de bloqueo

La programación asincrónica es una práctica recomendada. Sin embargo, evite siempre las referencias a la propiedad `Result` o las llamadas al método `Wait` en una instancia `Task`. Este enfoque puede provocar el agotamiento de subprocesos.


[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

Como Azure Functions usa Azure App Service, también debe conocer las guías de App Service.
* [Patterns and Practices HTTP Performance Optimizations](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/) (Patrones y procedimientos de optimización del rendimiento de HTTP)

