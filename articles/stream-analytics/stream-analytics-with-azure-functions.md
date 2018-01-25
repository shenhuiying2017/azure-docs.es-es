---
title: "Ejecución de Azure Functions con trabajos de Azure Stream Analytics | Microsoft Docs"
description: Aprenda a configurar Azure Functions como un receptor de salida para los trabajos de Stream Analytics.
keywords: salida de datos, datos de streaming, instancia de Azure Functions
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: f2f4a8d8cda752dc6ed197b8402119f7cbcaf58f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Ejecución de Azure Functions con trabajos de Azure Stream Analytics 

Puede ejecutar Azure Functions con Azure Stream Analytics al configurar Functions como uno de los receptores de salida para el trabajo de Stream Analytics. Functions es una experiencia de procesos a petición orientada a eventos que permite implementar el código desencadenado por los eventos que se producen en servicios de Azure o en servicios de terceros. Esta capacidad que tiene Functions para responder a los desencadenadores hace que sea una salida natural para los trabajos de Stream Analytics.

Stream Analytics llama a Functions mediante desencadenadores HTTP. El adaptador de salida de Functions permite a los usuarios conectar instancias de Functions a Stream Analytics, de manera que los eventos se puedan desencadenar basándose en consultas de Stream Analytics. 

En este tutorial se muestra cómo conectar Stream Analytics a [Azure Redis Cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md), mediante el uso de [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Configuración de un trabajo de Stream Analytics para que ejecute una función 

En esta sección se muestra cómo configurar un trabajo de Stream Analytics para ejecutar una función que escribe datos en Azure Redis Cache. El trabajo de Stream Analytics lee eventos de Azure Event Hubs y ejecuta una consulta que invoca a la función. Esta función lee los datos del trabajo de Stream Analytics y los escribe en Azure Redis Cache.

![Diagrama que muestra las relaciones entre los servicios de Azure](./media/stream-analytics-with-azure-functions/image1.png)

Para llevar a cabo esta tarea, se requieren los siguientes pasos:
* [Creación de un trabajo de Stream Analytics con Event Hubs como entrada](#create-stream-analytics-job-with-event-hub-as-input)  
* [Creación de una instancia de Azure Redis Cache](#create-an-azure-redis-cache)  
* [Creación de una función en Azure Functions que pueda escribir datos en Azure Redis Cache](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Actualización del trabajo de Stream Analytics con la función como salida](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Búsqueda de resultados en Azure Redis Cache](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Creación de un trabajo de Stream Analytics con Event Hubs como entrada

Siga el tutorial [Detección de fraudes en tiempo real](stream-analytics-real-time-fraud-detection.md) para crear un centro de eventos, iniciar la aplicación del generador de eventos y crear un trabajo de Stream Analytics. (Omita los pasos para crear la consulta y la salida. En su lugar, consulte las secciones siguientes para configurar la salida de Functions).

## <a name="create-an-azure-redis-cache-instance"></a>Creación de una instancia de Azure Redis Cache

1. Cree una caché en Azure Redis Cache mediante los pasos descritos en [Creación de una caché](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Después de crear la caché, en **Configuración**, seleccione **Teclas de acceso**. Anote la **cadena de conexión principal**.

   ![Captura de pantalla de la cadena de conexión de Azure Redis Cache](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Creación de una función en Azure Functions que pueda escribir datos en Azure Redis Cache

1. Consulte la sección [Creación de una aplicación de función](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) de la documentación de Functions. Esto explica cómo crear una aplicación de función y una [función desencadenada por HTTP en Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function) mediante el lenguaje CSharp.  

2. Vaya a la función **run.csx**. Actualícela con el código siguiente. (Asegúrese de reemplazar "\<su cadena de conexión de redis cache va aquí\>" por la cadena de conexión principal de Azure Redis Cache que recuperó en la sección anterior).  

   ```csharp
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
    }    

   ```

   Cuando Stream Analytics recibe una excepción "La entidad de solicitud HTTP es demasiado grande" de una función, disminuye el tamaño de los lotes que envía a Azure Functions. En la función, utilice el código siguiente para comprobar que Stream Analytics no envía lotes demasiado grandes. Asegúrese de que los valores de tamaño y número máximo de lotes que se usan en la función sean coherentes con los valores que se ingresan en el portal de Stream Analytics.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. En el editor de texto de su elección, cree un archivo JSON denominado **project.json**. Utilice el código siguiente y guárdelo en el equipo local. Este archivo contiene las dependencias de paquetes NuGet requeridas por la función de C#.  
   
   ```json
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

   ```
 
4. Vuelva a Azure Portal. Desde la pestaña **Características de la plataforma**, vaya a la función. En **Herramientas de desarrollo**, seleccione **Editor de App Service**. 
 
   ![Captura de pantalla del editor de App Service](./media/stream-analytics-with-azure-functions/image3.png)

5. En Editor de App Service, haga clic con el botón derecho en el directorio raíz y cargue el archivo **project.json**. Cuando la carga se haya realizado correctamente, actualice la página. Ahora debería ver un archivo generado automáticamente llamado **project.lock.json**. El archivo generado automáticamente contiene referencias a los archivos .dll que se especifican en el archivo project.json.  

   ![Captura de pantalla del editor de App Service](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Actualización del trabajo de Stream Analytics con la función como salida

1. En Azure Portal, abra el trabajo de Stream Analytics.  

2. Vaya a la función y seleccione **Información general** > **Salidas** > **Agregar**. Para agregar una nueva salida, seleccione **Función de Azure** para la opción de receptor. El nuevo adaptador de salida de Functions está disponible con las propiedades siguientes:  

   |**Nombre de propiedad**|**Descripción**|
   |---|---|
   |Alias de salida| Nombre descriptivo que se usará en la consulta del trabajo para hacer referencia a la salida. |
   |Opción de importación| Puede utilizar la función de la suscripción actual o proporcionar la configuración manualmente si la función se encuentra en otra suscripción. |
   |Function App| Nombre de la aplicación de Functions |
   |Función| Nombre de la función de la aplicación de Functions (nombre de la función run.csx).|
   |Tamaño máximo de lote|Establece el tamaño máximo de cada lote de salida, que se envía a la función. De forma predeterminada, este valor se establece en 256 KB.|
   |Número máximo de lotes|Especifica el número máximo de eventos de cada lote que se envía a la función. El valor predeterminado es 100. Esta propiedad es opcional.|
   |Clave|Permite usar una función de otra suscripción. Proporcione el valor de clave para acceder a la función. Esta propiedad es opcional.|

3. Proporcione un nombre para el alias de salida. En este tutorial, lo vamos a llamar **saop1** (puede utilizar el nombre que desee). Rellene otros detalles.  

4. Abra el trabajo de Stream Analytics y actualice la consulta a lo siguiente. (Asegúrese de reemplazar el texto "saop1" si se ha denominado el receptor de salida de manera diferente).  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Inicie la aplicación telcodatagen.exe ejecutando el comando siguiente en la línea de comandos (use el formato `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Inicie el trabajo de Stream Analytics.

## <a name="check-azure-redis-cache-for-results"></a>Búsqueda de resultados en Azure Redis Cache

1. Vaya a Azure Portal y encuentre su instancia de Azure Redis Cache. Seleccione **Consola**.  

2. Utilice los [comandos de Redis Cache](https://redis.io/commands) para comprobar que los datos están en Redis Cache. (El comando toma el formato Get {key}). Por ejemplo: 

   **Get "12/19/2017 21:32:24 - 123414732"**

   Este comando debe imprimir el valor de la clave especificada:

   ![Captura de pantalla de la salida de Azure Redis Cache](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Problemas conocidos

En Azure Portal, al intentar restablecer el valor de Tamaño máximo de lotes o Número máximo de lotes en valor vacío (predeterminado), el valor vuelve a cambiar al valor especificado anteriormente al guardar. Especifique manualmente los valores predeterminados para estos campos en este caso.

