<properties 
	pageTitle="Creación de entradas de Análisis de transmisiones de Azure | Microsoft Azure" 
	description="Obtenga información acerca de cómo conectarse a y configurar los orígenes de entrada para soluciones de Análisis de transmisiones."
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
	ms.date="08/05/2015" 
	ms.author="jeffstok"/>

# Creación de entradas de Análisis de transmisiones

## Descripción de entradas de Análisis de transmisiones
---
Las entradas de Análisis de transmisiones se definen como una conexión a un origen de datos. A medida que los datos se envían a ese origen de datos, el trabajo de Análisis de transmisiones los consume y los procesa en tiempo real. Las entradas se dividen en dos grupos distintos, entradas de flujo de datos y entradas de datos de referencia. Análisis de transmisiones cuenta con integración de primera clase con Almacenamiento de blobs y Centros de eventos de orígenes de entrada desde dentro y fuera de la suscripción de trabajo. Los formatos de datos admitidos son Avro, CSV y JSON.

## Entradas de secuencia de datos
---
En el nivel básico, las definiciones de trabajo de Análisis de transmisiones deben incluir al menos un origen de entrada de secuencia de datos para su uso y transformación por parte del trabajo. Almacenamiento de blobs de Azure y Centros de eventos de Azure se admiten como orígenes de entrada de flujos de datos. Los orígenes de entrada de Centros de eventos de Azure se usan para recopilar transmisiones de eventos desde varios dispositivos y servicios. Los ejemplos de transmisiones de datos pueden ser elementos como fuentes de actividades de redes sociales, información sobre acciones o datos de sensores.

De forma alternativa, Almacenamiento de blobs de Azure puede usarse como origen de entrada para la ingesta de datos en masa. Es importante tener en cuenta que cuando se usa Blobs de Azure, los datos se encuentran en reposo y, por lo tanto, Análisis de transmisiones interpretará que todos los datos contenidos en un blob tienen una marca de tiempo de la creación del propio blob. Es decir, a menos que los registros del blob contengan marcas de tiempo y se utilice la palabra clave MARCA DE TIEMPO DE.

## Entradas de datos de referencia
---
Análisis de transmisiones también admite un segundo tipo de origen de entrada: datos de referencia. Se trata de datos auxiliares que se usan normalmente para realizar correlaciones y búsquedas, y los datos de aquí suelen ser estáticos o cambiar con poca frecuencia. Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia. Los blobs de origen de datos de referencia están limitados a 50 MB de tamaño.

Es posible actualizar los datos de referencia especificando un patrón de ruta de acceso en la configuración de entrada que usa los tokens de {date} y {time}. El trabajo cargará el blob correspondiente según la fecha y hora que se codifican en los nombres de blob con la zona horaria UTC. Esta secuencia de blobs de datos de referencia con una fecha y hora codificadas es necesaria para garantizar la coherencia de resultados. Por ejemplo, si hay un retraso en el procesamiento y tenemos que volver a cargar un archivo blob, esperamos que haya un archivo en la misma ubicación sin que se modifique. De lo contrario, es posible que se visualice un resultado distinto. El único escenario compatible es agregar nuevos blobs con una fecha y hora futuras codificadas en el patrón de ruta de acceso.

Por ejemplo, si el trabajo tiene una entrada de referencia configurada en el portal con el patrón de la ruta de acceso: /sample/{date}/{time}/products.csv, donde el formato de fecha es "AAAA-MM-DD" y el formato de hora es "HH: mm", el trabajo seleccionará un archivo de nombre /sample/2015-04-16/17:30/products.csv a las 5:30 p.m. el 16 de abril de 2015 según la hora UTC.


> [AZURE.NOTE]Actualmente, los trabajos de Análisis de transmisiones buscan datos de actualización de blobs de referencia solo cuando la hora coincide con la hora codificada en el nombre de blob: por ejemplo, búsqueda de trabajos /sample/2015-04-16/17:30/products.csv entre las 5:30 p.m. y las 5:30:59.999999999 p.m. el 16 de abril de 2015 según la hora UTC. Cuando el reloj marca las 5:31 p.m., para de buscar /sample/2015-04-16/17:30/products.csv y comienza a buscar /sample/2015-04-16/17:31/products.csv.

