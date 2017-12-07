---
title: "Profundización en la predicción del estado de vehículo y los hábitos de conducción: Azure | Microsoft Docs"
description: "Aproveche las posibilidades de Cortana Intelligence para obtener información en tiempo real y predictiva del estado de los vehículos y los hábitos de conducción."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev
ms.openlocfilehash: a21316ef6ab05918f07a09243b5ce04950ecd9dc
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Cuaderno de estrategias de la solución Vehicle Telemetry Analytics: profundización en la solución
Este menú vincula a las secciones de este cuaderno de estrategias: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

En este documento se detalla cada uno de los pasos descritos en la arquitectura de la solución. Se incluyen instrucciones y punteros para la personalización. 

## <a name="data-sources"></a>Orígenes de datos
Esta solución usa dos orígenes de datos diferentes:

* Conjunto de datos de señales de vehículo y diagnóstico simulados
* Catálogo del vehículo

Se incluye un simulador telemático del vehículo como parte de esta solución, tal como se muestra en la captura de pantalla siguiente. Este simulador emite información de diagnóstico y señales correspondientes al estado del vehículo y al patrón de conducción en un momento dado en el tiempo. Para descargar la solución Vehicle Telematics Simulator de Visual Studio y personalizarla de acuerdo a sus necesidades, vaya a la página web del [simulador telemático de vehículo](http://go.microsoft.com/fwlink/?LinkId=717075). El catálogo del vehículo contiene un conjunto de datos de referencia que asigna los números de chasis a los modelos correspondientes.

![Simulador telemático de vehículo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Este conjunto de datos con formato JSON contiene el esquema siguiente.

| Columna | Description | Valores |
| --- | --- | --- |
| VIN |Número de chasis generado aleatoriamente |Se obtiene de una lista maestra de 10 000 números de chasis generados aleatoriamente |
| Outside temperature |La temperatura exterior del entorno en el que se está conduciendo el vehículo  |Número generado aleatoriamente de 0 a 100 |
| Engine temperature |La temperatura del motor del vehículo |Número generado aleatoriamente de 0 a 500 |
| Velocidad |La velocidad del motor del vehículo en conducción |Número generado aleatoriamente de 0 a 100 |
| Fuel |El nivel de combustible del vehículo |Número generado aleatoriamente de 0 a 100 (indica el porcentaje de combustible) |
| EngineOil |El nivel de aceite del motor del vehículo |Número generado aleatoriamente de 0 a 100 (indica el porcentaje de aceite del motor) |
| Presión de los neumáticos |La presión de los neumáticos del vehículo |Número generado aleatoriamente de 0 a 50 (indica el porcentaje de presión de los neumáticos) |
| Odometer |La lectura del cuentakilómetros del vehículo |Número generado aleatoriamente de 0 a 200 000 |
| Accelerator_pedal_position |La posición del pedal del acelerador del vehículo |Número generado aleatoriamente de 0 a 100 (indica el porcentaje del acelerador) |
| Parking_brake_status |Indica si el vehículo está aparcado o no |Verdadero o falso |
| Headlamp_status |Indica si los faros están encendidos o no |Verdadero o falso |
| Brake_pedal_status |Indica si el pedal de freno está pisado o no |Verdadero o falso |
| Transmission_gear_position |La posición del cambio de marcha del vehículo |Estados: primera, segunda, tercera y cuarta, quinta, sexta, séptima, octava |
| Ignition_status |Indica si el vehículo está en marcha o detenido |Verdadero o falso |
| Windshield_wiper_status |Indica si el limpiaparabrisas está activado o no |Verdadero o falso |
| ABS |Indica si el ABS está activado o no |Verdadero o falso |
| Timestamp |La marca de tiempo del momento en el que se crea el punto de datos |Date |
| City |La ubicación del vehículo |Esta solución ofrece cuatro ciudades: Bellevue, Redmond, Sammamish, Seattle |

El conjunto de datos de referencia del modelo de los vehículos asigna los números de chasis a los modelos correspondientes. 

| VIN | Modelo |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Sedán |
| 8J0U8XCPRGW4Z3NQE |Híbrido |
| WORG68Z2PLTNZDBI7 |Berlina familiar |
| JTHMYHQTEPP4WBMRN |Sedán |
| W9FTHG27LZN1YWO0Y |Híbrido |
| MHTP9N792PHK08WJM |Berlina familiar |
| EI4QXI2AXVQQING4I |Sedán |
| 5KKR2VB4WHQH97PF8 |Híbrido |
| W9NSZ423XZHAONYXB |Berlina familiar |
| 26WJSGHX4MA5ROHNL |Descapotable |
| GHLUB6ONKMOSI7E77 |Familiar |
| 9C2RHVRVLMEJDBXLP |Compacto |
| BRNHVMZOUJ6EOCP32 |SUV pequeño |
| VCYVW0WUZNBTM594J |Deportivo |
| HNVCE6YFZSA5M82NY |SUV mediano |
| 4R30FOR7NUOBL05GJ |Familiar |
| WYNIIY42VKV6OQS1J |SUV grande |
| 8Y5QKG27QET1RBK7I |SUV grande |
| DF6OX2WSRA6511BVG |Cupé |
| Z2EOZWZBXAEW3E60T |Sedán |
| M4TV6IEALD5QDS3IR |Híbrido |
| VHRA1Y2TGTA84F00H |Berlina familiar |
| R0JAUHT1L1R3BIKI0 |Sedán |
| 9230C202Z60XX84AU |Híbrido |
| T8DNDN5UDCWL7M72H |Berlina familiar |
| 4WPYRUZII5YV7YA42 |Sedán |
| D1ZVY26UV2BFGHZNO |Híbrido |
| XUF99EW9OIQOMV7Q7 |Berlina familiar |
| 8OMCL3LGI7XNCC21U |Descapotable |
| ……. | |

## <a name="ingestion"></a>Ingesta de datos
Se usa una combinación de Azure Event Hubs, Azure Stream Analytics y Azure Data Factory para introducir las señales de vehículo, los eventos de diagnóstico y los análisis en tiempo real y por lotes. Todos estos componentes se crean y se configuran como parte de la implementación de la solución. 

### <a name="real-time-analysis"></a>Análisis en tiempo real
Los eventos generados por el simulador telemático de vehículo se publican en Event Hubs mediante el SDK de la instancia.  

![Panel del Centro de eventos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

El trabajo de Stream Analytics ingiere estos eventos de Event Hubs y procesa los datos en tiempo real para analizar el estado del vehículo.

![Procesamiento de datos del trabajo de Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


El trabajo de Stream Analytics:

* Ingiere los datos de Event Hubs.
* Realiza una combinación con los datos de referencia para asignar el número de chasis del vehículo al modelo correspondiente. 
* Los conserva en Azure Blob Storage para unos análisis eficaces por lotes. 

La siguiente consulta de Stream Analytics se usa para conservar los datos en Blob Storage: 

![Consulta de trabajo de Stream Analytics para la ingesta de datos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Análisis por lotes
También se genera un conjunto de datos de señales de vehículo y diagnóstico simulados para un análisis por lotes más completo. Este volumen adicional es necesario para garantizar un volumen de datos representativo para el procesamiento por lotes. Con este propósito, se utiliza PrepareSampleDataPipeline en el flujo de trabajo de Data Factory, para generar un conjunto de datos de señales de vehículo y diagnóstico simulados de un año completo. Para descargar la solución de Visual Studio de actividad .NET personalizada de Data Factory para realizar personalizaciones adaptadas a sus necesidades, vaya a la página web de la [actividad personalizada de Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077). 

Este flujo de trabajo muestra los datos de ejemplo preparados para el procesamiento por lotes.

![Datos de muestra preparados para el flujo de trabajo de procesamiento por lotes](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


La canalización consta de una actividad .NET de Data Factory.

![Actividad PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Una vez que la canalización se ejecute correctamente y se marque el conjunto de datos RawCarEventsTable como "Ready", se creará un año entero de datos de señales y diagnóstico de vehículo simulados. Verá la carpeta y el archivo siguientes creados en la cuenta de almacenamiento en el contenedor connectedcar:

![Resultados de PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Partición del conjunto de datos
En el paso de preparación de los datos se crean particiones de las señales semiestructuradas y sin procesar de los vehículos y del conjunto de datos de diagnóstico en formato AÑO/MES. Esta creación de particiones permite consultas más eficaces y un almacenamiento escalable a largo plazo al habilitar el paso de una cuenta a otra; por ejemplo, cuando la primera cuenta de blob se llena, se pasa a la siguiente. 

>[!NOTE] 
>Este paso de la solución solo es aplicable al procesamiento por lotes.

Administración de datos de entrada y salida:

* Los **datos de salida** (etiquetados PartitionedCarEventsTable) se guardarán durante un largo período de tiempo como formulario de datos fundacionales (sin procesamiento alguno) en la instancia de Data Lake del cliente. 
* Los **datos de entrada** de esta canalización se suelen descartar, ya que los de salida tienen plena fidelidad a la entrada. Se almacenan mejor (en particiones) para el uso posterior.

Flujo de trabajo de los eventos del automóvil en la partición.

![Flujo de trabajo de eventos de automóvil de partición](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Los datos sin procesar se dividen en particiones mediante una actividad de Hive de Azure HDInsight en PartitionCarEventsPipeline, como se muestra en la captura de pantalla siguiente. Se crean particiones AÑO/MES de los datos de ejemplo generados para un año en el paso de preparación de los datos. Las particiones se utilizan para generar señales de vehículo y datos de diagnóstico para cada mes (un total de 12 particiones) de un año. 

![Actividad PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Script PartitionConnectedCarEvents de Hive**

Para realizar la creación de particiones se utiliza el script partitioncarevents.hql de Hive. Se encuentra en la carpeta \demo\src\connectedcar\scripts del archivo zip descargado. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

Una vez que se ejecute correctamente la canalización, se habrán generado las siguientes particiones en la cuenta de almacenamiento en el contenedor connectedcar:

![Salida con particiones](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Los datos se han optimizado, ahora son más fáciles de administrar y están listos para su posterior procesamiento con el fin de obtener información de lote completa. 

## <a name="data-analysis"></a>Análisis de datos
En esta sección, verá cómo se combinan Stream Analytics, Azure Machine Learning, Data Factory y HDInsight para realizar un completo análisis avanzado sobre el estado del vehículo y los hábitos de conducción.

### <a name="machine-learning"></a>Machine Learning
El objetivo es predecir los vehículos que requieren mantenimiento o deben retirarse según ciertas estadísticas de estado, de acuerdo con las siguientes afirmaciones:

* Los vehículos requieren servicio de mantenimiento cuando se cumple una de las tres condiciones siguientes:
  
  * La presión de los neumáticos es baja.
  * El nivel de aceite del motor es bajo.
  * La temperatura del motor es alta.

* Los vehículos pueden tener un problema de seguridad y deben retirarse cuando se cumple una de las siguientes condiciones:
  
  * La temperatura del motor es alta, pero la temperatura exterior es baja.
  * La temperatura del motor es baja, pero la temperatura exterior es alta.

En función de las condiciones anteriores, las anomalías se detectan según dos modelos distintos. Un modelo es para la detección de que el vehículo necesita mantenimiento y el otro es para la detección de que debe retirarse. En ambos modelos, se usa el algoritmo integrado de análisis de componentes principales (PCA) para la detección de anomalías. 

#### <a name="maintenance-detection-model"></a>**Modelo de detección de mantenimiento**

Si alguno de los tres indicadores (presión de los neumáticos, aceite del motor o temperatura del motor) cumple su condición respectiva, el modelo de detección de mantenimiento informará sobre una anomalía. Como resultado, únicamente es necesario considerar estas tres variables para la compilación del modelo. En el experimento de Machine Learning, se usa el módulo **Select Columns in Dataset** (Seleccionar columnas en conjunto de datos) para extraer estas tres variables. A continuación, con el módulo de detección de anomalías basado en PCA se compilará el modelo de detección de anomalías. 

El análisis de componentes principales (PCA) es una técnica establecida en Machine Learning que se aplica a la selección de características, la clasificación y la detección de anomalías. PCA convierte un conjunto de casos con variables posiblemente relacionadas entre sí, en un conjunto de valores denominados componentes principales. La idea clave de modelado basado en PCA es proyectar los datos en un espacio dimensional inferior, para identificar las características y las anomalías más fácilmente.

Para cada entrada nueva en el modelo de detección, el detector de anomalías calcula su proyección de los vectores propios en primer lugar. A continuación, calcula el error de reconstrucción normalizado. Este error normalizado es la puntuación de la anomalía: cuanto mayor sea el error, más anómala será la instancia. 

En el problema de detección de mantenimiento, cada registro se considera como un punto en un espacio de tres dimensiones definido por las coordenadas de presión de los neumáticos, el aceite del motor y la temperatura del motor. Para capturar estas anomalías, con PCA se proyectan los datos originales en un espacio tridimensional o bidimensional. Por lo tanto, el parámetro del número de componentes que se usa en PCA se establece en dos. Este parámetro desempeña un rol importante en la aplicación de la detección de anomalías basada en PCA. Después de proyectar los datos con PCA, estas anomalías se identifican más fácilmente.

#### <a name="recall-anomaly-detection-model"></a>**Modelo de detección de anomalías de retirada**

En el modelo de detección de anomalías de retirada, se usan los módulos **Select Columns in Dataset** (Seleccionar columnas en conjunto de datos) y de detección de anomalías basado en PCA de forma similar. En concreto, en primer lugar se extraen tres variables: temperatura del motor, temperatura exterior y velocidad, con el módulo **Select Columns in Dataset** (Seleccionar columnas en conjunto de datos). También se incluye la variable de velocidad, ya que la temperatura del motor normalmente está relacionada con la velocidad. A continuación, con el módulo de detección de anomalías basado en PCA se proyectan los datos desde el espacio tridimensional en un espacio bidimensional. Se cumplen los criterios de retirada. El vehículo debe retirarse cuando existe una relación muy negativa entre la temperatura del motor y la temperatura exterior. Una vez realizado el PCA, con el algoritmo de detección de anomalías basado en PCA se capturan las anomalías. 

Con el aprendizaje de cualquier modelo, para el aprendizaje del modelo de detección de anomalías basado en PCA se usan datos normales como datos de entrada. (Los datos normales no requieren mantenimiento ni retirada). En el experimento de puntuación, se usa el modelo de detección de anomalías entrenado para detectar si el vehículo requiere mantenimiento o debe retirarse. 

### <a name="real-time-analysis"></a>Análisis en tiempo real
La siguiente consulta SQL de Stream Analytics se utiliza para obtener el promedio de todos los parámetros importantes del vehículo. Estos parámetros incluyen la velocidad del vehículo, el nivel de combustible, la temperatura del motor, la lectura del cuentakilómetros, la presión de los neumáticos, el nivel de aceite del motor, etc. Los promedios se utilizan para detectar anomalías, emitir alertas y determinar el estado general de los vehículos operativos en una región específica. A continuación, los promedios se ponen en correlación para los datos demográficos. 

![Consulta de Stream Analytics para el procesamiento en tiempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Todos los promedios se calculan con una ventana de saltos de tamaño constante de tres segundos. Se utiliza una ventana de saltos de tamaño constante porque se necesitan intervalos de tiempo sin superposición y contiguos. 

Para más información sobre las funcionalidades basadas en ventanas de Stream Analytics, consulte [Windowing (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx) [Ventanas (Azure Stream Analytics)].

#### <a name="real-time-prediction"></a>**Predicción en tiempo real**

Como parte de la solución se incluye una aplicación que ponga en funcionamiento el modelo de aprendizaje automático en tiempo real. La aplicación RealTimeDashboardApp se crea y se configura como parte de la implementación de la solución. La aplicación:

* Escucha a una instancia de Event Hubs donde Stream Analytics publica los eventos en un patrón de forma continua.

    ![Consulta de Stream Analytics para la publicación de los datos](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Recibe eventos. Para cada evento que recibe esta aplicación: 
   
   * Los datos se procesan con el punto de conexión de puntuación de solicitud-respuesta (RRS) de Machine Learning. El punto de conexión RRS se publica automáticamente como parte de la implementación.
   * La salida RRS se publica en un conjunto de datos de Power BI mediante las API de inserción.

Este patrón también es aplicable a aquellos escenarios en los que se desea integrar una aplicación de línea de negocio con el flujo de análisis en tiempo real. Estos escenarios incluyen las alertas, las notificaciones, la mensajería, etc.

Para descargar la solución RealtimeDashboardApp de Visual Studio para las personalizaciones, consulte la página web de [descarga de RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078). 

#### <a name="execute-the-real-time-dashboard-application"></a>**Ejecución de la aplicación de panel en tiempo real**
1. Extraiga RealtimeDashboardApp guárdela en la máquina local.

    ![Carpeta RealTimeDashboardApp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Ejecute la aplicación RealtimeDashboardApp.exe.

3. Escriba sus credenciales válidas de Power BI y seleccione **Iniciar sesión**.  

    ![Ventana de inicio de sesión de la aplicación de panel en tiempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Seleccione **Aceptar**.

    ![Ventana de inicio de sesión final de la aplicación de panel en tiempo real](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Si desea vaciar el conjunto de datos de Power BI, ejecute RealtimeDashboardApp con el parámetro "flushdata". 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Análisis por lotes
El objetivo es mostrar cómo Contoso Motors utiliza las funcionalidades de proceso de Azure para aprovechar los macrodatos. Estos datos revelan información valiosa sobre los patrones de conducción, el comportamiento de uso y el estado de los vehículos. Esta información hace posible:

* Mejorar la experiencia del cliente y abaratar los costos de esta, al proporcionar información sobre los hábitos de conducción y las formas de conducir que permiten el uso más eficiente del combustible.
* Aprender de forma proactiva acerca de los clientes y sus patrones de conducción para encauzar la toma de decisiones empresariales, y proporcionar servicios y productos inmejorables.

En esta solución, nuestro objetivo son las siguientes métricas:

* **Comportamiento agresivo al volante**: identifica la tendencia de los modelos, las ubicaciones, las condiciones de circulación y la época del año para obtener información sobre un patrón de comportamiento agresivo al volante. Contoso Motors puede usar esta información para campañas de marketing de seguros basados en el uso con nuevas características y prestaciones personalizadas.
* **Comportamiento de conducción con consumo eficiente de combustible**: identifica la tendencia de los modelos, las ubicaciones, las condiciones de circulación y la época del año para obtener información sobre un patrón de comportamiento de conducción con consumo eficiente de combustible. Contoso Motors puede usar esta información para campañas de marketing de nuevas prestaciones e información proactiva para los conductores sobre hábitos de conducción más económicos y ecológicos.
* **Modelos de retirada**: identifica los modelos que hay que retirar, al poner en funcionamiento los experimentos de Machine Learning de detección de anomalías.

Veamos cada una de estas métricas en detalle.

#### <a name="aggressive-driving-behavior-patterns"></a>**Patrones de comportamiento agresivo al volante**

Las particiones de las señales de vehículo y los datos de diagnóstico se procesan en AggresiveDrivingPatternPipeline, como se muestra en el flujo de trabajo siguiente. Hive se usa para determinar los modelos, la ubicación, el vehículo, las condiciones de conducción y otros parámetros que muestren un patrón de comportamiento agresivo al volante.

![Flujo de trabajo del patrón de comportamiento agresivo al volante](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Consulta de Hive para el patrón de comportamiento agresivo al volante***

El script aggresivedriving.hql de Hive se utiliza para analizar los patrones de comportamiento agresivo al volante. Se encuentra en la carpeta \demo\src\connectedcar\scripts del archivo zip descargado. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


El script usa la combinación de la posición del cambio de marcha del vehículo, el estado del pedal de freno y la velocidad para detectar comportamientos de conducción temeraria o comportamiento agresivo al volante en función del patrón de frenado a alta velocidad. 

Una vez que se ejecute correctamente la canalización, se habrán generado las siguientes particiones en la cuenta de almacenamiento en el contenedor connectedcar:

![Resultados de AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Patrón de comportamiento de conducción con consumo eficiente de combustible**

Las particiones de las señales de vehículo y los datos de diagnóstico se procesan en FuelEfficientDrivingPatternPipeline, como se muestra en el flujo de trabajo siguiente. Hive se usa para determinar los modelos, la ubicación, el vehículo, las condiciones de conducción y otras propiedades que muestren patrones de conducción con consumo eficiente de combustible.

![Patrón de conducción con consumo eficiente de combustible](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Consulta de Hive para el patrón de conducción con consumo eficiente de combustible***

El script fuelefficientdriving.hql de Hive se utiliza para analizar los patrones conducción con consumo eficiente de combustible. Se encuentra en la carpeta \demo\src\connectedcar\scripts del archivo zip descargado. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


El script usa la combinación de la posición del cambio de marcha del vehículo, el estado del pedal de freno, la velocidad y la posición del pedal acelerador para detectar comportamientos de conducción con consumo eficiente de combustible en función de patrones de aceleración, frenada y velocidad. 

Una vez que se ejecute correctamente la canalización, se habrán generado las siguientes particiones en la cuenta de almacenamiento en el contenedor connectedcar:

![Resultados de FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Predicciones del modelo de retirada**

El experimento de aprendizaje automático se proporciona y publica como un servicio web como parte de la implementación de la solución. En este flujo de trabajo se usa el punto de conexión de puntuación por lotes. Se ha registrado como un servicio vinculado de Data Factory que funciona con la actividad de puntuación por lotes de esta instancia.

![Punto de conexión de Machine Learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

El servicio vinculado registrado se usa en DetectAnomalyPipeline para puntuar los datos mediante el modelo de detección de anomalías. 

![Actividad de puntuación por lotes de Machine Learning en Data Factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

En esta canalización se llevan a cabo unos pasos para la preparación de los datos para que puedan usarse con el servicio web de puntuación por lotes. 

![DetectAnomalyPipeline para la predicción de la retirada](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Consulta de Hive de detección de anomalías***

Una vez finalizada la puntuación, una actividad de HDInsight procesa y agrega los datos que el modelo clasificara como anomalías. El modelo usa una puntuación de probabilidad de 0,60 o mayor.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Una vez que se ejecute correctamente la canalización, se habrán generado las siguientes particiones en la cuenta de almacenamiento en el contenedor connectedcar:

![Resultados de DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publicar

### <a name="real-time-analysis"></a>Análisis en tiempo real
Una de las consultas en el trabajo de Stream Analytics publica los eventos en una instancia de Event Hubs de resultados. 

![Trabajo de Stream Analytics publicado en una instancia de Event Hubs de resultados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

La siguiente consulta de Stream Analytics se usa para publicar resultados en la instancia de Event Hubs:

![Consulta de Stream Analytics para publicar en la instancia de Event Hubs de resultados](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Este flujo de eventos lo consume la aplicación RealTimeDashboardApp que se incluye en la solución. Esta aplicación utiliza el servicio web de solicitud-respuesta de Machine Learning para puntuar en tiempo real. Publica los datos resultantes en un conjunto de datos de Power BI para el consumo. 

### <a name="batch-analysis"></a>Análisis por lotes
Los resultados del procesamiento por lotes y en tiempo real se publican en las tablas de Azure SQL Database para el consumo. El servidor y la base de datos de SQL, y las tablas se crean automáticamente como parte del script de instalación. 

Los resultados del procesamiento por lotes se copian en el flujo de trabajo del data mart.

![Copia de los resultados del procesamiento por lotes en el flujo de trabajo de data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

El trabajo de Stream Analytics se publica en el data mart.

![Trabajo de Stream Analytics publicado en el data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

El valor del data mart se encuentra en el trabajo de Stream Analytics.

![Ajuste de puesto de datos en el trabajo de Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Consumo
Power BI ofrece a esta solución un panel completo para datos en tiempo real y visualizaciones de análisis predictivo. 

El panel final se parece a este ejemplo:

![Panel de Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Resumen
Este documento contiene un desglose detallado de la solución de análisis de telemetría de vehículos. Se usa un patrón de arquitectura lambda para el análisis en tiempo real y de procesamiento por lotes con predicciones y acciones. Este patrón se aplica a una amplia gama de casos de uso que requieren análisis con ruta de acceso activa (en tiempo real) y la ruta de acceso frío (lote). 

### <a name="references"></a>Referencias

* [Solución de simulador telemático de vehículo de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* 
            [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [SDK de Azure Event Hubs para la ingesta de flujos](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Funcionalidades de movimiento de datos de Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Actividad .NET en Azure Data Factory](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Solución de Visual Studio para la actividad .NET en Azure Data Factory para preparar datos de ejemplo](http://go.microsoft.com/fwlink/?LinkId=717077) 
