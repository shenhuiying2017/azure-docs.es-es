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
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>

# Creación de entradas de Análisis de transmisiones

## Descripción de entradas de Análisis de transmisiones
---
Las entradas de Análisis de transmisiones se definen como una conexión a un origen de datos. Análisis de transmisiones cuenta con integración de primera clase con Almacenamiento de blobs y Centro de eventos de orígenes de Azure desde dentro y fuera de la suscripción de trabajo. A medida que los datos se envían a ese origen de datos, el trabajo de Análisis de transmisiones los consume y los procesa en tiempo real. Las entradas se dividen en dos tipos distintos: entradas de flujo de datos y entradas de datos de referencia.

## Entradas de secuencia de datos
---
Los trabajos de Análisis de transmisiones deben incluir, al menos, una entrada de secuencia de datos para que el trabajo la consuma y transforme. Almacenamiento de blobs de Azure y Centros de eventos de Azure se admiten como orígenes de entrada de flujos de datos. Centros de eventos de Azure se utilizan para recopilar transmisiones de eventos desde varios dispositivos y servicios, como fuentes de actividades de medios sociales, información sobre acciones o datos de sensores. De forma alternativa, Almacenamiento de blobs de Azure puede usarse como origen de entrada para la ingesta de datos en masa.

## Entradas de datos de referencia
---
Análisis de transmisiones también admite un segundo tipo de entrada, conocido como datos de referencia. Se trata de datos auxiliares que se usan normalmente para realizar correlaciones y búsquedas, y los datos de aquí suelen ser estáticos o cambiar con poca frecuencia. Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia. Los blobs de origen de datos de referencia están limitados a 50 MB de tamaño.

