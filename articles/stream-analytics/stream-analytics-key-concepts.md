<properties 
	pageTitle="Obtenga información acerca de los conceptos clave de Análisis de transmisiones | Microsoft Azure" 
	description="Obtenga información sobre los conceptos clave de Análisis de transmisiones: componentes de un trabajo de Análisis de transmisiones, incluidas entradas y salidas admitidas, configuración del trabajo y métricas." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/01/2015" 
	ms.author="jeffstok" />


# Conceptos clave de Análisis de transmisiones: guía de los conceptos básicos de un trabajo de análisis de transmisiones 

Análisis de transmisiones de Azure es un servicio totalmente administrado que proporciona un procesamiento completo de eventos de baja latencia, alta disponibilidad y escalable a través de un flujo de datos en la nube. Análisis de transmisiones permite a los clientes configurar trabajos de streaming para analizar flujos de datos y realizar análisis casi en tiempo real. Este artículo explica los conceptos clave de un trabajo de Análisis de transmisiones.

## ¿Qué puede hacer en Análisis de transmisiones?
Con Análisis de transmisiones, puede:

- Realizar un procesamiento de eventos complejo en flujos de datos de alto volumen y gran velocidad   
- Recopilar datos de eventos de recursos o equipo distribuidos a nivel global, como vehículos conectados o redes públicas. 
- Procesar datos de telemetría para supervisión y diagnóstico casi en tiempo real. 
- Captura y archivado de eventos en tiempo real para su futuro procesamiento

Para obtener más información, consulte [Introducción a Análisis de transmisiones de Azure](stream-analytics-introduction.md).

Un trabajo de Análisis de transmisiones incluye lo siguiente: - uno o varios orígenes de entrada - una consulta sobre un flujo de datos de entrada - un destino de salida.


## Entradas

### Secuencia de datos

