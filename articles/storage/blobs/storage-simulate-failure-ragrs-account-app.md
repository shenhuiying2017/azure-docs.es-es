---
title: "Simulación de un error al acceder al almacenamiento con redundancia geográfica con acceso de lectura en Azure | Microsoft Docs"
description: "Simulación de un error al acceder al almacenamiento con redundancia geográfica con acceso de lectura"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/05/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 151e875bd72598b0b788d68eee7fb186fca86f46
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simulación de un error al acceder al almacenamiento con redundancia con acceso de lectura

Este tutorial es la segunda parte de una serie. En este tutorial, se inserta una respuesta con error mediante Fiddler para solicitudes a una cuenta de almacenamiento con [redundancia geográfica de solo lectura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) para simular un error y leer la aplicación desde el punto de conexión secundario.

![Aplicación de escenario](media/storage-simulate-failure-ragrs-account-app/scenario.png)

En la segunda parte de la serie, se aprende a:

> [!div class="checklist"]
> * Ejecutar y pausar la aplicación
> * Simular un error
> * Simular la restauración del punto de conexión principal

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

* Descargue e instale [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Para completar este tutorial, debe haber completado el tutorial anterior de almacenamiento: [Make your application data highly available with Azure storage][previous-tutorial] (Habilitar la alta disponibilidad de los datos de la aplicación con Azure Storage).

## <a name="launch-fiddler"></a>Inicio de Fiddler

Abra Fiddler, seleccione **Rules** (Reglas) y **Customize Rules** (Personalizar reglas).

![Personalización de reglas de Fiddler](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Se inicia Fiddler ScriptEditor, donde se muestra el archivo **SampleRules.js**. Este archivo se utiliza para personalizar Fiddler. Pegue el código de ejemplo siguiente en la función `OnBeforeResponse`. El nuevo código se comenta para garantizar que la lógica que crea no se implemente de inmediato. Cuando haya terminado, seleccione **File** (Archivo) y **Save** (Guardar) para guardar los cambios.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![Pegar la regla personalizada](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>Inicio y pausa de la aplicación

En Visual Studio, presione **F5** o seleccione **Iniciar** para iniciar la depuración de la aplicación. Una vez que la aplicación comienza a leer desde el punto de conexión principal, presione **cualquier tecla** en la ventana de consola para pausar la aplicación.

## <a name="simulate-failure"></a>Simulación de error

Con la aplicación en pausa ahora puede quitar la marca de comentario de la regla personalizada que se guardó en Fiddler en el paso anterior. Este código de ejemplo busca las solicitudes realizadas a la cuenta de almacenamiento con RA-GRS y, si la ruta de acceso contiene el nombre de la imagen, `HelloWorld`, devuelve un código de respuesta de `503 - Service Unavailable`.

Vaya a Fiddler y seleccione **Rules** (Reglas) -> **Customize Rules...** (Personalizar reglas...).  Quite la marca de comentario de las líneas siguientes y reemplace `STORAGEACCOUNTNAME` con el nombre de la cuenta de almacenamiento. Seleccione **File** (Archivo) -> **Save** (Guardar) para guardar los cambios.

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Para reanudar la aplicación, presione **cualquier tecla**.

Una vez que la aplicación empieza a ejecutarse de nuevo, las solicitudes al punto de conexión principal empiezan a producir errores. La aplicación intenta volver a conectarse al punto de conexión principal cinco veces. Después del umbral de error de cinco intentos, solicita la imagen desde el punto de conexión secundario de solo lectura. Una vez que la aplicación recupera la imagen veinte veces desde el punto de conexión secundario, la aplicación intenta conectarse al punto de conexión principal. Si aún no se puede acceder al punto de conexión principal, la aplicación reanuda la lectura desde el punto de conexión secundario. Este patrón es el patrón de [interruptor](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) descrito en el tutorial anterior.

![Pegar la regla personalizada](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>Simular la restauración del punto de conexión principal

Con la regla personalizada de Fiddler establecida en el paso anterior, las solicitudes al punto de conexión principal producen errores. Para simular de nuevo el funcionamiento del punto de conexión principal, elimine la lógica para insertar el error `503`.

Para pausar la aplicación, presione **cualquier tecla**.

### <a name="remove-the-custom-rule"></a>Eliminación de la regla personalizada

Vaya a Fiddler y seleccione **Rules** (Reglas) y **Customize Rules...** (Personalizar reglas...).  Agregue un comentario o quite la lógica personalizada en la función `OnBeforeResponse`, dejando la función predeterminada. Seleccione **File** (Archivo) y **Save** (Guardar) para guardar los cambios.

![Eliminación de la regla personalizada](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Cuando haya terminado, presione **cualquier tecla** para reanudar la aplicación. La aplicación continúa leyendo desde el punto de conexión principal hasta que hace novecientas noventa y nueve lecturas.

![Reanudación de la aplicación](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>pasos siguientes

En la segunda parte de la serie, aprendió a simular un error para probar el almacenamiento con redundancia geográfica de solo lectura, como los procedimientos para:

> [!div class="checklist"]
> * Ejecutar y pausar la aplicación
> * Simular un error
> * Simular la restauración del punto de conexión principal

Siga este vínculo para ver ejemplos de almacenamiento previamente creados.

> [!div class="nextstepaction"]
> [Ejemplos de script de almacenamiento de Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md