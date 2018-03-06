---
title: "Tutorial de uso compartido de bicicletas: preparación avanzada de datos con Azure Machine Learning Workbench"
description: "En este tutorial, realizará la preparación de los datos de un extremo a otro mediante Azure Machine Learning Workbench"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: ca7239fd3e31c7a6cfc6fb64e04afb376e01c190
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>Tutorial: Uso de Azure Machine Learning Workbench para la preparación avanzada de datos (datos de uso compartido de bicicletas)
Azure Machine Learning (versión preliminar) es una solución integrada y completa de análisis avanzado y ciencia de datos diseñada para que los científicos de datos profesionales preparen datos, desarrollen experimentos e implementen modelos a escala de nube.

En este tutorial, usará Machine Learning (versión preliminar) para obtener información sobre cómo:
> [!div class="checklist"]
> * Preparar datos de forma interactiva con la herramienta de preparación de datos de Machine Learning.
> * Importar, transformar y crear un conjunto de datos de prueba.
> * Generar un paquete de preparación de datos.
> * Ejecutar el paquete de preparación de datos con Python.
> * Generar un conjunto de datos de entrenamiento mediante la reutilización del paquete de preparación de datos para los archivos de entrada adicionales.
> * Ejecutar scripts en una ventana de la CLI de Azure local.
> * Ejecutar scripts en un entorno de Azure HDInsight en la nube.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* Una instalación local de Azure Machine Learning Workbench. Para más información, siga la [guía de inicio rápido de instalación](quickstart-installation.md).
* Si no tiene instalada la CLI de Azure, siga las instrucciones para [instalar la última versión de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).
* Un [clúster HDInsights Spark](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal) creado en Azure.
* Una cuenta de almacenamiento de Azure.
* Debe estar familiarizado con la creación de un proyecto nuevo en Workbench.
* Aunque no es necesario, resulta útil tener instalado el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/), ya que le permite cargar, descargar y visualizar los blobs en su cuenta de Storage.

