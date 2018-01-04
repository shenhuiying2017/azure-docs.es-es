---
title: "Tutorial de uso compartido de bicicletas: preparación avanzada de datos con Azure Machine Learning Workbench"
description: "Tutorial de preparación de datos de un extremo a otro mediante Azure Machine Learning Workbench"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial, azure
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 69f6911a95be382b06313d984f09c7e85aec10df
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="bike-share-tutorial-advanced-data-preparation-with-azure-machine-learning-workbench"></a>Tutorial de uso compartido de bicicletas: preparación avanzada de datos con Azure Machine Learning Workbench
Los servicios de Azure Machine Learning (versión preliminar) son una solución integrada de análisis avanzado y ciencia de datos de un extremo a otro diseñada para que los científicos de datos profesionales preparen datos, desarrollen experimentos e implementen modelos a escala de nube.

En este tutorial, usará los servicios de Azure Machine Learning (versión preliminar) para obtener información sobre cómo:
> [!div class="checklist"]
> * Preparar datos de forma interactiva con la herramienta de preparación de datos de Azure Machine Learning
> * Importar, transformar y crear un conjunto de datos de prueba
> * Generar un paquete de preparación de datos
> * Ejecutar el paquete de preparación de datos con Python
> * Generar un conjunto de datos de aprendizaje mediante la reutilización del paquete de preparación de datos para los archivos de entrada adicionales

> [!IMPORTANT]
> En este tutorial solo se preparan los datos, no se compila el modelo de predicción.
>
> Puede usar los datos preparados para entrenar sus propios modelos de predicción. Por ejemplo, podría crear un modelo para predecir la demanda de bicicletas durante un período de 2 horas.

## <a name="prerequisites"></a>requisitos previos
* Azure Machine Learning Workbench debe instalarse localmente. Para obtener más información, siga la [guía de inicio rápido de instalación](quickstart-installation.md).
* Debe estar familiarizado con la creación de un proyecto en Workbench.

