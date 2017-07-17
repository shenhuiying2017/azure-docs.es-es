---
title: "Transmisión de datos de Stream Analytics a Data Lake Store| Microsoft Docs"
description: "Uso de Análisis de transmisiones para transmitir datos en el Almacén de Azure Data Lake"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 4ecf4f8594f7a274bec231fb74c4caa22c3cc354
ms.openlocfilehash: b5f2ae124ca3276e15e0d1f75d655ec346bf8ee8
ms.contentlocale: es-es
ms.lasthandoff: 01/06/2017


---
<a id="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics" class="xliff"></a>
# Transmisión de datos del blob de Almacenamiento de Azure al Almacén de Data Lake mediante el Análisis de transmisiones
En este artículo aprenderá a utilizar el Almacén de Azure Data Lake como salida para un trabajo de Análisis de transmisiones de Azure. Este artículo muestra un escenario simple que lee datos desde un blob de Almacenamiento de Azure (entrada) y los escribe en el Almacén de Data Lake (salida).

> [!NOTE]
> En este momento, la creación y la configuración de salidas de Data Lake Store para Análisis de transmisiones solo se admiten en el [Portal de Azure clásico](https://manage.windowsazure.com). Por lo tanto, algunas partes de este tutorial usarán el Portal de Azure clásico.
>
>

<a id="prerequisites" class="xliff"></a>
## Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Cuenta de Almacenamiento de Azure**. Usará un contenedor de blobs desde esta cuenta para introducir datos en un trabajo de Análisis de transmisiones. En este tutorial, se asume que tiene una cuenta de almacenamiento denominada **storageforasa** y un contenedor dentro de la cuenta denominado **storageforasacontainer**. Una vez creado el contenedor, va a cargar un archivo de datos de ejemplo en él. 
  
* **Cuenta del Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md). Supongamos que tiene una cuenta de Data Lake Store llamada **asadatalakestore**. 