## <a name="data-acquisition"></a>Adquisición de datos
En este tutorial se usa el [conjunto de datos Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) y los datos meteorológicos de Boston de [NOAA](http://www.noaa.gov/).

1. Descargue los archivos de datos de los siguientes vínculos en su entorno de desarrollo local:

   * [Datos meteorológicos de Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Datos de los viajes de Hubway tomados del sitio web de Hubway:

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Descomprima cada archivo .zip después de la descarga.

## <a name="upload-data-files-to-azure-blob-storage"></a>Carga de archivos de datos en Azure Blob Storage
Puede usar Azure Blob Storage para hospedar los archivos de datos.

1. Use la misma cuenta de almacenamiento que se usó para el clúster de HDInsight que está usando.

    ![Cuenta de almacenamiento de clúster de HDInsight](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. Cree un nuevo contenedor denominado **data-files** para almacenar los archivos de datos de **BikeShare**.

3. Cargue los archivos de datos. Cargue `BostonWeather.csv` a una carpeta llamada `weather`. Cargue los archivos de datos de viajes en una carpeta llamada `tripdata`.

    ![Carga de los archivos de datos](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> También puede usar el Explorador de Azure Storage para cargar blobs. Use esta herramienta para ver el contenido de cualquiera de los archivos generados en el tutorial.

## <a name="learn-about-the-datasets"></a>Información sobre los conjuntos de datos
1. El archivo sobre el __tiempo de Boston__ contiene los siguientes campos relacionados con el tiempo, notificados cada hora:

   * **DATE**

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Los datos de __Hubway__ están organizados en archivos por año y mes. Por ejemplo, el archivo denominado `201501-hubway-tripdata.zip` contiene un archivo .csv con datos de enero de 2015. Los datos contienen los campos siguientes, donde cada fila representa un viaje en bicicleta:

   * **Duración del viaje (en segundos)**

   * **Fecha y hora de inicio**

   * **Fecha y hora de finalización**

   * **Nombre e identificador de la estación de inicio**

   * **Nombre e identificador de la estación de finalización**

   * **Identificador de bicicleta**

   * **Tipo de usuario (ocasional = usuario del pase de 24 o 72 horas; miembro = miembro anual o mensual)**

   * **Código postal (si el usuario es miembro)**

   * **Sexo (indicado por el miembro)**

## <a name="create-a-new-project"></a>Creación de un nuevo proyecto
1. Inicie **Machine Learning Workbench** desde el menú Inicio o el iniciador.

2. Cree un nuevo proyecto de Machine Learning. Haga clic en el botón **+** situado en la página **Projects** (Proyectos) o seleccione **File** > **New** (Archivo > Nuevo).

   * Use la plantilla **Bike Share**.

   * Asígnele al proyecto el nombre **BikeShare**. 

## <a id="newdatasource"></a>Crear un origen de datos

1. Cree un origen de datos. Seleccione el botón **Datos** (icono de cilindro) en la barra de herramientas de la izquierda para mostrar la vista **Data** (Datos).

   ![Pestaña de vista de datos](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Agregue un origen de datos. Seleccione el icono **+** y, después, **Add Data Source** (Agregar origen de datos).

   ![Opción Add Data Source (Agregar origen de datos)](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Agregar datos meteorológicos

1. **Data Store** (Almacén de datos): seleccione el almacén de datos que contiene los datos. Dado que está usando archivos, seleccione **File(s)/Directory** (Archivos o directorio). Seleccione **Next** (Siguiente) para continuar.

   ![Entrada File(s)/Directory (Archivos o directorio)](media/tutorial-bikeshare-dataprep/datasources.png)

2. **File Selection** (Selección de archivos): agregue los datos meteorológicos. Busque y seleccione el archivo `BostonWeather.csv` que cargó en Azure Blob Storage anteriormente. Seleccione **Next** (Siguiente).

   ![Selección de archivos con BostonWeather.csv seleccionado](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **File Details** (Detalles del archivo): compruebe el esquema de archivo que se ha detectado. Azure Machine Learning Workbench analiza los datos del archivo y deduce el esquema que se usará.

   ![Comprobación de los detalles del archivo](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > En algunos casos, Workbench podría no detectar el esquema correcto. Siempre debe comprobar que los parámetros son correctos para su conjunto de datos. En el caso de los datos meteorológicos, compruebe que están establecidos en los valores siguientes:
   >
   > * __File Type__ (Tipo de archivo): archivo delimitado (csv, tsv, txt, etc.)
   > * __Separator__ (Separador): coma [,]
   > * __Comment Line Character__ (Carácter de línea de comentario): no hay ningún valor establecido
   > * __Skip Lines Mode__ (Modo Saltar líneas): no se saltan
   > * __File Encoding__ (Codificación de archivos): utf-8
   > * __Promote Headers Mode__ (Modo Promover encabezados): se usan los encabezados del primer archivo

   La vista previa de los datos debe mostrar las columnas siguientes:

   * **Path**

   * **DATE**

   * **REPORTTYPE**

   * **HOURLYDRYBULBTEMPF**
   
   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

   Para continuar, seleccione **Next** (Siguiente).

4. **Data Types** (Tipos de datos): revise los tipos de datos que se detectan automáticamente. Azure Machine Learning Workbench analiza los datos del archivo y deduce los tipos de datos que se usarán.

   a. Para estos datos, cambie **DATA TYPE** (Tipo de datos) para todas las columnas a **String** (Cadena).

   > [!NOTE]
   > El tipo de datos String se usa para resaltar las capacidades de Workbench más adelante en este tutorial. 

   ![Revisión de los tipos de datos](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. Para continuar, seleccione __Next__ (Siguiente). 

5. **Sampling** (Muestreo): para crear un esquema de muestreo, seleccione **Edit** (Editar). Seleccione la nueva fila __Top 10000__ (Primeros 10 000) que se ha agregado y, después, seleccione __Edit__ (Editar). Establezca __Sample Strategy__ (Estrategia de muestreo) en **Full File** (Archivo completo) y, después, seleccione **Apply** (Aplicar).

   ![Adición de una nueva estrategia de muestreo](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   Para usar la estrategia de __archivo completo__, seleccione la entrada __Full File__ (Archivo completo) y, después, seleccione __Set as Active__ (Establecer como activo). Aparece una estrella junto a __Full File__ (Archivo completo) para indicar que es la estrategia activa.

   ![Full File (Archivo completo) como estrategia activa](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Para continuar, seleccione **Next** (Siguiente).

6. **Path Column** (Columna de ruta de acceso): use la sección __Path Column__ (Columna de ruta de acceso) para incluir la ruta de acceso del archivo completo como una columna en los datos importados. Seleccione __Do Not Include Path Column__ (No incluir la columna de ruta de acceso).

   > [!TIP]
   > Es útil incluir la ruta de acceso como una columna si va a importar una carpeta que contenga numerosos archivos con nombres de archivo diferentes. También es útil si los nombres de archivo contienen información que quiere extraer más adelante.

   ![Path Column (Columna de ruta de acceso) establecida para que no se incluya](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Finish** (Finalizar): para terminar de crear el origen de datos, seleccione el botón **Finish** (Finalizar).

    Se abre una nueva pestaña de origen de datos denominada __BostonWeather__. Se muestra un ejemplo de los datos en una vista de cuadrícula. El ejemplo se basa en el esquema de muestreo activo especificado anteriormente.

    Observe que en el panel **Steps** (Pasos) del lado derecho de la pantalla se muestran las acciones individuales realizadas durante la creación de este origen de datos.

   ![Presentación del origen de datos, el muestreo y los pasos](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Visualizar las métricas del origen de datos

Seleccione __Metrics__ (Métricas) en la parte superior izquierda de la vista de cuadrícula de la pestaña. Esta vista muestra la distribución y otras estadísticas agregadas de los datos muestreados.

![Presentación de las métricas](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Para configurar la visibilidad de las estadísticas, use la lista desplegable **Choose Metric** (Elegir métrica). Active y desactive aquí las métricas para cambiar la vista de cuadrícula.

Para volver a la vista de __datos__, seleccione __Data__ (Datos) en la parte superior izquierda de la página.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>Agregar un origen de datos al paquete de preparación de datos

1. Seleccione __Prepare__ (Preparar) para empezar a preparar los datos. 

2. Cuando se le pida, escriba un nombre para el paquete de preparación de datos, como **BikeShare Data Prep**. 

3. Seleccione __OK__ (Aceptar) para continuar.

   ![Cuadro de diálogo Preparar](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Aparecerá un nuevo paquete denominado **BikeShare Data Prep** en la sección __Data Preparation__ (Preparación de datos) de la pestaña __Data__ (Datos). 

   Para mostrar el paquete, seleccione esta entrada. 

5. Seleccione el botón **>>** para expandir __Dataflows__ (Flujos de datos) y mostrar los flujos de datos que contiene el paquete. En este ejemplo, __BostonWeather__ es el único flujo de datos.

   > [!IMPORTANT]
   > Un paquete puede contener varios flujos de datos.

   ![Flujos de datos en el paquete](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrar datos por valor
1. Para filtrar los datos, haga clic con el botón derecho en una celda con un valor determinado, seleccione __Filter__ (Filtrar) y elija el tipo de filtro. Seleccione el tipo de filtro.

2. Para este tutorial, seleccione una celda que contenga el valor `FM-15`. A continuación, establezca el filtro en **equals** (es igual a).  Los datos se filtran y devuelven solo las filas en las que __REPORTTYPE__ es `FM-15`.

   ![Cuadro de diálogo Filtro](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 es un tipo de informe meteorológico aeronáutico de rutina (METAR). Los informes FM-15 se consideran empíricamente los más completos, ya que en ellos faltan pocos datos.

## <a name="remove-a-column"></a>Quitar una columna

Ya no necesita la columna __REPORTTYPE__. Haga clic con el botón derecho en el encabezado de columna y seleccione **Remove Column** (Quitar columna).

   ![Opción Remove Column (Quitar columna)](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Cambiar tipos de datos y eliminar errores
1. Seleccione Ctrl (comando ⌘ en Mac) mientras selecciona encabezados de columna para seleccionar varias columnas a la vez. Use este método para seleccionar los siguientes encabezados de columna:

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Haga clic con el botón derecho en uno de los encabezados de columna seleccionados y seleccione **Convert Field Type to Numeric** (Convertir el tipo de campo en numérico). Esto convierte el tipo de datos de las columnas en numérico.

   ![Convertir varias columnas en numéricas](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtre los valores de error. Algunas columnas tienen problemas de conversión del tipo de datos. Este problema se indica en rojo en la __barra de calidad de datos__ de la columna.

   Para quitar las filas que tienen errores, haga clic con el botón derecho en el encabezado de columna **HOURLYDRYBULBTEMPF**. Seleccione **Filter Column** (Filtrar columna). Use el valor predeterminado **I Want To** (Quiero) como **Keep Rows** (Conservar filas). Cambie la lista desplegable **Conditions** (Condiciones) para seleccionar **is not error** (no es un error). Seleccione **OK** (Aceptar) para aplicar el filtro.

   ![Filtrar valores de error](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. Para eliminar las filas de error restantes de las demás columnas, repita este proceso de filtro para las columnas **HOURLYRelativeHumidity** y **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Usar transformaciones por ejemplos

Para usar los datos de una predicción para bloques temporales de dos horas, debe calcular el promedio de las condiciones meteorológicas en períodos de dos horas. Haga lo siguiente:

* Divida la columna **DATE** en columnas **Date**  (Fecha) y **Time** (Hora) diferentes. Vea la sección siguiente para conocer los pasos detallados.

* Derive una columna **Hour_Range** de la columna **Time** (Hora). Vea la sección siguiente para conocer los pasos detallados.

* Derive una columna **Date\_Hour\_Range** de las columnas **DATE** y **Hour_Range**. Vea la sección siguiente para conocer los pasos detallados.

### <a name="split-column-by-example"></a>División de columnas por ejemplos

1. Divida la columna **DATE** en columnas **Date**  (Fecha) y **Time** (Hora) diferentes. Haga clic con el botón derecho en el encabezado de columna **DATE** y seleccione **Split Column by Example** (Dividir columna por ejemplos).

   ![Entrada Split Column by Example (Dividir columna por ejemplos)](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Machine Learning Workbench identifica automáticamente un delimitador significativo y crea dos columnas mediante la división de los datos en valores de fecha y de hora. 

3. Seleccione __OK__ (Aceptar) para confirmar los resultados de la operación de división.

   ![Columnas divididas DATE_1 y DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Derivación de columnas por ejemplos

1. Para derivar un intervalo de dos horas, haga clic con el botón derecho en el encabezado de columna __DATE\_2__ y seleccione **Derive Column by Example** (Derivar columna por ejemplos).

   ![Entrada Derive Column by Example (Derivar columna por ejemplos)](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Se agrega una nueva columna vacía con valores null.

2. Haga clic en la primera celda vacía de la nueva columna. Para proporcionar un ejemplo del intervalo de tiempo que le interesa, escriba **12AM-2AM** en la nueva columna y presione ENTRAR.

   ![Nueva columna con un valor de 12AM-2AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Machine Learning Workbench sintetiza un programa basado en los ejemplos que haya proporcionado y lo aplica a las filas restantes. Las demás filas se rellenan automáticamente en función del ejemplo proporcionado. Workbench también analiza los datos e intenta identificar casos extremos. 

   > [!IMPORTANT]
   > La identificación de los casos límite puede no funcionar en Mac en la versión actual de Workbench. Omita los pasos 3 y 4 siguientes en Mac. En su lugar, presione __OK__ (Aceptar) cuando todas las filas contengan los valores derivados.
   
3. El texto **Analyzing Data** (Analizando datos) encima de la cuadrícula indica que Workbench está intentando detectar casos extremos. Cuando finalice, el estado cambiará a **Review next suggested row** (Revisar siguiente fila sugerida) o **No suggestions** (Ninguna sugerencia). En este ejemplo, se devuelve **Review next suggested row** (Revisar siguiente fila sugerida).

4. Para revisar los cambios sugeridos, seleccione **Review next suggested row** (Revisar siguiente fila sugerida). En la pantalla, se resalta la celda que debe revisar y corregir (si es necesario).

   ![Review next suggested row (Revisar siguiente fila sugerida)](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Seleccione __OK__ (Aceptar) para confirmar la transformación.
 
5. Ahora vuelve a la vista de cuadrícula de datos de __BostonWeather__. La cuadrícula contiene las tres columnas agregadas previamente.

   ![Vista de cuadrícula con filas agregadas](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > Todos los cambios realizados se conservan en el panel **Steps** (Pasos). Vaya al paso que ha creado en el panel **Steps** (Pasos), haga clic en la flecha hacia abajo y seleccione **Edit** (Editar). Se muestra la ventana avanzada de **Derive Column by Example** (Derivar columna por ejemplos). Todos los ejemplos se conservan aquí. También puede agregar ejemplos manualmente. Para ello, haga doble clic en una fila de la cuadrícula siguiente. Seleccione **Cancel** (Cancelar) para volver a la cuadrícula principal sin aplicar los cambios. Para acceder a esta vista, seleccione **Advanced mode** (Modo avanzado) mientras realiza una transformación **Derive Column by Example** (Derivar columna por ejemplos).

6. Para cambiar el nombre de la columna, haga doble clic en el encabezado de columna y escriba **Hour Range** (Intervalo de horas). Presione ENTRAR para guardar el cambio.

   ![Cambio de nombre de las columnas](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Para derivar el intervalo de fecha y hora, seleccione al mismo tiempo las columnas **Date\_1** y **Hour Range** (Intervalo de horas), haga clic con el botón derecho y seleccione **Derive column by example** (Derivar columna por ejemplos).

   ![Derivación de columnas por ejemplos](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Escriba **Jan 01, 2015 12AM-2AM** como en el ejemplo en la primera fila y seleccione ENTRAR.

   Workbench determina la transformación en función del ejemplo proporcionado. En este ejemplo, el resultado es que el formato de fecha se cambia y se concatena con el período de dos horas.

   ![Ejemplo Jan 01, 2015 12AM-2AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > En un equipo Mac, haga lo siguiente en lugar del paso 8:
   >
   > * Vaya a la primera celda que contenga **Feb 01, 2015 12AM-2AM**. Debería ser la fila 15. Corrija el valor a **Jan 02, 2015 12AM-2AM** y presione ENTRAR. 
   

8. Espere a que el estado cambie de **Analyzing Data** (Analizando datos) a **Review next suggested row** (Revisar siguiente fila sugerida). Este proceso podría tardar varios segundos. Seleccione el vínculo de estado para desplazarse a la fila sugerida. 

   ![Fila sugerida para revisar](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   La fila tiene un valor null porque el valor de la fecha de origen podría ser dd/mm/aaaa o mm/dd/aaaa. Escriba el valor correcto **Jan 13, 2015 2AM-4AM** y presione ENTRAR. Workbench usa los dos ejemplos para mejorar la derivación de las filas restantes.

   ![Datos con el formato correcto](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Seleccione **Aceptar** para confirmar la transformación.

   ![Cuadrícula de transformación completada](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Para usar el modo avanzado (**Advanced mode**) de **Derive Column by Example** (Derivar columna por ejemplos) para este paso, seleccione la flecha hacia abajo en el panel **Steps** (Pasos). En la cuadrícula de datos, hay casillas junto a las columnas **DATE\_1** y **Hour Range** (Intervalo de horas). Desactive la casilla que aparece junto a la columna **Hour Range** (Intervalo de horas) para ver cómo cambia la salida. En ausencia de la columna **Hour Range** (Intervalo de horas) como entrada, **12AM-2AM** (12 a. m.-2 a. m.) se trata como una constante y se anexa a los valores derivados. Seleccione **Cancel** (Cancelar) para volver a la cuadrícula principal sin aplicar los cambios.
   ![Advanced Mode](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png) (Modo avanzado)

10. Para cambiar el nombre de la columna, haga doble clic en el encabezado. Cambie el nombre a **Date Hour Range** (Intervalo de fecha y hora) y presione ENTRAR.

11. Seleccione al mismo tiempo las columnas **DATE**, **DATE\_1**, **DATE\_2** y **Hour Range** (Intervalo de horas). Haga clic con el botón derecho y seleccione **Remove column** (Quitar columna).

## <a name="summarize-data-mean"></a>Resumir datos (media)

El paso siguiente consiste en resumir las condiciones meteorológicas a partir de la media de valores, agrupados por intervalos de horas.

1. Seleccione la columna **Date Hour Range** (Intervalo de fecha y hora) y, en el menú **Transforms** (Transformaciones), seleccione **Summarize** (Resumir).

    ![Menú Transforms (Transformaciones)](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Para resumir los datos, arrastre las columnas de la cuadrícula en la parte inferior de la página a los paneles de la izquierda y la derecha en la parte superior. El panel izquierdo contiene el texto **Drag columns here to group data** (Arrastre las columnas aquí para agrupar los datos). El panel derecho contiene el texto **Drag columns here to summarize data** (Arrastre las columnas aquí para resumir los datos). 

    a. Arrastre la columna **Date Hour Range** (Intervalo de fecha y hora) de la cuadrícula en la parte inferior al panel izquierdo. Arrastre **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** y **HOURLYWindSpeed** al panel derecho. 

    b. En el panel derecho, seleccione **Mean** (Media) como medida **Aggregate** (Agregada) para cada columna. Seleccione **OK** (Aceptar) para finalizar el resumen.

    ![Pantalla de datos resumidos](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>Transformación de un flujo de datos mediante un script

Algunos modelos pueden convergir más rápidamente si se cambian los datos de las columnas numéricas a un intervalo de 0 a 1. Actualmente, no hay ninguna transformación integrada para realizar esta transformación de forma genérica. Use un script de Python para realizar esta operación.

1. En el menú **Transform** (Transform), seleccione **Transform Dataflow (Script)** (Transformar flujo de datos [script]).

2. Escriba el código siguiente en el cuadro de texto que aparece. Si ha usado los nombres de las columnas, el código debería funcionar sin necesidad de modificaciones. Lo que está haciendo es escribir una lógica sencilla de normalización mínima-máxima en Python.

    > [!WARNING]
    > El script espera los nombres de las columnas que se han usado anteriormente en este tutorial. Si tiene nombres de columna diferentes, debe cambiar los nombres en el script.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > El script de Python debe devolver `df` al final. Este valor se usa para rellenar la cuadrícula.
    
   ![Cuadro de diálogo Transform Dataflow (Script) (Transformar flujo de datos [script])](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Seleccione __OK__ (Aceptar) para usar el script. Las columnas numéricas de la cuadrícula ahora contienen valores en el intervalo de 0 a 1.

    ![Cuadrícula que contiene valores entre 0 y 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Ya ha acabado de preparar los datos meteorológicos. Ahora, preparará los datos de los viajes.

## <a name="load-trip-data"></a>Cargar datos de los viajes

1. Para importar el archivo `201701-hubway-tripdata.csv`, siga los pasos indicados en la sección [Crear un origen de datos](#newdatasource). Durante el proceso de importación, use las opciones siguientes:

    * __File Selection__ (Selección de archivos): seleccione **Azure Blob**.

    * __Sampling Scheme__ (Esquema de muestreo): use el esquema de muestreo **Full File** (Archivo completo) y active la muestra.

    * __Data Type__ (Tipo de datos): acepte los valores predeterminados.

2. Después de importar los datos, seleccione __Prepare__ (Preparar) para empezar a preparar los datos. Seleccione el paquete **BikeShare Data Prep.dprep** existente y haga clic en __Aceptar__.

    Este proceso agrega un **flujo de datos** al archivo existente de **preparación de datos** en lugar de crear uno nuevo.

    ![Seleccione el paquete existente](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Una vez cargada la cuadrícula, expanda __DATAFLOWS__ (FLUJOS DE DATOS). Ahora hay dos flujos de datos: **BostonWeather** y **201701-hubway-tripdata**. Seleccione la entrada **201701-hubway-tripdata**.

    ![Entrada 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>Uso del inspector de mapas

Para preparar los datos, hay disponibles unas visualizaciones útiles llamadas inspectores para los datos de cadena, numéricos y geográficos. Le ayudan a comprender mejor los datos y a identificar los valores atípicos. Siga estos pasos para utilizar el inspector de mapas.

1. Seleccione al mismo tiempo las columnas **start station latitude** (latitud de la estación de inicio) y **start station longitude** (longitud de la estación de inicio). Haga clic con el botón derecho en una de las columnas y seleccione **Map** (Mapa).

    > [!TIP]
    > Para habilitar la selección de varios elementos al mismo tiempo, mantenga presionada la tecla Ctrl (comando ⌘ en Mac) y seleccione el encabezado de cada columna.

    ![Visualización de mapas](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Para maximizar la visualización de mapas, seleccione el icono **Maximize** (Maximizar). Para ajustar el mapa a la ventana, seleccione el icono **E** situado en el lado superior izquierdo de la visualización.

    ![Imagen maximizada](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Haga clic en el botón **Minimize** (Minimizar) para volver a la vista de cuadrícula.

## <a name="use-the-column-statistics-inspector"></a>Uso del inspector de estadísticas de columna

Para usar el inspector de estadísticas de columna, haga clic con el botón derecho en la columna __tripduration__ (duración del viaje) y seleccione __Column Statistics__ (Estadísticas de columna).

![Entrada de estadísticas de columna](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Este proceso agrega una nueva visualización titulada __tripduration Statistics__ (Estadísticas de duración del viaje) en el panel __INSPECTORS__ (INSPECTORES).

![Inspector de estadísticas de duración del viaje](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> El valor máximo de la duración del viaje es de 961 814 minutos, que es aproximadamente dos años. Parece que hay algunos valores atípicos en el conjunto de datos.

## <a name="use-the-histogram-inspector"></a>Uso del inspector de histogramas

Para intentar identificar los valores atípicos, haga clic con el botón derecho en la columna __tripduration__ y seleccione __Histogram__ (Histogram).

![Inspector de histogramas](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

El histograma no es útil porque los valores atípicos sesgan el gráfico.

## <a name="add-a-column-by-using-script"></a>Adición de una columna mediante un script

1. Haga clic con el botón derecho en la columna **tripduration** y seleccione **Add Column (Script)** (Agregar columna [script]).

    ![Menú Add Column (Script) (Agregar columna [script])](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. En el cuadro de diálogo __Add Column (Script)__ (Agregar columna [script]), use los siguientes valores:

    * __New Column Name__ (Nuevo nombre de columna): logtripduration

    * __Insert this New Column After__ (Insertar esta nueva columna después de): tripduration

    * __New Column Code__ (Nuevo código de columna): `math.log(row.tripduration)`

    * __Code Block Type__ (Tipo de bloque de código): expresión

   ![Cuadro de diálogo Add Column (Script) (Agregar columna [script])](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Seleccione __OK__ (Aceptar) para agregar la columna **logtripduration**.

4. Haga clic con el botón derecho en la columna y seleccione **Histogram** (Histograma).

    ![Histograma de la columna logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    Visualmente, este histograma parece una distribución normal con una cola anómala.

## <a name="use-an-advanced-filter"></a>Uso de un filtro avanzado

El uso de un filtro en los datos actualiza los inspectores con la nueva distribución. 

1. Haga clic con el botón derecho en la columna **logtripduration** y seleccione **Filter Column** (Filtrar columna). 

2. En el cuadro de diálogo __Edit__ (Editar), use los siguientes valores:

    * __Filter this Number Column__: logtripduration (Filtrar esta columna de número)

    * __I Want To__: Keep Rows (Quiero: Conservar filas)

    * __When__: Any of the Conditions below are True (logical OR) (Cuándo: cualquiera de las siguientes condiciones sea True [OR lógico])

    * __If this Column__: less than (Si esta columna: es menor que)

    * __The Value__: 9 (El valor: 9)

    ![Opciones de filtro](media/tutorial-bikeshare-dataprep/loftripfilter.png)

3. Seleccione __OK__ (Aceptar) para aplicar el filtro.

    ![Histogramas actualizados después de aplicar el filtro](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>El efecto de halo

1. Maximice el histograma de **logtripduration**. Hay un histograma azul superpuesto sobre un histograma gris. Esta visualización se conoce como **efecto de halo**:

    * El histograma gris representa la distribución antes de la operación (en este caso, la operación de filtrado).

    * El histograma azul representa el histograma después de la operación. 

   El efecto de halo ayuda a visualizar el efecto de una operación en los datos.

   ![Efecto de halo](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Observe que el histograma azul parece más corto en comparación con el anterior. Esto se debe a que se vuelven a crear automáticamente los cubos de datos en el nuevo intervalo.

2. Para quitar el halo, seleccione __Edit__ (Editar) y desactive la opción __Show halo__ (Mostrar halo).

    ![Opciones del histograma](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Seleccione **OK** (Aceptar) para deshabilitar el efecto de halo. A continuación, minimice el histograma.

### <a name="remove-columns"></a>Quitar columnas

En los datos del viaje, cada fila representa una acción de tomar una bicicleta. Para este tutorial, solo necesita las columnas **starttime** y **start station id**. Puede quitar las demás columnas. Para ello, seleccione al mismo tiempo estas dos columnas, haga clic con el botón derecho en el encabezado de columna y, después, seleccione **Keep Column** (Conservar columna). Las demás columnas se quitan.

![Opción Keep Column (Conservar columna)](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Resumen de los datos (recuento)

Para resumir la demanda de bicicletas durante un período de dos horas, use columnas derivadas.

1. Haga clic con el botón derecho en la columna **starttime** y seleccione **Derive Column by Example** (Derivar columna por ejemplos).

    ![Opción Derive Column by Example (Derivar columna por ejemplos)](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Para el ejemplo, escriba el valor **Jan 01, 2017 12AM-2AM** para la primera fila.

    > [!IMPORTANT]
    > En el ejemplo anterior de derivación de columnas, ha usado varios pasos para derivar una columna que contenía el período de fecha y hora. En este ejemplo, puede ver que esta operación se puede realizar en un solo paso si se proporciona un ejemplo de la salida final.

    > [!NOTE]
    > Puede dar un ejemplo en cualquiera de las filas. Para el ejemplo, el valor **Jan 01, 2017 12AM-2AM** es válido para la primera fila de datos.

    ![Datos de ejemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > En un equipo Mac, siga este paso en lugar del paso 3:
   >
   > * Vaya a la primera celda que contenga **Jan 01, 2017 1AM-2AM**. Debería ser la fila 14. Corrija el valor a **Jan 01, 2017 12AM-2AM** y presione ENTRAR. 

3. Espere hasta que la aplicación calcule los valores de todas las filas. Este proceso podría tardar varios segundos. Una vez completado el análisis, use el vínculo __Review next suggested row__ (Revisar siguiente fila sugerida) para revisar los datos.

   ![Análisis terminado con un vínculo de revisión](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Asegúrese de que los valores calculados son correctos. Si no es así, actualice el valor con el valor esperado y presione ENTRAR. Después, espere a que se complete el análisis. Complete el proceso de **Review next suggested row** (Revisar siguiente fila sugerida) hasta que vea **No suggestions** (Ninguna sugerencia). Cuando vea **No suggestions** (Ninguna sugerencia), significa que la aplicación ha examinado los casos extremos y está satisfecha con el programa sintetizado. Se recomienda llevar a cabo una inspección visual de los datos transformados antes de aceptar la transformación. 

4. Seleccione **OK** (Aceptar) para confirmar la transformación. Cambie el nombre de la columna recién creada a **Date Hour Range** (Intervalo de fecha y hora).

    ![Columnas con nombre cambiado](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Haga clic con el botón derecho en el encabezado de columna **starttime** y seleccione **Remove Column** (Quitar columna).

6. Para resumir los datos, en el menú __Transform__ (Transformar), seleccione __Summarize__ (Resumir). Para crear la transformación, siga estos pasos:

    * Arrastre __Date Hour Range__ (Intervalo de fecha y hora) y __start station id__ (identificador de la estación de inicio) al panel de la izquierda **Group By** (Agrupar por).

    * Arrastre __start station id__ (identificador de la estación de inicio) al panel de la derecha de **resumen de datos**.

   ![Opciones de resumen](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Seleccione **OK** (Aceptar) para confirmar el resultado del resumen.

## <a name="join-dataflows"></a>Combinar flujos de datos

Para combinar los datos meteorológicos con los datos del viaje, siga estos pasos:

1. En el menú __Transforms__ (Transformaciones), seleccione __Join__ (Combinar).

2. __Tables__ (Tablas): seleccione **BostonWeather** en **Left** como flujo de datos izquierdo y **201701-hubway-tripdata** en **Right** como flujo de datos derecho. Para continuar, seleccione **Next** (Siguiente).

    ![Selecciones de tablas](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Key Columns__ (Columnas clave): seleccione la columna **Date Hour Range** (Intervalo de fecha y hora) de ambas tablas y haga clic en __Next__ (Siguiente).

    ![Selecciones de columnas de clave](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Join Type__ (Tipo de combinación): seleccione __Matching rows__ (Filas coincidentes) como tipo de combinación y, después, seleccione __Finish__ (Finalizar).

    ![Tipo de combinación de filas coincidentes](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Este proceso crea un flujo de datos denominado __Join Result__ (Resultado de la combinación).

## <a name="create-additional-features"></a>Crear características adicionales

1. Para crear una columna que contenga el día de la semana, haga clic con el botón derecho en la columna **Date Hour Range** (Intervalo de fecha y hora) y seleccione **Derive column by Example** (Derivar columna por ejemplos). Use un valor de __Sun__ (Dom) para una fecha que sea domingo. Por ejemplo, **Jan 01, 2017 12AM-2AM**. Seleccione ENTRAR y, después, **OK** (Aceptar). Cambie el nombre de esta columna a __Día de la semana__.

    ![Creación de una nueva columna para el día de la semana](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Para crear una columna que contenga el período de tiempo de una fila, haga clic con el botón derecho en la columna **Date Hour Range** (Intervalo de fecha y hora) y seleccione **Derive column by Example** (Derivar columna por ejemplos). Use el valor **12AM-2AM** para la fila que contiene **Jan 01, 2017 12AM-2AM**. Seleccione ENTRAR y, después, **OK** (Aceptar). Cambie el nombre de esta columna a **Period** (Período).

    ![Columna Period](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Para quitar las columnas **Date Hour Range** (Intervalo de fecha y hora) y **r_Date Hour Range**, presione Ctrl (comando ⌘ en Mac) y seleccione cada encabezado de columna. Haga clic con el botón derecho y seleccione **Remove Column** (Quitar columna).

## <a name="read-data-from-python"></a>Leer datos de Python

Puede ejecutar un paquete de preparación de datos desde Python o PySpark y recuperar el resultado como una **trama de datos**.

Para generar un script de Python de ejemplo, haga clic con el botón derecho en __BikeShare Data Prep__ y seleccione __Generate Data Access Code File__ (Generar archivo de código de acceso a datos). Se crea el archivo de Python de ejemplo en la **carpeta del proyecto** y también se carga en una pestaña en Workbench. El siguiente script de Python es un ejemplo del código que se genera:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

En este tutorial, el nombre del archivo es `BikeShare Data Prep.py`. Este archivo se usa más adelante en el tutorial.

## <a name="save-test-data-as-a-csv-file"></a>Guardar los datos de prueba como un archivo CSV

Para guardar el flujo de datos **Join Result** (Resultado de la combinación) en un archivo .csv, debe cambiar el script `BikeShare Data Prep.py`. 

1. Abra el proyecto para editarlo en Visual Studio Code.

    ![Proyecto abierto en Visual Studio Code](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. Actualice el script de Python en el archivo `BikeShare Data Prep.py` usando el código siguiente:

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. Reemplace `Your Azure Storage blob path` por la ruta de acceso al archivo de salida que se va a crear. Reemplace las variables `blobfolder` y `csvfiles`.

## <a name="create-an-hdinsight-run-configuration"></a>Creación de una configuración de ejecución de HDInsight

1. En Machine Learning Workbench, abra la ventana de la línea de comandos, seleccione el menú **File** (Archivo) y, después, **Open Command Prompt** (Abrir símbolo del sistema). Se inicia el símbolo del sistema en la carpeta del proyecto con el símbolo `C:\Projects\BikeShare>`.

    ![Abrir símbolo del sistema](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Debe usar la ventana de línea de comandos (que se abre desde Workbench) para realizar los pasos siguientes.

2. Utilice el símbolo del sistema para iniciar sesión en Azure. 

   La aplicación Workbench y la CLI usan memorias caché de credenciales independientes al autenticarse con recursos de Azure. Esto solo tiene que hacerlo una vez hasta que expire el token almacenado en caché. El comando `az account list` devuelve la lista de suscripciones disponibles para su inicio de sesión. Si hay más de una, utilice el valor del identificador de la suscripción deseada. Establezca dicha suscripción como cuenta predeterminada que se usará con el comando `az account set -s` y, luego, especifique el valor del identificador de la suscripción. A continuación, confirme el valor con el comando `show` de la cuenta.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Cree la configuración de ejecución de HDInsight. Necesita el nombre del clúster y la contraseña de `sshuser`.

    ```azurecli
    az ml computetarget attach --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password> --type cluster
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> Cuando se crea un proyecto en blanco, las configuraciones de ejecución predeterminadas son **local** y **docker**. Este paso crea una nueva configuración de ejecución que está disponible en Workbench al ejecutar los scripts. 

## <a name="run-in-an-hdinsight-cluster"></a>Ejecución en un clúster de HDInsight

Vuelva a la aplicación Machine Learning Workbench para ejecutar el script en el clúster de HDInsight.

1. Vuelva a la pantalla de inicio de su proyecto; para ello, seleccione el icono de **inicio** situado a la izquierda.

2. Seleccione **hdinsight** en la lista desplegable para ejecutar su script en el clúster de HDInsight.

3. Seleccione **Run** (Ejecutar). El script se envía como un trabajo. Cuando el estado del trabajo cambie a __Completed__ (Completado), el archivo se habrá escrito en la ubicación especificada en su contenedor de Storage.

    ![Script de ejecución de HDInsight](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Sustituir orígenes de datos

En los pasos anteriores, ha usado los orígenes de datos `201701-hubway-tripdata.csv` y `BostonWeather.csv` para preparar los datos de prueba. Para usar el paquete con los demás archivos de datos del viaje, siga estos pasos:

1. Cree un origen de datos nuevo mediante los pasos indicados anteriormente, pero cambie lo siguiente en el proceso:

    * __File Selection__ (Selección de archivos): seleccione al mismo tiempo los seis archivos CSV restantes de datos del viaje.

    ![Carga de los seis archivos restantes](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > La entrada __+ 5__ indica que hay cinco archivos adicionales, aparte del que aparece.

    * __File Details__ (Detalles del archivo): establezca __Promote Headers Mode__ (Modo Promover encabezados) en **All Files Have The Same Headers** (Todos los archivos tienen los mismos encabezados). Este valor indica que cada uno de los archivos contiene el mismo encabezado.

    ![Selección de detalles de archivo](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Guarde el nombre de este origen de datos porque se usará en pasos posteriores.

2. Seleccione el icono de carpeta para ver los archivos del proyecto. Expanda el directorio __aml\_config__ y seleccione el archivo `hdinsight.runconfig`.

    ![Ubicación de hdinsight.runconfig](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. Seleccione el botón **Edit** (Editar) para abrir el archivo en Visual Studio Code.

4. Agregue las siguientes líneas al final del archivo `hdinsight.runconfig` y, después, seleccione el icono de disco para guardar el archivo.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Este cambio reemplaza el origen de datos original por el que contiene los seis archivos de datos del viaje.

## <a name="save-training-data-as-a-csv-file"></a>Guardar los datos de aprendizaje como un archivo CSV

1. Busque el archivo Python `BikeShare Data Prep.py` que modificó anteriormente. Proporcione una ruta de acceso de archivo diferente para guardar los datos de entrenamiento.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

2. Use la carpeta `traindata` como salida de los datos de entrenamiento.

3. Para enviar un trabajo nuevo, seleccione **Run** (Ejecutar). Asegúrese de que **hdinsight** se haya seleccionado. Se envía un trabajo con la configuración nueva. La salida de este trabajo son los datos de entrenamiento. Estos datos se crean con los mismos pasos de preparación de datos que ha seguido anteriormente. El trabajo puede tardar unos minutos en finalizar.


## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Pasos siguientes
Ha finalizado el tutorial de preparación de datos de uso compartido de bicicletas. En este tutorial, ha usado Machine Learning (versión preliminar) para obtener información sobre cómo:
> [!div class="checklist"]
> * Preparar datos de forma interactiva con la herramienta de preparación de datos de Machine Learning.
> * Importar, transformar y crear un conjunto de datos de prueba.
> * Generar un paquete de preparación de datos.
> * Ejecutar el paquete de preparación de datos con Python.
> * Generar un conjunto de datos de entrenamiento mediante la reutilización del paquete de preparación de datos para los archivos de entrada adicionales.

Ahora, obtenga más información sobre la preparación de los datos:
> [!div class="nextstepaction"]
> [Guía de usuario sobre preparación de los datos](data-prep-user-guide.md)
