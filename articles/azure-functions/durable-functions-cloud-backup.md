---
title: 'Escenarios de distribución ramificada de entrada/salida en Durable Functions: Azure'
description: Aprenda a implementar un escenario de distribución ramificada de entrada y salida en la extensión Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e7b7b6af1f41eb0077d8a8605eb2a553c251f8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763855"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Escenario de distribución ramificada de entrada/salida en Durable Functions: ejemplo de copia de seguridad en la nube

La *distribución ramificada de entrada y salida* hace referencia al patrón de ejecución simultánea de varias funciones y la agregación de resultados. En este artículo se explica un ejemplo que usa [Durable Functions](durable-functions-overview.md) para implementar un escenario de distribución ramificada de entrada y salida. El ejemplo es una instancia de Durable Functions que realiza una copia de seguridad parcial o total del contenido de una aplicación en Azure Storage.

## <a name="prerequisites"></a>requisitos previos

* [Instale Durable Functions](durable-functions-install.md).
* Complete el tutorial de [Hello Sequence](durable-functions-sequence.md).

## <a name="scenario-overview"></a>Información general de escenario

En este ejemplo, las funciones cargan todos los archivos de un directorio especificado de forma repetitiva en Blob Storage. También cuenta el número de bytes que se han cargado.

Es posible escribir una sola función que se encargue de todo. El problema principal que encontraría es la **escalabilidad**. La ejecución de una función solo se puede realizar en una única máquina virtual, por lo que el rendimiento se verá limitado por el de esa máquina virtual concreta. Otro problema es la **confiabilidad**. Si se produce un error a mitad de camino, o si todo el proceso tarda más de 5 minutos, se podría producir un error de copia de seguridad en un estado completado parcialmente, por lo que tendría que reiniciarse.

Un enfoque más sólido sería escribir dos funciones normales: una que enumere los archivos y agregue los nombres de archivo a una cola, y otra que lea de la cola y cargue los archivos en Blob Storage. Esto es mejor en cuanto a rendimiento y confiabilidad, pero deberá aprovisionar y administrar una cola. Más importante aún, se introduce una complejidad considerable en términos de **administración de estado** y **coordinación** si desea hacer algo más, como notificar el total de bytes cargados.

Los enfoques de Durable Functions proporcionan todas las ventajas mencionadas con muy poca sobrecarga.

## <a name="the-functions"></a>Funciones

En este artículo se explican las funciones siguientes en la aplicación de ejemplo:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

En las siguientes secciones se explican la configuración y el código que se utilizan para el scripting C#. Al final del artículo se muestra el código para el desarrollo de Visual Studio.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>La orquestación de copia de seguridad en la nube (código de ejemplo de Azure Portal y Visual Studio Code)

