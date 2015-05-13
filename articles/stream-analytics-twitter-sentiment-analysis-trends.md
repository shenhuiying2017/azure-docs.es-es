<properties
   pageTitle="Analizar opiniones y tendencias en Twitter en tiempo real | Microsoft Azure"
   description="Aprenda a usar Análisis de transmisiones para analizar opiniones y tendencias en Twitter en tiempo real. Este tutorial incluye los pasos desde la generación de eventos a los datos en un panel dinámico."
   services="stream-analytics"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="stream-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="jeffstok"/>


# Análisis de los medios sociales: análisis de opinión de Twitter en tiempo real

En este tutorial, aprenderá a crear una solución mediante la incorporación de eventos de Twitter a centros de eventos, la escritura de consultas de Análisis de transmisiones para analizar los datos y, a continuación, el almacenamiento de los resultados o mediante un panel para ofrecer perspectivas en tiempo real.

## Escenario

Un sitio web multimedia de noticias está interesado en obtener una ventaja sobre sus competidores al proporcionar contenido del sitio inmediatamente relevante para sus lectores. Utilizan perspectivas de las redes sociales en temas relevantes para sus lectores al realizar análisis en tiempo real de los datos de Twitter. En concreto, para identificar qué temas son tendencias, necesitan el análisis en tiempo real del volumen de tweets y la opinión sobre los temas clave.