<a id="create-a-stream-analytics-job" class="xliff"></a>
## Creación de un trabajo de Análisis de transmisiones
Primero debe crear un trabajo de Análisis de transmisiones que incluya un origen de entrada y un destino de salida. Para este tutorial, el origen es un contenedor de blobs de Azure y el destino es el Almacén de Data Lake.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. En el panel izquierdo, haga clic en **Trabajos de Stream Analytics** y luego haga clic en **Agregar**.

    ![Creación de un trabajo de Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Creación de un trabajo de Stream Analytics")

    > [!NOTE]
    > Asegúrese de crear el trabajo en la misma región que la cuenta de almacenamiento o incurrirá en costos adicionales derivados de mover datos entre regiones.
    >

<a id="create-a-blob-input-for-the-job" class="xliff"></a>
## Creación de una entrada de blob para el trabajo

1. Abra la página del trabajo de Stream Analytics y en el panel izquierdo haga clic en la pestaña **Inputs** (Entradas) y luego haga clic en **Agregar**.

    ![Adición de una entrada al trabajo](./media/data-lake-store-stream-analytics/create.input.1.png "Adición de una entrada al trabajo")

2. En la hoja **Nueva entrada**, proporcione los valores siguientes.

    ![Adición de una entrada al trabajo](./media/data-lake-store-stream-analytics/create.input.2.png "Adición de una entrada al trabajo")

    * En **Alias de entrada**, escriba un nombre único para la entrada del trabajo.
    * En **Source type** (Tipo de origen), seleccione **Flujo de datos**.
    * En **Origen**, seleccione **Blob storage**.
    * En **Suscripción**, seleccione **Usar almacenamiento de blobs de la suscripción actual**.
    * En **Cuenta de almacenamiento**, seleccione la cuenta de almacenamiento que creó como parte de los requisitos previos. 
    * En **Contenedor**, seleccione el contenedor que creó en la cuenta de almacenamiento seleccionada.
    * En **Formato de serialización de eventos**, seleccione **CSV**.
    * En **Delimitador**, seleccione **tabulación**.
    * En **Codificación**, seleccione **UTF-8**.

    Haga clic en **Crear**. El portal ahora agrega la entrada y prueba la conexión a ella.


<a id="create-a-data-lake-store-output-for-the-job" class="xliff"></a>
## Creación de una salida del Almacén de Data Lake para el trabajo

1. Haga clic en la página del trabajo de Stream Analytics, haga clic en la pestaña **Outputs** (Salidas) y después haga clic en **Agregar**.

    ![Adición de una salida al trabajo](./media/data-lake-store-stream-analytics/create.output.1.png "Adición de una salida al trabajo")

2. En la hoja **Nueva salida**, proporcione los siguientes valores.

    ![Adición de una salida al trabajo](./media/data-lake-store-stream-analytics/create.output.2.png "Adición de una salida al trabajo")

    * En **Alias de salida**, escriba un nombre único para la salida del trabajo. Se trata de un nombre descriptivo utilizado en las consultas para dirigir la salida de la consulta a este Almacén de Data Lake.
    * En **Receptor**, seleccione **Data Lake Store**.
    * Se le pedirá que autorice el acceso a la cuenta de Data Lake Store. Haga clic en **Autorizar**.

3. En la hoja **Nueva salida**, proporcione los siguientes valores.

    ![Adición de una salida al trabajo](./media/data-lake-store-stream-analytics/create.output.3.png "Adición de una salida al trabajo")

    * En **Account name** (Nombre de cuenta), seleccione la cuenta de Data Lake Store que ya ha creado como ubicación a la que quiere que se envíe la salida del trabajo.
    * En **Patrón de prefijo de la ruta**, escriba una ruta de archivo usada para escribir los archivos en la cuenta de Data Lake Store especificada.
    * En **Date format** (Formato de fecha), si usó un token de fecha en la ruta del prefijo, puede seleccionar el formato de fecha en el que se organizan sus archivos.
    * En **Time format** (Formato de hora), si usó un token de hora en la ruta del prefijo, especifique el formato de hora en el que se organizan sus archivos.
    * En **Formato de serialización de eventos**, seleccione **CSV**.
    * En **Delimitador**, seleccione **tabulación**.
    * En **Codificación**, seleccione **UTF-8**.
    
    Haga clic en **Crear**. El portal ahora agrega la salida y prueba la conexión a ella.
    
<a id="run-the-stream-analytics-job" class="xliff"></a>
## Ejecución del trabajo de Análisis de transmisiones

1. Para ejecutar un trabajo de Stream Analytics, debe ejecutar una consulta desde la pestaña **Consulta**. En este tutorial, puede ejecutar la consulta de ejemplo mediante el reemplazo de los marcadores de posición con los alias de entrada y salida del trabajo, tal como se muestra en la captura de pantalla siguiente.

    ![Ejecución de una consulta](./media/data-lake-store-stream-analytics/run.query.png "Ejecución de una consulta")

2. Haga clic en **Guardar** en la parte superior de la pantalla y, a continuación, en **Overview** (Introducción), haga clic en **Iniciar**. En el cuadro de diálogo, seleccione **Hora personalizada** y, después, seleccione la fecha y la hora actuales.

    ![Establecimiento de la hora del trabajo](./media/data-lake-store-stream-analytics/run.query.2.png "Establecimiento de la hora del trabajo")

    Haga clic en **Iniciar** para iniciar el trabajo. Puede tardar unos minutos en iniciarse el trabajo.

3. Para desencadenar el trabajo que selecciona los datos del blob, copie un archivo de datos de ejemplo en el contenedor de blobs. Puede obtener un archivo de datos de ejemplo en el [repositorio Git de Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). En este tutorial, vamos a copiar el archivo **vehicle1_09142014.csv**. Puede utilizar varios clientes, como el [explorador de Almacenamiento de Azure](http://storageexplorer.com/), para cargar datos en un contenedor de blobs.

4. En la pestaña **Overview** (Introducción), en **Monitoring** (Supervisión), consulte cómo se han procesado los datos.

    ![Supervisión de trabajos](./media/data-lake-store-stream-analytics/run.query.3.png "Supervisión de trabajos")

5. Finalmente, puede comprobar que los datos de salida del trabajo están disponibles en la cuenta de Data Lake Store. 

    ![Comprobación de la salida](./media/data-lake-store-stream-analytics/run.query.4.png "Comprobación de la salida")

    En el panel del Explorador de datos, tenga en cuenta que la salida se escribe en una ruta de carpeta, según lo especificado en la configuración de salida de Data Lake Store (`streamanalytics/job/output/{date}/{time}`).  

<a id="see-also" class="xliff"></a>
## Consulte también
* [Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure](data-lake-store-hdinsight-hadoop-use-portal.md)

