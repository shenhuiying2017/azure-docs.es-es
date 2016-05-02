<properties
   pageTitle="Transmisión de datos de Análisis de transmisiones al Almacén de Data Lake | Azure"
   description="Uso de Análisis de transmisiones para transmitir datos en el Almacén de Azure Data Lake"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/20/2016"
   ms.author="nitinme"/>

# Transmisión de datos del blob de Almacenamiento de Azure al Almacén de Data Lake mediante el Análisis de transmisiones

En este artículo aprenderá a utilizar el Almacén de Azure Data Lake como salida para un trabajo de Análisis de transmisiones de Azure. Este artículo muestra un escenario simple que lee datos desde un blob de Almacenamiento de Azure (entrada) y los escribe en el Almacén de Data Lake (salida).

>[AZURE.NOTE] En este momento, la creación y la configuración de salidas del Almacén de Data Lake para Análisis de transmisiones solo se admiten en el [Portal de Azure clásico](manage.windowsazure.com). Por lo tanto, algunas partes de este tutorial usarán el Portal de Azure clásico.

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

- **Habilite su suscripción de Azure** para la versión de vista previa pública del almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).

- **Cuenta de Almacenamiento de Azure**. Usará un contenedor de blobs desde esta cuenta para introducir datos en un trabajo de Análisis de transmisiones. Para este tutorial, va a crear una cuenta de almacenamiento denominada **datalakestoreasa** y un contenedor dentro de la cuenta denominado **datalakestoreasacontainer**. Una vez creado el contenedor, va a cargar un archivo de datos de ejemplo en él. Puede obtener un archivo de datos de ejemplo en el [repositorio Git de Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Puede utilizar varios clientes, como el [explorador de Almacenamiento de Azure](http://storageexplorer.com/), para cargar datos en un contenedor de blobs.

	>[AZURE.NOTE] Si crea la cuenta en el Portal de Azure, asegúrese de crearla con el modelo de implementación **clásico**. Esto garantiza que la cuenta de almacenamiento se puede acceder desde el Portal de Azure clásico, que es lo que usamos para crear un trabajo de Análisis de transmisiones. Para instrucciones sobre cómo crear una cuenta de almacenamiento desde el Portal de Azure con la implementación clásica, consulte [Acerca de las cuentas de Almacenamiento de Azure](../storage/storage-create-storage-account/#create-a-storage-account).
	>
	> O bien, puede crear una cuenta de almacenamiento desde el Portal de Azure clásico.

- **Cuenta de Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md).


## Creación de un trabajo de Análisis de transmisiones

Primero debe crear un trabajo de Análisis de transmisiones que incluya un origen de entrada y un destino de salida. Para este tutorial, el origen es un contenedor de blobs de Azure y el destino es el Almacén de Data Lake.

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).

2. En la parte inferior izquierda de la pantalla, haga clic en **Nuevo** > **Servicios de datos** > **Análisis de transmisiones** > **Creación rápida**. Proporcione los valores siguientes, tal como se muestra a continuación y haga clic en **Crear trabajo de Análisis de transmisiones**.

	![Creación de un trabajo de Análisis de transmisiones](./media/data-lake-store-stream-analytics/create.job.png "Creación de un trabajo de Análisis de transmisiones")

## Creación de una entrada de blob para el trabajo

1. Haga clic en la página del trabajo de Análisis de transmisiones, haga clic en la pestaña **Entradas** y después haga clic en **Agregar una entrada** para iniciar un asistente.

2. En la página **Agregar una entrada a su trabajo**, seleccione **Flujo de datos** y, finalmente, haga clic en la flecha de avance.

	![Agregar una entrada al trabajo](./media/data-lake-store-stream-analytics/create.input.1.png "Agregar una entrada al trabajo")

3. En la página **Agregar un flujo de datos a su trabajo**, seleccione **Almacenamiento de blobs** y, finalmente, haga clic en la flecha de avance.

	![Agregar un flujo de datos al trabajo](./media/data-lake-store-stream-analytics/create.input.2.png "Agregar un flujo de datos al trabajo")

4. En la página **Configuración del almacenamiento de blobs**, proporcione detalles sobre el almacenamiento de blobs que va a utilizar como origen de datos de entrada.

	![Proporcionar la configuración de Almacenamiento de blobs](./media/data-lake-store-stream-analytics/create.input.3.png "Proporcionar la configuración de Almacenamiento de blobs")

	* **Escriba un alias de entrada**. Se trata de un nombre único indicado para la entrada de trabajo.
	* **Seleccione una cuenta de almacenamiento**. Asegúrese de que la cuenta de almacenamiento está en la misma región que el trabajo de Análisis de transmisiones o incurrirá en costos adicionales derivados de mover datos entre regiones.
	* **Proporcione un contenedor de almacenamiento**. Puede crear un contenedor nuevo o seleccionar uno existente.

	Haga clic en la flecha hacia adelante.

5. En la página **Configuración de serialización**, establezca el formato de serialización como **CSV**, el delimitador como **tabulador**, la codificación como **UTF8** y, finalmente, haga clic en la marca de verificación.

	![Proporcionar la configuración de la serialización](./media/data-lake-store-stream-analytics/create.input.4.png "Proporcionar la configuración de la serialización")

6. Cuando haya terminado con el asistente, se agregará la entrada de blobs en la pestaña **Entradas** y la columna **Diagnóstico** debe mostrar **Aceptar**. También puede probar explícitamente la conexión a la entrada con el botón **Probar conexión** situado en la parte inferior.

## Creación de una salida del Almacén de Data Lake para el trabajo

1. Haga clic en la página del trabajo de Análisis de transmisiones, haga clic en la pestaña **Salidas** y después haga clic en **Agregar una salida** para iniciar un asistente.

2. En la página **Agregar una salida a su trabajo**, seleccione **Almacén de Data Lake** y, finalmente, haga clic en la flecha de avance.

	![Agregar una salida al trabajo](./media/data-lake-store-stream-analytics/create.output.1.png "Agregar una salida al trabajo")

3. En la página **Autorizar conexión**, si ya ha creado una cuenta de Almacén de Data Lake, haga clic en **Autorizar ahora**. De lo contrario, haga clic en **Registrarse ahora** para crear una nueva cuenta. Para este tutorial, supongamos que ya tiene una cuenta de Almacén de Data Lake creada (como se mencionó en el requisito previo). Se le autorizará automáticamente con las credenciales con las que ha iniciado sesión en el Portal de Azure clásico.

	![Autorizar el Almacén de Azure Data Lake](./media/data-lake-store-stream-analytics/create.output.2.png "Autorizar el Almacén de Azure Data Lake")

4. En la página **Configuración de Almacén de Data Lake**, escriba la información tal como se muestra en la captura de pantalla siguiente.

	![Especificar la configuración de Almacén de Data Lake](./media/data-lake-store-stream-analytics/create.output.3.png "Especificar la configuración de Almacén de Data Lake")

	* **Escriba un alias de salida**. Se trata de un nombre único indicado para la salida del trabajo.
	* **Especifique una cuenta de Almacén de Data Lake**. Debe de haberla creado, tal como se mencionó en el requisito previo.
	* **Especifique un patrón del prefijo de la ruta de acceso**. Esto es necesario para identificar los archivos de salida que se escriben en el Almacén de Data Lake por el trabajo de Análisis de transmisiones. Como los títulos de las salidas escritos por el trabajo están en un formato GUID, la inclusión de un prefijo ayudará a identificar la salida escrita. Si desea incluir una marca de fecha y hora como parte del prefijo, asegúrese de incluir `{date}/{time}` en el patrón del prefijo. Si incluye esta opción, los campos **Formato de fecha** y **Formato de hora** se habilitan y podrá seleccionar el formato preferido.

	Haga clic en la flecha hacia adelante.

5. En la página **Configuración de serialización**, establezca el formato de serialización como **CSV**, el delimitador como **tabulador**, la codificación como **UTF8** y, finalmente, haga clic en la marca de verificación.

	![Especificar el formato de salida](./media/data-lake-store-stream-analytics/create.output.4.png "Especificar el formato de salida")

6. Cuando haya terminado con el asistente, se agregará la salida de Almacén de Data Lake en la pestaña **Salidas** y la columna **Diagnóstico** debe mostrar **Aceptar**. También puede probar explícitamente la conexión a la salida con el botón **Probar conexión** situado en la parte inferior.

## Ejecución del trabajo de Análisis de transmisiones

Para ejecutar un trabajo de Análisis de transmisiones, debe ejecutar una consulta desde la pestaña Consulta. En este tutorial, puede ejecutar la consulta de ejemplo mediante el reemplazo de los marcadores de posición con los alias de entrada y salida del trabajo, tal como se muestra en la captura de pantalla siguiente.

![Ejecutar consulta](./media/data-lake-store-stream-analytics/run.query.png "Ejecutar consulta")

Haga clic en **Guardar** desde la parte inferior de la pantalla y, después, haga clic en **Iniciar**. En el cuadro de diálogo, seleccione **Hora personalizada** y, después, seleccione una fecha del pasado, como **1/1/2016**. Haga clic en la marca de verificación para iniciar el trabajo. Puede tardar unos minutos en iniciarse el trabajo.

![Establecer el tiempo de trabajo](./media/data-lake-store-stream-analytics/run.query.2.png "Establecer el tiempo de trabajo")

Cuando se inicia el trabajo, haga clic en la pestaña **Supervisión** para ver cómo se procesan los datos.

![Supervisar el trabajo](./media/data-lake-store-stream-analytics/run.query.3.png "Supervisar el trabajo")

Por último, puede usar el [Portal de Azure](portal.azure.com) para abrir la cuenta de Almacén de Data Lake y compruebe si los datos se han escrito correctamente en la cuenta.

![Comprobar salida](./media/data-lake-store-stream-analytics/run.query.4.png "Comprobar salida")

En el panel Explorador de datos, tenga en cuenta que la salida se escribe en una carpeta, según lo especificado en la configuración de salida del Almacén de Data Lake (`streamanalytics/job/output/{date}/{time}`).

## Consulte también

* [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0420_2016-->