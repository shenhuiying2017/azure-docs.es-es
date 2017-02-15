---
title: "Análisis de sentimiento en tiempo real de Twitter con Stream Analytics | Microsoft Docs"
description: "Aprenda a usar Análisis de transmisiones para el análisis en tiempo real de opinión de Twitter. Pasos desde la generación de eventos a los datos en un panel dinámico."
keywords: "análisis de tendencias de twitter en tiempo real, análisis de opiniones, análisis de medios sociales, ejemplo de análisis de tendencias"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 3c97604b17636f011ddb2acda40fbc77afeab590
ms.openlocfilehash: 9f7e9008f29b2b1a3a0422133e15871c4ce7cca8


---
# <a name="social-media-analysis-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análisis de los medios sociales: análisis en tiempo real de la opinión en Twitter en Análisis de transmisiones de Azure
Aprenda a crear una solución de análisis de sentimiento para análisis de redes sociales al poner eventos en tiempo real de Twitter en Azure Event Hubs. Escribirá una consulta de Azure Stream Analytics para analizar los datos. Después, almacenará los resultados para un examen posterior o usará un panel y [Power BI](https://powerbi.com/) para ofrecer perspectivas en tiempo real.

Las herramientas de análisis de los medios sociales ayudan a las organizaciones a comprender las tendencias, es decir, los temas y actitudes que cuentan con un gran volumen de publicaciones en las redes sociales. Los análisis de sentimientos, denominados también *minería de opinión*, usan las herramientas de análisis de medios sociales para determinar las actitudes hacia un producto, una idea, etc. El análisis de tendencias de Twitter en tiempo real es un buen ejemplo porque el modelo de suscripción hashtag le permite escuchar palabras clave específicas y desarrollar análisis de sentimientos en la fuente.

## <a name="scenario-sentiment-analysis-in-real-time"></a>Escenario: Análisis de sentimientos en tiempo real
Una empresa que tiene un sitio web multimedia de noticias está interesada en obtener una ventaja sobre sus competidores al proporcionar contenido del sitio inmediatamente relevante para sus lectores. La empresa usa análisis de las redes sociales en temas relevantes para sus lectores mediante la elaboración de análisis de sentimientos en tiempo real de los datos de Twitter. En concreto, para identificar los temas que son tendencias en tiempo real en Twitter, la empresa necesita el análisis en tiempo real del volumen de tweets y la opinión sobre los temas clave. Por lo que en esencia requieren un motor que analice los análisis de sentimientos basado en esta fuente de medios sociales.

## <a name="prerequisites"></a>Requisitos previos
* Cuenta de Twitter y [token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) del Centro de descarga de Microsoft
* Opcional: código fuente de cliente de Twitter desde [Github](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Crear una entrada del centro de eventos y un grupo de consumidores
La aplicación de ejemplo genera eventos y los inserta en una instancia de Event Hubs (un centro de eventos, para abreviar). Los Event Hubs de Service Bus son el método preferido de ingesta de eventos para Stream Analytics. Consulte la documentación de Event Hubs en la [documentación de Azure Service Bus](/azure/service-bus/).

Use los pasos siguientes para crear un centro de eventos.

1. En Azure Portal, haga clic en **NUEVO** > **APP SERVICES** > **SERVICE BUS** > **CENTRO DE EVENTOS** > **CREACIÓN RÁPIDA** y especifique un nombre, una región y un espacio de nombres nuevo o existente.  
2. Como procedimiento recomendado, debe leer cada trabajo de Stream Analytics de un solo grupo de consumidores de Event Hubs. Le guiaremos a través del proceso de creación de un grupo de consumidores más adelante. Puede obtener más información sobre los grupos de consumidores en [información general de Azure Event Hubs](https://msdn.microsoft.com/library/azure/dn836025.aspx). Para crear un grupo de consumidores, vaya al centro de eventos recién creado, haga clic en la pestaña **GRUPOS DE CONSUMIDORES** y en **CREAR** en la parte inferior de la página y luego proporcione un nombre para el grupo de consumidores.
3. Para otorgar acceso al Centro de eventos, necesitamos crear una directiva de acceso compartido. Haga clic en la pestaña **CONFIGURAR** de su Centro de eventos.
4. En **DIRECTIVAS DE ACCESO COMPARTIDO**, cree una nueva directiva con permisos para **ADMINISTRAR**.

   ![Directivas de acceso compartido en las que puede crear una nueva directiva con permisos para Administrar.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)
5. Haga clic en **GUARDAR** en la parte inferior de la página.
6. Vaya al **PANEL**, haga clic en **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la página y luego copie y guarde la información de conexión. (Utilice el icono de copia que aparece bajo el icono de búsqueda).

## <a name="configure-and-start-the-twitter-client-application"></a>Configuración e inicio de la aplicación cliente Twitter
Hemos proporcionado una aplicación cliente que conectará datos con Twitter a través de las [API de streaming de Twitter](https://dev.twitter.com/streaming/overview) para recopilar eventos Tweet sobre un conjunto de temas con parámetros. La herramienta de código abierto [Sentiment140](http://help.sentiment140.com/) se usa para asignar un valor de sentimiento a cada tweet.

* 0 = negativo
* 2 = neutral
* 4 = positivo

Después, se insertan eventos Tweet en el centro de eventos.  

Siga estos pasos para configurar la aplicación:

1. [Descargue la solución TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2. Abra TwitterClient.exe.config y reemplace oauth_consumer_key, oauth_consumer_secret, oauth_token, oauth_token_secret con tokens de Twitter que tengan sus valores.  

   [Pasos para generar un token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   Tenga en cuenta que necesitará crear una aplicación vacía para generar un token.  
3. Reemplace los valores de EventHubConnectionString y EventHubName en el archivo TwitterClient.exe.config con la cadena de conexión y el nombre de su centro de eventos. La cadena de conexión que ha copiado anteriormente proporciona la cadena de conexión y el nombre de su centro de eventos, así que no se olvide de separar y colocar cada una de ellas en el campo correcto. Por ejemplo, considere la siguiente cadena de conexión:

     Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub

   El archivo TwitterClient.exe.config debe contener la configuración como en el ejemplo siguiente:

     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"   add key="EventHubName" value="yourhub"

   Es importante tener en cuenta que el texto "EntityPath =" **no** aparece en el valor de EventHubName.
4. *Opcional:* ajuste las palabras clave que se buscarán.  De forma predeterminada, esta aplicación busca "Azure, Skype, XBox, Microsoft, Seattle".  Puede ajustar los valores de twitter_keywords en el archivo **TwitterClient.exe.config**, si quiere.
5. Ejecute TwitterClient.exe para iniciar la aplicación. Verá los eventos Tweet con los valores de **CreatedAt**, **Topic** y **SentimentScore** que se envían al centro de eventos.

   ![Análisis de opinión: valores de SentimentScore enviados a un centro de eventos.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones
Ahora que los eventos Tweet se transmiten en tiempo real desde Twitter, podemos configurar un trabajo de Stream Analytics para analizar estos eventos en tiempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionamiento de un trabajo de Stream Analytics
1. En [Azure Portal](https://manage.windowsazure.com/), haga clic en **NUEVO** > **DATA SERVICES** > **STREAM ANALYTICS** > **CREACIÓN RÁPIDA**.
2. Especifique los valores siguientes y, a continuación, haga clic en **CREAR TRABAJO DE ANÁLISIS DE TRANSMISIONES**:

   * **NOMBRE DEL TRABAJO**: escriba un nombre del trabajo.
   * **REGIÓN**: seleccione la región donde desea ejecutar el trabajo. Considere la posibilidad de colocar el trabajo y el centro de eventos en la misma región para garantizar un mejor rendimiento y asegurarse de no pagar la transferencia de datos entre regiones.
   * **CUENTA DE ALMACENAMIENTO**: elija la cuenta de almacenamiento de Azure que quiera usar para almacenar los datos de supervisión de todos los trabajos de Stream Analytics que se ejecutan en esta región. Tiene la opción de elegir una cuenta de almacenamiento existente o crear uno nuevo.
3. Haga clic en **ANÁLISIS DE TRANSMISIONES** en el panel izquierdo para ver una lista de los trabajos de Análisis de transmisiones.  
   ![Icono de servicio de Análisis de transmisiones](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

   Se mostrará el nuevo trabajo con el estado **CREADO**. Tenga en cuenta que el botón **INICIO** en la parte inferior de la página está deshabilitado. Debe configurar la entrada, la salida y la consulta del trabajo antes de iniciar el trabajo.

### <a name="specify-job-input"></a>Especificación de la entrada del trabajo
1. En su trabajo de Stream Analytics, haga clic en **ENTRADAS** en la parte superior de la página y luego haga clic en **AGREGAR ENTRADA**. El cuadro de diálogo que se abre le guiará a través de una serie de pasos para configurar la entrada.
2. Haga clic en **FLUJO DE DATOS** y luego haga clic con el botón derecho.
3. Haga clic en **CENTRO DE EVENTOS** y luego haga clic con el botón derecho.
4. Escriba o seleccione los valores siguientes en la tercera página:

   * **ALIAS DE ENTRADA**: escriba un nombre descriptivo para esta entrada del trabajo, como *TwitterStream*. Tenga en cuenta que se usará este nombre en la consulta más adelante.
     **CENTRO DE EVENTOS**: si el Centro de eventos que ha creado está en la misma suscripción que el trabajo de Stream Analytics, seleccione el espacio de nombres en el que está el Centro de eventos.

     Si el Centro de eventos está en otra suscripción, haga clic en **Usar centro de eventos de otra suscripción** y escriba manualmente la información de **ESPACIO DE NOMBRES DE SERVICE BUS**, **NOMBRE DE CENTRO DE EVENTOS**, **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**, **CLAVE DE DIRECTIVA DE CENTRO DE EVENTOS** y **RECUENTO DE PARTICIONES DE CENTRO DE EVENTOS**.
   * **NOMBRE DE CENTRO DE EVENTOS**: seleccione el nombre del Centro de eventos.
   * **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**: seleccione la directiva del Centro de eventos que ha creado anteriormente en este tutorial.
   * **GRUPO DE CONSUMIDORES DEL CENTRO DE EVENTOS**: escriba el nombre del grupo de consumidores que ha creado anteriormente en este tutorial.
5. Haga clic con el botón derecho.
6. Especifique los siguientes valores:

   * **FORMATO DEL SERIALIZADOR DE EVENTOS**: JSON
   * **CODIFICACIÓN**: UTF8
7. Haga clic en el botón de **COMPROBACIÓN** para agregar este origen y comprobar que Stream Analytics puede conectarse correctamente al Centro de eventos.

### <a name="specify-job-query"></a>Especificación de la consulta de trabajo
Stream Analytics admite un modelo de consulta declarativa simple que describe las transformaciones. Para obtener más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial le ayudará a crear y probar varias consultas sobre datos de Twitter.

#### <a name="sample-data-input"></a>Entrada de datos de ejemplo
Para validar la consulta con datos de trabajo reales, puede usar la característica **DATOS DE EJEMPLO** para extraer los eventos del flujo y crear un archivo .json de los eventos para las pruebas.

1. Seleccione la entrada del Centro de eventos y luego haga clic en **DATOS DE EJEMPLO** en la parte inferior de la página.
2. En el cuadro de diálogo que aparece, especifique una **HORA DE INICIO** para empezar a recopilar datos y una **DURACIÓN** para indicar la cantidad de datos adicionales que se consumirán.
3. Haga clic en el botón **DETALLES** y luego en el vínculo **Haga clic aquí** para descargar y guardar el archivo .json.

#### <a name="pass-through-query"></a>Consulta de paso a través
Para empezar, haremos una consulta de paso a través simple que proyecta todos los campos de un evento.

1. Haga clic en **CONSULTA** en la parte superior de la página de trabajo de Stream Analytics.
2. En el editor de código, reemplace la plantilla de consulta inicial por lo siguiente:

     SELECT * FROM TwitterStream

   Asegúrese de que el nombre del origen de entrada coincida con el nombre de la entrada que ha especificado anteriormente.
3. Haga clic en **Prueba** en el editor de consultas.
4. Vaya a su archivo .json de ejemplo.
5. Haga clic en el botón de **COMPROBACIÓN** y vea los resultados debajo de la definición de consulta.

   ![Resultados mostrados a continuación de la definición de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic-tumbling-window-with-aggregation"></a>Recuento de tweets por tema: ventana de saltos de tamaño constante con agregación
Para comparar el número de menciones entre temas, se usará una [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de menciones por tema cada cinco segundos.

1. Cambie la consulta en el editor de código a:

     SELECT System.Timestamp as Time, Topic, COUNT(*)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic

   Esta consulta usa la palabra clave **TIMESTAMP BY** para especificar un campo de marca de tiempo en la carga que se usará en el cálculo temporal. Si no se ha especificado este campo, la operación de ventana se realizará usando el tiempo de cada evento llegado al Centro de eventos.  Obtenga más información en la sección "Tiempo de llegada frente a tiempo de aplicación" de [Referencia de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

   Esta consulta tiene acceso a una marca de tiempo para el final de cada ventana con la propiedad **System.Timestamp**.
2. Haga clic en **Volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

#### <a name="identify-trending-topics-sliding-window"></a>Identificación de tendencias: ventana deslizante
Para identificar las tendencias buscaremos temas que crucen un umbral para las menciones en un período determinado de tiempo. Para los fines de este tutorial, buscaremos temas que se mencionen más de 20 veces en los últimos cinco segundos con una [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1. Cambie la consulta en el editor de código por:   SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY SLIDINGWINDOW(s, 5), topic   HAVING COUNT(*) > 20
2. Haga clic en **Volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.

   ![Resultado de la consulta de ventana deslizante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment-tumbling-window-with-aggregation"></a>Recuento de menciones y opinión: ventana de saltos de tamaño constante con agregación
La consulta final que probaremos usa una **ventana de saltos** para obtener el número de menciones, promedio, mínimo, máximo y desviación estándar de la puntuación de la opinión de cada tema cada cinco segundos.

1. Cambie la consulta en el editor de código a:

     SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),   Max(SentimentScore), STDEV(SentimentScore)   FROM TwitterStream TIMESTAMP BY CreatedAt   GROUP BY TUMBLINGWINDOW(s, 5), Topic
2. Haga clic en **Volver a ejecutar** en el editor de consultas para ver los resultados de la consulta.
3. Esta es la consulta que usaremos para nuestro panel.  Haga clic en **GUARDAR** en la parte inferior de la página.

## <a name="create-output-sink"></a>Creación de receptores de salida
Ahora que hemos definido una secuencia de eventos, una entrada de Centro de eventos para introducir eventos y una consulta para realizar una transformación en la secuencia, el último paso es definir un receptor de salida para el trabajo.  Escribiremos los eventos de tweet agregados de nuestra consulta de trabajo en Azure Blob Storage.  También podría insertar los resultados en Azure SQL Database, Azure Table Storage o Event Hubs, según las necesidades específicas de su aplicación.

Siga estos pasos para crear un contenedor para Blob Storage si aún no tiene ninguno:

1. Use una cuenta de almacenamiento existente o cree una nueva cuenta de almacenamiento; para ello, haga clic en **NUEVO** > **DATA SERVICES** > **ALMACENAMIENTO** > **CREACIÓN RÁPIDA** y siga las instrucciones que aparecen en pantalla.
2. Seleccione la cuenta de almacenamiento, haga clic en **CONTENEDORES** en la parte superior de la página y luego haga clic en **AGREGAR**.
3. Especifique un **NOMBRE** para el contenedor y establezca su **ACCESO** en **Blob público**.

## <a name="specify-job-output"></a>Especificación de la salida del trabajo
1. En su trabajo de Stream Analytics, haga clic en **SALIDA** en la parte superior de la página y luego haga clic en **AGREGAR SALIDA**. El cuadro de diálogo que se abre le guiará a través de varios pasos para configurar la salida.
2. Haga clic en **BLOB STORAGE** y luego haga clic con el botón derecho.
3. Escriba o seleccione los valores siguientes en la tercera página:

   * **Alias de salida**: escriba un nombre descriptivo para esta salida de trabajo.
   * **SUSCRIPCIÓN**: si el almacenamiento Blob Storage que ha creado está en la misma suscripción que el trabajo de Stream Analytics, haga clic en **Usar la cuenta de almacenamiento de la suscripción actual**. Si el almacenamiento está en otra suscripción, haga clic en **Usar la cuenta de almacenamiento de otra suscripción** y especifique manualmente la información de **CUENTA DE ALMACENAMIENTO**, **CLAVE DE CUENTA DE ALMACENAMIENTO** y **CONTENEDOR**.
   * **Cuenta de almacenamiento**: seleccione el nombre de la cuenta de almacenamiento
   * **Contenedor**: seleccione el nombre del contenedor
   * **PREFIJO DE NOMBRE DE ARCHIVO**: escriba un prefijo de archivo que se usará al escribir la salida de blob.
4. Haga clic con el botón derecho.
5. Especifique los siguientes valores:
   * **FORMATO DEL SERIALIZADOR DE EVENTOS**: JSON
   * **CODIFICACIÓN**: UTF8
6. Haga clic en el botón de **COMPROBACIÓN** para agregar este origen y comprobar que Stream Analytics puede conectarse correctamente a la cuenta de almacenamiento.

## <a name="start-job"></a>Iniciar trabajo
Puesto que se ha especificado una entrada, consulta y salida de trabajo, estamos preparados iniciar el trabajo de Stream Analytics.

1. Desde **Panel** del trabajo, haga clic en **INICIAR** en la parte inferior de la página.
2. En el cuadro de diálogo que se abre, haga clic en **HORA DE INICIO DEL TRABAJO** y luego haga clic en el botón de **COMPROBACIÓN** en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a **Iniciando** y en breve cambiará a **En ejecución**.

## <a name="view-output-for-sentiment-analysis"></a>Consulta de la salida del análisis de opinión
Después de que el trabajo se esté ejecutando y esté procesando el flujo de Twitter en tiempo real, elija cómo quiere ver la salida del análisis de sentimiento. Use una herramienta como [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) para ver la salida del trabajo en tiempo real. Desde aquí, puede usar [Power BI](https://powerbi.com/) para extender su aplicación con el fin de incluir un panel personalizado como el que se muestra en la captura de pantalla siguiente.

![Análisis de los medios sociales: salida del análisis de opinión (minería de opinión) de Análisis de transmisiones en un panel de Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


