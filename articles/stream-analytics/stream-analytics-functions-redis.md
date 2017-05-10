---
title: Procesamiento en tiempo real de Stream Analytics para Azure Functions | Microsoft Docs
description: "Obtenga información sobre cómo usar una función de Azure conectada a una cola del Bus de servicio para rellenar una instancia de Caché en Redis de Azure desde la salida de un trabajo de Análisis de transmisiones."
keywords: "flujo de datos, caché en redis, cola del bus de servicio"
services: stream-analytics
author: ryancrawcour
manager: jhubbard
documentationcenter: 
ms.assetid: d428bb33-4244-4001-b93d-c77bed816527
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: ryancraw
ms.translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 3a915f782eddaa91bcfcc3f2b2c32eee752c319c
ms.contentlocale: es-es
ms.lasthandoff: 05/01/2017


---
# <a name="how-to-store-data-from-azure-stream-analytics-in-an-azure-redis-cache-using-azure-functions"></a>Almacenamiento de datos desde Análisis de transmisiones de Azure en Caché en Redis de Azure con Funciones de Azure
Análisis de transmisiones de Azure permite desarrollar e implementar rápidamente soluciones de bajo costo para obtener información en tiempo real de dispositivos, sensores, infraestructura y aplicaciones o cualquier transmisión de datos. Permite varios casos de uso, como la administración y supervisión en tiempo real, comando y control, detección de fraudes, automóviles conectados y mucho más. En muchos de estos escenarios, puede que desee almacenar datos generados por Análisis de transmisiones de Azure en un almacén de datos distribuidos, como Caché en Redis de Azure.

Imaginemos que forma parte de una empresa de telecomunicaciones. Intenta detectar un fraude de SIM en el que varias llamadas se originan en la misma identidad y al mismo tiempo, pero en ubicaciones geográficas distintas. Su tarea es almacenar todas las llamadas telefónicas potencialmente fraudulentas en una instancia de Caché en Redis de Azure. En este blog, le brindamos la orientación que necesita para poder completar fácilmente la tarea. 

## <a name="prerequisites"></a>Requisitos previos
Completar el tutorial [Detección de fraudes en tiempo real][fraud-detection] para ASA

## <a name="architecture-overview"></a>Introducción a la arquitectura
![Captura de pantalla de arquitectura](./media/stream-analytics-functions-redis/architecture-overview.png)

Tal como se muestra en la ilustración anterior, Análisis de transmisiones permite consultar la transmisión de datos de entrada y enviarlos a una salida. Según el resultado, Funciones de Azure puede desencadenar algún tipo de evento. 

En este blog, nos centramos en la parte de esta canalización que corresponde a Funciones de Azure, o más específicamente, en cómo desencadenar un evento que almacena datos fraudulentos en la caché.
Una vez que complete el tutorial [Detección de fraudes en tiempo real][fraud-detection], tiene una entrada (un centro de eventos), una consulta y una salida (almacenamiento de blobs) ya configuradas y en ejecución. En este blog, cambiamos la salida para usar en su lugar una cola del Bus de servicio. Luego, conectamos una Función de Azure con esta cola. 

## <a name="create-and-connect-a-service-bus-queue-output"></a>Creación y conexión de una salida de cola del Bus de servicio
Para crear una cola de Service Bus, siga los pasos 1 y 2 de la sección .NET que aparece en [Introducción a las colas de Service Bus][servicebus-getstarted].
Ahora vamos a conectar la cola con el trabajo de Análisis de transmisiones que se creó en el anterior tutorial de detección de fraudes.

1. En Azure Portal, vaya a la hoja **Salidas** del trabajo y seleccione **Agregar** en la parte superior de la página.
   
    ![Agregar salidas](./media/stream-analytics-functions-redis/adding-outputs.png)
2. Elija **Cola de Service Bus** en **Receptor** y siga las instrucciones que aparecen en la pantalla. Asegúrese de elegir el espacio de nombres de la cola de Service Bus que creó en [Introducción a las colas de Service Bus][servicebus-getstarted]. Haga clic en el botón "Correcto" cuando termine.
3. Especifique los siguientes valores:
   
   * **Formato del serializador de eventos**: JSON
   * **Codificación**: UTF8
   * **FORMATO**: separado por líneas
4. Haga clic en el botón **Crear** para agregar este origen y comprobar que Análisis de transmisiones puede conectarse correctamente a la cuenta de almacenamiento.
5. En la pestaña **Consulta** , reemplace la consulta actual por la siguiente. Reemplace *[EL NOMBRE DE SERVICE BUS] * por el nombre de la salida que creó en el paso 3. 
   
    ```    
   
        SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
   
        INTO [YOUR SERVICE BUS NAME]
   
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   
        WHERE CS1.SwitchNum != CS2.SwitchNum
   
    ```