## Creación de una secuencia de entrada de datos de Centro de eventos
---
### Información general de Centros de eventos
Centros de eventos son ingestores de eventos altamente escalables y se trata del método de ingestión de datos más común a un trabajo de Análisis de transmisiones. Centros de eventos y Análisis de transmisiones juntos proporcionan a los clientes una solución de extremo a extremo para el análisis en tiempo real; los centros de eventos permiten a los clientes incluir eventos en Azure en tiempo real y los trabajos de Análisis de transmisiones pueden procesarlos en tiempo real. Por ejemplo, los clientes pueden enviar clics en la web, lecturas del sensor, eventos de registro en línea en los centros de eventos y crear trabajos de Análisis de transmisiones para usar centros de eventos como las secuencias de datos de entrada para filtrar, agregar y unir en tiempo real. Los centros de eventos pueden usarse también para la salida de datos. Para obtener más detalles sobre Centros de eventos, consulte la documentación de [Centros de eventos](https://azure.microsoft.com/services/event-hubs/ "Centros de eventos").

### Grupos de consumidores
Cada entrada de Centro de eventos de Análisis de transmisiones se debe configurar para tener su propio grupo de consumidores. Cuando un trabajo contiene varias entradas o autocombinación, es posible que más de un lector de bajada lea alguna de las entradas, lo que afecta la cantidad de lectores que existen en un solo grupo de consumidores. Para evitar superar el límite de Centro de eventos de 5 lectores por grupo de consumidores por partición, una práctica recomendable es designar un grupo de consumidores para cada trabajo de Análisis de transmisiones. Tenga en cuenta que también hay un límite de 20 grupos de consumidores por Centro de eventos. Para obtener detalles, consulte la [Guía de programación de Centros de eventos](https://msdn.microsoft.com/library/azure/dn789972.aspx "Guía de programación de Centros de eventos").

## Creación de un flujo de datos de entrada de Centro de eventos
---
### Incorporación de un Centro de eventos como una entrada de flujo de datos  ###

1. En la pestaña de entradas del trabajo Análisis de transmisiones, haga clic en **AGREGAR ENTRADA** y luego seleccione la opción predeterminada, **Flujo de datos**; a continuación, haga clic con el botón derecho.

    ![imagen1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Luego seleccione **Centro de eventos**.

    ![imagen6](./media/stream-analytics-connect-data-event-inputs/06-stream-analytics-create-inputs.png)

3. Escriba o seleccione los campos siguientes y haga clic con el botón derecho:

    - **Alias de entrada**: nombre descriptivo que se utilizará en la consulta de trabajo para hacer referencia a esta entrada.  
    - **Espacio de nombres del bus de servicio**: un espacio de nombres del bus de servicio es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres del Bus de servicio.  
    - **Centro de eventos**: el nombre de la entrada de Centro de eventos.  
    - **Nombre de la directiva del centro de eventos**: la directiva de acceso compartido, que se puede crear en la pestaña Configuración de Centro de eventos. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso.  
    - **Grupo de consumidores del Centro de eventos** (opcional): el grupo de consumidores para introducir datos desde Centro de eventos. Si no se especifica, los trabajos de Análisis de transmisiones utilizan el grupo de consumidores predeterminado para introducir datos desde el centro de eventos. Se recomienda usar un grupo de consumidores distinto para cada trabajo de Análisis de transmisiones.  

    ![imagen7](./media/stream-analytics-connect-data-event-inputs/07-stream-analytics-create-inputs.png)

4. Especifique la configuración siguiente:

    - **Formato de serialización de eventos**: para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización (JSON, CSV o Avro) que utiliza para los flujos de datos entrantes.  
    - **Codificación**: por el momento, UTF-8 es el único formato de codificación compatible.  

    ![imagen8](./media/stream-analytics-connect-data-event-inputs/08-stream-analytics-create-inputs.png)

5. Haga clic en el botón de comprobación para completar el asistente y compruebe que Análisis de transmisiones se puede conectar correctamente a Centro de eventos.

## Creación de una entrada de flujo de datos de Almacenamiento de blobs
---
El almacenamiento de blobs ofrece una solución rentable y escalable para aquellos escenarios con grandes cantidades de datos no estructurados para almacenar en la nube. Los datos de Almacenamiento de blobs generalmente se consideran como datos "en reposo", pero Análisis de transmisiones puede procesarlos como un flujo de datos. Un escenario común para las entradas de Almacenamiento de blobs con Análisis de transmisiones es el procesamiento de registros, donde la telemetría se captura desde un sistema y es necesario analizarla y procesarla para extraer datos significativos. Es importante tener en cuenta que la marca de tiempo predeterminada de los eventos de Almacenamiento de blobs en Análisis de transmisiones es la marca de tiempo cuando se creó el blob. Para procesar los datos como una transmisión mediante una marca de tiempo en la carga del evento, se debe utilizar la palabra clave [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx). Para obtener más información sobre Almacenamiento de blobs, consulte la documentación de [Almacenamiento de blobs](http://azure.microsoft.com/services/storage/blobs/).

### Incorporación de Almacenamiento de blobs como una entrada de flujo de datos  ###

1. En la pestaña de entradas del trabajo Análisis de transmisiones, haga clic en **AGREGAR ENTRADA** y luego seleccione la opción predeterminada, **Flujo de datos**; a continuación, haga clic con el botón derecho.

    ![imagen1](./media/stream-analytics-connect-data-event-inputs/01-stream-analytics-create-inputs.png)

2. Seleccione **Almacenamiento de blobs** y haga clic con el botón secundario.

    ![imagen2](./media/stream-analytics-connect-data-event-inputs/02-stream-analytics-create-inputs.png)

3. Escriba o seleccione los campos siguientes:

    - **Alias de entrada**: nombre descriptivo que se utilizará en la consulta de trabajo para hacer referencia a esta entrada.  
    - **Cuenta de almacenamiento**: si la cuenta de almacenamiento se encuentra en una suscripción distinta que el trabajo de streaming, serán necesarios el nombre de cuenta de almacenamiento y la clave de cuenta de almacenamiento.  
    - **Contenedor de almacenamiento**: los contenedores proporcionan una agrupación lógica de los blobs almacenados en el servicio BLOB de Microsoft Azure. Cuando carga un blob al servicio BLOB, debe especificar un contenedor para ese blob.  

    ![imagen3](./media/stream-analytics-connect-data-event-inputs/03-stream-analytics-create-inputs.png)

4. Haga clic en la casilla **Configurar opciones avanzadas** para elegir la opción de configurar el patrón de prefijo de ruta de acceso para blobs de lectura en una ruta de acceso personalizada. Si no se especifica este campo, Análisis de transmisiones leerá todos los blobs del contenedor.

    ![imagen4](./media/stream-analytics-connect-data-event-inputs/04-stream-analytics-create-inputs.png)

5. Elija la configuración siguiente:

    - **Formato de serialización de eventos**: para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización (JSON, CSV o Avro) que utiliza para los flujos de datos entrantes.  
    - **Codificación**: por el momento, UTF-8 es el único formato de codificación compatible.  


    ![imagen5](./media/stream-analytics-connect-data-event-inputs/05-stream-analytics-create-inputs.png)

6. Haga clic en el botón de comprobación para completar el asistente y compruebe que Análisis de transmisiones se puede conectar correctamente a la cuenta de Almacenamiento de blobs.

## Creación de datos de referencia de Almacenamiento de blobs
---
Almacenamiento de blobs se puede utilizar para definir los datos de referencia de un trabajo de Análisis de transmisiones. Son datos estáticos o que cambian con lentitud que se utilizan para realizar consultas o correlacionar datos. Es posible actualizar los datos de referencia especificando un patrón de ruta de acceso en la configuración de entrada que usa los tokens de {date} y {time}. Análisis de flujo actualizará las definiciones de datos de referencia según este patrón de ruta de acceso. Por ejemplo, un patrón de `"/sample/{date}/{time}/products.csv"` con un formato de fecha de "AAAA-MM-DD" y un formato de hora de "HH:mm" indica a Análisis de transmisiones que seleccione el blob actualizado `"/sample/2015-04-16/17:30/products.csv"` a las 5:30 p.m. del 16 de abril de 2015, zona horaria UTC.

> [AZURE.NOTE]Actualmente, los trabajos de Análisis de transmisiones buscan datos de actualización de blobs de referencia solo cuando la hora coincide con la hora codificada en el nombre de blob: por ejemplo, búsqueda de trabajos /sample/2015-04-16/17:30/products.csv entre las 5:30 p.m. y las 5:30:59.999999999 p.m. el 16 de abril de 2015 según la hora UTC. Cuando el reloj marca las 5:31 p.m., para de buscar /sample/2015-04-16/17:30/products.csv y comienza a buscar /sample/2015-04-16/17:31/products.csv.

La única vez que se tienen en cuenta los blobs de datos de referencia anteriores es cuando comienza el trabajo. En ese momento, el trabajo busca el blob que tiene la hora/fecha más reciente codificadas con su nombre con un valor antes de la hora de inicio del trabajo (el blob de datos de referencia más reciente desde antes de la hora de inicio del trabajo). Esto se hace para garantizar que no hay datos de referencia vacíos establecidos al principio del trabajo. Si no se encuentra ninguno, se producirá un error en el trabajo y se mostrará un aviso de diagnóstico al usuario:

### Incorporación de Almacenamiento de blobs como datos de referencia  ###

1. En la pestaña de entradas del trabajo Análisis de transmisiones, haga clic en **AGREGAR ENTRADA** y luego seleccione **Datos de referencia**; a continuación, haga clic con el botón derecho.

    ![imagen9](./media/stream-analytics-connect-data-event-inputs/09-stream-analytics-create-inputs.png)

2.	Escriba o seleccione los campos siguientes:

    - **Alias de entrada**: nombre descriptivo que se utilizará en la consulta de trabajo para hacer referencia a esta entrada.  
    - **Cuenta de almacenamiento**: si la cuenta de almacenamiento se encuentra en una suscripción distinta que el trabajo de streaming, serán necesarios el nombre de cuenta de almacenamiento y la clave de cuenta de almacenamiento.  
    - **Contenedor de almacenamiento**: los contenedores proporcionan una agrupación lógica de los blobs almacenados en el servicio BLOB de Microsoft Azure. Cuando carga un blob al servicio BLOB, debe especificar un contenedor para ese blob.  
    - **Patrón de ruta de acceso**: la ruta de acceso de archivo para ubicar los blobs dentro del contenedor especificado. Dentro de la ruta, puede elegir especificar una o más instancias de las siguientes 2 variables: {date}, {time}.  

    ![imagen10](./media/stream-analytics-connect-data-event-inputs/10-stream-analytics-create-inputs.png)

3. Elija la configuración siguiente:

    - **Formato de serialización de eventos**: para asegurarse de que las consultas funcionen como se espera, Análisis de transmisiones debe saber cuál es el formato de serialización (JSON, CSV o Avro) que utiliza para los flujos de datos entrantes.  
    - **Codificación**: por el momento, UTF-8 es el único formato de codificación compatible.  

    ![imagen12](./media/stream-analytics-connect-data-event-inputs/12-stream-analytics-create-inputs.png)

4.	Haga clic en el botón de comprobación para completar el asistente y compruebe que Análisis de transmisiones se puede conectar correctamente a la cuenta de Almacenamiento de blobs.

    ![imagen11](./media/stream-analytics-connect-data-event-inputs/11-stream-analytics-create-inputs.png)


## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Nov15_HO3-->