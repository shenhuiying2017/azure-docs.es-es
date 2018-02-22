---
title: "Simulación de un error al acceder al almacenamiento con redundancia geográfica con acceso de lectura en Azure | Microsoft Docs"
description: "Simulación de un error al acceder al almacenamiento con redundancia geográfica con acceso de lectura"
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: tutorial
ms.date: 12/23/2017
ms.author: v-ruogun
ms.openlocfilehash: 9ebf773cf39d832416dce820e67201c21a679296
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>Simulación de un error al acceder al almacenamiento con redundancia con acceso de lectura

Este tutorial es la segunda parte de una serie.  En este tutorial, puede usar [Fiddler](#simulate-a-failure-with-fiddler) o [Enrutamiento estático](#simulate-a-failure-with-an-invalid-static-route) para simular un error de solicitud en el punto de conexión principal de la cuenta de almacenamiento [con redundancia geográfica con acceso de lectura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) y que la aplicación se lea desde el punto de conexión secundario.

![Aplicación de escenario](media/storage-simulate-failure-ragrs-account-app/scenario.png)

Para completar este tutorial, debe haber completado el tutorial anterior de almacenamiento: [Make your application data highly available with Azure storage][previous-tutorial] (Habilitar la alta disponibilidad de los datos de la aplicación con Azure Storage).

En la segunda parte de la serie, se aprende a:

> [!div class="checklist"]
> * Ejecutar y pausar la aplicación
> * Simular un error con [Fiddler](#simulate-a-failure-with-fiddler) o [una ruta estática no válida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simular la restauración del punto de conexión principal


## <a name="prerequisites"></a>requisitos previos

Para simular un error mediante Fiddler: 

* Descargue e [instale Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="simulate-a-failure-with-fiddler"></a>Simule un error con Fiddler

Para simular un error con Fiddler, inserte una respuesta de error para las solicitudes al punto de conexión principal de la cuenta de almacenamiento con redundancia geográfica con acceso de lectura.

Siga los pasos a continuación para simular un error y la restauración del punto de conexión principal con Fiddler.

### <a name="launch-fiddler"></a>Inicio de Fiddler

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

### <a name="start-and-pause-the-application"></a>Inicio y pausa de la aplicación

Ejecute la aplicación en el editor de texto o entorno de desarrollo integrado. Una vez que la aplicación comienza a leer desde el punto de conexión principal, presione **cualquier tecla** en la ventana de consola para pausar la aplicación.

### <a name="simulate-failure"></a>Simulación de error

Con la aplicación en pausa, ahora puede quitar la marca de comentario de la regla personalizada que se guardó en Fiddler en el paso anterior. El código de ejemplo busca las solicitudes realizadas a la cuenta de almacenamiento con redundancia geográfica con acceso de lectura y, si la ruta de acceso contiene el nombre de la imagen, `HelloWorld`, devuelve un código de respuesta de `503 - Service Unavailable`.

Vaya a Fiddler y seleccione **Rules** (Reglas) -> **Customize Rules...** (Personalizar reglas...).  Quite la marca de comentario de las líneas siguientes y reemplace `STORAGEACCOUNTNAME` con el nombre de la cuenta de almacenamiento. Seleccione **File** (Archivo) -> **Save** (Guardar) para guardar los cambios. 

> [!NOTE]
> Si ejecuta la aplicación de ejemplo en Linux, debe reiniciar Fiddler siempre que edite el archivo **CustomRule.js** para que Fiddler instale la lógica personalizada. 
> 
> 


```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

Para reanudar la aplicación, presione **cualquier tecla**.

Una vez que la aplicación empieza a ejecutarse de nuevo, las solicitudes al punto de conexión principal empiezan a producir errores. La aplicación intenta volver a conectarse al punto de conexión principal cinco veces. Después del umbral de error de cinco intentos, solicita la imagen desde el punto de conexión secundario de solo lectura. Una vez que la aplicación recupera la imagen veinte veces desde el punto de conexión secundario, la aplicación intenta conectarse al punto de conexión principal. Si aún no se puede acceder al punto de conexión principal, la aplicación reanuda la lectura desde el punto de conexión secundario. Este patrón es el patrón de [interruptor](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) descrito en el tutorial anterior.

![Pegar la regla personalizada](media/storage-simulate-failure-ragrs-account-app/figure3.png)

### <a name="simulate-primary-endpoint-restoration"></a>Simular la restauración del punto de conexión principal

Con la regla personalizada de Fiddler establecida en el paso anterior, las solicitudes al punto de conexión principal producen errores. Para simular de nuevo el funcionamiento del punto de conexión principal, elimine la lógica para insertar el error `503`.

Para pausar la aplicación, presione **cualquier tecla**.

Vaya a Fiddler y seleccione **Rules** (Reglas) y **Customize Rules...** (Personalizar reglas...).  Agregue un comentario o quite la lógica personalizada en la función `OnBeforeResponse`, dejando la función predeterminada. Seleccione **File** (Archivo) y **Save** (Guardar) para guardar los cambios.

![Eliminación de la regla personalizada](media/storage-simulate-failure-ragrs-account-app/figure5.png)

Cuando haya terminado, presione **cualquier tecla** para reanudar la aplicación. La aplicación continúa leyendo desde el punto de conexión principal hasta que hace novecientas noventa y nueve lecturas.

![Reanudación de la aplicación](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="simulate-a-failure-with-an-invalid-static-route"></a>Simulación de un error con una ruta estática no válida 
Puede crear una ruta estática no válida para todas las solicitudes al punto de conexión principal de la cuenta de almacenamiento [con redundancia geográfica con acceso de lectura](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS). En este tutorial, se utiliza el host local como puerta de enlace para enrutar las solicitudes a la cuenta de almacenamiento. El uso del host local como puerta de enlace hace que todas las solicitudes al punto de conexión principal de la cuenta de almacenamiento creen un bucle dentro del host que provoca errores. Siga los pasos a continuación para simular un error y la restauración del punto de conexión principal con una ruta estática no válida. 

### <a name="start-and-pause-the-application"></a>Inicio y pausa de la aplicación

Ejecute la aplicación en el editor de texto o entorno de desarrollo integrado. Una vez que la aplicación comienza a leer desde el punto de conexión principal, presione **cualquier tecla** en la ventana de consola para pausar la aplicación. 

### <a name="simulate-failure"></a>Simulación de error

Con la aplicación en pausa, inicie el símbolo del sistema en Windows como administrador o ejecute el terminal como raíz en Linux. Para información sobre el dominio del punto de conexión principal de la cuenta de almacenamiento, escriba el siguiente comando en un símbolo del sistema o el terminal.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
``` 
 Reemplace `STORAGEACCOUNTNAME` por el nombre de la cuenta de almacenamiento. Copie la dirección IP de la cuenta de almacenamiento en un editor de texto para usarla después. Para obtener la dirección IP del host local, escriba `ipconfig` en el símbolo del sistema de Windows o `ifconfig` en el terminal de Linux. 

Para agregar una ruta estática para un host de destino, escriba el siguiente comando en un símbolo del sistema de Windows o terminal de Linux. 


# <a name="linuxtablinux"></a>[Linux](#tab/linux)

  route add <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

  route add <destination_ip> <gateway_ip>

---
 
Reemplace `<destination_ip>` con la dirección IP de la cuenta de almacenamiento y `<gateway_ip>`, con la dirección IP del host local. Para reanudar la aplicación, presione **cualquier tecla**.

Una vez que la aplicación empieza a ejecutarse de nuevo, las solicitudes al punto de conexión principal empiezan a producir errores. La aplicación intenta volver a conectarse al punto de conexión principal cinco veces. Después del umbral de error de cinco intentos, solicita la imagen desde el punto de conexión secundario de solo lectura. Una vez que la aplicación recupera la imagen veinte veces desde el punto de conexión secundario, la aplicación intenta conectarse al punto de conexión principal. Si aún no se puede acceder al punto de conexión principal, la aplicación reanuda la lectura desde el punto de conexión secundario. Este patrón es el patrón de [interruptor](/azure/architecture/patterns/circuit-breaker.md) descrito en el tutorial anterior.

### <a name="simulate-primary-endpoint-restoration"></a>Simular la restauración del punto de conexión principal

Para simular que el punto de conexión principal vuelve a funcionar, elimine la ruta estática del punto de conexión principal de la tabla de enrutamiento. Esto permite que todas las solicitudes al punto de conexión principal se enruten mediante la puerta de enlace predeterminada. 

Para eliminar la ruta estática de un host de destino (cuenta de almacenamiento), escriba el siguiente comando en un símbolo del sistema de Windows o terminal de Linux. 
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)

route del <destination_ip> gw <gateway_ip>

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

route delete <destination_ip> <gateway_ip>

---

Para reanudar la aplicación, presione **cualquier tecla**. La aplicación continúa leyendo desde el punto de conexión principal hasta que hace novecientas noventa y nueve lecturas.

![Reanudación de la aplicación](media/storage-simulate-failure-ragrs-account-app/figure4.png)


## <a name="next-steps"></a>pasos siguientes

En la segunda parte de la serie, aprendió a simular un error para probar el almacenamiento con redundancia geográfica con acceso de lectura, como los procedimientos para:

> [!div class="checklist"]
> * Ejecutar y pausar la aplicación
> * Simular un error con [Fiddler](#simulate-a-failure-with-fiddler) o [una ruta estática no válida](#simulate-a-failure-with-an-invalid-static-route) 
> * Simular la restauración del punto de conexión principal

Siga este vínculo para ver ejemplos de almacenamiento previamente creados.

> [!div class="nextstepaction"]
> [Ejemplos de script de almacenamiento de Azure](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md