## <a name="create-an-azure-redis-cache"></a>Creación de una instancia de Caché en Redis de Azure
Para crear una instancia de Caché en Redis de Azure, siga la sección .NET de [Uso de Caché en Redis de Azure][use-rediscache] hasta la sección llamada ***Configuración de los clientes de caché***.
Una vez que finalice, tendrá una nueva instancia de Caché en Redis. En **Toda la configuración**, seleccione **Claves de acceso** y anote el contenido de ***Cadena de conexión principal***.

![Captura de pantalla de arquitectura](./media/stream-analytics-functions-redis/redis-cache-keys.png)

## <a name="create-an-azure-function"></a>Creación de una Función de Azure
Siga el tutorial [Creación de su primera función de Azure][functions-getstarted] para comenzar a trabajar con Azure Functions. Si ya cuenta con una función de Azure que desea usar, omita este paso y vaya a [Escritura en Caché en Redis](#Writing-to-Redis-Cache)

1. En el portal, seleccione App Services en el menú de navegación de la izquierda y haga clic en el nombre de la Function App de Azure para ir al sitio web de esta.
    ![Captura de pantalla de lista de funciones de App Services](./media/stream-analytics-functions-redis/app-services-function-list.png)
2. Haga clic en **Nueva función > ServiceBusQueueTrigger – C#**. Siga estas instrucciones en los siguientes campos:
   
   * **Nombre de cola**: el mismo nombre que escribió cuando creó la cola en [Introducción a las colas de Service Bus][servicebus-getstarted] (no el nombre del bus de servicio). Asegúrese de usar la cola que está conectada con la salida de Análisis de transmisiones.
   * **Conexión de Service Bus**: seleccione **Agregar una cadena de conexión**. Para encontrar la cadena de conexión, vaya al portal clásico, seleccione **Service Bus**, el bus de servicio que creó y luego **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la pantalla. Asegúrese de estar en la pantalla principal de esta página. Copie y pegue la cadena de conexión. Puede escribir cualquier nombre de conexión.
     
       ![Captura de pantalla de conexión del Bus de servicio](./media/stream-analytics-functions-redis/servicebus-connection.png)
   * **AccessRights**: elija **Administrar**.
3. Haga clic en **Crear**

## <a name="writing-to-redis-cache"></a>Escritura en Caché en Redis
Creamos una función de Azure que lee desde una cola del Bus de servicio. Todo lo que queda por hacer es usar la función para escribir estos datos en Caché en Redis. 

1. Seleccione la función **ServiceBusQueueTrigger** que acaba de crear y haga clic en **Configuración de Function App** en la esquina superior derecha. Seleccione **Ir a Configuración de App Service > Configuración > Configuración de la aplicación**.
2. En la sección Cadenas de conexión, cree un nombre en la sección **Nombre** . Pegue la cadena de conexión principal que encontró en el paso **Creación de una instancia de Redis Cache** en la sección **Valor**. Seleccione **Personalizado** donde aparece **SQL Database**.
3. Haga clic en **Guardar** en la parte superior.
   
    ![Captura de pantalla de conexión del Bus de servicio](./media/stream-analytics-functions-redis/function-connection-string.png)
4. Vuelva a Configuración de App Service y seleccione **Herramientas > Editor de App Service (versión preliminar) > Activar > Ir**.
   
    ![Captura de pantalla de conexión del Bus de servicio](./media/stream-analytics-functions-redis/app-service-editor.png)
5. En un editor de su preferencia, cree un archivo JSON llamado **project.json** que incluya lo siguiente y guárdelo en el disco local.
   
        {
            "frameworks": {
                "net46": {
                    "dependencies": {
                        "StackExchange.Redis":"1.1.603",
                        "Newtonsoft.Json": "9.0.1"
                    }
                }
            }
        }
6. Cargue este archivo en el directorio raíz de la función (no en WWWROOT). Debería aparecer automáticamente un archivo llamado **project.lock.json** ,con la confirmación de que se importaron los paquetes NuGet "StackExchange.Redis" y "Newtonsoft.Json".
7. En el archivo **run.csx** , reemplace el código generado previamente por el código siguiente. En la función lazyConnection, reemplace "CONN NAME" por el nombre que creó en el paso 2 de **Almacenamiento de datos en Caché en Redis**.

````

    using System;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static void Run(string myQueueItem, TraceWriter log)
    {
        log.Info($"Function processed message: {myQueueItem}");

        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = Connection.GetDatabase();
        log.Info($"Created database {db}");

        // Parse JSON and extract the time
        var message = JsonConvert.DeserializeObject<dynamic>(myQueueItem);
        string time = message.time;
        string callingnum1 = message.callingnum1;

        // Perform cache operations using the cache object...
        // Simple put of integral data types into the cache
        string key = time + " - " + callingnum1;
        db.StringSet(key, myQueueItem);
        log.Info($"Object put in database. Key is {key} and value is {myQueueItem}");

        // Simple get of data types from the cache
        string value = db.StringGet(key);
        log.Info($"Database got: {value}"); 
    }

    // Connect to the Service Bus
    private static Lazy<ConnectionMultiplexer> lazyConnection = 
        new Lazy<ConnectionMultiplexer>(() =>
            {
                var cnn = ConfigurationManager.ConnectionStrings["CONN NAME"].ConnectionString
                return ConnectionMultiplexer.Connect();
            });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }

````

## <a name="start-the-stream-analytics-job"></a>Inicio del trabajo de Análisis de transmisiones
1. Inicie la aplicación telcodatagen.exe. Se usa de esta manera: ````telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]````
2. En la hoja del trabajo de Análisis de transmisiones del portal, haga clic en **Iniciar** en la parte superior de la página.
   
    ![Captura de pantalla de Iniciar trabajo](./media/stream-analytics-functions-redis/starting-job.png)
3. En la hoja **Iniciar trabajo** que aparece, seleccione **Ahora** y haga clic en el botón **Iniciar** en la parte inferior de la pantalla. El estado del trabajo cambia a Iniciando y, luego de un tiempo, cambia a En ejecución.
   
    ![Captura de pantalla de selección de tiempo de Iniciar trabajo](./media/stream-analytics-functions-redis/start-job-time.png)

## <a name="run-solution-and-check-results"></a>Ejecución de la solución y comprobación de los resultados
Vuelva a la página **ServiceBusQueueTrigger** , donde debería ver instrucciones de registro. Estos registros muestran que obtuvo algún elemento de la cola del Bus de servicio, lo puso en la base de datos y lo recuperó con el tiempo como clave.

Para comprobar que los datos están en la Caché en Redis, vaya a la página de Caché en Redis del portal nuevo (como aparece en el paso anterior, [Creación de una instancia de Caché en Redis de Azure](#Create-an-Azure-Redis-Cache) ) y seleccione Consola.

Ahora puede escribir comandos de Redis para confirmar que los datos están, de hecho, en la caché.

![Captura de pantalla de Consola de Redis](./media/stream-analytics-functions-redis/redis-console.png)

## <a name="next-steps"></a>Pasos siguientes
Estamos entusiasmados con las nuevas acciones que Análisis de transmisiones y Funciones de Azure pueden hacer en conjunto. Además, esperamos que esta unión le brinde nuevas posibilidades. Si tiene comentarios sobre lo que espera a continuación, no dude en usar el [sitio de Azure UserVoice](https://feedback.azure.com/forums/270577-stream-analytics).

Si es primera vez que usa Microsoft Azure, le invitamos a probarlo y registrarse para obtener una [cuenta de evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/). Si es primera vez que usa Análisis de transmisiones, le invitamos a [crear su primer trabajo de Análisis de transmisiones](stream-analytics-create-a-job.md).

Si necesita ayuda o tiene alguna pregunta, publíquela en [MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics) o en los foros de [Stackoverflow](http://stackoverflow.com/questions/tagged/azure-stream-analytics). 

También puede consultar los siguientes recursos:

* [Referencia para desarrolladores de Funciones de Azure](../azure-functions/functions-reference.md)
* [Referencia para desarrolladores de C# de Funciones de Azure](../azure-functions/functions-reference-csharp.md)
* [Referencia para desarrolladores de F# de Azure Functions](../azure-functions/functions-reference-fsharp.md)
* [Referencia para desarrolladores de NodeJS de Funciones de Azure](../azure-functions/functions-reference.md)
* [Enlaces y desencadenadores de las Funciones de azure](../azure-functions/functions-triggers-bindings.md)
* [Supervisión de Caché en Redis de Azure](../redis-cache/cache-how-to-monitor.md)

Para mantenerse al día con las noticias y características más recientes, siga la cuenta [@AzureStreaming](https://twitter.com/AzureStreaming) en Twitter.

[fraud-detection]: stream-analytics-real-time-fraud-detection.md
[servicebus-getstarted]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[use-rediscache]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md
[functions-getstarted]: ../azure-functions/functions-create-first-azure-function.md

