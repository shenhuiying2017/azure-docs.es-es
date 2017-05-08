---
title: "Análisis de opinión en tiempo real de Twitter con Azure Stream Analytics | Microsoft Docs"
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
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: d4fae06cb240c1687b059ae991b8d09f94e2ffc3
ms.lasthandoff: 05/01/2017

---

# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análisis de sentimiento de Twitter en tiempo real en Azure Stream Analytics

Aprenda a crear una solución de análisis de sentimiento para análisis de redes sociales al poner eventos en tiempo real de Twitter en Azure Event Hubs. En este escenario, escribirá una consulta de Azure Stream Analytics para analizar los datos. Después, almacenará los resultados para un uso posterior o usará un panel y [Power BI](https://powerbi.com/) para ofrecer perspectivas en tiempo real.

Las herramientas de análisis de las redes sociales ayudan a las organizaciones a comprender los temas que son tendencias. Los temas que son tendencias son asuntos y actitudes con un gran volumen de entradas en las redes sociales. Los análisis de sentimientos, denominados también *minería de opinión*, usan las herramientas de análisis de medios sociales para determinar las actitudes hacia un producto, una idea, etc.

El análisis de tendencias de Twitter en tiempo real es un buen ejemplo de una herramienta de análisis, porque el modelo de suscripción hashtag le permite escuchar palabras clave específicas y desarrollar análisis de opinión de la fuente.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Escenario: Análisis de opinión de redes sociales en tiempo real

Una empresa que tiene un sitio web multimedia de noticias está interesada en obtener una ventaja sobre sus competidores al proporcionar contenido del sitio inmediatamente relevante para sus lectores. La empresa usa el análisis de las redes sociales en temas relevantes para sus lectores mediante la elaboración de análisis de opinión en tiempo real de los datos de Twitter.

En concreto, para identificar los temas que son tendencias en tiempo real en Twitter, la empresa necesita el análisis en tiempo real del volumen de tweets y la opinión sobre los temas clave. En otras palabras, la necesidad es un motor que analice los análisis de opinión basados en esta fuente de redes sociales.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure
* Una cuenta de Twitter y [token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* El archivo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) de GitHub
* Opcional: el código fuente de cliente de Twitter desde [Github](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input"></a>Creación de una entrada de centro de eventos

La aplicación de ejemplo genera eventos y los inserta en una instancia de Event Hubs (un centro de eventos, para abreviar). Los Event Hubs de Service Bus son el método preferido de ingesta de eventos para Stream Analytics. Para más información, revise la documentación del centro de eventos en la [documentación de Azure Service Bus](/azure/service-bus/).

### <a name="create-an-event-hub"></a>Creación de un centro de eventos

Siga los pasos siguientes para crear un centro de eventos:

1. En [Azure Portal](https://portal.azure.com), seleccione **NUEVO**, escriba **Event Hubs** y, después, seleccione **Event Hubs** en la búsqueda resultante. Seleccione **Crear**.

2. Proporcione un nombre para el centro de eventos y cree un grupo de recursos. Hemos especificado `socialtwitter-eh` y `socialtwitter-rg`, respectivamente. Seleccione la opción para anclar la cuenta en el panel y, luego, seleccione el botón **Crear**.

3. Una vez completada la implementación, seleccione el centro de eventos. Después, en **Entidades**, seleccione **Event Hubs**.

4. Para crear el centro de eventos, seleccione el botón **+ Centro de eventos**. Vuelva a indicar su nombre (el nuestro era `socialtwitter-eh`) y seleccione **Crear**.

5. Para otorgar acceso al centro de eventos, necesitamos crear una directiva de acceso compartido. Seleccione el centro de eventos y, luego, en **Configuración**, seleccione **Directivas de acceso compartido**.

6. En **Directivas de acceso compartido**, cree una directiva con permisos para **ADMINISTRAR** mediante la selección de **+ Agregar**. Asigne un nombre a la directiva, active **ADMINISTRAR** y, luego, seleccione **Crear**.

7. Seleccione la nueva directiva una vez creada y, luego, seleccione el botón Copiar de la entidad **CADENA DE CONEXIÓN - CLAVE PRINCIPAL**. Lo necesitaremos más adelante en el ejercicio. Luego, vuelva al panel.

![Puntos de conexión de directivas de acceso compartido](./media/stream-analytics-twitter-sentiment-analysis-trends/keysandendpoints.png)

## <a name="configure-and-start-the-twitter-client-application"></a>Configuración e inicio de la aplicación cliente Twitter

Hemos creado una aplicación cliente que conecta datos con Twitter a través de las [API de streaming de Twitter](https://dev.twitter.com/streaming/overview) para recopilar eventos Tweet sobre un conjunto de temas con parámetros. La herramienta de código abierto [Sentiment140](http://help.sentiment140.com/) asigna un valor de opinión a cada tweet de la forma siguiente:

* 0 = negativo
* 2 = neutral
* 4 = positivo

Después, se insertan eventos Tweet en el centro de eventos.  

### <a name="set-up-the-application"></a>Configuración de la aplicación
 Siga estos pasos para configurar la aplicación:

1. [Descargue el archivo TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) y descomprímalo.

2. Ejecute la aplicación `TwitterWPFClient.exe`. Después, escriba los datos del secreto y la clave de API de Twitter, el secreto y token de acceso de Twitter, y también la información del centro de eventos. Finalmente, defina con qué palabras clave quiere determinar la opinión.

### <a name="generate-an-oauth-access-token"></a>Generación de un token de acceso de OAuth
Para más información, consulte [Pasos para generar un token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens).  

 Para generar un token, tiene que crear una aplicación vacía.  

1. Reemplace los valores de EventHubConnectionString y EventHubName en el archivo TwitterWpfClient.exe.config con la cadena de conexión y el nombre de su centro de eventos. La cadena de conexión que ha copiado anteriormente proporciona la cadena de conexión y el nombre de su centro de eventos. Así que no se olvide de separar y colocar cada una de ellas en el campo correcto. Por ejemplo, considere la siguiente cadena de conexión:

 ```
    Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
 ```  

   El archivo TwitterWpfClient.exe.config debe contener la configuración como se muestra en el ejemplo siguiente:

 ```
      add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
      add key="EventHubName" value="yourhub"
   ```
   > [!NOTE]
   > El texto "EntityPath =" **no** aparece en el valor de EventHubName.

    También puede especificar los valores de su información de conexión de Twitter y Azure directamente en el cliente. La misma lógica se aplica cuando no se usa "EntityPath =".

   ![wpfclient](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

2. **Opcional:** ajuste las palabras clave que se buscarán. De forma predeterminada, esta aplicación busca algunas palabras clave sobre juegos.  Puede ajustar los valores de **twitter_keywords** en el archivo TwitterWpfClient.exe.config, si quiere.

3. Ejecute TwitterWpfClient.exe. A continuación, seleccione el botón de inicio verde para recopilar la opinión social. Va a ver los eventos Tweet con los valores de **CreatedAt**, **Topic** y **SentimentScore** que se envían al centro de eventos.

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones

Ahora que los eventos Tweet se transmiten en tiempo real desde Twitter, podemos configurar un trabajo de Stream Analytics para analizar estos eventos en tiempo real.

### <a name="provision-a-stream-analytics-job"></a>Aprovisionamiento de un trabajo de Stream Analytics

Para aprovisionar un trabajo de Stream Analytics, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), haga clic en **NUEVO**, escriba **STREAM ANALYTICS** y después seleccione el resultado del icono de Stream Analytics.

2. Especifique los valores siguientes y, luego, seleccione **CREAR**.

   * **NOMBRE DEL TRABAJO**: escriba un nombre del trabajo.

   * **Grupo de recursos**: seleccione el grupo de recursos que creó anteriormente en este ejercicio con la opción "usar existente".

   * **CUENTA DE ALMACENAMIENTO**: elija la cuenta de almacenamiento de Azure que quiere usar para almacenar los datos de supervisión de todos los trabajos de Stream Analytics que se ejecutan en esta región. Tiene la opción de elegir una cuenta de almacenamiento existente o crear uno nuevo.   

![Nuevo trabajo](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

Una vez creado el trabajo, se abre en Azure Portal.

## <a name="specify-the-job-input"></a>Especificación de la entrada del trabajo
Para especificar la entrada del trabajo, siga estos pasos:

1. En el trabajo de Stream Analytics, en **Topología de trabajo** en medio del panel de trabajo, seleccione **ENTRADAS**. Después, seleccione **AGREGAR**.

    A continuación, el portal le solicita parte de la información siguiente. La mayoría los valores predeterminados funcionan y se definen a continuación:

   * **ALIAS DE ENTRADA**: escriba un nombre descriptivo para esta entrada de trabajo, como `TwitterStream`. Va a usar este nombre en la consulta más adelante.  

   * **NOMBRE DE CENTRO DE EVENTOS**: seleccione el nombre del Centro de eventos.

   * **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**: seleccione la directiva del Centro de eventos que ha creado anteriormente en este tutorial.

2. Seleccione el botón **Crear**.

## <a name="specify-the-job-query"></a>Especificación de la consulta del trabajo

Stream Analytics admite un modelo de consulta declarativa simple que describe las transformaciones. Para obtener más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial le ayuda a crear y probar varias consultas sobre datos de Twitter.

Para comparar el número de menciones entre temas, se usa una [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de menciones por tema cada cinco segundos.

Cambie la consulta en el editor de código a la siguiente y, luego, seleccione **Guardar**:

```
SELECT System.Timestamp as Time, Topic, COUNT(*)
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY TUMBLINGWINDOW(s, 5), Topic
```   

Esta consulta usa la palabra clave **TIMESTAMP BY** para especificar un campo de marca de tiempo en la carga que se usará en el cálculo temporal. Si no se ha especificado este campo, la operación de ventana se realiza usando el tiempo de cada evento llegado al centro de eventos. Obtenga más información en la sección "Tiempo de llegada frente a tiempo de aplicación" de [Referencia de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Esta consulta tiene acceso a una marca de tiempo para el final de cada ventana con la propiedad **System.Timestamp**.

![Cuadro Consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/querybox.png)

## <a name="create-an-output-sink"></a>Creación de un receptor de salida

Ahora que hemos definido una secuencia de eventos, una entrada de Centro de eventos para introducir eventos y una consulta para realizar una transformación en la secuencia, el último paso es definir un receptor de salida para el trabajo.  

Escribiremos los eventos de tweet agregados de nuestra consulta de trabajo en Azure Blob Storage.  También puede insertar los resultados en Azure SQL Database, Azure Table Storage o Event Hubs, según las necesidades específicas de su aplicación.

## <a name="specify-the-job-output"></a>Especificación de la salida del trabajo
Para especificar la salida del trabajo, siga estos pasos:

1. En su trabajo de Stream Analytics, seleccione **SALIDA**, que se encuentra en **Topología de trabajo** y, luego, seleccione **AGREGAR**.

2. Escriba o seleccione los valores siguientes en el panel:

   * **Alias de salida**: escriba un nombre descriptivo para esta salida de trabajo. Esta demostración usará `Output`.

   * **Receptor**: seleccione **Blob Storage**.

   * **CUENTA DE ALMACENAMIENTO**: seleccione **Crear una nueva cuenta de almacenamiento**.

    * **CUENTA ALMACENAMIENTO**: asigne un nombre a la nueva cuenta de almacenamiento (`socialtwitter` en este ejemplo).

    * **CONTENEDOR**: asigne un nombre al nuevo contenedor (`socialtwitter` en este ejemplo).

3. Deje el resto de las entradas con los valores predeterminados. Por último, seleccione **Crear**.

## <a name="start-the-job"></a>Inicio del trabajo

Puesto que se ha especificado una entrada, consulta y salida de trabajo, estamos preparados iniciar el trabajo de Stream Analytics.

Para iniciar el trabajo, siga estos pasos:

1. En el panel de información general del trabajo, en la parte superior de la página, seleccione **INICIAR**.

2. En el cuadro de diálogo que se abre, seleccione **HORA DE INICIO DEL TRABAJO** y luego seleccione el botón **COMPROBAR** en la parte inferior del cuadro de diálogo. El estado del trabajo cambia primero a **Iniciando** y, después, a **En ejecución**.

![Trabajo en ejecución](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Consulta de la salida del análisis de opinión

Después de que el trabajo haya comenzado a ejecutarse y esté procesando el flujo de Twitter en tiempo real, elija cómo quiere ver la salida del análisis de opinión.

Use una herramienta como [Explorador de almacenamiento de Azure](https://http://storageexplorer.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) para ver la salida del trabajo en tiempo real. Desde aquí, puede usar [Power BI](https://powerbi.com/) para extender su aplicación con el fin de incluir un panel personalizado como el que se muestra en la captura de pantalla siguiente.

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)

## <a name="create-another-query-to-identify-trending-topics"></a>Creación de otra consulta para identificar los temas que son tendencias

Otra consulta de ejemplo que hemos creado para este escenario se basa en [Ventana deslizante](https://msdn.microsoft.com/library/azure/dn835051.aspx). Para identificar los temas que son tendencias, buscamos temas que crucen un umbral para las menciones en un período determinado de tiempo.

Para los fines de este tutorial, buscamos temas que se mencionen más de 20 veces en los últimos cinco segundos.

```
SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
FROM TwitterStream TIMESTAMP BY CreatedAt
GROUP BY SLIDINGWINDOW(s, 5), topic
HAVING COUNT(*) > 20
```

## <a name="use-field-headers"></a>Uso de encabezados de campo

Las etiquetas de campo que puede utilizar en este ejercicio se muestran en esta tabla. Puede experimentar libremente con el editor de consultas.

Propiedad JSON | Definición
--- | ---
CreatedAt | La hora en que se creó el tweet
Tema. | El tema que coincide con la palabra clave especificada
SentimentScore | La puntuación de opinión de Sentiment140
Autor | El identificador de Twitter que envía el tweet
Texto | El cuerpo completo del tweet


## <a name="get-support"></a>Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

