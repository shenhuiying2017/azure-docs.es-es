---
title: "Análisis de opinión en tiempo real de Twitter con Azure Stream Analytics | Microsoft Docs"
description: "Aprenda a usar Análisis de transmisiones para el análisis en tiempo real de opinión de Twitter. Pasos desde la generación de eventos a los datos en un panel dinámico."
keywords: "análisis de tendencias de twitter en tiempo real, análisis de opiniones, análisis de medios sociales, ejemplo de análisis de tendencias"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 42068691-074b-4c3b-a527-acafa484fda2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: samacha
ms.openlocfilehash: 98230a8b61d1776a9ab23fd416af306efc700959
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Análisis de sentimiento de Twitter en tiempo real en Azure Stream Analytics

Aprenda a crear una solución de análisis de sentimiento para análisis de redes sociales al poner eventos en tiempo real de Twitter en Azure Event Hubs. Después, va a escribir una consulta de Azure Stream Analytics para analizar los datos y o bien almacenar los resultados para un uso posterior o bien usar un panel y [Power BI](https://powerbi.com/) para ofrecer información en tiempo real.

Las herramientas de análisis de las redes sociales ayudan a las organizaciones a comprender los temas que son tendencias. Los temas que son tendencias son asuntos y actitudes con un gran volumen de entradas en las redes sociales. Los análisis de sentimientos, denominados también *minería de opinión*, usan las herramientas de análisis de medios sociales para determinar las actitudes hacia un producto, una idea, etc. 

El análisis de tendencias de Twitter en tiempo real es un buen ejemplo de una herramienta de análisis, porque el modelo de suscripción hashtag le permite escuchar palabras clave específicas (hashtags) y desarrollar análisis de opinión de la fuente.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Escenario: Análisis de opinión de redes sociales en tiempo real

Una empresa que tiene un sitio web multimedia de noticias está interesada en obtener una ventaja sobre sus competidores al proporcionar contenido del sitio inmediatamente relevante para sus lectores. La empresa usa el análisis de las redes sociales en temas relevantes para sus lectores mediante la elaboración de análisis de opinión en tiempo real de los datos de Twitter.

Para identificar los temas que son tendencias en tiempo real en Twitter, la empresa necesita el análisis en tiempo real del volumen de tweets y la opinión sobre los temas clave. En otras palabras, la necesidad es un motor que analice los análisis de opinión basados en esta fuente de redes sociales.

## <a name="prerequisites"></a>Requisitos previos
En este tutorial, va a utilizar una aplicación cliente que se conecta a Twitter y busca tweets que tienen ciertos hashtags (que se pueden establecer). Para ejecutar la aplicación y analizar los tweets mediante Azure Stream Analytics, debe tener lo siguiente:

* Una suscripción de Azure
* Una cuenta de Twitter 
* Una aplicación de Twitter y el [token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) para esa aplicación. Se proporcionarán después instrucciones detalladas sobre cómo crear una aplicación de Twitter.
* La aplicación TwitterWPFClient, que lee la fuente de Twitter. Para obtener esta aplicación, descargue el archivo [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) de GitHub y descomprima el paquete en una carpeta en el equipo. Si quiere ver el código fuente y ejecutar la aplicación en un depurador, puede obtener el código fuente en [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Creación de un centro de eventos para la entrada de Stream Analytics

La aplicación de ejemplo genera eventos y los inserta en un centro de eventos de Azure. Los centros de eventos de Azure son el método preferido de ingesta de eventos para Stream Analytics. Para más información, consulte las [páginas de documentación](../event-hubs/event-hubs-what-is-event-hubs.md) de Azure Event Hubs.


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Creación de un espacio de nombres del centro de eventos y un centro de eventos
En este procedimiento, cree primero un espacio de nombres del centro de eventos y agregue luego un centro de eventos a dicho espacio de nombres. Los espacios de nombres del centro de eventos se usan para agrupar lógicamente instancias de bus de eventos relacionados. 

1. Inicie sesión en Azure Portal y haga clic en **Nuevo** > **Internet de las cosas** > **Centro de eventos**. 

2. En la hoja **Crear espacio de nombres**, especifique un nombre de espacio de nombres, como `<yourname>-socialtwitter-eh-ns`. Puede utilizar cualquier nombre para el espacio de nombres, pero debe ser válido para una dirección URL y ser único en Azure. 
    
3. Seleccione una suscripción y cree o elija un grupo de recursos. Luego haga clic en **Crear**. 

    ![Creación de un espacio de nombres del centro de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Cuando el espacio de nombres termine de implementarse, busque el espacio de nombres del centro de eventos en la lista de recursos de Azure. 

5. Haga clic en el nuevo espacio de nombres y, en la hoja del espacio de nombres, haga clic en **+&nbsp;Centro de eventos**. 

    ![Botón Agregar centro de eventos para crear un centro de eventos ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Asigne al nuevo centro de eventos el nombre `socialtwitter-eh`. Puede usar otro nombre. Si lo hace, tome nota del mismo, porque más adelante se necesita el nombre. No es preciso establecer otras opciones del centro de eventos.

    ![Hoja para la creación de un centro de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Haga clic en **Crear**.


### <a name="grant-access-to-the-event-hub"></a>Concesión de acceso al centro de eventos

Para que un proceso pueda enviar datos a un centro de eventos, este debe tener una directiva que permita el acceso adecuado. La directiva de acceso genera una cadena de conexión que incluye la información de autorización.

1.  En la hoja del espacio de nombres, haga clic en **Event Hubs** y luego en el nombre del nuevo centro de eventos.

2.  En la hoja del centro de eventos, haga clic en **Directivas de acceso compartido** y luego en **+&nbsp;Agregar**.

    >[!NOTE]
    >Asegúrese de que trabaja en el centro de eventos, no en el espacio de nombres del centro de eventos.

3.  Agregue una directiva denominada `socialtwitter-access` y, en **Notificación**, seleccione **Administrar**.

    ![Hoja para la creación de una directiva de acceso al centro de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Haga clic en **Crear**.

5.  Una vez implementada la directiva, haga clic en ella en la lista de directivas de acceso compartido.

6.  Busque el cuadro **CADENA DE CONEXIÓN: CLAVE PRINCIPAL** y haga clic en el botón Copiar que se encuentra junto a la cadena de conexión. 
    
    ![Copia de la clave principal de la cadena de conexión de la directiva de acceso](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Pegue la cadena de conexión en un editor de texto. Necesita esta cadena de conexión en la siguiente sección, después de hacerle pequeñas algunas modificaciones.

    La cadena de conexión tiene el siguiente aspecto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Observe que la cadena de conexión contiene varios pares clave-valor, separados por punto y coma: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` y `EntityPath`.  

    > [!NOTE]
    > Por seguridad, partes de la cadena de conexión del ejemplo se han quitado.

8.  En el editor de texto, quite el par `EntityPath` de la cadena de conexión (no se olvide quitar el punto y coma que lo precede). Cuando haya finalizado, la cadena de conexión tiene el siguiente aspecto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Configuración e inicio de la aplicación cliente Twitter
La aplicación cliente obtiene los eventos Tweet directamente de Twitter. Para ello, necesita permiso para llamar a las API de streaming de Twitter. Para configurar ese permiso, debe crear una aplicación de Twitter, que genera credenciales exclusivas (por ejemplo, un token de OAuth). Después, puede configurar la aplicación cliente para que use estas credenciales cuando realiza llamadas de API. 

### <a name="create-a-twitter-application"></a>Crear una aplicación de Twitter
Si no dispone de una aplicación de Twitter que pueda usar para este tutorial, puede crear una. Ya debe tener una cuenta de Twitter.

> [!NOTE]
> Puede cambiar el proceso exacto en Twitter para crear una aplicación y obtener las claves, secretos y token. Si estas instrucciones no coinciden con lo que se ve en el sitio de Twitter, consulte la documentación para desarrolladores de Twitter.

1. Vaya a la [página de administración de aplicaciones de Twitter](https://apps.twitter.com/). 

2. Cree una aplicación. 

    * Para la dirección URL del sitio web, especifique una dirección URL válida. No es necesario para que sea un sitio activo. (No puede especificar simplemente `localhost`).
    * Deje en blanco el campo de devolución de llamada. La aplicación cliente que se utiliza en este tutorial no requiere devoluciones de llamada.

    ![Creación de una aplicación en Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Si lo desea, puede cambiar los permisos de la aplicación a solo lectura.

4. Cuando se cree la aplicación, vaya a la página **Keys and Access Tokens** (Claves y tokens de acceso).

5. Haga clic en el botón para generar un token de acceso y un secreto del token de acceso.

Tenga esta información a mano, ya que la necesitará en el procedimiento siguiente.

>[!NOTE]
>Las claves y los secretos de la aplicación de Twitter proporcionan acceso a su cuenta de Twitter. Considere esta información como confidencial, al igual que la contraseña de Twitter. Por ejemplo, no inserte esta información en una aplicación que proporcione a otros usuarios. 


### <a name="configure-the-client-application"></a>Configuración de la aplicación cliente
Hemos creado una aplicación cliente que se conecta a datos de Twitter a través de las [API de streaming de Twitter](https://dev.twitter.com/streaming/overview) para recopilar eventos Tweet sobre un conjunto de temas específicos. La aplicación utiliza la herramienta de código abierto [Sentiment140](http://help.sentiment140.com/), que asigna el valor de opiniones siguiente a cada tweet:

* 0 = negativo
* 2 = neutral
* 4 = positivo

Cuando los eventos Tweet tengan asignado un valor de opinión, se insertan en el centro de eventos que creó anteriormente.

Antes de que se ejecute la aplicación, requiere cierta información del usuario, como las claves de Twitter y la cadena de conexión del centro de eventos. Puede proporcionar la información de configuración de las maneras siguientes:

* Ejecute la aplicación y, después, use la interfaz de usuario de la aplicación para especificar las claves, los secretos y la cadena de conexión. Si lo hace, la información de configuración se utiliza para la sesión actual, pero no se guarda.
* Edite el archivo .config de la aplicación y establezca los valores en él. Este enfoque conserva la información de configuración, pero también significa que esta información potencialmente confidencial se almacena en texto sin formato en el equipo.

El siguiente procedimiento documenta ambos enfoques. 

1. Asegúrese de que ha descargado y descomprimido la aplicación [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip), tal como se muestra en los requisitos previos.

2. Para establecer los valores en tiempo de ejecución (y solo para la sesión actual), ejecute la aplicación `TwitterWPFClient.exe`. Cuando la aplicación se lo solicite, introduzca los valores siguientes:

    * La clave de consumidor de Twitter (clave de API).
    * El secreto de consumidor de Twitter (secreto de API).
    * El token de acceso de Twitter.
    * El secreto del token de acceso de Twitter.
    * La información de la cadena de conexión que guardó anteriormente. Asegúrese de que usa la cadena de conexión que se quitó del par clave-valor `EntityPath`.
    * Las palabras clave de Twitter para las que quiere determinar la opinión.

   ![Aplicación de TwitterWpfClient en ejecución, mostrando los valores difuminados](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Para establecer los valores de forma persistente, utilice un editor de texto para abrir el archivo TwitterWpfClient.exe.config. Después, en el elemento `<appSettings>`, siga estos pasos:

    * Establezca `oauth_consumer_key` en la clave de consumidor de Twitter (clave de API). 
    * Establezca `oauth_consumer_secret` en el secreto de consumidor de Twitter (secreto de API).
    * Establezca `oauth_token` en el token de acceso de Twitter.
    * Establezca `oauth_token_secret` en el secreto de token de acceso de Twitter.

    Después, en el elemento `<appSettings>`, realice estos cambios:

    * Establezca `EventHubName` en el nombre del centro de eventos (es decir, en el valor de la ruta de acceso de la entidad).
    * Establezca `EventHubNameConnectionString` en la cadena de conexión. Asegúrese de que usa la cadena de conexión que se quitó del par clave-valor `EntityPath`.

    La sección `<appSettings>` tendrá un aspecto similar al siguiente. (Para mayor claridad y seguridad, se han ajustado algunas líneas y se han quitado algunos caracteres).

    ![Archivo de configuración de la aplicación TwitterWpfClient en un editor de texto, que muestra las claves y secretos de Twitter, así como la información de la cadena de conexión de centro de eventos](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Si todavía no ha iniciado la aplicación, ejecute ahora TwitterWpfClient.exe. 

5. Haga clic en el botón de inicio verde para recopilar la opinión social. Va a ver los eventos Tweet con los valores de **CreatedAt**, **Topic** y **SentimentScore** que se envían al centro de eventos.

    ![Aplicación de TwitterWpfClient en ejecución, mostrando una lista de tweets](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Si ve errores y no ve una secuencia de tweets en la parte inferior de la ventana, vuelva a comprobar las claves y secretos. Compruebe también la cadena de conexión (asegúrese de que no incluye la clave `EntityPath` y el valor).


## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones

Ahora que los eventos Tweet se transmiten en tiempo real desde Twitter, se puede configurar un trabajo de Stream Analytics para analizar estos eventos en tiempo real.

1. En Azure Portal, haga clic en **Nuevo** > **Internet de las cosas** > **Trabajo de Stream Analytics**.

2. Asigne al trabajo el nombre `socialtwitter-sa-job` y especifique una suscripción, un grupo de recursos y una ubicación.

    Es conveniente colocar el trabajo y el centro de eventos en la misma región para obtener el mejor rendimiento y no pagar por transferir datos de una región a otra.

    ![Creación de un trabajo de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Haga clic en **Crear**.

    El trabajo se crea y el portal muestra los detalles del trabajo.


## <a name="specify-the-job-input"></a>Especificación de la entrada del trabajo

1. En el trabajo de Stream Analytics, en **Topología de trabajo** en medio de la hoja de trabajo, haga clic en **Entradas**. 

2. En la hoja **Entradas**, haga clic en **+&nbsp;Agregar** y rellene la hoja con estos valores:

    * **Alias de entrada**: use el nombre `TwitterStream`. Si usa otro nombre, anótelo porque lo necesitará más adelante.
    * **Tipo de origen**: seleccione **Flujo de datos**.
    * **Origen**: seleccione **Centro de eventos**.
    * **Opción de importación**: seleccione **Usar centro de eventos de la suscripción actual**. 
    * **Espacio de nombres de Service Bus**: seleccione el espacio de nombres del centro de eventos que creó antes (`<yourname>-socialtwitter-eh-ns`).
    * **Centro de eventos**: seleccione el centro de eventos que creó antes (`socialtwitter-eh`).
    * **Nombre de la directiva del centro de eventos**: seleccione la directiva de acceso que creó antes (`socialtwitter-access`).

    ![Creación de una entrada del trabajo de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Haga clic en **Crear**.


## <a name="specify-the-job-query"></a>Especificación de la consulta del trabajo

Stream Analytics admite un modelo de consulta declarativa simple que describe las transformaciones. Para obtener más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Este tutorial le ayuda a crear y probar varias consultas sobre datos de Twitter.

Para comparar el número de menciones entre temas, se usa una [ventana de saltos de tamaño constante](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de menciones por tema cada cinco segundos.

1. Cierre la hoja **Entradas** si no lo ha hecho ya.

2. En la hoja del trabajo, haga clic en el cuadro **Consulta**. Azure muestra las entradas y salidas que están configuradas para el trabajo y le permite crear una consulta que permite transformar el flujo de entrada a medida que se envía a la salida.

3. Asegúrese de que la aplicación TwitterWpfClient se está ejecutando. 

3. En la hoja **Consulta**, haga clic en los puntos que aparecen junto a la entrada `TwitterStream` y seleccione **Datos de ejemplo de la entrada**.

    ![Opciones de menú para usar datos de muestra en la entrada del trabajo de Stream Analytics, con la opción "Datos de ejemplo de la entrada" seleccionada](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Se abrirá una hoja que permite especificar cuántos datos de muestra quiere obtener, para lo que se define el tiempo durante el que se lee el flujo de entrada.

4. Establezca **Minutos** en 3 y haga clic en **Aceptar**. 
    
    ![Opciones de muestreo del flujo de entrada, con "3 minutos" seleccionada.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure muestrea los datos del flujo de entrada correspondientes a tres minutos y le avisa cuando los datos de muestra están listos. (Tardará unos minutos). 

    Los datos de muestra se almacenan temporalmente y están disponibles mientras tenga abierta la ventana de consulta. Si cierra la ventana de consulta, los datos de muestra se descartan y tendrá que crear un nuevo conjunto de datos de muestra. 

5. Cambie la consulta en el editor de código a:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Si no usó `TwitterStream` como alias para la entrada, sustituya su alias por `TwitterStream` en la consulta.  

    Esta consulta usa la palabra clave **TIMESTAMP BY** para especificar un campo de marca de tiempo en la carga que se usará en el cálculo temporal. Si no se ha especificado este campo, la operación de ventana se realiza usando el tiempo de cada evento llegado al centro de eventos. Obtenga más información en la sección "Tiempo de llegada frente a tiempo de aplicación" de [Referencia de consulta de Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Esta consulta tiene acceso a una marca de tiempo para el final de cada ventana con la propiedad **System.Timestamp**.

5. Haga clic en **Probar**. La consulta se ejecuta en los datos que se han muestreado.
    
6. Haga clic en **Guardar**. Con esto guarda la consulta como parte del trabajo de Stream Analytics. (No guarda los datos de muestra).


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentación con distintos campos de la secuencia 

En la tabla siguiente se enumeran los campos que forman parte de los datos de streaming de JSON. Puede experimentar libremente con el editor de consultas.

|Propiedad JSON | Definición|
|--- | ---|
|CreatedAt | La hora en que se creó el tweet|
|Tema. | El tema que coincide con la palabra clave especificada|
|SentimentScore | La puntuación de opinión de Sentiment140|
|Autor | El identificador de Twitter que envía el tweet|
|Texto | El cuerpo completo del tweet|


## <a name="create-an-output-sink"></a>Creación de un receptor de salida

Se ha definido un flujo de eventos, una entrada de centro de eventos para la ingesta de eventos y una consulta para realizar una transformación en el flujo de datos. El último paso consiste en definir un receptor de salida para el trabajo.  

En este tutorial, va a escribir los eventos Tweet agregados de nuestra consulta de trabajo en Azure Blob Storage.  También puede insertar los resultados en Azure SQL Database, Azure Table Storage, Event Hubs o Power BI, según las necesidades específicas de su aplicación.

## <a name="specify-the-job-output"></a>Especificación de la salida del trabajo

1. En la sección **Topología de trabajo**, haga clic en el cuadro **Salida**. 

2. En la hoja **Salidas**, haga clic en **+&nbsp;Agregar** y rellene la hoja con estos valores:

    * **Alias de salida**: use el nombre `TwitterStream-Output`. 
    * **Receptor**: seleccione **Blob Storage**.
    * **Opciones de importación**: seleccione **Usar almacenamiento de blobs de la suscripción actual**.
    * **Cuenta de Storage**. Seleccione **Crear una nueva cuenta de almacenamiento**.
    * **Cuenta de Storage** (segundo cuadro). Escriba `YOURNAMEsa`, donde `YOURNAME` sea su nombre u otra cadena única. El nombre solo puede incluir letras minúsculas y números y ser único en Azure. 
    * **Contenedor**. Escriba `socialtwitter`.
    El nombre de la cuenta de almacenamiento y el nombre del contenedor se usan conjuntamente para especificar un URI para la instancia de Blob Storage, similar al siguiente: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Hoja "Nueva salida" del trabajo de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Haga clic en **Crear**. 

    Azure crea la cuenta de almacenamiento y genera una clave automáticamente. 

5. Cierre la hoja **Salidas**. 


## <a name="start-the-job"></a>Inicio del trabajo

Se especifican una entrada de trabajo, la consulta y la salida. Está listo para iniciar el trabajo de Stream Analytics.

1. Asegúrese de que la aplicación TwitterWpfClient se está ejecutando. 

2. En la hoja del trabajo, haga clic en **Iniciar**.

    ![Inicio del trabajo de Análisis de transmisiones](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. En la hoja **Iniciar trabajo**, en **Hora de inicio de salida del trabajo**, seleccione **Ahora** y después haga clic en **Iniciar**. 

    ![Hoja "Iniciar trabajo" del trabajo de Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure le avisa cuando se ha iniciado el trabajo y, en la hoja del trabajo, el estado aparece como **En ejecución**.

    ![Trabajo en ejecución](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Consulta de la salida del análisis de opinión

Después de que el trabajo haya comenzado a ejecutarse y esté procesando el flujo de Twitter en tiempo real, puede ver la salida del análisis de opinión.

Puede usar una herramienta como [Explorador de Azure Storage](https://http://storageexplorer.com/) o [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction) para ver la salida del trabajo en tiempo real. Desde aquí, puede usar [Power BI](https://powerbi.com/) para extender su aplicación con el fin de incluir un panel personalizado como el que se muestra en la captura de pantalla siguiente:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Creación de otra consulta para identificar los temas que son tendencias

Otra consulta que puede usar para entender la opinión de Twitter se basa en una [ventana deslizante](https://msdn.microsoft.com/library/azure/dn835051.aspx). Para identificar los temas que son tendencias, se buscan temas que crucen un umbral para las menciones en un período de tiempo especificado.

Para los fines de este tutorial, busque temas que se mencionen más de 20 veces en los últimos cinco segundos.

1. En la hoja de trabajo, haga clic en **Detener** para detener el trabajo. 

2. En la sección **Topología de trabajo**, haga clic en el cuadro **Consulta**. 

3. Cambie la consulta a lo siguiente:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Haga clic en **Guardar**.

5. Asegúrese de que la aplicación TwitterWpfClient se está ejecutando. 

6. Haga clic en **Iniciar** para reiniciar el trabajo con la nueva consulta.


## <a name="get-support"></a>Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