La única vez que se tienen en cuenta los blobs de datos de referencia anteriores es cuando comienza el trabajo. En ese momento, el trabajo busca el blob que tiene la hora/fecha más reciente codificadas con su nombre con un valor antes de la hora de inicio del trabajo (el blob de datos de referencia más reciente desde antes de la hora de inicio del trabajo). Esto se hace para garantizar que no hay datos de referencia vacíos establecidos al principio del trabajo. Si no se encuentra ninguno, se producirá un error en el trabajo y se mostrará un aviso de diagnóstico al usuario:

    “Initializing input without a valid reference data blob for UTC time <job start time>.”

 
## Creación de una entrada de secuencia de datos
---
Para crear una entrada de secuencia de datos, vaya a la pestaña **Entradas** del trabajo Análisis de transmisiones y haga clic en **Agregar entrada** en la parte inferior de la página.

![imagen1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

 La creación de una entrada de secuencia de datos ofrecerá dos opciones al usuario, [**Centro de eventos**](Creating-an-Event-hub-input-data-stream) o [**Almacenamiento de blobs**](Creación de una secuencia de datos de entrada de Almacenamiento de blobs). Seleccione el valor adecuado para el tipo de secuencia que se va a procesar.

![imagen2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

## Creación de una secuencia de entrada de datos de Centro de eventos
---
### Información general de Centros de eventos
Los centros de eventos son ingestores de eventos altamente escalables; normalmente son la manera más común para introducir datos de Análisis de transmisiones. Están diseñados para recopilar secuencias de eventos de una serie de diferentes dispositivos y servicios. Centros de eventos y Análisis de transmisiones juntos proporcionan a los clientes una solución de extremo a extremo para el análisis en tiempo real; los centros de eventos permiten a los clientes incluir eventos en Azure en tiempo real y los trabajos de Análisis de transmisiones pueden procesarlos en tiempo real. Por ejemplo, los clientes pueden enviar clics en la web, lecturas del sensor, eventos de registro en línea en los centros de eventos y crear trabajos de Análisis de transmisiones para usar centros de eventos como las secuencias de datos de entrada para filtrar, agregar y unir en tiempo real. Los centros de eventos pueden usarse también para la salida de datos. Un posible uso de Centros de eventos es cuando la salida de un trabajo de Análisis de transmisiones será la entrada de otro trabajo de transmisión. Para más información sobre Centros de eventos, vea la documentación de [Centros de eventos](https://azure.microsoft.com/services/event-hubs/ "Centros de eventos").

### Grupos de consumidores
Cada entrada de trabajo de Análisis de transmisiones debe configurarse para tener su propio grupo de consumidores de Centro de eventos. Cuando un trabajo contiene autocombinación o varias entradas, es posible que más un lector de bajada lea alguna entrada, lo que hace que el número total de lectores en un solo grupo de consumidores supere el límite del Centro de eventos de 5 lectores por grupo de consumidores. En este caso, la consulta deberá dividirse en varias consultas y resultados intermedios que se enrutan a través de Centros de eventos adicionales. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por Centro de eventos. Para obtener más información, consulte la [Guía de programación de Centros de eventos](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guía de programación de Centros de eventos").

## Ejemplo de creación de una entrada de Centro de eventos en el Portal de Azure
---
A continuación se muestra un ejemplo paso a paso para configurar un Centro de eventos como entrada. Para empezar a usar una entrada de Centro de eventos, el usuario debe tener la siguiente información recopilada sobre el Centro de eventos:

1. Alias de entrada: un alias de entrada con nombre descriptivo que hará referencia a la consulta de trabajo.
2. El nombre del espacio de nombres de Bus de servicio 
3. El nombre del Centro de eventos.
3. El nombre de la directiva del Centro de eventos.
4. Opcional: Nombre del grupo de consumidores del Centro de eventos.
	- El grupo de consumidores para introducir datos desde el centro de eventos. Si no se especifica, los trabajos de Análisis de transmisiones utilizan el grupo de consumidores predeterminado para introducir datos desde el centro de eventos. Se recomienda usar un grupo de consumidores distinto para cada trabajo de Análisis de transmisiones.
5. Se utiliza el formato de serialización para la secuencia de datos (Avro, CSV y JSON).

Seleccione primero **AGREGAR ENTRADA** desde la página Entradas del trabajo Análisis de transmisiones.

![imagen1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

A continuación, seleccione Centro de eventos como entrada.

![imagen6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

A continuación, especifique la información en los campos como se muestra a continuación para Centro de eventos.

![imagen7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

A continuación, compruebe que el formato de serialización de eventos sea correcto para la secuencia de datos.

![imagen8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

A continuación, seleccione **Completo** y se creará la entrada de secuencia de datos de Centro de eventos.

## Creación de una secuencia de datos de entrada de Almacenamiento de blobs
---
El almacenamiento de blobs ofrece una solución rentable y escalable para aquellos escenarios con grandes cantidades de datos no estructurados para almacenar en la nube. Se considera normalmente que estos datos están en reposo. Un escenario donde un blob puede ser útil para una entrada de secuencia de datos sería el análisis de registros en los que los registros ya se han capturado a partir de sistemas y que tienen que analizarse, y datos significativos extraídos de ellos. Otro posible escenario es el análisis de almacenamiento de datos en reposo. Para obtener más información sobre Almacenamiento de blobs, vea la documentación de [Almacenamiento de blobs](http://azure.microsoft.com/services/storage/blobs/).

A continuación se muestra un ejemplo paso a paso para configurar Almacenamiento de blobs como entrada. Para empezar a usar una entrada de Almacenamiento de blobs de Azure, se requiere la siguiente información:

1. Alias de entrada: un alias de entrada con nombre descriptivo que hará referencia a la consulta de trabajo.
2. Si la cuenta de almacenamiento se encuentra en una suscripción distinta que el trabajo de streaming, serán necesarios el nombre de cuenta de almacenamiento y la clave de cuenta de almacenamiento.
3. El nombre del contenedor.
4. El prefijo del nombre de archivo.
5. Qué formato de serialización se usa para los datos (Avro, CSV y JSON).

En la pestaña de entradas del trabajo Análisis de transmisiones, haga clic en **AGREGAR ENTRADA** y, a continuación, seleccione la opción predeterminada, **Secuencia de datos**. ![imagen1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

A continuación, seleccione **Almacenamiento de blobs**

![imagen2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

A continuación, especifique la información en los campos como se muestra a continuación para la cuenta de almacenamiento.

![imagen3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

> [AZURE.NOTE]Si hace clic en la casilla Configurar opciones avanzadas, puede especificar el patrón de prefijo de ruta de acceso para blobs de lectura en una ruta de acceso personalizada. Si no se especifica este campo, Análisis de transmisiones leerá todos los blobs del contenedor.

![imagen4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

A continuación, elija la configuración de serialización correcta para los datos. Las opciones son JSON, CSV y Avro.

![imagen5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

A continuación, seleccione **Completo** y se creará la entrada de secuencia de datos de Almacenamiento de blobs.

## Creación de una entrada de datos de referencia de Almacenamiento de blobs
---
Puede aprovecharse Almacenamiento de blobs para utilizar capacidades de datos de referencia.

A continuación se muestra un ejemplo paso a paso para configurar Almacenamiento de blobs como entrada de datos de referencia. Para comenzar, se requiere la siguiente información:

1. Alias de entrada: un alias de entrada con nombre descriptivo que hará referencia a la consulta de trabajo.
2. Si la cuenta de almacenamiento se encuentra en una suscripción distinta que el trabajo de streaming, serán necesarios el nombre de cuenta de almacenamiento y la clave de cuenta de almacenamiento.
3. El nombre del contenedor.
4. El prefijo del nombre de archivo.
5. Qué formato de serialización se usa para los datos (CSV y JSON).
6. El patrón de la ruta de acceso. La ruta de acceso de archivo para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede elegir especificar una o más instancias de las siguientes 2 variables: {date} y {time}.


En la pestaña de entradas del trabajo Análisis de transmisiones, haga clic en **AGREGAR ENTRADA** y, a continuación, seleccione la opción predeterminada, **Datos de referencia**.

![imagen9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

A continuación, especifique la información en los campos como se muestra a continuación para la cuenta de almacenamiento y Almacenamiento de blobs.

![imagen10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

Asegúrese de desplazarse hacia abajo para especificar el patrón de prefijo para la jerarquía de la ruta de acceso que contiene el blob, así como el formato de los campos de fecha y hora.

![imagen12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

Ahora elija la configuración de serialización correcta para los datos. Las opciones son JSON, CSV y Avro.

![imagen11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)

A continuación, seleccione **Completo** y se creará la entrada de datos de referencia de Almacenamiento de blobs.


## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-es/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=August15_HO6-->