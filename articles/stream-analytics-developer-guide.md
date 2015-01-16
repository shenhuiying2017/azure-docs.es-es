<properties title="Azure Stream Analytics developer guide" pageTitle="Guía para desarrolladores de Stream Analytics | Azure" description="Obtenga información acerca de cómo desarrollar aplicaciones de análisis de secuencias de Azure" metaKeywords="" services="stream analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />


# Guía para desarrolladores de Azure Stream Analytics 

[Este tema es la documentación preliminar y está sujeta a cambios en las futuras versiones.] 

Azure Stream Analytics es un servicio totalmente administrado que proporciona un procesamiento complejo de eventos de baja latencia, alta disponibilidad y escalable a través de la transmisión de datos en la nube. En la versión preliminar, Stream Analytics permite a los clientes configurar trabajos de streaming para analizar las secuencias de datos y realizar análisis casi en tiempo real.  

Escenarios de destino de Stream Analytics:

- Realización de procesamiento de eventos complejo en datos de alto volumen y gran velocidad   
- Recopilación de datos de eventos de recursos o equipo distribuidos a nivel global, como vehículos conectados o redes públicas 
- Procesamiento de datos de telemetría para supervisión y diagnóstico casi en tiempo real 
- Captura y archivado de eventos en tiempo real para su futuro procesamiento

Para obtener más información, consulte [Introducción a Azure Stream Analytics][stream.analytics.introduction]. 

Los trabajos de Stream Analytics se definen como uno o más orígenes de entrada, una consulta sobre los datos de secuencias entrantes y un destino de salida.  


##En este artículo