## Requisitos previos
1.	Se requiere una cuenta de Twitter para este tutorial.  
2.	Este tutorial utiliza un generador de eventos situado en GitHub. Descárguelo [aquí](https://github.com/streamanalytics/samples/tree/master/TwitterClient) y siga estos pasos para configurar la solución.

## Cree una entrada del centro de eventos y un grupo de consumidores

La aplicación de ejemplo genera eventos y los inserta en una instancia de los centros de eventos \(un centro de eventos, para abreviar\). Los centros de eventos de Bus de servicio son el método preferido de recopilación de eventos para Análisis de transmisiones. Consulte la documentación de los centros de eventos en la [documentación del bus de servicio](/documentation/services/service-bus/)

Siga estos pasos para crear un centro de eventos.

1.	En el portal de Azure, haga clic en **NUEVO** \> **SERVICIOS DE APLICACIONES** \> **BUS DE SERVICIO** \> **CENTRO DE EVENTOS** \> **CREACIÓN RÁPIDA** y proporcione un nombre, una región y un espacio de nombres nuevo o existente para crear un nuevo centro de eventos.  
2.	Como práctica recomendada, debe leer cada trabajo de Análisis de transmisiones de un solo grupo de consumidores del centro de eventos. Le guiaremos a través del proceso de creación de un grupo de consumidores y podrá obtener más información aquí. Para crear un grupo de consumidores, vaya al centro de eventos recién creado y haga clic en la pestaña **GRUPOS DE CONSUMIDORES** y, después, haga clic en **CREAR** en la parte inferior de la página y proporcione un nombre para el grupo de consumidores.
3.	Para otorgar acceso al centro de eventos, necesitamos crear una directiva de acceso compartido. Haga clic en la pestaña **CONFIGURAR** de su centro de eventos.
4.	En **DIRECTIVAS DE ACCESO COMPARTIDO**, cree una nueva directiva con permisos para **ADMINISTRAR**.



  ![Directivas de acceso compartido en las que puede crear una nueva directiva con permisos para Administrar.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)

5.	Haga clic en **GUARDAR** en la parte inferior de la página.
6.	Vaya a **PANEL** y haga clic en **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la página y copie y guarde la información de conexión. \(Utilice el icono de copia que aparece bajo el icono de búsqueda\).

## Configuración e inicio de la aplicación del generador de eventos

Hemos proporcionado una aplicación cliente que derivará datos de Twitter mediante las [API de REST de Twitter](https://dev.twitter.com/rest/public) para recopilar eventos de Tweet sobre un conjunto de temas con parámetros. La herramienta de código abierto [Sentiment140](http://help.sentiment140.com/), de otro proveedor, se utiliza para asignar un valor de opinión a cada tweet \(0: negativo, 2: neutro, 4: positivo\) y, a continuación, se insertan eventos Tweet en el centro de eventos.

Siga estos pasos para configurar la aplicación:

1.	[Descargue la solución TwitterClient](https://github.com/streamanalytics/samples/tree/master/TwitterClient)
2.	Abra App.config y reemplace oauth\_consumer\_key, oauth\_consumer\_secret, oauth\_token, oauth\_token\_secret con tokens de Twitter con sus valores.  

	[Pasos para generar un token de acceso de OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)

	Tenga en cuenta que necesitará crear una aplicación vacía para generar un token.
3.	Reemplace los valores de EventHubConnectionString y EventHubName en el archivo App.config con la cadena de conexión del centro de eventos y el nombre.
4.	*Opcional:* ajuste las palabras clave que se buscarán. De forma predeterminada, esta aplicación busca "Azure, Skype, XBox, Microsoft, Seattle". Puede ajustar los valores de twitter\_keywords en el archivo App.config, si lo desea.
5.	Compile la solución
6.	Inicie la aplicación. Verá los eventos Tweet con los valores de CreatedAt, tema y SentimentScore que se envían al centro de eventos:

	![Valores de SentimentScore enviados a un centro de eventos.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## Creación de un Análisis de transmisiones

Ahora que tenemos una secuencia de eventos Tweet, podemos configurar un trabajo de Análisis de transmisiones para analizar estos eventos en tiempo real.

### Aprovisionamiento de un trabajo de Stream Analytics

1.	En el [Portal de Azure](https://manage.windowsazure.com/), haga clic en **NUEVO** \> **SERVICIOS DE DATOS** \> **ANÁLISIS DE TRANSMISIONES** \> **CREACIÓN RÁPIDA**.
2.	Especifique los valores siguientes y, a continuación, haga clic en **CREAR TRABAJO DE ANÁLISIS DE TRANSMISIONES**:

	* **NOMBRE DEL TRABAJO**: escriba un nombre del trabajo.
	* **REGIÓN**: seleccione la región donde desea ejecutar el trabajo. Considere la posibilidad de colocar el trabajo y el centro de eventos en la misma región para garantizar un mejor rendimiento y asegurarse de no pagar la transferencia de datos entre regiones.
	* **CUENTA DE ALMACENAMIENTO**: elija la cuenta de almacenamiento que desea usar para almacenar los datos de supervisión de todos los trabajos de Análisis de transmisiones que se ejecutan en esta región. Tiene la opción de elegir una cuenta de almacenamiento existente o crear uno nuevo.

3.	Haga clic en **ANÁLISIS DE TRANSMISIONES** en el panel izquierdo para ver una lista de los trabajos de Análisis de transmisiones. ![Icono de servicio de Análisis de transmisiones](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)

4.	Se mostrará el nuevo trabajo con el estado **CREADO**. Tenga en cuenta que el botón **INICIO** en la parte inferior de la página está deshabilitado. Debe configurar la entrada, la salida y la consulta del trabajo antes de iniciar el trabajo.


### Especificación de la entrada del trabajo
1.	En su trabajo de Análisis de transmisiones, Haga clic en **ENTRADAS** en la parte superior de la página y luego haga clic en **AGREGAR ENTRADA**. El cuadro de diálogo que se abre le guiará a través de una serie de pasos para configurar la entrada.
2.	Seleccione **FLUJO DE DATOS** y, a continuación, haga clic con el botón secundario.
3.	Seleccione **CENTRO DE EVENTOS** y, a continuación, haga clic con el botón secundario.
4.	Escriba o seleccione los valores siguientes en la tercera página:

	* **ALIAS DE ENTRADA**: escriba un nombre descriptivo para esta entrada del trabajo, como TwitterStream. Tenga en cuenta que va a utilizar este nombre en la consulta más adelante. **CENTRO DE EVENTOS**: si el centro de eventos que ha creado está en la misma suscripción que el trabajo de Análisis de transmisiones, seleccione el espacio de nombres en el que está el centro de eventos.

			Si el centro de eventos está en una suscripción diferente, seleccione **"Usar eventos concentrador de otra suscripción**, y escriba manualmente la información de **Espacio de nombres de servicio de Bus**, **Nombre de centro de eventos**, **Nombre de directiva de centro de eventos**, **Clave de directiva de centro de eventos** y **Recuento de particiones de centro de eventos**.

	* **NOMBRE DE CENTRO DE EVENTOS**: seleccione el nombre del centro de eventos
	* **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**: seleccione la directiva del centro de eventos que creó anteriormente en este tutorial.
	* **GRUPO DE CONSUMIDORES DEL CENTRO DE EVENTOS**: escriba el grupo de consumidores que creó anteriormente en este tutorial.
5.	Haga clic con el botón secundario.
6.	Especifique los siguientes valores:

	* **FORMATO DEL SERIALIZADOR DE EVENTOS**: JSON
	* **CODIFICACIÓN**: UTF8

7.	Haga clic en el botón de comprobación para agregar este origen y comprobar que Stream Analytics puede conectarse correctamente al centro de eventos.

### Especificación de la consulta de trabajo

Análisis de transmisiones admite un modelo de consulta declarativa simple para describir las transformaciones. Para obtener más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx). Este tutorial le ayudará a crear y probar varias consultas sobre datos de Twitter.

#### Entrada de datos de ejemplo

Para validar la consulta con datos de trabajo reales, puede utilizar la característica DATOS DE EJEMPLO para extraer los eventos del flujo y crear un archivo .JSON de los eventos para las pruebas.

1.	Seleccione la entrada del centro de eventos y haga clic en **DATOS DE EJEMPLO**, en la parte inferior de la página.
2.	En el cuadro de diálogo que aparece, especifique una **HORA DE INICIO** para empezar a recopilar datos y una **DURACIÓN** para indicar la cantidad de datos adicionales que se consumirá.
3.	Haga clic en el botón **DETALLES** y, a continuación, en el vínculo **Haga clic aquí** para descargar y guardar el archivo .JSON que se genera.

#### Consulta de paso a través
Para empezar, haremos una consulta de paso a través simple que proyecta todos los campos de un evento.

1.	Haga clic en **CONSULTA**, en la parte superior de la página de trabajo de Análisis de transmisiones.
2.	En el editor de código, reemplace la plantilla de consulta inicial por lo siguiente:

		SELECT * FROM TwitterStream

	Asegúrese de que el nombre del origen de entrada coincida con el nombre de la entrada que especificó anteriormente.

3.	Haga clic en **PRUEBA** en el editor de consultas
4.	Vaya a su archivo .JSON de ejemplo
5.	Haga clic en el botón de comprobación y vea los resultados que aparecen debajo de la definición de consulta.

	![Resultados mostrados a continuación de la definición de consulta](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### Recuento de tweets por tema: ventana de saltos de tamaño constante con agregación

Para comparar el número de menciones entre temas, se aprovechará una [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) para obtener el recuento de menciones por tema cada 5 segundos.

1.	Cambie la consulta en el editor de código a:

		SELECT System.Timestamp as Time, Topic, COUNT(*)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

	Tenga en cuenta que esta consulta utiliza la palabra clave **MARCA DE TIEMPO POR** para especificar un campo de marca de tiempo en la carga que se usará en el cálculo temporal. Si no se ha especificado este campo, la operación de ventana se realizará usando el tiempo de cada evento llegado al centro de eventos. Obtenga más información en "Tiempo de llegada frente a tiempo de aplicación" en la [Referencia de consulta de Análisis de transmisiones](https://msdn.microsoft.com/library/azure/dn834998.aspx).

	Esta consulta también tiene acceso a una marca de tiempo para el final de cada ventana con **System.Timestamp**.

2.	Haga clic en **VOLVER A EJECUTAR** en el editor de consultas para ver los resultados de la consulta.

#### Identificación de tendencias: ventana deslizante

Para identificar las tendencias buscaremos temas que crucen un umbral para las menciones en un período determinado de tiempo. Para este tutorial, buscaremos temas que se mencionan más de 20 veces en 5 segundos con un [SlidingWindow](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1.	Cambie la consulta en el editor de código a:

		SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY SLIDINGWINDOW(s, 5), topic
		HAVING COUNT(*) > 20

2.	Haga clic en **VOLVER A EJECUTAR** en el editor de consultas para ver los resultados de la consulta.

	![Resultado de la consulta de ventana deslizante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### Recuento de menciones y opinión: ventana de saltos de tamaño constante con agregación

La consulta final que probaremos utiliza una ventana de saltos de tamaño constante para obtener el número de menciones y promedio, mínimo, máximo y desviación estándar de la puntuación de la opinión de cada tema cada 5 segundos.

1.	Cambie la consulta en el editor de código a:

		SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
    	Max(SentimentScore), STDEV(SentimentScore)
		FROM TwitterStream TIMESTAMP BY CreatedAt
		GROUP BY TUMBLINGWINDOW(s, 5), Topic

2.	Haga clic en **VOLVER A EJECUTAR** en el editor de consultas para ver los resultados de la consulta.
3.	Ésta es la consulta que utilizamos para nuestro panel. Haga clic en **GUARDAR** en la parte inferior de la página.


## Creación de receptores de salida

Ahora que hemos definido una secuencia de eventos, una entrada de centro de eventos para introducir eventos y una consulta para realizar una transformación en la secuencia, el último paso es definir un receptor de salida para el trabajo. Escribiremos los eventos de tweet agregados de nuestra consulta de trabajo en un blob de Azure. También podría insertar los resultados en la base de datos SQL, el almacén de tablas o el centro de eventos, según las necesidades de su aplicación.

Siga estos pasos para crear un contenedor para el almacenamiento de blobs, si aún no tiene ninguno:

1.	Utilice una cuenta de almacenamiento existente o cree una nueva cuenta de almacenamiento; para ello, haga clic en **NUEVO** \> **SERVICIOS DE DATOS** \> **ALMACENAMIENTO** \> **CREACIÓN RÁPIDA** \> y siga las instrucciones que aparecen en pantalla.
2.	Seleccione la cuenta de almacenamiento y, a continuación, haga clic en **CONTENEDORES** en la parte superior de la página y, a continuación, haga clic en **AGREGAR**.
3.	Especifique un **NOMBRE** para el contenedor y establezca su **ACCESO** al blob público.

## Especificación de la salida del trabajo

1.	En su trabajo de Análisis de transmisiones, Haga clic en **SALIDA** en la parte superior de la página y luego haga clic en **AGREGAR SALIDA**. El cuadro de diálogo que se abre le guiará a través de una serie de pasos para configurar la salida.
2.	Seleccione **ALMACENAMIENTO DE BLOBS** y, a continuación, haga clic con el botón secundario.
3.	Escriba o seleccione los valores siguientes en la tercera página:

	* **ALIAS DE SALIDA**: escriba un nombre descriptivo para esta entrada de trabajo.
	* **SUSCRIPCIÓN**: si el almacenamiento de blobs que creó está en la misma suscripción que el trabajo de Análisis de transmisiones, seleccione **Usar cuenta de almacenamiento de la suscripción actual**. Si el almacenamiento está en otra suscripción, seleccione **Utilizar almacenamiento de otra suscripción** y especifique manualmente la información de **CUENTA DE ALMACENAMIENTO**, **CLAVE DE LA CUENTA DE ALMACENAMIENTO**, **CONTENEDOR**.
	* **CUENTA DE ALMACENAMIENTO**: seleccione el nombre de la cuenta de almacenamiento
	* **CONTENEDOR**: seleccione el nombre del contenedor
	* **PREFIJO DE NOMBRE DE ARCHIVO**: escriba un prefijo de archivo que se usará al escribir la salida del blob

4.	Haga clic con el botón secundario.
5.	Especifique los siguientes valores:
	* **FORMATO DEL SERIALIZADOR DE EVENTOS**: JSON
	* **CODIFICACIÓN**: UTF8
6.	Haga clic en el botón de comprobación para agregar este origen y comprobar que Análisis de transmisiones puede conectarse correctamente a la cuenta de almacenamiento.

## Iniciar trabajo

Puesto que se ha especificado una entrada, consulta y salida de trabajo, estamos preparados iniciar el trabajo de Análisis de transmisiones.

1.	Desde **PANEL** del trabajo, haga clic en **INICIAR** en la parte inferior de la página.
2.	En el cuadro de diálogo que aparece, seleccione **HORA DE INICIO DEL TRABAJO** y, a continuación, haga clic en el botón de marca de verificación en la parte inferior del cuadro de diálogo. El estado del trabajo cambiará a **Iniciando** y en breve pasará a **En ejecución**.


## Visualización de la salida

Utilice una herramienta como [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) o [Explorer Azure](http://www.cerebrata.com/products/azure-explorer/introduction) para ver la salida del trabajo en tiempo real. Desde aquí, puede ampliar la aplicación para incluir un panel personalizado sobre la salida, como el que aparece a continuación con [Power BI](https://powerbi.com/).

![Análisis de transmisiones de salida en un panel de Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=52-->