La función `E2_BackupSiteContent` utiliza la norma *function.json* para las funciones de orquestador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Este es el código que implementa la función de orquestador:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (solo Functions v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Esta función de orquestador básicamente hace lo siguiente:

1. Toma un valor `rootDirectory` como parámetro de entrada.
2. Llama a una función para obtener una lista recursiva de los archivos de `rootDirectory`.
3. Realiza varias llamadas de función paralelas para cargar los archivos en Azure Blob Storage.
4. Espera a que finalicen todas las cargas.
5. Devuelve los bytes totales que se han cargado en Azure Blob Storage.

Observe la línea `await Task.WhenAll(tasks);` (C#) y `yield context.df.Task.all(tasks);` (JS). *No* se esperó a ninguna de las llamadas a la función `E2_CopyFileToBlob`. Se hace a propósito para que se ejecuten en paralelo. Cuando se pasa esta matriz de tareas a `Task.WhenAll`, obtenemos una tarea que no finalizará *hasta que se completen todas las operaciones de copia*. Si está familiarizado con la biblioteca TPL en. NET, esto no supondrá una novedad para usted. La diferencia es que estas tareas se pueden ejecutar en varias máquinas virtuales al mismo tiempo y la extensión Durable Functions garantiza que la ejecución de un extremo a otro es resistente al reciclaje de procesos.

Las tareas son muy similares al concepto de JavaScript de promesas. Sin embargo, `Promise.all` presenta algunas diferencias en relación a `Task.WhenAll`. El concepto de `Task.WhenAll` se ha transferido como parte del módulo de JavaScript `durable-functions` y es exclusivo de él.

Después de esperar a `Task.WhenAll` (o retener a `context.df.Task.all`), sabemos que todas las llamadas de función han finalizado y nos han devuelto valores. Cada llamada a `E2_CopyFileToBlob` devuelve el número de bytes cargados, por lo que calcular el recuento total de bytes es cuestión de agregar todos los valores devueltos.

## <a name="helper-activity-functions"></a>Funciones auxiliares de actividad

Las funciones auxiliares de actividad, al igual que otros ejemplos, son básicamente funciones normales que usan el desencadenador de enlace `activityTrigger`. Por ejemplo, el archivo *function.json* para `E2_GetFileList` tiene el siguiente aspecto:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Y esta es la implementación:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (solo Functions v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

La implementación de JavaScript de `E2_GetFileList` usa el módulo `readdirp` para leer de forma recursiva la estructura de directorios.

> [!NOTE]
> Tal vez se pregunte por qué no se podía poner directamente el código en la función de orquestador. Se podría, pero esto infringiría una de las reglas fundamentales de funciones de orquestador, que es que nunca hacen E/S, acceso al sistema de archivos local incluido.

El archivo *function.json* para `E2_CopyFileToBlob` es igual de simple:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

La implementación C# también es bastante sencilla. Usa características de enlace de Azure Functions más avanzadas (es decir, el parámetro `Binder`), pero no es necesario preocuparse de esos detalles para este tutorial.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (solo Functions v2)

La implementación de JavaScript no tiene acceso a la característica `Binder` de Azure Functions, por lo que [Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node) toma su lugar. Tenga en cuenta que el SDK requiere un valor de configuración de la aplicación `AZURE_STORAGE_CONNECTION_STRING`.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

Con la implementación se carga el archivo desde el disco y transmite de forma asincrónica el contenido a un blob con el mismo nombre en el contenedor "backups". El valor devuelto es el número de bytes copiados en el almacenamiento, que utilizará la función de orquestador para calcular el agregado total.

> [!NOTE]
> Es un ejemplo perfecto de cómo se mueven las operaciones de E/S a una función `activityTrigger`. No solo se puede distribuir el trabajo en muchas máquinas virtuales diferentes, sino que también obtendrá las ventajas de establecer puntos de control del progreso. Si el proceso de host se finaliza por alguna razón, sabrá qué cargas ya se han completado.

## <a name="run-the-sample"></a>Ejecución del ejemplo

Puede iniciar la orquestación mediante el envío de la siguiente solicitud HTTP POST.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> La función `HttpStart` que se está invocando solo funciona con contenido con formato JSON. Por este motivo, se necesita el encabezado `Content-Type: application/json` y se codifica la ruta de acceso de directorio como cadena JSON.

Esta solicitud HTTP desencadena el orquestador `E2_BackupSiteContent` y pasa la cadena `D:\home\LogFiles` como un parámetro. La respuesta proporciona un vínculo para obtener el estado de esta operación de copia de seguridad:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Dependiendo de cuántos archivos de registro tenga en la aplicación de función, esta operación puede tardar varios minutos en completarse. Puede obtener el estado más reciente al consultar la dirección URL en el encabezado `Location` de la respuesta HTTP 202 anterior.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

En este caso, la función todavía se está ejecutando. Es posible que vea la entrada que se guardó en el estado de orquestador y la hora de la última actualización. Se pueden seguir usando los valores del encabezado `Location` para sondear la finalización. Cuando el estado sea "Completado", verá un valor de respuesta HTTP similar al siguiente:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Ahora verá que la orquestación ha finalizado y el tiempo aproximado que tardó en completarse. También verá un valor para el campo `output`, lo que indica que se han cargado alrededor de 450 KB de registros.

## <a name="visual-studio-sample-code"></a>Código de ejemplo de Visual Studio

Esta es la orquestación como archivo único de C# en un proyecto de Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Pasos siguientes

Este ejemplo ha mostrado cómo implementar el modelo de distribución ramificada de salida y entrada. En el ejemplo siguiente se muestra cómo implementar el patrón de supervisión con [temporizadores durables](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [Ejecutar el ejemplo de supervisión](durable-functions-monitor.md)