Cada definición de trabajo de Stream Analytics debe contener al menos un origen de entrada de secuencia de datos para su uso y transformación por parte del trabajo. [Almacenamiento de blobs de Azure](http://azure.microsoft.com/documentation/services/storage/) y [Centros de eventos de Azure](http://azure.microsoft.com/services/event-hubs/) se admiten como orígenes de entrada de flujos de datos. Los orígenes de entrada de Centros de eventos se usan para recopilar streaming de eventos procedentes de varios dispositivos y servicios diferentes, mientras que el almacenamiento de blobs se puede usar como origen de entrada para la introducción de grandes cantidades de datos. Como los blobs no transmiten datos, los trabajos de Análisis de transmisiones por blobs no serán de carácter temporal a menos que los registros del blob contengan marcas de tiempo.

### Datos de referencia
Análisis de transmisiones también admite un segundo tipo de origen de entrada: datos de referencia. Se trata de datos auxiliares usados para realizar correlaciones y búsquedas, y los datos de aquí suelen ser estáticos o cambiar con poca frecuencia. [Almacenamiento de blobs de Azure](http://azure.microsoft.com/documentation/services/storage/) es el único origen de entrada admitido para los datos de referencia. Los blobs de origen de datos de referencia están limitados a 50 MB de tamaño.

Para habilitar la compatibilidad para actualizar los datos de referencia, el usuario debe especificar una lista de blobs en la configuración de entrada utilizando los tokens {date} y {time} dentro del patrón de la ruta de acceso. El trabajo cargará el blob correspondiente según la fecha y hora que se codifican en los nombres de blob con la zona horaria UTC.

Por ejemplo, si el trabajo tiene una entrada de referencia configurada en el portal con el patrón de la ruta de acceso: /sample/{date}/{time}/products.csv, donde el formato de fecha es "AAAA-MM-DD" y el formato de hora es "HH: mm", el trabajo seleccionará un archivo de nombre /sample/2015-04-16/17:30/products.csv a las 5:30 p.m. el 16 de abril de 2015 según la hora UTC (que equivale a las 10:30 a.m. el 16 de abril de 2015 según la Hora estándar del Pacífico).


### Serialización
Para garantizar el comportamiento correcto de las consultas, Stream Analytics debe conocer el formato de serialización que se utiliza en las secuencias de datos de entrada. Los formatos admitidos actualmente son JSON, CSV y Avro para flujos de datos y CSV o JSON para los datos de referencia.

### Propiedades generadas
Según el tipo de entrada usado en el trabajo, se generarán algunos campos adicionales con metadatos de eventos. Estos campos se pueden consultar igual que otras columnas de entrada. Si un evento existente tiene un campo que tiene el mismo nombre que una de las propiedades siguientes, se sobrescribirá con los metadatos de entrada.

<table border="1">
	<tr>
		<th></th>
		<th>Propiedad</th>
		<th>Descripción</th>
	</tr>
	<tr>
		<td rowspan="4" valign="top"><strong>Blob</strong></td>
		<td>BlobName</td>
		<td>Nombre del blob de entrada de donde procede el evento.</td>
	</tr>
	<tr>
		<td>EventProcessedUtcTime</td>
		<td>Fecha y la hora en que se procesó el registro de blob.</td>
	</tr>
	<tr>
		<td>BlobLastModifiedUtcTime</td>
		<td>Fecha y la hora en que se modificó por última vez el blob.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>Identificador de partición de base cero para el adaptador de entrada.</td>
	</tr>
	<tr>
		<td rowspan="3" valign="top"><b>Centro de eventos</b></td>
		<td>EventProcessedUtcTime</td>
		<td>Fecha y la hora en que se produjo el evento.</td>
	</tr>
	<tr>
		<td>EventEnqueuedUtcTime</td>
		<td>Fecha y la hora en que el Centro de eventos recibió el evento.</td>
	</tr>
	<tr>
		<td>PartitionId</td>
		<td>Identificador de partición de base cero para el adaptador de entrada.</td>
	</tr>
</table>

###Particiones con datos de entrada lentos o inexistentes.
Al leer en orígenes de entrada con varias particiones y una o más particiones se quedan atrás o no tienen datos, el trabajo de streaming debe decidir cómo controlar esta situación para que los eventos mantengan un flujo constante a través del sistema. La opción de entrada 'Máximo retraso de llegada permitido' controla ese comportamiento y se establece de forma predeterminada para esperar por los datos indefinidamente, lo que significa que las marcas de tiempo de los eventos no se alterarán, pero también que los eventos fluirán en función de la partición de entrada más lenta y dejarán de fluir si una o más particiones de entrada no tienen datos. Esto resulta útil si los datos se distribuyen uniformemente entre particiones de entrada y la coherencia de la hora entre eventos es fundamental.

También puede optar por esperar solo durante un tiempo limitado; 'Máximo retraso de llegada permitido' determina el retraso tras el cual el trabajo decidirá avanzar, dejando atrás las particiones de entrada atrasadas y actuar en los eventos según la opción 'Acción en eventos con retraso', quitar los eventos o ajustar las marcas de tiempo de los eventos si llegan datos más adelante. Esto resulta útil si la latencia es decisiva y se tolera el cambio de marca de tiempo , pero es posible que la entrada no se distribuya uniformemente.

###Particiones con eventos desordenados
Cuando la consulta del trabajo de streaming usa la palabra clave TIMESTAMP BY, no hay ninguna garantía sobre el orden en el que llegarán los eventos a la entrada; algunos eventos de la misma partición de entrada pueden quedarse atrás; el parámetro 'máximo desorden permitido dentro de una entrada' hace que el trabajo de streaming actúe en eventos que quedan fuera del margen de tolerancia de orden según el valor de 'Acción en eventos de retraso', quitar los eventos o ajustar las marcas de tiempo de los eventos.

### Recursos adicionales
Para obtener más información sobre la creación de orígenes de entrada, consulte [Guía para desarrolladores de Centros de eventos de Azure](http://msdn.microsoft.com/library/azure/dn789972.aspx) y [Uso del Almacenamiento de blobs de Azure](../storage/storage-dotnet-how-to-use-blobs.md).



## Consultar
La lógica para filtrar, manipular y procesar los datos de entrada se definen en la consulta de trabajos de Análisis de transmisiones. Las consultas se escriben utilizando el lenguaje de consulta de Análisis de transmisiones, un lenguaje parecido a SQL que es principalmente un subconjunto de sintaxis estándar de Transact-SQL con algunas extensiones específicas para consultas temporales.

### Basado en ventanas
Las extensiones basadas en ventanas permiten la realización de agregaciones y cálculos sobre subconjuntos de eventos que han tenido lugar en algún período de tiempo. Las funciones basadas en ventanas se invocan mediante la instrucción **GROUP BY**. Por ejemplo, la consulta siguiente cuenta los eventos recibidos por segundo:

	SELECT Count(*) 
	FROM Input1 
	GROUP BY TumblingWindow(second, 1) 

### Pasos de ejecución
Para consultas más complejas, se puede usar la cláusula SQL estándar **WITH** para especificar un conjunto de resultados temporal con nombre. Por ejemplo, esta consulta usa **WITH** para realizar una transformación con dos pasos de ejecución:
 
	WITH step1 AS ( 
		SELECT Avg(Reading) as avr 
		FROM temperatureInput1 
		GROUP BY Building, TumblingWindow(hour, 1) 
	) 

	SELECT Avg(avr) AS campus_Avg 
	FROM step1 
	GROUP BY TumblingWindow (day, 1) 

Para obtener más información sobre el lenguaje de consulta, consulte [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](http://go.microsoft.com/fwlink/?LinkID=513299).

## Salida
En el destino de salida se escribirán los resultados del trabajo de Análisis de transmisiones. Los resultados se escriben continuamente en el destino de salida a medida que el trabajo procesa los eventos de entrada. Se admiten los siguientes destinos de salida:

- Centros de eventos de Azure: elija Centros de eventos como destino de salida en escenarios en los que sea necesario preparar varias canalizaciones de streaming como, por ejemplo, para emitir comandos de vuelta a los dispositivos.
- Almacenamiento de blobs de Azure: use el almacenamiento de blobs para archivar salidas a largo plazo o para almacenar datos para su posterior procesamiento.
- Almacenamiento de tablas de Azure: se trata de un almacén de datos estructurados con menos restricciones en el esquema. Las entidades con un esquema diferente y diferentes tipos pueden almacenarse en la misma tabla de Azure. El almacenamiento de tablas de Azure puede usarse para almacenar datos con de persistencia y recuperación eficaz. Para obtener más información, consulte [Introducción al almacenamiento de Azure](../storage/storage-introduction.md) y [Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).
- Base de datos SQL de Azure: este destino de salida es adecuado para datos de carácter relacional o para aplicaciones que dependen del contenido que se hospeda en una base de datos.

## Unidades de streaming ##
Para poder ofrecer una experiencia de rendimiento más predecible a los clientes, Análisis de transmisiones de Azure usa Unidades de streaming (SU) para representar los recursos y la capacidad de ejecutar un trabajo. Las SU proporcionan una forma de describir el evento relativo en función de una medida que combina la CPU, la memoria, la capacidad de procesamiento y las tasas de lectura y escritura. Cada unidad de streaming corresponde aproximadamente a 1 MB por segundo de rendimiento. Cada trabajo de Análisis de transmisiones de Azure necesita como mínimo una unidad de streaming, que es el valor predeterminado de todos los trabajos. Para más información acerca de cómo seleccionar el número correcto de SU para un trabajo, consulte [Escalar trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)

## Trabajos de escala

La métrica del porcentaje de uso de SU que se define más adelante, es un indicador de la necesidad de escalar un trabajo de Análisis de transmisiones de Azure. Un alto porcentaje de uso de SU puede deberse a que se ha usado una gran ventana en una consulta, grandes eventos en la entrada, una ventana con gran tolerancia a elementos desordenados o a una combinación de los anteriores. Para evitar este problema, puede optar por dividir la consulta o desglosarla en más pasos y agregar más SU de la pestaña Escalar.

Es posible que observe que los recursos de línea base siguen usándose incluso sin eventos de entrada, ya que el mismo sistema consume cierta cantidad de recursos. La cantidad de recursos consumidos por el sistema también pueden fluctuar con el tiempo.

Para obtener más información, consulte [Trabajos de escala de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md).


## Trabajos de supervisión y solución de problemas

### Cuenta de almacenamiento de supervisión regional

Para habilitar la supervisión de trabajos, Análisis de transmisiones requiere que se designe una cuenta de almacenamiento de Azure para la supervisión de los datos de cada región que contenga trabajos de Análisis de transmisiones. Esto se configura en el momento de la creación de los trabajos.

### Métricas
Las siguientes métricas están disponibles para supervisar el uso y el rendimiento de los trabajos de Stream Analytics:

- Porcentaje de uso de SU: este indicador le muestra la capacidad relativa de procesamiento de eventos de uno o varios de los pasos de la consulta. Si este indicador llega o supera el 80 %, existe una gran probabilidad de que el procesamiento de eventos se retrase o deje de avanzar.
- Errores: número de mensajes de error que provoca un trabajo de Análisis de transmisiones.
- Eventos de entrada: cantidad de datos recibidos por el trabajo de Análisis de transmisiones, en términos de recuento de eventos.
- Eventos de salida: cantidad de datos enviados por el trabajo de Análisis de transmisiones al destino de salida, en términos de recuento de eventos.
- Eventos desordenados: número de eventos recibidos fuera de orden que se eliminan o se les asigna una marca de tiempo ajustada, según la directiva de fuera de orden.
- Errores de conversión de datos: número de errores de conversión de datos que produce un trabajo de Análisis de transmisiones.

### Registros de operaciones
El mejor método para depurar o solucionar problemas de un trabajo de Análisis de transmisiones lo ofrecen los registros de operaciones de Azure. Para tener acceso a los registros de operaciones, vaya a la sección **Servicios de administración** del portal. Para inspeccionar los registros del trabajo, establezca **Tipo de servicio** en **Análisis de transmisiones** y **Nombre del servicio** en el nombre del trabajo.


## Trabajos de administración 

### Iniciar y detener trabajos
Al iniciar un trabajo, se le pedirá que especifique un valor de **Iniciar salida**, que determina cuándo comenzará el trabajo a producir la salida resultante. Si la consulta asociada incluye una ventana, el trabajo comenzará a seleccionar entradas de los orígenes de entrada al principio de la duración de ventana necesaria con el fin de producir el primer evento de salida en el tiempo especificado. Existen tres opciones: **Hora de inicio del trabajo**, **Personalizada** y **Última vez que se detuvo**. El valor predeterminado es la **Hora de inicio del trabajo**. En los casos en que un trabajo se detuvo temporalmente, lo mejor es elegir **Última vez que se detuvo** para el valor de **Iniciar salida** para reanudar el trabajo a partir de la última hora de salida y evitar la pérdida de datos. Para la opción **Personalizada**, debe especificar una fecha y una hora. Esta configuración resulta útil para especificar cuántos datos históricos de los orígenes de entrada se usarán o para seleccionar la introducción de datos a partir de una hora específica.

### Trabajos de configuración
Puede ajustar los siguientes valores de nivel superior para un trabajo de Stream Analytics:

- **Iniciar salida**: use esta opción para especificar cuándo comenzará el trabajo a producir la salida resultante. Si la consulta asociada incluye una ventana, el trabajo comenzará a seleccionar entradas de los orígenes de entrada al principio de la duración de ventana necesaria con el fin de producir el primer evento de salida en el tiempo especificado. Hay dos opciones: **Hora de inicio del trabajo** y **Personalizada**. El valor predeterminado es la **Hora de inicio del trabajo**. Para la opción **Personalizada**, debe especificar una fecha y una hora. Esta configuración es útil para especificar cuántos datos históricos de los orígenes de entrada se consumirán o para seleccionar la ingesta de datos a partir de una hora específica, por ejemplo, cuándo un trabajo se detuvo por última vez. 
- **Directiva de fuera de orden**: configuración para gestionar eventos que no llegan de manera secuencial al trabajo de Análisis de transmisiones. Puede designar un umbral de tiempo para reordenar los eventos especificando una ventana de tolerancia y también determinar la acción que se realizará en eventos fuera de esta ventana: **Eliminar** o **Ajustar**. **Eliminar** quitará todos los eventos recibidos sin orden y **Ajustar** cambiará el sistema. Marca de tiempo de eventos fuera de secuencia para la marca de tiempo del evento ordenado recibido más recientemente. 
- **Directiva de llegadas tarde** : al leer en orígenes de entrada que tienen varias particiones, y una o más particiones se retrasa o no tiene datos, el trabajo de streaming debe determinar cómo gestionar esta situación para que los eventos continúen fluyendo a través del sistema. La opción de entrada 'Máximo retraso de llegada permitido' controla ese comportamiento y se establece de forma predeterminada para esperar por los datos indefinidamente, lo que significa que las marcas de tiempo de los eventos no se alterarán, pero también que los eventos fluirán en función de la partición de entrada más lenta y dejarán de fluir si una o más particiones de entrada no tienen datos. Esto resulta útil si los datos se distribuyen uniformemente entre particiones de entrada y la coherencia de la hora entre eventos es fundamental. El usuario también puede optar por esperar solo durante un tiempo limitado; 'Máximo retraso de llegada permitido' determina el retraso tras el cual el trabajo decidirá avanzar, dejando atrás las particiones de entrada atrasadas y actuar en los eventos según la opción 'Acción en eventos con retraso', quitar los eventos o ajustar las marcas de tiempo de los eventos si llegan datos más adelante. Esto resulta útil si la latencia es decisiva y se tolera el cambio de marca de tiempo , pero es posible que la entrada no se distribuya uniformemente.
- **Configuración regional**: use este valor para especificar la preferencia de internacionalización para el trabajo de Análisis de transmisiones. Aunque las marcas de tiempo de los datos no dependen de la configuración regional, los valores de esta opción afectan al modo en que el trabajo analizará, comparará y ordenará los datos. En la versión preliminar, solo se admite **es-es**.

### Estado

El estado de los trabajos de Análisis de transmisiones se puede inspeccionar en el portal de Azure. Los trabajos en ejecución pueden estar en uno de estos dos estados: **En ejecución** o **Degradado**. La definición de cada uno de estos estados se indica a continuación:

- **En ejecución**: El trabajo está asignado, procesando entradas o en espera de procesar entradas. Si el trabajo muestra un estado En ejecución sin generar salida, es probable que la ventana de tiempo de procesamiento de datos sea grande o la lógica de consulta sea complicada. Otra razón puede ser que actualmente no haya ningún dato que se esté enviándose al trabajo.
- **Degradado**: este estado indica que un trabajo de Análisis de transmisiones detecta uno de los siguientes errores: errores de comunicación de entrada y salida, errores de consulta o errores en tiempo de ejecución con posibilidad de reintentar la operación. Para distinguir el tipo de errores que detecta el trabajo, vea los registros de operación.


## Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-es/home?forum=AzureStreamAnalytics).


## Pasos siguientes

Ahora que está familiarizado con los conceptos básicos de Análisis de transmisiones, intente:

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=July15_HO4-->