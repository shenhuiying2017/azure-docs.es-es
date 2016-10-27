<properties
    pageTitle="Introducción a Azure Stream Analytics para el procesamiento de datos desde dispositivos de IoT. | Microsoft Azure"
    description="Flujos de datos y SensorTags de IoT con análisis de transmisiones y procesamiento de datos en tiempo real"
    keywords="solución de IoT, introducción a IoT"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introducción a Análisis de transmisiones de Azure para el procesamiento de datos desde dispositivos de IoT

En este tutorial, aprenderá a crear una lógica de procesamiento de transmisiones para recopilar datos desde dispositivos de Internet de las cosas (IoT). Usaremos un caso de uso real de Internet de las cosas para mostrar cómo puede crear una solución de forma rápida y económica.

## <a name="prerequisites"></a>Requisitos previos

-   [Suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Archivos de datos y consultas de ejemplo que se pueden descargar desde [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Escenario

Contoso es una empresa del sector de la automatización industrial que ha automatizado completamente su proceso de fabricación. La maquinaria de esta planta cuenta con sensores capaces de emitir flujos de datos en tiempo real. En este escenario, un administrador del piso de producción desea tener información en tiempo real de los datos provenientes de los sensores para buscar patrones y llevar a cabo las acciones que sean necesarias. Usaremos el lenguaje de consulta de Stream Analytics (SAQL) sobre los datos de los sensores para encontrar patrones interesantes en los flujos de datos entrantes.

Estos datos provienen de un dispositivo SensorTag de Texas Instruments.

![SensorTag de Texas Instruments](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

La carga de los datos está en formato JSON y tiene un aspecto similar al siguiente:


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

En un escenario real, podría haber cientos de estos sensores generando eventos en forma de secuencia. Lo ideal sería que hubiera un dispositivo de puerta de enlace que ejecutara código para insertar estos eventos en [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) o en [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Un trabajo de Análisis de transmisiones ingeriría estos eventos desde Centros de eventos y ejecutar consultas de análisis en tiempo real en las secuencias. Después, los resultados se podrían enviar a una de las [salidas admitidas](stream-analytics-define-outputs.md).

Para facilitar su uso, esta guía de introducción proporciona un archivo con datos de ejemplo que se capturan desde dispositivos de SensorTag reales. Puede ejecutar consultas en los datos de ejemplo y ver los resultados. En tutoriales subsiguientes, aprenderá a conectar el trabajo a las entradas y salidas y a implementarlas en el servicio de Azure.

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Análisis de transmisiones

1. En [Azure Portal](http://manage.windowsazure.com), haga clic en **STREAM ANALYTICS** y haga clic en **NUEVO** en la esquina inferior izquierda de la página para crear un nuevo trabajo de análisis.

    ![Crear un nuevo trabajo de Análisis de transmisiones](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Haga clic en **CREACIÓN RÁPIDA**.

3. En **CUENTA DE ALMACENAMIENTO DE SUPERVISION REGIONAL**, haga clic en **CREAR UNA NUEVA CUENTA DE ALMACENAMIENTO** y asígnele un nombre único. Análisis de transmisiones de Azure usará esta cuenta para almacenar la información de supervisión de todos los futuros trabajos.

    > [AZURE.NOTE] Debe crear esta cuenta de almacenamiento solo una vez por región. Este almacenamiento se compartirá entre todos los trabajos de Stream Analytics que se creen en esa región.

4. Haga clic en **CREAR EL TRABAJO DE ANÁLISIS DE TRANSMISIONES** en la parte inferior de la página.

    ![Configuración de la cuenta de almacenamiento](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## <a name="azure-stream-analytics-query"></a>Consulta de Análisis de transmisiones de Azure

Haga clic en la pestaña **CONSULTA** para ir al Editor de consultas. La pestaña **CONSULTA** contiene una consulta T-SQL que realiza la transformación de los datos de eventos entrantes.

## <a name="archive-your-raw-data"></a>Archivado de los datos sin procesar

La forma más sencilla de una consulta es una consulta de paso a través que archiva todos los datos de entrada en la salida designada.

![Consulta de trabajo de archivo](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Ahora, descargue el archivo de datos de ejemplo de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) en una ubicación de su equipo. Copie y pegue la consulta del archivo PassThrough.txt. Haga clic en el botón **Test** (Probar) que aparece abajo y, a continuación, seleccione el archivo de datos denominado HelloWorldASA-InputStream.json en la ubicación de la descarga.

![Botón Test (Probar) de Análisis de transmisiones](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![Flujo de entrada de prueba](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

Los resultados de la consulta se pueden ver en el explorador, tal como se muestra en la siguiente captura de pantalla.

![Resultados de prueba](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## <a name="filter-the-data-based-on-a-condition"></a>Filtro de los datos en función de una condición

Intentemos filtrar los resultados según una condición. Nos gustaría mostrar los resultados solo para los eventos que provienen desde el "SensorA". La consulta se encuentra en el archivo Filtering.txt.

![Filtrado de un flujo de datos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Tenga en cuenta que la consulta que distingue mayúsculas de minúsculas compara un valor de cadena. Haga clic en el botón **Volver a ejecutar** para ejecutar la consulta. La consulta devolverá 389 filas de 1860 eventos.

![Segundos resultados de salida de la prueba de consulta](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## <a name="alert-to-trigger-a-business-workflow"></a>Alertas para desencadenar un flujo de trabajo de negocio

Aumentemos el grado de detalle de la consulta. En cada tipo de sensor, deseamos supervisar la temperatura media por ventana de 30 segundos y mostrar los resultados solo si la temperatura media supera los 100 grados. Escribiremos la consulta siguiente y, luego, haremos clic en **Volver a ejecutar** para ver los resultados. La consulta se encuentra en el archivo ThresholdAlerting.txt.

![Consulta de filtro de 30 segundos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Ahora debe ver los resultados que contienen solo las 245 filas y nombres de sensores en los que la temperatura promedio supera los 100 grados. Esta consulta agrupa el flujo de eventos por **dspl**, que es el nombre del sensor y con respecto a una **ventana de saltos de tamaño constante** de 30 segundos. Las consultas temporales deben indicar cómo deseamos que transcurra el tiempo. Mediante la cláusula **TIMESTAMP BY**, hemos especificado la columna **OUTPUTTIME** para asociar los tiempos con todos los cálculos temporales. Para obtener información detallada, lea los artículos de MSDN sobre las funciones de [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Ventana](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![Temperatura superior a 100 grados](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## <a name="detect-absence-of-events"></a>Detección de la ausencia de eventos

¿Cómo podemos escribir una consulta que busque una falta de eventos de entrada? Busquemos la última vez que un sensor envió datos y luego no envió ningún evento en el minuto posterior. La consulta se encuentra en el archivo AbsenseOfEvent.txt.

![Detección de la ausencia de eventos](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Aquí vamos a usar una combinación **LEFT OUTER** en el mismo flujo de datos (autocombinación). Para una combinación **INNER**, solo se devuelve un resultado cuando se encuentra una coincidencia.  En el caso de una combinación **LEFT OUTER**, si un evento procedente del lado izquierdo de la combinación no tiene coincidencia, se devolverá una fila con el valor NULL en todas las columnas de la derecha. Esta técnica resulta muy útil para buscar la ausencia de eventos. Para más información acerca de [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx), consulte la documentación de MSDN.

![Resultados de combinación](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## <a name="conclusion"></a>Conclusión

El objetivo de este tutorial es mostrar cómo escribir diferentes consultas en el lenguaje de consulta de Stream Analytics y ver los resultados en el explorador. Sin embargo, se trata solo de una introducción. Es mucho más lo que puede hacer con Stream Analytics. Stream Analytics admite una gran variedad de entradas y salidas, e incluso puede usar las funciones de Azure Machine Learning, lo que hace que sea una herramienta sólida para el análisis de flujos de datos. Puede empezar a explorar más sobre Stream Analytics mediante nuestro [mapa de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Para más información acerca de cómo escribir consultas, lea el artículo sobre [patrones comunes de consulta](./stream-analytics-stream-analytics-query-patterns.md).



<!--HONumber=Oct16_HO2-->


