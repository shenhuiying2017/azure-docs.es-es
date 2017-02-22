---
title: Procedimientos recomendados de Azure Functions | Microsoft Docs
description: "Información acerca de los procedimientos recomendados y los patrones de Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "azure functions, patrones, procedimientos recomendados, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/09/2016
ms.author: wesmc
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: c638bf42b9adf906f195d77268637d056f7b00a9

---

# <a name="best-practices-for-azure-functions"></a>Procedimientos recomendados de Azure Functions

##<a name="overview"></a>Información general

En este artículo se proporciona una colección de prácticas recomendadas para tener en cuenta al implementar aplicaciones de funciones. Tenga en cuenta que Azure Function App es un Azure App Service. Por tanto, se aplicarán los procedimientos recomendados.


## <a name="avoid-large-long-running-functions"></a>Evitar funciones grandes de ejecución prolongada

Las funciones grandes de ejecución prolongada pueden causar problemas de tiempo de espera inesperados. Una función puede ser grande debido a sus numerosas dependencias de Node.js. La importación de estas dependencias puede provocar mayores tiempos de carga que dan lugar a tiempos de espera inesperados. Las dependencias de Node.js se podrían cargar explícitamente mediante varias instrucciones `require()` en el código. También podrían ser implícitas según un único módulo cargado por el código que tiene sus propias dependencias internas.  

Siempre que sea posible, refactorice funciones grandes en conjuntos más pequeños de funciones que trabajen juntos y devuelvan respuestas rápidas. Por ejemplo, un webhook o una función de desencadenador HTTP podría requerir una respuesta de confirmación en un determinado período de tiempo. Puede pasar la carga útil de desencadenador HTTP a una cola para ser procesada por una función de desencadenador de cola. Este enfoque permite aplazar el trabajo real y devolver una respuesta inmediata. Es habitual que los webhooks requieran una respuesta inmediata.


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



## <a name="use-async-code-but-avoid-taskresult"></a>Usar código asincrónico pero evitar Task.Result

La programación asincrónica es una práctica recomendada. Sin embargo, evite siempre hacer referencia a la propiedad `Task.Result`. Este enfoque básicamente realiza una espera activa en un bloqueo de otro subproceso. El mantenimiento de un bloqueo crea la posibilidad de interbloqueos.




## <a name="next-steps"></a>Pasos siguientes
Para obtener más información, consulte los siguientes recursos:

* [Referencia para desarrolladores de Funciones de Azure](functions-reference.md)
* [Referencia para desarrolladores de C# de Funciones de Azure](functions-reference-csharp.md)
* [Referencia para desarrolladores de F# de Azure Functions](functions-reference-fsharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](functions-reference-node.md)




<!--HONumber=Feb17_HO2-->