## <a name="data-acquisition"></a>Adquisición de datos
En este tutorial se usa el [conjunto de datos de Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) y los datos meteorológicos de Boston de [NOAA](http://www.noaa.gov/).

1. Descargue los archivos de datos de los siguientes vínculos en su entorno de desarrollo local. 
   * [Datos meteorológicos de Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv). 
   * Datos de los viajes de Hubway tomados del sitio web de Hubway.

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Descomprima cada archivo .zip después de la descarga.

## <a name="learn-about-the-datasets"></a>Información sobre los conjuntos de datos
1. El archivo sobre el __tiempo de Boston__ contiene los siguientes campos relacionados con el tiempo, notificado cada hora:
   * DATE
   * REPORTTPYE
   * HOURLYDRYBULBTEMPF
   * HOURLYRelativeHumidity
   * HOURLYWindSpeed

2. Los datos de __Hubway__ están organizados en archivos por año y mes. Por ejemplo, el archivo denominado `201501-hubway-tripdata.zip` contiene un archivo .csv con datos de enero de 2015. Los datos contienen los campos siguientes, donde cada fila representa un viaje en bicicleta:

   * Duración del viaje (en segundos)
   * Fecha y hora de inicio
   * Fecha y hora de finalización
   * Nombre e identificador de la estación de inicio
   * Nombre e identificador de la estación de finalización
   * Identificador de bicicleta
   * Tipo de usuario (ocasional = usuario del pase de 24 o 72 horas; miembro = miembro anual o mensual)
   * Código postal (si el usuario es miembro)
   * Sexo (indicado por el miembro)

## <a name="create-a-new-project"></a>Crear un nuevo proyecto
1. Inicie **Azure Machine Learning Workbench** desde el menú Inicio o el iniciador.

2. Cree un proyecto de Azure Machine Learning.  Haga clic en el botón **+** situado en la página **Proyectos**, o en **Archivo** > **Nuevo**.
   - Use la plantilla **Proyecto en blanco**.
   - Asígnele al proyecto el nombre **BikeShare**. 

## <a id="newdatasource"></a>Crear un origen de datos

1. Cree un origen de datos. Haga clic en el botón **Datos** (icono de cilindro) en la barra de herramientas de la izquierda. De este modo se muestra la **vista de datos**.

   ![Imagen de la pestaña de vista de datos](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Agregue un origen de datos. Seleccione el icono **+** y, después, **Agregar origen de datos**.

   ![Imagen de la entrada Agregar origen de datos](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Agregar datos meteorológicos

1. **Almacén de datos**: seleccione el almacén de datos que contiene los datos. Dado que está usando archivos, seleccione **File(s)/Directory** (Archivos o directorio). Seleccione **Siguiente** para continuar.

   ![Imagen de la entrada Archivos o directorio](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Selección de archivos**: agregue los datos meteorológicos. Busque y seleccione el archivo `BostonWeather.csv` que ha descargado anteriormente. Haga clic en **Next**.

   ![Imagen de la selección de archivos con BostonWeater.csv seleccionado](media/tutorial-bikeshare-dataprep/pickweatherdatafile.png)

3. **Detalles del archivo**: compruebe el esquema de archivo que se ha detectado. Azure Machine Learning Workbench analiza los datos del archivo y deduce el esquema que se usará.

   ![Imagen de los detalles del archivo](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > En algunos casos, Workbench podría no detectar el esquema correcto. Siempre debe comprobar que los parámetros son correctos para su conjunto de datos. En el caso de los datos meteorológicos, compruebe que están establecidos en los valores siguientes:
   >
   > * __Tipo de archivo__: archivo delimitado (csv, tsv, txt, etc.)
   > * __Separador__: coma [,]
   > * __Comment Line Character__ (Carácter de línea de comentario): no hay ningún valor establecido
   > * __Skip Lines Mode__ (Modo Saltar líneas): no se saltan
   > * __Codificación de archivos__: utf-8
   > * __Promote Headers Mode__ (Modo Promover encabezados): se usan los encabezados del primer archivo

   La vista previa de los datos debe mostrar las columnas siguientes:
   * **Ruta de acceso**
   * **DATE**
   * **REPORTTYPE**
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

   Para continuar, seleccione **Siguiente**.

4. **Tipos de datos**: revise los tipos de datos que se detectan automáticamente. Azure Machine Learning Workbench analiza los datos del archivo y deduce los tipos de datos que se usarán.

   Para estos datos, cambie el valor `DATA TYPE` de todas las columnas a `String`.

   > [!NOTE]
   > Se usa `String` para resaltar las capacidades de Workbench más adelante en este tutorial. 

   ![Imagen de los tipos de datos](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   Para continuar, seleccione __Siguiente__. 

5. **Muestreo**: para crear un esquema de muestreo, seleccione el botón **+ Nuevo**. Seleccione la nueva fila __Top 10000__ (Primeros 10 000) que se ha agregado y, después, seleccione __Editar__. Establezca __Sample Strategy__ (Estrategia de muestreo) en **Full File** (Archivo completo) y, después, seleccione **Aplicar**.

   ![Imagen de la adición de una nueva estrategia de muestreo](media/tutorial-bikeshare-dataprep/weatherdatasampling.png)

   Para usar la estrategia de __archivo completo__, seleccione la entrada __Full File__ (Archivo completo) y, después, haga clic en __Establecer como activo__. Aparece una estrella junto a __Full File__ (Archivo completo) para indicar que es la estrategia activa.

   ![Imagen de la opción de archivo completo seleccionada como estrategia activa](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Para continuar, seleccione **Siguiente**.

6. **Columna de ruta de acceso**: la sección __Path Column__ (Columna de ruta de acceso) permite incluir la ruta de acceso del archivo completo como una columna en los datos importados. Seleccione __Do Not Include Path Column__ (No incluir la columna de ruta de acceso).

   > [!TIP]
   > Es útil incluir la ruta de acceso como una columna si va a importar una carpeta que contenga numerosos archivos con nombres de archivo diferentes. También es útil si los nombres de archivo contienen información que quiere extraer más adelante.

   ![Imagen de la columna de ruta de acceso establecida para que no se incluya](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Finalizar**: para terminar de crear el origen de datos, seleccione el botón **Finalizar**.

    Se abre una nueva pestaña de origen de datos denominada __BostonWeather__. Se muestra un ejemplo de los datos en una vista de cuadrícula. El ejemplo se basa en el esquema de muestreo activo especificado anteriormente.

    Observe que en el panel **Pasos** del lado derecho de la pantalla se muestran las acciones individuales realizadas durante la creación de este origen de datos.

   ![Imagen en la que se muestran el origen de datos, el muestreo y los pasos](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Visualizar las métricas del origen de datos

Seleccione el botón __Métricas__ situado en la parte superior izquierda de la vista de cuadrícula de la pestaña. Esta vista muestra la distribución y otras estadísticas agregadas de los datos muestreados.

![Imagen de las visualizaciones de métricas](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Para configurar la visibilidad de las estadísticas, use el menú desplegable **Choose Metric** (Elegir métrica). Active y desactive aquí las métricas para cambiar la vista de cuadrícula.

Para volver a la __vista de datos__, seleccione __Datos__ en la parte superior izquierda de la página.

## <a name="add-data-source-to-data-preparation-package"></a>Agregar un origen de datos al paquete de preparación de datos

1. Seleccione __Preparar__ para empezar a preparar los datos. 

2. Cuando se le pida, escriba un nombre para el paquete de preparación de datos, como `BikeShare Data Prep`. 

3. Seleccione __Aceptar__ para continuar.

   ![Imagen del cuadro de diálogo de preparación](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Aparecerá un nuevo paquete denominado **BikeShare Data Prep** en la sección __Preparación de datos__ de la pestaña __Datos__. 

   Para mostrar el paquete, seleccione esta entrada. 

5. Seleccione el botón **>>** para expandir los __flujos de datos__ que contiene el paquete. En este ejemplo, __BostonWeather__ es el único flujo de datos.

   > [!IMPORTANT]
   > Un paquete puede contener varios flujos de datos.

   ![Imagen de los flujos de datos contenidos en el paquete](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrar datos por valor
1. Para filtrar los datos, haga clic con el botón derecho en una celda con un valor determinado, seleccione __Filtro__ y elija el tipo de filtro.

2. Para este tutorial, seleccione una celda que contenga el valor `FM-15` y, después, establezca el filtro en **es igual a**.  Ahora los datos están filtrados y solo devuelven las filas en las que __REPORTTYPE__ es `FM-15`.

   ![Imagen del cuadro de diálogo de filtro](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 es un tipo de Informe meteorológico aeronáutico de rutina (METAR). Los informes FM-15 se consideran empíricamente los más completos, ya que en ellos faltan pocos datos.

## <a name="remove-a-column"></a>Quitar una columna

Ya no necesita la columna __REPORTTYPE__. Haga clic con el botón derecho en el encabezado de columna y seleccione **Quitar columna**.

   ![Imagen de la opción de quitar columna](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Cambiar tipos de datos y eliminar errores
1. Si presiona __Ctrl (comando ⌘ en Mac)__ mientras selecciona encabezados de columna, puede seleccionar varias columnas a la vez. Use este método para seleccionar los siguientes encabezados de columna:
   * **HOURLYDRYBULBTEMPF**
   * **HOURLYRelativeHumidity**
   * **HOURLYWindSpeed**

2. **Haga clic con el botón derecho** en uno de los encabezados de columna seleccionados y haga clic en **Convert Field Type to Numeric** (Convertir el tipo de campo en numérico). Esto convierte el tipo de datos de las columnas en numérico.

   ![Convertir varias columnas en numéricas](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtre los valores de error. Algunas columnas tienen problemas de conversión del tipo de datos. Este problema se indica en rojo en la __barra de calidad de datos__ de la columna.

   Para quitar las filas que tienen errores, haga clic con el botón derecho en el encabezado de columna **HOURLYDRYBULBTEMPF**. Seleccione **Filtrar columna**. Use el valor predeterminado **Quiero** como **Conservar filas**. Cambie el menú desplegable **Condiciones** para seleccionar **is not error** (no es un error). Seleccione **Aceptar** para aplicar el filtro.

4. Para eliminar las filas de error restantes de las demás columnas, repita este proceso de filtro para las columnas **HOURLYRelativeHumidity** y **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Usar transformaciones por ejemplos

Para usar los datos de una predicción para bloques temporales de dos horas, debe calcular el promedio de las condiciones meteorológicas en períodos de dos horas. Para ello, puede ejecutar las acciones siguientes:

* Divida la columna **DATE** en diferentes columnas de **Fecha** y **Hora**. Vea la sección siguiente para conocer los pasos detallados.

* Derive una columna **Hour_Range** de la columna **Hora**. Vea la sección siguiente para conocer los pasos detallados.

* Derive una columna **Date\_Hour\_Range** de las columnas **DATE** y **Hour_Range**. Vea la sección siguiente para conocer los pasos detallados.

### <a name="split-column-by-example"></a>División de columnas por ejemplos

1. Divida la columna **DATE** en diferentes columnas de fecha y hora. Haga clic con el botón derecho en el encabezado de columna **DATE** y seleccione **Split Column by Example** (Dividir columna por ejemplos).

   ![Imagen de la entrada Dividir columna por ejemplos](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. Azure Machine Learning Workbench identifica automáticamente un delimitador significativo y crea dos columnas mediante la división de los datos en valores de fecha y de hora. 

3. Seleccione __Aceptar__ para confirmar los resultados de la operación de división.

   ![Imagen de las columnas divididas DATE_1 y DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Derivación de columnas por ejemplos

1. Para derivar un intervalo de dos horas, haga clic con el botón derecho en el encabezado de columna __DATE\_2__ y seleccione **Derive Column by Example** (Derivar columna por ejemplos).

   ![Imagen de la entrada Derivar columna por ejemplos](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Se agrega una nueva columna vacía con valores null.

2. Haga clic en la primera celda vacía de la nueva columna. Proporcione un ejemplo del intervalo de tiempo que le interesa. Para ello, escriba `12AM-2AM` en la nueva columna y presione ENTRAR.

   ![Imagen de la nueva columna con un valor de 12AM-2AM (12 a. m.-2 a. m.)](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > Azure ML Workbench sintetiza un programa basado en los ejemplos que usted ha proporcionado y lo aplica a las filas restantes. Las demás filas se rellenan automáticamente en función del ejemplo proporcionado. Workbench también analiza los datos e intenta identificar casos extremos. 
  
3. El texto **Analyzing Data** (Analizando datos) encima de la cuadrícula indica que Workbench está intentando detectar casos extremos. Cuando finalice, el estado cambiará a **Review next suggested row** (Revisar siguiente fila sugerida) o **No suggestions** (Ninguna sugerencia). En este ejemplo, se devuelve **Review next suggested row** (Revisar siguiente fila sugerida).

4. Para revisar los cambios sugeridos, seleccione **Review next suggested row** (Revisar siguiente fila sugerida). En la pantalla se resalta la celda que debe revisar y corregir (si es necesario).

   ![Imagen de fila que se debe revisar](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Seleccione __Aceptar__ para confirmar la transformación.
 
5. Ahora vuelve a la vista de cuadrícula de datos de __BostonWeather__. La cuadrícula contiene las tres columnas agregadas previamente.

   ![Imagen de la vista de cuadrícula con las filas agregadas](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   >  Todos los cambios realizados se conservan en el panel **Pasos**. Vaya al paso que ha creado en el panel **Pasos**, haga clic en la flecha abajo y seleccione **Editar**. Se muestra la ventana avanzada de **Derive Column by Example** (Derivar columna por ejemplos). Todos los ejemplos se conservan aquí. También puede agregar ejemplos manualmente. Para ello, haga doble clic en una fila de la cuadrícula siguiente. Seleccione **Cancelar** para volver a la cuadrícula principal sin aplicar los cambios. También puede tener acceso a esta vista si selecciona el **modo avanzado** mientras realiza una transformación **Derive Column by Example** (Derivar columna por ejemplos).

6. Para cambiar el nombre de la columna, haga doble clic en el encabezado de columna y escriba **Hour Range** (Intervalo de horas). Presione **ENTRAR** para guardar el cambio.

   ![Cambiar el nombre de la columna](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Para derivar el intervalo de fecha y hora, seleccione al mismo tiempo las columnas **Date\_1** y **Hour Range** (Intervalo de horas), haga clic con el botón derecho y seleccione **Derive column by example** (Derivar columna por ejemplos).

   ![Derivar columnas por ejemplos](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Escriba `Jan 01, 2015 12AM-2AM` como ejemplo en la primera fila y presione **ENTRAR**.

   Workbench determina la transformación en función del ejemplo proporcionado. En este ejemplo, el resultado es que el formato de fecha se cambia y se concatena con el período de dos horas.

   ![Imagen del ejemplo "Jan 01, 2015 12AM-2AM" (1 de enero de 2015, 12 a. m.-2 a. m.)](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)


8. Espere a que el estado cambie de **Analyzing Data** (Analizando datos) a **Review next suggested row** (Revisar siguiente fila sugerida). Esto podría tardar varios segundos. Seleccione el vínculo de estado para desplazarse a la fila sugerida. 

   ![Imagen de la fila sugerida que se va a revisar](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   La fila tiene un valor null porque el valor de la fecha de origen podría ser dd/mm/aaaa o mm/dd/aaaa. Escriba el valor correcto de `Jan 13, 2015 2AM-4AM` y presione **ENTRAR**. Workbench usa los dos ejemplos para mejorar la derivación de las filas restantes.

   ![Imagen de los datos con el formato correcto](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Seleccione **Aceptar** para confirmar la transformación.

   ![Imagen de la cuadrícula de transformación completada](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Puede usar el modo avanzado de **Derive column by example** (Derivar columna por ejemplos) para este paso. Para ello, haga clic en la flecha abajo en el panel **Pasos**. En la cuadrícula de datos, hay casillas junto a los nombres de las columnas **DATE\_1** y **Hour Range** (Intervalo de horas). Desactive la casilla que aparece junto a la columna **Hour Range** (Intervalo de horas) para ver cómo cambia la salida. En ausencia de la columna **Hour Range** (Intervalo de horas) como entrada, **12AM-2AM** (12 a. m.-2 a. m.) se trata como una constante y se anexa a los valores derivados. Seleccione **Cancelar** para volver a la cuadrícula principal sin aplicar los cambios.

10. Para cambiar el nombre de la columna, haga doble clic en el encabezado. Cambie el nombre a **Date Hour Range** (Intervalo de fecha y hora) y presione **ENTRAR**.

11. Seleccione al mismo tiempo las columnas **DATE**, **DATE\_1**, **DATE\_2** y **Hour Range** (Intervalo de horas). Haga clic con el botón derecho y seleccione **Quitar columna**.

## <a name="summarize-data-mean"></a>Resumir datos (media)

El paso siguiente consiste en resumir las condiciones meteorológicas a partir de la media de valores, agrupados por intervalos de horas.

1. Seleccione la columna **Date Hour Range** (Intervalo de fecha y hora) y, después, seleccione **Resumir** en el menú **Transformaciones**.

    ![Menú Transformaciones](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Para resumir los datos, arrastre las columnas de la cuadrícula en la parte inferior de la página a los paneles de la izquierda y la derecha en la parte superior. El panel izquierdo contiene el texto **Drag columns here to group data** (Arrastre las columnas aquí para agrupar los datos). El panel derecho contiene el texto **Drag columns here to summarize data** (Arrastre las columnas aquí para resumir los datos). 

    Arrastre la columna **Date Hour Range** (Intervalo de fecha y hora) de la cuadrícula en la parte inferior al panel izquierdo. Arrastre **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** y **HOURLYWindSpeed** al panel derecho. 

    En el panel derecho, seleccione **Media** como medida **Agregada** para cada columna. Haga clic en **Aceptar** para completar el resumen.

   ![Imagen de la pantalla de datos resumidos](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-using-script"></a>Transformar un flujo de datos mediante un script

Algunos modelos pueden convergir más rápidamente si se cambian los datos de las columnas numéricas a un intervalo de 0-1. Actualmente no existe ninguna transformación integrada para realizar esta transformación de forma genérica, pero se puede usar un script de Python para llevar a cabo esta operación.

1. En el menú **Transformar**, seleccione **Transform Dataflow** (Transformar flujo de datos).

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
    
    ![Cuadro de diálogo del script para transformar el flujo de datos](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Seleccione __Aceptar__ para usar el script. Las columnas numéricas de la cuadrícula ahora contienen valores en el intervalo 0-1.

    ![Cuadrícula con valores comprendidos entre 0 y 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Ya ha acabado de preparar los datos meteorológicos. Ahora, preparará los datos de los viajes.

## <a name="load-trip-data"></a>Cargar datos de los viajes

1. Para importar el archivo `201701-hubway-tripdata.csv`, siga los pasos indicados en la sección [Crear un origen de datos](#newdatasource). Durante el proceso de importación, use las opciones siguientes:

    * __Esquema de muestreo__: use el esquema de muestreo del **archivo completo** (active la muestra). 
    * __Tipo de datos__: acepte los valores predeterminados.

2. Después de importar los datos, seleccione el botón __Preparar__ para empezar a preparar los datos. Seleccione el paquete **BikeShare Data Prep.dprep** existente y haga clic en __Aceptar__.

    Este proceso agrega un **flujo de datos** al archivo existente de **preparación de datos** en lugar de crear uno nuevo.

    ![Imagen de la selección del paquete existente](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Una vez que se haya cargado la cuadrícula, expanda __DATAFLOWS__ (FLUJOS DE DATOS). Ahora hay dos flujos de datos: **BostonWeather** y **201701-hubway-tripdata**. Seleccione la entrada **201701-hubway-tripdata**.

    ![Imagen de la entrada 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-map-inspector"></a>Usar el inspector de mapas

Para la preparación de datos, hay una serie de visualizaciones útiles llamadas **Inspectores** para datos de cadena, numéricos y geográficos, que ayudan a comprender mejor los datos e identificar valores atípicos. Siga estos pasos para usar el inspector de mapas:

1. Seleccione al mismo tiempo las columnas **start station latitude** (latitud de la estación de inicio) y **start station longitude** (longitud de la estación de inicio). Haga clic con el botón derecho en una de las columnas y seleccione **Mapa**.

    > [!TIP]
    > Para habilitar la selección de varios elementos al mismo tiempo, mantenga presionada la tecla __Ctrl (comando ⌘ en Mac)__ y seleccione el encabezado de cada columna.

    ![Imagen de la visualización de mapas](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Para maximizar la visualización de mapas, seleccione el icono **Maximizar**. Para ajustar el mapa a la ventana, seleccione el icono **E** situado en el lado superior izquierdo de la visualización.

    ![Imagen maximizada](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Haga clic en el botón **Minimizar** para volver a la vista de cuadrícula.

## <a name="use-column-statistics-inspector"></a>Usar el inspector de estadísticas de columna

Para usar el inspector de estadísticas de columna, haga clic con el botón derecho en la columna __tripduration__ (duración del viaje) y seleccione __Column Statistics__ (Estadísticas de columna).

![Entrada de estadísticas de columna](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Este proceso agrega una nueva visualización titulada __tripduration Statistics__ (Estadísticas de duración del viaje) en el panel __INSPECTORS__ (INSPECTORES).

![Imagen del inspector de estadísticas de duración del viaje](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> El valor máximo de la duración del viaje es de **961 814 minutos**, que es aproximadamente dos años. Parece que hay algunos valores atípicos en el conjunto de datos.

## <a name="use-histogram-inspector"></a>Usar el inspector de histogramas

Para intentar identificar los valores atípicos, haga clic con el botón derecho en la columna __tripduration__ (duración del viaje) y seleccione __Histograma__.

![Inspector de histogramas](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

El histograma no resulta útil, ya que los valores atípicos distorsionan el gráfico.

## <a name="add-column-using-script"></a>Agregar una columna mediante un script

1. Haga clic con el botón derecho en la columna **tripduration** (duración del viaje) y seleccione **Add Column (Script)** (Agregar columna (script)).

    ![Imagen del menú Agregar columna (script)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. En el cuadro de diálogo __Add Column (Script)__ (Agregar columna (script)), use los siguientes valores:

    * __Nuevo nombre de columna__: logtripduration (duración del viaje del registro)
    * __Insertar esta nueva columna después de__: tripduration (duración del viaje)
    * __Nuevo código de columna__: `math.log(row.tripduration)`
    * __Tipo de bloque de código__: expresión

   ![Cuadro de diálogo Agregar columna (script)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Seleccione __Aceptar__ para agregar la columna **logtripduration** (duración del viaje del registro).

4. Haga clic con el botón derecho en la columna y seleccione **Histograma**.

    ![Histograma de la columna logtripduration (duración del viaje del registro)](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

  Visualmente, este histograma parece una distribución normal con una cola anómala.

## <a name="use-advanced-filter"></a>Usar un filtro avanzado

El uso de un filtro en los datos actualiza los inspectores con la nueva distribución. Haga clic con el botón derecho en la columna **logtripduration** (duración del viaje del registro) y seleccione **Filtrar columna**. En el cuadro de diálogo __Editar__, use los siguientes valores:

* __Filtrar esta columna de número__: logtripduration (duración del viaje del registro)
* __Quiero__: conservar filas
* __Cuándo__: cualquiera de las siguientes condiciones es True (OR lógico)
* __Si esta columna__: es menor que
* __El valor__: 9

![Opciones de filtro](media/tutorial-bikeshare-dataprep/loftripfilter.png)

Seleccione __Aceptar__ para aplicar el filtro.

![Histogramas actualizados después de aplicar el filtro](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>El efecto de halo

1. Maximice el histograma de **logtripduration** (duración del viaje del registro). Hay un histograma azul superpuesto sobre un histograma gris. Esta visualización se conoce como **efecto de halo**:

    * El **histograma gris** representa la distribución antes de la operación (en este caso, la operación de filtrado).
    * El **histograma azul** representa el histograma después de la operación. 

   El efecto de halo ayuda a visualizar el efecto de una operación en los datos.

   ![Imagen del efecto de halo](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > Observe que el histograma azul parece más corto en comparación con el anterior. Esto se debe a que se vuelven a crear automáticamente depósitos de los datos en el nuevo intervalo.

2. Para quitar el halo, seleccione __Editar__ y desactive la opción __Show halo__ (Mostrar halo).

    ![Opciones del histograma](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Seleccione **Aceptar** para deshabilitar el efecto de halo y, después, minimice el histograma.

### <a name="remove-columns"></a>Quitar columnas

En los datos del viaje, cada fila representa una acción de tomar una bicicleta. Para este tutorial, solo necesita las columnas **starttime** y **start station id**. Puede quitar las demás columnas. Para ello, seleccione al mismo tiempo estas dos columnas, haga clic con el botón derecho en el encabezado de columna y, después, seleccione **Keep Column** (Conservar columna). Las demás columnas se quitan.

![Imagen de la opción de conservar columna](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Resumir datos (recuento)

Para resumir la demanda de bicicletas durante un período de 2 horas, use columnas derivadas.

1. Haga clic con el botón derecho en la columna **starttime** (hora de inicio) y seleccione **Derive Column by Example** (Derivar columna por ejemplos).

    ![Imagen de la opción para derivar por ejemplos](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Por ejemplo, escriba un valor de `Jan 01, 2017 12AM-2AM` para la primera fila.

    > [!IMPORTANT]
    > En el ejemplo anterior de derivación de columnas, ha usado varios pasos para derivar una columna que contenía el período de fecha y hora. En este ejemplo, puede ver que esta operación se puede realizar en un solo paso si se proporciona un ejemplo de la salida final.

    > [!NOTE]
    > Puede dar un ejemplo en cualquiera de las filas. En este ejemplo, el valor de `Jan 01, 2017 12AM-2AM` es válido para la primera fila de datos.

    ![Imagen de los datos de ejemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)
   
3. Espere hasta que la aplicación calcule los valores de todas las filas. Esto podría tardar varios segundos. Una vez completado el análisis, use el vínculo __Review next suggested row__ (Revisar siguiente fila sugerida) para revisar los datos.

   ![Imagen del análisis completado con un vínculo de revisión](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Asegúrese de que los valores calculados son correctos. Si no es así, actualice el valor con el valor esperado y presione ENTRAR. Después, espere a que se complete el análisis. Complete el proceso de **Review next suggested row** (Revisar siguiente fila sugerida) hasta que vea **No suggestions** (Ninguna sugerencia). Cuando vea **No suggestions** (Ninguna sugerencia), significa que la aplicación ha examinado los casos extremos y está satisfecha con el programa sintetizado. Se recomienda llevar a cabo una inspección visual de los datos transformados antes de aceptar la transformación. 

4. Seleccione **Aceptar** para confirmar la transformación. Cambie el nombre de la columna recién creada a **Date Hour Range** (Intervalo de fecha y hora).

    ![Imagen de la columna con el nombre cambiado](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Haga clic con el botón derecho en el encabezado de columna **starttime** (hora de inicio) y seleccione **Quitar columna**.

6. Para resumir los datos, seleccione __Resumir__ en el menú __Transformar__. Para crear la transformación, lleve a cabo las acciones siguientes:

    * Arrastre __Date Hour Range__ (Intervalo de fecha y hora) y __start station id__ (identificador de la estación de inicio) al panel de la izquierda (Agrupar por).
    * Arrastre __start station id__ (identificador de la estación de inicio) al panel de la derecha (Resumir datos).

   ![Imagen de las opciones de resumen](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Seleccione **Aceptar** para confirmar el resultado del resumen.

## <a name="join-dataflows"></a>Combinar flujos de datos

Para combinar los datos meteorológicos con los datos del viaje, siga estos pasos:

1. Seleccione __Combinar__ en el menú __Transformar__.

2. __Tablas__: seleccione **BostonWeather** como flujo de datos de la izquierda y **201701-hubway-tripdata** como flujo de datos de la derecha. Para continuar, seleccione **Siguiente**.

    ![Imagen de la selección de tablas](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Columnas clave__: seleccione la columna **Date Hour Range** (Intervalo de fecha y hora) de ambas tablas y haga clic en __Siguiente__.

    ![Imagen de la combinación de columnas clave](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Tipo de combinación__: seleccione __Matching rows__ (Filas coincidentes) como tipo de combinación y haga clic en __Finalizar__.

    ![Tipo de combinación de filas coincidentes](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Este proceso crea un flujo de datos denominado __Join Result__ (Resultado de la combinación).

## <a name="create-additional-features"></a>Crear características adicionales

1. Para crear una columna que contenga el día de la semana, haga clic con el botón derecho en la columna **Date Hour Range** (Intervalo de fecha y hora) y seleccione **Derive column by Example** (Derivar columna por ejemplos). Use un valor de __Sun__ (Dom) para una fecha que sea domingo. Por ejemplo, **Jan 01, 2017 12AM-2AM** (1 de enero de 2017, 12 a. m.-2 a. m.). Presione **ENTRAR** y seleccione **Aceptar**. Cambie el nombre de esta columna a __Día de la semana__.

    ![Imagen de la creación de una columna que contiene el día de la semana](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Para crear una columna que contenga el período de tiempo de una fila, haga clic con el botón derecho en la columna **Date Hour Range** (Intervalo de fecha y hora) y seleccione **Derive column by Example** (Derivar columna por ejemplos). Use un valor de **12AM-2AM** (12 a. m.-2 a. m.) para la fila que contenga **Jan 01, 2017 12AM-2AM** (1 de enero de 2017, 12 a. m.-2 a. m.). Presione **ENTRAR** y seleccione **Aceptar**. Cambie el nombre de esta columna a **Período**.

    ![Imagen de la columna Período](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Para quitar las columnas **Date Hour Range** (Intervalo de fecha y hora) y **rDate Hour Range**, presione **Ctrl (comando ⌘ en Mac)** y seleccione cada encabezado de columna. Haga clic con el botón derecho y seleccione **Quitar columna**.

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

Para guardar el flujo de datos **Join Result** (Resultado de la combinación) en un archivo .CSV, debe cambiar el script `BikeShare Data Prep.py`. Actualice el script de Python con el código siguiente:

```python
from azureml.dataprep.package import run

# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTest.csv
df.to_csv('Your Test Data File Path here')
```

Seleccione **Ejecutar** en la parte superior de la pantalla. El script se envía como un **trabajo** en el equipo local. Cuando el estado del trabajo cambie a __Completado__, el archivo se habrá escrito en la ubicación especificada.

## <a name="substitute-data-sources"></a>Sustituir orígenes de datos

En los pasos anteriores, ha usado los orígenes de datos `201701-hubway-tripdata.csv` y `BostonWeather.csv` para preparar los datos de prueba. Para usar el paquete con los demás archivos de datos del viaje, siga estos pasos:

1. Cree un **origen de datos** mediante los pasos indicados anteriormente, pero cambie lo siguiente en el proceso:

    * __Selección de archivos__: al seleccionar un archivo, elija al mismo tiempo los seis archivos CSV restantes de datos del viaje.

        ![Cargar los seis archivos restantes](media/tutorial-bikeshare-dataprep/selectsixfiles.png)

        > [!NOTE]
        > La entrada __+ 5__ indica que hay cinco archivos adicionales, aparte del que aparece.

    * __Detalles del archivo__: establezca __Promote Headers Mode__ (Modo Promover encabezados) en **All Files Have The Same Headers** (Todos los archivos tienen los mismos encabezados). Este valor indica que cada uno de los archivos contiene el mismo encabezado.

        ![Selección de detalles de archivo](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Guarde el nombre de este origen de datos, ya que se usará en pasos posteriores.

2. Seleccione el icono de carpeta para ver los archivos del proyecto. Expanda el directorio __aml\_config__ y seleccione el archivo `local.runconfig`.

    ![Imagen de la ubicación de local.runconfig](media/tutorial-bikeshare-dataprep/localrunconfig.png) 

3. Agregue las siguientes líneas al final del archivo `local.runconfig` y, después, seleccione el icono de disco para guardar el archivo.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Este cambio reemplaza el origen de datos original por el que contiene los seis archivos de datos del viaje.

## <a name="save-training-data-as-a-csv-file"></a>Guardar los datos de aprendizaje como un archivo CSV

Vaya al archivo de Python `BikeShare Data Prep.py` que ha editado anteriormente y proporcione una ruta de acceso al archivo diferente para guardar los datos de aprendizaje.

```python
from azureml.dataprep.package import run
# dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
df = run('BikeShare Data Prep.dprep', dataflow_idx=2)

# Example file path: C:\\Users\\Jayaram\\BikeDataOut\\BikeShareTrain.csv
df.to_csv('Your Training Data File Path here')
```

Para enviar un trabajo nuevo, use el icono **Ejecutar** situado en la parte superior de la página. Se envía un **trabajo** con la configuración nueva. La salida de este trabajo son los datos de aprendizaje. Estos datos se crean mediante los mismos pasos de preparación de datos que ha creado anteriormente. El trabajo podría tardar varios minutos en completarse.

## <a name="next-steps"></a>pasos siguientes
Ha completado el tutorial de preparación de datos de uso compartido de bicicletas. En este tutorial, ha usado los servicios de Azure Machine Learning (versión preliminar) para obtener información sobre cómo:
> [!div class="checklist"]
> * Preparar datos de forma interactiva con la herramienta de preparación de datos de Azure Machine Learning
> * Importar, transformar y crear un conjunto de datos de prueba
> * Generar un paquete de preparación de datos
> * Ejecutar el paquete de preparación de datos con Python
> * Generar un conjunto de datos de aprendizaje mediante la reutilización del paquete de preparación de datos para los archivos de entrada adicionales

Ahora, obtenga más información sobre la preparación de los datos:
> [!div class="nextstepaction"]
> [Guía de usuario sobre preparación de los datos](data-prep-user-guide.md)
