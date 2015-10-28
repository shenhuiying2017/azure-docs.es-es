<properties 
	pageTitle="Creación de salidas de Análisis de transmisiones de Azure | Microsoft Azure" 
	description="Obtenga información acerca de cómo conectarse a y configurar los destinos de salida para soluciones de Análisis de transmisiones." 
	documentationCenter="" 
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>

# Creación de salidas de Análisis de transmisiones

## Descripción de salidas de Análisis de transmisiones ##
---
Al crear un trabajo de Análisis de transmisiones, una de las consideraciones es cómo se consume la salida del trabajo. ¿Cómo visualizan los consumidores de la transformación de datos los resultados del trabajo Análisis de transmisiones? ¿Qué herramientas van a utilizar para analizar la salida? ¿El almacenamiento o retención de datos es un requisito?

Análisis de transmisiones de Azure proporciona siete métodos diferentes para almacenar y ver los resultados del trabajo. Base de datos SQL, Almacenamiento de blobs, Centros de eventos, Colas de Bus de servicio, Temas de Bus de servicio, Power BI y Almacenamiento de tablas. Esto proporciona facilidad de visualización de la salida del trabajo y flexibilidad en el consumo y almacenamiento de los resultados del trabajo de almacenamiento de datos y otros fines.

## Uso de una base de datos de SQL como salida ##
---
Puede usarse Base de datos SQL como salida de datos que son relacionales por naturaleza o de aplicaciones que dependen del contenido hospedado en una base de datos relacional. Para obtener más información sobre Bases de datos SQL de Azure, consulte [Bases de datos SQL de Azure](http://azure.microsoft.com/services/sql-database/).

### Parámetros ###

Para comenzar a usar una base de datos SQL, se necesitarán la siguiente información acerca de Base de datos SQL:

1. Nombre del servidor
2. Nombre de la base de datos
3. Una combinación de nombre de usuario y contraseña válidos.
4. Nombre de la tabla de salida. Esta tabla debe existir, el trabajo no se creará.

### Adición de Base de datos SQL como salida ###

![graphic1][graphic1]

Vaya a la pestaña de resultados del trabajo y haga clic en el comando **AGREGAR SALIDA** y haga clic en Siguiente.

![graphic2][graphic2]

Elija **Base de datos SQL** como salida.

![graphic3][graphic3]

Escriba la información de la base de datos en la página siguiente. El alias de salida es el nombre descriptivo usado en las consultas para dirigir la salida de la consulta a esta base de datos. El alias predeterminado si la única salida presente es "salida".

![graphic4][graphic4]

Si la base de datos existe dentro de la misma suscripción como trabajo de Análisis de transmisiones, la opción para seleccionar "Usar la base de datos SQL de la suscripción actual", está disponible. A continuación, seleccione la base de datos en la lista desplegable.

![graphic5][graphic5]

Haga clic en siguiente para agregar esta salida. Deberían iniciarse dos operaciones; la primera es agregar la salida.

![graphic6][graphic6]

La segunda es probar la conexión. Análisis de transmisiones de Azure intentará conectarse a Base de datos SQL y verificar que las credenciales que ha escrito sean correctas y que la tabla sea accesible.

## Uso de Almacenamiento de blobs como salida ##
---
Para obtener una introducción sobre Almacenamiento de blobs de Azure y su uso, consulte la documentación en [Uso de blobs](./articles/storage/storage-dotnet-how-to-use-blobs.md).

### Parámetros ###

Para empezar a usar una salida de Almacenamiento de blobs, será necesaria la siguiente información:

1. Si la cuenta de almacenamiento se encuentra en una suscripción distinta que el trabajo de streaming, aparecerán los campos para proporcionar el nombre de cuenta de almacenamiento y la clave de cuenta de almacenamiento.
2. El nombre del contenedor.
3. El prefijo del nombre de archivo.
4. Qué formato de serialización se usa para los datos (Avro, CSV y JSON).

### Adición Almacenamiento de blobs como salida ###

Seleccione la salida en Almacenamiento de blobs.

![graphic20][graphic20]

Después proporcione los detalles que se muestran a continuación:

![graphic21][graphic21]

## Uso de un Centro de eventos como salida ##
---
### Información general ###
 
Los centros de eventos son ingestores de eventos altamente escalables; normalmente son la manera más común para introducir datos de Análisis de transmisiones. Su control robusto para una gran cantidad de eventos también hace que sean perfectos para la salida del trabajo. Un uso del Centro de eventos como salida es cuando la salida de un trabajo de Análisis de transmisiones será la entrada de otro trabajo de transmisión. Para obtener más información sobre Centros de eventos, visite el portal en [Centros de eventos](https://azure.microsoft.com/services/event-hubs/ "Centros de eventos").
 
### Parámetros ###

Hay unos cuantos parámetros que son necesarios para configurar las secuencias de datos del Centro de eventos.

1. Espacio de nombres de Bus de servicio: espacio de nombres de Bus de servicio del centro de eventos. Un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo Centro de eventos, también se crea un espacio de nombres del Bus de servicio. 
2. Nombre de centro de eventos: el nombre del centro de eventos. Es el nombre que especificó al crear un nuevo Centro de eventos. 
3. Nombre de directiva de centro de eventos: nombre de la directiva de acceso compartido para tener acceso al centro de eventos. Las directivas de acceso compartido pueden configurarse para un centro de eventos en la Pestaña Configurar. Cada directiva de acceso compartido tendrá un nombre, los permisos establecidos y claves de acceso generadas.
4. Clave de la directiva de centro de eventos: clave principal o secundaria de la directiva de acceso compartido para tener acceso al centro de eventos.  
5. Columna de clave de partición: parámetro opcional para las salidas del centro de eventos. Esta columna contiene la clave de partición para la salida del Centro de eventos.

### Adición de un Centro de eventos como salida ###

1. Haga clic en **Salida** en la parte superior de la página y luego haga clic en **Agregar salida**. Seleccione el Centro de eventos como opción de salida y haga clic en el botón derecho en la parte inferior de la ventana.

    ![graphic38][graphic38]

2. Proporcione la información relevante en los campos para la salida y cuando finalice, haga clic en el botón derecho al final de la ventana para continuar.

    ![graphic36][graphic36]

3. Compruebe que el formato de serialización de eventos, el tipo de codificación y el formato dispongan de los valores adecuados y haga clic en la casilla para completar la acción.

    ![graphic37][graphic37]

## Uso de Power BI como salida ##
---
### Información general ###
Power BI puede utilizarse como salida para un trabajo de Análisis de transmisiones para proporcionar una experiencia de visualización enriquecida para usuarios de Análisis de transmisiones. Esta capacidad puede utilizarse para paneles operacionales, generación de informes e informes basados en métricas. Para obtener más información sobre Power BI, visite el sitio de [Power BI](https://powerbi.microsoft.com/).

### Parámetros ###

Hay unos cuantos parámetros que son necesarios para configurar la salida de Power BI.

1. Alias de salida: cualquier alias de salida de nombre descriptivo al que es fácil hacer referencia. Este alias de salida es especialmente útil si se decide tener varias salidas para un trabajo. En ese caso, se hará referencia a este alias en la consulta. Por ejemplo, use el valor del alias de salida = “OutPbi”.
2. Nombre del conjunto de datos: proporcione un nombre del conjunto de datos que desea que tenga la salida de Power BI. Por ejemplo, use "pbidemo".
2. Nombre de tabla: proporcione un nombre de tabla en el conjunto de datos de la salida de Power BI. Por ejemplo, use "pbidemo". **Actualmente, la salida de Power BI de trabajos de Análisis de transmisiones solo puede tener una tabla en un conjunto de datos.**

### Adición de Power BI como salida ###

1.  Haga clic en **Salida** en la parte superior de la página y luego haga clic en **Agregar salida**. Seleccione Power BI como la opción de salida.

    ![graphic22][graphic22]

2.  Aparecerá una pantalla similar a la siguiente.

    ![graphic23][graphic23]

3.  En este paso, proporcione la cuenta profesional o educativa para la autorización de la salida de Power BI. Si aún no se ha registrado en Power BI, elija **Suscríbase ahora**.
4.  A continuación, aparecerá una pantalla similar a la siguiente.

    ![graphic24][graphic24]


>	[AZURE.NOTE] One should not explicitly create the dataset and table in the Power BI dashboard. The dataset and table will be automatically populated when the job is started and the job starts pumping output into Power BI. Note that if the job query doesn’t return any results, the dataset and table will not be created. Also be aware that if Power BI already had a dataset and table with the same name as the one provided in this Stream Analytics job, the existing data will be overwritten.

*	Haga clic en **Aceptar**, **Probar conexión**; ahora la configuración de la salida ha finalizado.


## Uso de Almacenamiento de tablas de Azure para una salida ##
---
El almacenamiento de tablas ofrece un tipo de almacenamiento de alta disponibilidad y escalabilidad masiva, de forma que las aplicaciones pueden escalarse automáticamente para ajustarse a la demanda de los usuarios. Almacenamiento de tablas es un almacén de claves/atributos NoSQL de Microsoft que puede aprovechar para datos estructurados con menos restricciones en el esquema. El almacenamiento de tablas de Azure puede usarse para almacenar datos con de persistencia y recuperación eficaz. Para obtener más información sobre Almacenamiento de tablas de Azure, visite [Almacenamiento de tablas de Azure](./articles/storage/storage-introduction.md).

### Parámetros ###

Para empezar a usar Almacenamiento de tablas de Azure, se requiere la siguiente información:

1. Nombre de la cuenta de almacenamiento (si este almacenamiento está en una suscripción diferente del trabajo de transmisión).
2. Clave de la cuenta de almacenamiento (si este almacenamiento está en una suscripción diferente del trabajo de transmisión).
3. Nombre de la tabla de salida (se creará si no existe).
4. Clave de partición (obligatoria).
5. Clave de fila

Para un mejor diseño de la clave de partición y la clave de fila, consulte el siguiente artículo [Diseño de una estrategia de partición escalable para el almacenamiento de tablas de Azure](https://msdn.microsoft.com/library/azure/hh508997.aspx).

### Uso de Almacenamiento de tablas de Azure como salida ###

![graphic11][graphic11]

Vaya a la pestaña de resultados del trabajo y haga clic en el comando **AGREGAR SALIDA** y haga clic en Siguiente.

![graphic12][graphic12]

Elija **Almacenamiento de tablas** como salida.

![graphic13][graphic13]

Escriba la información de la tabla de Azure en la página siguiente. El alias de salida es el nombre que puede usar en la consulta para dirigir la salida de la consulta a esta tabla.

![graphic14][graphic14]

![graphic15][graphic15]

Tamaño del lote es el número de registros para una operación por lotes. Normalmente, el valor predeterminado es suficiente para la mayoría de los trabajos. Consulte [Especificaciones de la operación por lotes de tablas](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obtener más información sobre la modificación de esta configuración.

Si existe una cuenta de Almacenamiento de Azure dentro de la misma suscripción que utiliza para crear el trabajo, seleccione "Usar cuenta de almacenamiento de la suscripción actual" y seleccione la cuenta de almacenamiento en la lista desplegable.

Haga clic en siguiente para agregar esta salida. Deberían iniciarse dos operaciones; la primera es agregar la salida.

![graphic16][graphic16]

La segunda es probar la conexión. Análisis de transmisiones de Azure intentará conectarse a esa cuenta de almacenamiento y verificar que las credenciales especificadas sean correctas.

## Colas del Bus de servicio ##
---
### Introducción a conceptos de colas del Bus de servicio ###
Las colas del Bus de servicio ofrecen una entrega de mensajes según el modelo El primero en entrar es el primero en salir (FIFO) a uno o más destinatarios de la competencia. Normalmente, se espera que los receptores reciban y procesen los mensajes en el orden temporal en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes.

Para obtener más información sobre colas del Bus de servicio, consulte [Colas, temas y suscripciones del Bus de servicio](https://msdn.microsoft.com/library/azure/hh367516.aspx "Colas, temas y suscripciones del bus de servicio") y [Introducción a colas del Bus de servicio](http://blogs.msdn.com/b/appfabric/archive/2011/05/17/an-introduction-to-service-bus-queues.aspx "Introducción a colas del Bus de servicio").

### Parámetros ###

Para empezar a usar una salida de colas del Bus de servicio, será necesaria la siguiente información:

1. Alias de salida: cualquier alias de salida de nombre descriptivo al que es fácil hacer referencia. Este alias de salida es especialmente útil si se decide tener varias salidas para un trabajo. En ese caso, se hará referencia a este alias en la consulta de trabajo.
2. El espacio de nombres y el nombre del Bus de servicio.
3. Nombre de cola: las colas son entidades de mensajería, similares a los centros de eventos y temas. Están diseñados para recopilar secuencias de eventos de una serie de diferentes dispositivos y servicios. Cuando se crea una cola, se proporciona también un nombre específico.
4. Qué formato de serialización se usa para los datos (Avro, CSV y JSON).

### Adición de las colas del Bus de servicio como una salida ###

![graphic31][graphic31]

Después, proporcione los detalles que se muestran a continuación y seleccione Siguiente.

![graphic32][graphic32]

Compruebe que el formato de datos y la serialización sean correctos.

![graphic33][graphic33]

## Temas de Bus de servicio ##
---
### Introducción a conceptos de temas del Bus de servicio ###
Mientras las colas del Bus de servicio proporciona un método de comunicación uno a uno del remitente al receptor, los temas del Bus de servicio proporciona una forma de comunicación de uno a muchos.

Para obtener más información sobre temas del Bus de servicio, consulte [Colas, temas y suscripciones del Bus de servicio](https://msdn.microsoft.com/library/azure/hh367516.aspx "Colas, temas y suscripciones del bus de servicio") y [Introducción a temas del Bus de servicio](http://blogs.msdn.com/b/appfabric/archive/2011/05/25/an-introduction-to-service-bus-topics.aspx "Introducción a temas del Bus de servicio").

### Parámetros ###

Para empezar a usar una salida de temas del Bus de servicio, será necesaria la siguiente información:

1. Alias de salida: cualquier alias de salida de nombre descriptivo al que es fácil hacer referencia. Este alias de salida es especialmente útil si se decide tener varias salidas para un trabajo. En ese caso, se hará referencia a este alias en la consulta.
2. El espacio de nombres y el nombre del Bus de servicio.
3. Nombre de tema: los temas son entidades de mensajería, similares a los centros de eventos y colas. Están diseñados para recopilar secuencias de eventos de una serie de diferentes dispositivos y servicios. Cuando se crea un tema, se proporciona también un nombre específico. Los mensajes enviados a un tema no estarán disponibles a menos que se cree una suscripción, así que asegúrese de que hay una o más suscripciones en el tema.
4. Qué formato de serialización se usa para los datos (Avro, CSV y JSON).

### Adición de los temas del Bus de servicio como una salida ###

Seleccione salida para los temas del Bus de servicio.

![graphic34][graphic34]

Después, proporcione los detalles que se muestran a continuación y seleccione Siguiente.

![graphic35][graphic35]

Compruebe que el formato de datos y la serialización sean correctos.

![graphic33][graphic33]


## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




[graphic1]: ./media/stream-analytics-connect-data-event-outputs/1-stream-analytics-connect-data-event-input-output.png
[graphic2]: ./media/stream-analytics-connect-data-event-outputs/2-stream-analytics-connect-data-event-input-output.png
[graphic3]: ./media/stream-analytics-connect-data-event-outputs/3-stream-analytics-connect-data-event-input-output.png
[graphic4]: ./media/stream-analytics-connect-data-event-outputs/4-stream-analytics-connect-data-event-input-output.png
[graphic5]: ./media/stream-analytics-connect-data-event-outputs/5-stream-analytics-connect-data-event-input-output.png
[graphic6]: ./media/stream-analytics-connect-data-event-outputs/6-stream-analytics-connect-data-event-input-output.png
[graphic7]: ./media/stream-analytics-connect-data-event-outputs/7-stream-analytics-connect-data-event-input-output.png
[graphic8]: ./media/stream-analytics-connect-data-event-outputs/8-stream-analytics-connect-data-event-input-output.png
[graphic9]: ./media/stream-analytics-connect-data-event-outputs/9-stream-analytics-connect-data-event-input-output.png
[graphic10]: ./media/stream-analytics-connect-data-event-outputs/10-stream-analytics-connect-data-event-input-output.png
[graphic11]: ./media/stream-analytics-connect-data-event-outputs/11-stream-analytics-connect-data-event-input-output.png
[graphic12]: ./media/stream-analytics-connect-data-event-outputs/12-stream-analytics-connect-data-event-input-output.png
[graphic13]: ./media/stream-analytics-connect-data-event-outputs/13-stream-analytics-connect-data-event-input-output.png
[graphic14]: ./media/stream-analytics-connect-data-event-outputs/14-stream-analytics-connect-data-event-input-output.png
[graphic15]: ./media/stream-analytics-connect-data-event-outputs/15-stream-analytics-connect-data-event-input-output.png
[graphic16]: ./media/stream-analytics-connect-data-event-outputs/16-stream-analytics-connect-data-event-input-output.png
[graphic17]: ./media/stream-analytics-connect-data-event-outputs/17-stream-analytics-connect-data-event-input-output.png
[graphic18]: ./media/stream-analytics-connect-data-event-outputs/18-stream-analytics-connect-data-event-input-output.png
[graphic19]: ./media/stream-analytics-connect-data-event-outputs/19-stream-analytics-connect-data-event-input-output.png
[graphic20]: ./media/stream-analytics-connect-data-event-outputs/20-stream-analytics-connect-data-event-input-output.png
[graphic21]: ./media/stream-analytics-connect-data-event-outputs/21-stream-analytics-connect-data-event-input-output.png
[graphic22]: ./media/stream-analytics-connect-data-event-outputs/22-stream-analytics-connect-data-event-input-output.png
[graphic23]: ./media/stream-analytics-connect-data-event-outputs/23-stream-analytics-connect-data-event-input-output.png
[graphic24]: ./media/stream-analytics-connect-data-event-outputs/24-stream-analytics-connect-data-event-input-output.png
[graphic25]: ./media/stream-analytics-connect-data-event-outputs/25-stream-analytics-connect-data-event-input-output.png
[graphic26]: ./media/stream-analytics-connect-data-event-outputs/26-stream-analytics-connect-data-event-input-output.png
[graphic27]: ./media/stream-analytics-connect-data-event-outputs/27-stream-analytics-connect-data-event-input-output.png
[graphic28]: ./media/stream-analytics-connect-data-event-outputs/28-stream-analytics-connect-data-event-input-output.png
[graphic29]: ./media/stream-analytics-connect-data-event-outputs/29-stream-analytics-connect-data-event-input-output.png
[graphic30]: ./media/stream-analytics-connect-data-event-outputs/30-stream-analytics-connect-data-event-input-output.png
[graphic31]: ./media/stream-analytics-connect-data-event-outputs/31-stream-analytics-connect-data-event-input-output.png
[graphic32]: ./media/stream-analytics-connect-data-event-outputs/32-stream-analytics-connect-data-event-input-output.png
[graphic33]: ./media/stream-analytics-connect-data-event-outputs/33-stream-analytics-connect-data-event-input-output.png
[graphic34]: ./media/stream-analytics-connect-data-event-outputs/34-stream-analytics-connect-data-event-input-output.png
[graphic35]: ./media/stream-analytics-connect-data-event-outputs/35-stream-analytics-connect-data-event-input-output.png
[graphic36]: ./media/stream-analytics-connect-data-event-outputs/36-stream-analytics-connect-data-event-input-output.png
[graphic37]: ./media/stream-analytics-connect-data-event-outputs/37-stream-analytics-connect-data-event-input-output.png
[graphic38]: ./media/stream-analytics-connect-data-event-outputs/38-stream-analytics-connect-data-event-input-output.png

<!---HONumber=Oct15_HO3-->