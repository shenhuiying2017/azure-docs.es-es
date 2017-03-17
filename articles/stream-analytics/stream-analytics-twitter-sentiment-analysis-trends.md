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
ms.date: 03/03/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 576d8663f61d344628a38a98bf9902f4194949d0
ms.lasthandoff: 03/03/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análisis de sentimiento de Twitter en tiempo real en Azure Stream Analytics

Aprenda a crear una solución de análisis de sentimiento para análisis de redes sociales al poner eventos en tiempo real de Twitter en Azure Event Hubs. Escribirá una consulta de Azure Stream Analytics para analizar los datos. Después, almacenará los resultados para un examen posterior o usará un panel y [Power BI](https://powerbi.com/) para ofrecer perspectivas en tiempo real.

Las herramientas de análisis de los medios sociales ayudan a las organizaciones a comprender las tendencias, es decir, los temas y actitudes que cuentan con un gran volumen de publicaciones en las redes sociales. Los análisis de sentimientos, denominados también *minería de opinión*, usan las herramientas de análisis de medios sociales para determinar las actitudes hacia un producto, una idea, etc. El análisis de tendencias de Twitter en tiempo real es un buen ejemplo porque el modelo de suscripción hashtag le permite escuchar palabras clave específicas y desarrollar análisis de sentimientos en la fuente.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Escenario: Análisis de sentimiento de medios sociales en tiempo real

Una empresa que tiene un sitio web multimedia de noticias está interesada en obtener una ventaja sobre sus competidores al proporcionar contenido del sitio inmediatamente relevante para sus lectores. La empresa usa análisis de las redes sociales en temas relevantes para sus lectores mediante la elaboración de análisis de sentimientos en tiempo real de los datos de Twitter. En concreto, para identificar los temas que son tendencias en tiempo real en Twitter, la empresa necesita el análisis en tiempo real del volumen de tweets y la opinión sobre los temas clave. Por lo que en esencia requieren un motor que analice los análisis de sentimientos basado en esta fuente de medios sociales.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure
* Cuenta de Twitter y [token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* El archivo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) de GitHub
* Opcional: código fuente de cliente de Twitter desde [Github](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input"></a>Creación de una entrada de centro de eventos

La aplicación de ejemplo genera eventos y los inserta en una instancia de Event Hubs (un centro de eventos, para abreviar). Los Event Hubs de Service Bus son el método preferido de ingesta de eventos para Stream Analytics. Para obtener más información, revise la documentación de Event Hubs en la [documentación de Azure Service Bus](/azure/service-bus/).

### <a name="use-the-following-steps-to-create-an-event-hub"></a>Use los pasos siguientes para crear un centro de eventos.

1. En [Azure Portal](https://portal.azure.com), haga clic en **NUEVO** > y escriba *Event Hubs*; después, seleccione **Event Hubs** en la búsqueda resultante. A continuación, haga clic en **Crear**.
2. Proporcione un nombre para el centro de eventos y cree un grupo de recursos. He especificado `socialtwitter-eh` y `socialtwitter-rg` respectivamente. Active la casilla para anclar la cuenta en el panel y, luego, haga clic en el botón **Crear**.
3. Una vez completada la implementación, haga clic en el centro de eventos y, luego, en **Event Hubs**, que se encuentra bajo **Entidades**.
4. Haga clic en el botón **+ Centro de eventos** para crear el centro de eventos. Proporcione el nombre otra vez (el que usé yo era `socialtwitter-eh`) y haga clic en **Crear**.
5. Para otorgar acceso al centro de eventos, necesitamos crear una directiva de acceso compartido. Haga clic en el centro de eventos y, luego, en **Directivas de acceso compartido**, que se encuentra bajo **Configuración**.
6. En **DIRECTIVAS DE ACCESO COMPARTIDO**, cree una nueva directiva con permisos para **ADMINISTRAR** haciendo clic en **+ Agregar**. Asigne un nombre a la directiva y active **ADMINISTRAR**; luego, haga clic en **Crear**.
7. Haga clic en la nueva directiva una vez que se cree y, luego, haga clic en el botón Copiar de la entidad **CADENA DE CONEXIÓN - CLAVE PRINCIPAL**. Necesitaremos esto más adelante en el ejercicio. Luego, vuelva al panel.

![Puntos de conexión de directivas de acceso compartido](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configuración e inicio de la aplicación cliente Twitter

Hemos proporcionado una aplicación cliente que conectará datos con Twitter a través de las [API de streaming de Twitter](https://dev.twitter.com/streaming/overview) para recopilar eventos Tweet sobre un conjunto de temas con parámetros. La herramienta de código abierto [Sentiment140](http://help.sentiment140.com/) se usa para asignar un valor de sentimiento a cada tweet.

* 0 = negativo
* 2 = neutral
* 4 = positivo

Después, se insertan eventos Tweet en el centro de eventos.  

### <a name="follow-these-steps-to-set-up-the-application"></a>Siga estos pasos para configurar la aplicación:

1. [Descargue el archivo TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) y descomprímalo.
2. Ejecute la aplicación `TwitterWPFClient.exe` y escriba los datos del secreto y la clave de API de Twitter, el secreto y token de acceso de Twitter, y también la información del centro de eventos de Azure. Finalmente, defina con qué palabras clave quiere determinar el sentimiento. Tenga en cuenta si se especifica más de una palabra (mediante comas para definir varios valores) y quiere obtener CUALQUIER resultado, debe deslizar el control a "Match ANY" (Coincidir con CUALQUIERA).

   [Pasos para generar un token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  

   Necesitará crear una aplicación vacía para generar un token.  

3. Reemplace los valores de EventHubConnectionString y EventHubName en el archivo TwitterWpfClient.exe.config con la cadena de conexión y el nombre de su centro de eventos. La cadena de conexión que ha copiado anteriormente proporciona la cadena de conexión y el nombre de su centro de eventos, así que no se olvide de separar y colocar cada una de ellas en el campo correcto. Por ejemplo, considere la siguiente cadena de conexión:  
   
   `Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub`
   
   El archivo TwitterWpfClient.exe.config debe contener la configuración como en el ejemplo siguiente:
   
   ```
     add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
     add key="EventHubName" value="yourhub"
   ```
   
   Es importante tener en cuenta que el texto "EntityPath =" **no** aparece en el valor de EventHubName.
   
   También puede especificar los valores de su información de conexión de Twitter y Azure directamente en el cliente. La misma lógica se aplica cuando no se usa "EntityPath =".
   
   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

4. *Opcional:* ajuste las palabras clave que se buscarán.  De forma predeterminada, esta aplicación busca algunas palabras clave sobre juegos.  Puede ajustar los valores de **twitter_keywords** en el archivo TwitterWpfClient.exe.config, si quiere.
5. Ejecute TwitterWpfClient.exe y haga clic en el botón de inicio verde para recopilar sentimientos de medios sociales. Verá los eventos Tweet con los valores de **CreatedAt**, **Topic** y **SentimentScore** que se envían al centro de eventos.

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones

Ahora que los eventos Tweet se transmiten en tiempo real desde Twitter, podemos configurar un trabajo de Stream Analytics para analizar estos eventos en tiempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionamiento de un trabajo de Stream Analytics

En [Azure Portal](https://portal.azure.com/), haga clic en **NUEVO**, escriba **STREAM ANALYTICS** y haga clic en el resultado del icono de Stream Analytics. Especifique los valores siguientes y, luego, haga clic en **CREAR**.

   * **NOMBRE DEL TRABAJO**: escriba un nombre del trabajo.
   * **Grupo de recursos**: seleccione el grupo de recursos que creó anteriormente en este ejercicio con la opción "usar existente".
   * **CUENTA DE ALMACENAMIENTO**: elija la cuenta de almacenamiento de Azure que quiera usar para almacenar los datos de supervisión de todos los trabajos de Stream Analytics que se ejecutan en esta región. Tiene la opción de elegir una cuenta de almacenamiento existente o crear uno nuevo.   

![Nuevo trabajo](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Una vez que el trabajo se crea, se abrirá en Azure Portal.

## <a name="specify-the-job-input"></a>Especificación de la entrada del trabajo

En el trabajo de Stream Analytics, haga clic en **ENTRADAS** en medio del panel de trabajo, en la topología de trabajo; luego, haga clic en **Agregar**. El Porta le solicitará cierta información que se enumera a continuación. La mayoría de los valores predeterminados serán suficientes, pero se definen a continuación a título informativo.
  
   * **ALIAS DE ENTRADA**: escriba un nombre descriptivo para esta entrada de trabajo, como `TwitterStream`. Se usará este nombre en la consulta más adelante.
   * **NOMBRE DE CENTRO DE EVENTOS**: seleccione el nombre del Centro de eventos.
   * **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**: seleccione la directiva del Centro de eventos que ha creado anteriormente en este tutorial.

Haga clic en el botón **Crear** .

## <a name="specify-job-query"></a>Especificación de la consulta de trabajo

Stream Analytics admite un modelo de consulta declarativa simple que describe las transformaciones. Para obtener más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial le ayudará a crear y probar varias consultas sobre datos de Twitter.

Para comparar el número de menciones entre temas, se usará una [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de menciones por tema cada cinco segundos.

Cambiar la consulta en el editor de código al código siguiente y, luego, haga clic en **Guardar**:

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

Esta consulta usa la palabra clave **TIMESTAMP BY** para especificar un campo de marca de tiempo en la carga que se usará en el cálculo temporal. Si no se ha especificado este campo, la operación de ventana se realizará usando el tiempo de cada evento llegado al Centro de eventos.  Obtenga más información en la sección "Tiempo de llegada frente a tiempo de aplicación" de [Referencia de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Esta consulta tiene acceso a una marca de tiempo para el final de cada ventana con la propiedad **System.Timestamp**.

![Cuadro Consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-output-sink"></a>Creación de receptores de salida

Ahora que hemos definido una secuencia de eventos, una entrada de Centro de eventos para introducir eventos y una consulta para realizar una transformación en la secuencia, el último paso es definir un receptor de salida para el trabajo.  Escribiremos los eventos de tweet agregados de nuestra consulta de trabajo en Azure Blob Storage.  También podría insertar los resultados en Azure SQL Database, Azure Table Storage o Event Hubs, según las necesidades específicas de su aplicación.

## <a name="specify-job-output"></a>Especificación de la salida del trabajo

En su trabajo de Stream Analytics, haga clic en **SALIDA**, que se encuentra en **Topología de trabajo**; luego, haga clic en **AGREGAR**. Escriba o seleccione los valores siguientes en el panel:
   
   * **Alias de salida**: escriba un nombre descriptivo para esta salida de trabajo. Esta demostración usará `Output`.
   * **RECEPTOR**: seleccione Blob Storage.
   * **CUENTA DE ALMACENAMIENTO**: seleccione "Crear una nueva cuenta de almacenamiento".
    * **CUENTA ALMACENAMIENTO**: asigne un nombre a la nueva cuenta de almacenamiento (`socialtwitter` en este ejemplo).
    * **CONTENEDOR**: asigne un nombre al nuevo contenedor (`socialtwitter` en este ejemplo).

Deje el resto de las entradas con los valores predeterminados. Finalmente, haga clic en **Crear**.

## <a name="start-the-job"></a>Inicio del trabajo

Puesto que se ha especificado una entrada, consulta y salida de trabajo, estamos preparados iniciar el trabajo de Stream Analytics.

En el panel de información general del trabajo solo tiene que hacer clic en la opción **INICIAR** de la parte superior de la página.

En el cuadro de diálogo que se abre, haga clic en **HORA DE INICIO DEL TRABAJO** y luego haga clic en el botón de **COMPROBACIÓN** en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a **Iniciando** y en breve cambiará a **En ejecución**.

![Trabajo en ejecución](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Consulta de la salida del análisis de opinión

Después de que el trabajo se esté ejecutando y esté procesando el flujo de Twitter en tiempo real, elija cómo quiere ver la salida del análisis de sentimiento. Use una herramienta como [Explorador de almacenamiento de Azure](https://http://storageexplorer.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) para ver la salida del trabajo en tiempo real. Desde aquí, puede usar [Power BI](https://powerbi.com/) para extender su aplicación con el fin de incluir un panel personalizado como el que se muestra en la captura de pantalla siguiente.

![powerbi](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="another-query-of-interest--in-this-scenario"></a>Otra consulta de interés en este escenario

Otra consulta de ejemplo que hemos creado para este escenario se basa en [Ventana deslizante](https://msdn.microsoft.com/library/azure/dn835051.aspx). Para identificar las tendencias buscaremos temas que crucen un umbral para las menciones en un período determinado de tiempo. Para los fines de este tutorial, buscaremos temas que se mencionen más de 20 veces en los últimos cinco segundos.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="get-support"></a>Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