+ [Entradas](#inputs) 
+ [Query](#query)
+ [Salida](#output)
+ [Trabajos de escala](#scale)
+ [Trabajos de supervisión y solución de problemas](#monitor)
+ [Trabajos de administración](#manage)
+ [Pasos siguientes](#nextsteps)



##<a name="inputs"></a>Entradas

### Secuencia de datos

Cada definición de trabajo de Stream Analytics debe contener al menos un origen de entrada de secuencia de datos para su uso y transformación por parte del trabajo.  [El almacenamiento de blobs de Azure][azure.blob.storage] y los [Centros de eventos del bus de servicio de Azure][azure.event.hubs] se admiten como orígenes de entrada de secuencia de datos.  Los orígenes de entrada de centros de eventos se utilizan para recopilar secuencias de eventos procedentes de varios dispositivos y servicios diferentes, mientras que el almacenamiento de blobs se puede utilizar como origen de entrada para la ingestión de grandes cantidades de datos.  Como los blobs no transmiten datos, los trabajos de Stream Analytics sobre blobs no serán de carácter temporal a menos que los registros del blob contengan marcas de tiempo.

### Datos de referencia

Stream Analytics también admite un segundo tipo de origen de entrada: datos de referencia.  Se trata de datos auxiliares usados para realizar correlaciones y búsquedas, y los datos de aquí suelen ser estáticos o cambiar con poca frecuencia.  En la versión preliminar, el almacenamiento de blobs es el único origen de entrada admitido para los datos de referencia.

### Serialización
Para garantizar el comportamiento correcto de las consultas, Stream Analytics debe conocer el formato de serialización que se utiliza en las secuencias de datos de entrada. Los formatos admitidos actualmente son JSON, CSV y Avro para los datos de transmisión por secuencias y CSV para los datos de referencia.

### Propiedades generadas
Según el tipo de entrada usado en el trabajo, se generarán algunos campos adicionales con metadatos de eventos.  Estos campos se pueden consultar igual que otras columnas de entrada.  Si un evento existente tiene un campo que tiene el mismo nombre que una de las propiedades siguientes, se sobrescribirá con los metadatos de entrada.

<table border="1">
	<tr>
		<th></th>
		<th>Propiedad</th>
		<th>Descripción</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>El nombre del blob de entrada de donde procede el evento</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>La fecha y la hora en que se procesó el registro de blob</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>La fecha y la hora en que se modificó por última vez el blob</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>El identificador de partición basado en cero para el adaptador de entrada</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Centro de eventos</b></td>
		<td>EventProcessedUtcTime</td>
		<td>La fecha y la hora en que se produjo el evento</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>La fecha y la hora en que el Centro de eventos recibió el evento</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>El identificador de partición basado en cero para el adaptador de entrada</td>
	</tr>
</table>



###Recursos adicionales
Para obtener más información sobre la creación de orígenes de entrada, consulte [Guía para desarrolladores de Centros de eventos de Azure][azure.event.hubs.developer.guide] y [Uso del almacenamiento de blobs de Azure][azure.blob.storage.use].  



##<a name="query"></a>Query
La lógica para filtrar, manipular y procesar los datos de entrada se definen en la consulta de trabajos de Stream Analytics.  Las consultas se escriben utilizando el lenguaje de consulta de Stream Analytics, un lenguaje parecido a SQL que es principalmente un subconjunto de sintaxis estándar de T-SQL con algunas extensiones específicas de consultas temporales.

###Basado en ventanas
Las extensiones basadas en ventanas permiten la realización de agregaciones y cálculos sobre subconjuntos de eventos que han tenido lugar en algún período de tiempo. Las funciones basadas en ventanas se invocan mediante la instrucción GROUP BY. Por ejemplo, la consulta siguiente cuenta los eventos recibidos por segundo: 

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

###Pasos de ejecución
Para consultas más complejas, se puede usar la cláusula SQL estándar WITH para especificar un conjunto de resultados temporal con nombre.  Por ejemplo, esta consulta usa WITH para realizar una transformación con dos pasos de ejecución:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Para obtener más información sobre el lenguaje de consulta, consulte [Referencia del lenguaje de consulta de Azure Stream Analytics][stream.analytics.query.language.reference]. 

##<a name="output"></a>Salida
El origen de salida es donde se escribirán los resultados del trabajo de Stream Analytics. Los resultados se escriben continuamente en el origen de salida conforme el trabajo procesa los eventos de entrada.  Se admiten los siguientes orígenes de salida:

- Centros de eventos del bus de servicio de Azure: elija Centros de eventos como origen de salida para escenarios donde sea necesario componer juntas varias canalizaciones de streaming, como la emisión de comandos de vuelta a los dispositivos.
- Blobs de almacenamiento de Azure: use el almacenamiento de blobs para archivar salidas a largo plazo o para almacenar datos para su posterior procesamiento.
- Base de datos SQL de Azure: este origen de salida es adecuado para los datos de carácter relacional o para aplicaciones que dependen de contenido que se hospeda en una base de datos.


##<a name="scale"></a>Trabajos de escala

Un trabajo de Stream Analytics se puede escalar a través de la configuración de unidades de streaming, que definen la cantidad de potencia de procesamiento que recibe un trabajo. Cada unidad de streaming corresponde aproximadamente a 1 MB por segundo de rendimiento. Cada suscripción tiene una cuota de 12 unidades de streaming por región que se asignan entre los trabajos de esa región.

Para obtener más información, consulte [Trabajos de escala de Azure Stream Analytics][stream.analytics.scale.jobs].


##<a name="monitor"></a>Trabajos de supervisión y solución de problemas

###Cuenta de almacenamiento de supervisión regional

Para permitir la supervisión de trabajos, Stream Analytics requiere que designe una cuenta de almacenamiento de Azure para la supervisión de los datos en cada región que contenga trabajos de Stream Analytics.  Esto se configura en el momento de la creación de los trabajos.  

###Métricas
Las siguientes métricas están disponibles para supervisar el uso y el rendimiento de los trabajos de Stream Analytics:

- Rendimiento entrante: cantidad de datos recibidos por el trabajo de Stream Analytics, en términos de recuento de eventos.
- Rendimiento saliente: cantidad de datos enviados por el trabajo de Stream Analytics al origen de salida, en términos de recuento de eventos.
- Recuento de errores: número de mensajes de error que provoca un trabajo de Stream Analytics.

###Registros de operaciones
El mejor enfoque para depurar o solucionar los problemas de trabajo de Stream Analytics es a través de los registros de operaciones de Azure.  Para tener acceso a los registros de operaciones, vaya a la sección Servicios de administración del portal.  Para inspeccionar los registros de su trabajo, establezca el tipo de servicio en "Stream Analytics" y el nombre del servicio en el nombre de su trabajo.


##<a name="manage"></a>Trabajos de administración 

###Inicio y detención de los trabajos
En la versión preliminar de Stream Analytics, la detención de un trabajo no conserva ningún estado acerca de los últimos eventos consumidos por el trabajo.  Como resultado, reiniciar un trabajo detenido puede dar lugar a eventos perdidos o datos duplicados.  Si se debe detener temporalmente un trabajo, la práctica recomendada es inspeccionar la salida y usar la hora de inserción del último registro para aproximar cuándo se detiene el trabajo.  Luego, especifique esta hora en la configuración del inicio de la salida en la pestaña Configurar cuando el trabajo se reinicie.
Esta es una limitación temporal, y permitir que el trabajo se inicie y se detenga sin pérdida de datos es algo que hay que corregir prioritariamente en las futuras versiones.  

###Trabajos de configuración
Puede ajustar los siguientes valores de nivel superior para un trabajo de Stream Analytics:

- Iniciar salida: especifica cuándo este trabajo comenzará a producir la salida resultante. Si la consulta asociada incluye una ventana, el trabajo comenzará a seleccionar entradas de los orígenes de entrada al principio de la duración de ventana necesaria con el fin de producir el primer evento de salida en el tiempo especificado. Hay dos opciones: hora de inicio del trabajo y personalizado. El valor predeterminado es la hora de inicio del trabajo. Para la opción personalizada, debe especificar una fecha y una hora. Esta configuración es útil para especificar cuántos datos históricos de los orígenes de entrada se consumirán o para seleccionar la ingesta de datos a partir de una hora específica, por ejemplo, cuándo un trabajo se detuvo por última vez. 
- Directiva de fuera de orden: configuración para gestionar eventos que no llegan al trabajo de Stream Analytics de manera secuencial. Puede designar un umbral de tiempo para reordenar los eventos especificando una ventana de tolerancia y también determinar la acción que se realizará en eventos fuera de esta ventana: Eliminar o Ajustar.  Eliminar quitará todos los eventos recibidos sin orden y Ajustar cambiará el valor de System.Timestamp de los eventos de fuera de orden a la marca de tiempo del evento ordenado recibido más recientemente.  
- Configuración regional: use este valor para especificar la preferencia de internacionalización para el trabajo de Stream Analytics. Aunque las marcas de tiempo de los datos no dependen de la configuración regional, los valores de esta opción afectan al modo en que el trabajo analizará, comparará y ordenará los datos. En la versión preliminar, solo se admite en-US.


##<a name="support"></a>Obtenga soporte técnico
Si necesita más ayuda, consulte el [foro de Azure Stream Analytics][stream.analytics.forum]. 


##<a name="nextsteps"></a>Pasos siguientes

- [Introducción a Azure Stream Analytics][stream.analytics.introduction]
- [Introducción al uso de Azure Stream Analytics][stream.analytics.get.started]
- [Escalación de trabajos de Azure Stream Analytics][stream.analytics.scale.jobs]
- [Problemas conocidos y limitaciones de Azure Stream Analytics][stream.analytics.limitations]
- [Referencia del lenguaje de consultas de Azure Stream Analytics][stream.analytics.query.language.reference]
- [Referencia de la API de REST de administración de Azure Stream Analytics][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/es-es/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/es-es/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/es-es/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
