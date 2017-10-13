---
title: "Integración de Azure Stream Analytics y Machine Learning | Microsoft Docs"
description: "Cómo utilizar una función definida por el usuario y Aprendizaje automático en un trabajo de Análisis de transmisiones"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 07/06/2017
ms.author: samacha
ms.openlocfilehash: 243ee799d2cddb1baf5b8046eee6eaf182463d2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Análisis de opiniones mediante Azure Stream Analytics y Azure Machine Learning
En este artículo se explica cómo configurar rápidamente un trabajo sencillo de Azure Stream Analytics que integre Azure Machine Learning. Un modelo de análisis de opiniones de Machine Learning de la galería de Cortana Intelligence se usa para analizar datos de texto que se están transmitiendo y determinar la puntuación de opiniones en tiempo real. Cortana Intelligence Suite permite realizar esta tarea sin preocuparse por las complejidades de la creación de un modelo de análisis de opiniones.

Puede aplicar lo que aprenda en este artículo a escenarios como estos:

* Análisis de opiniones en tiempo real en datos de Twitter que se están transmitiendo.
* Análisis de registros de chats de clientes con el personal de soporte técnico.
* Evaluación de comentarios en foros, blogs y vídeos. 
* Muchos otros escenarios de puntuación predictiva en tiempo real.

En un escenario real, los datos se obtendrían directamente de un flujo de datos de Twitter. Para simplificar el tutorial, se ha escrito de modo que el trabajo de Streaming Analytics obtenga los tweets de un archivo CSV de Azure Blob Storage. Puede crear su propio archivo CSV o usar un archivo CSV de ejemplo, como se muestra en la siguiente imagen:

![tweets de ejemplo de un archivo CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

El trabajo de Streaming Analytics que cree aplicará el modelo de análisis de opiniones como una función definida por el usuario (UDF) a los datos de texto de ejemplo del almacén de blobs. La salida (el resultado del análisis de opiniones) se escribe en el mismo almacén de blobs en otro archivo CSV. 

La imagen siguiente muestra esta configuración. Como se ha indicado, para un escenario más realista, puede sustituir el almacenamiento de blobs por datos de Twitter que se estén transmitiendo desde una entrada de Azure Event Hubs. Además, puede crear una virtualización en tiempo real de [Microsoft Power BI](https://powerbi.microsoft.com/) de la opinión agregada.    

![Información general sobre la integración de Stream Analytics Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, asegúrese de que dispone de lo siguiente:

* Una suscripción de Azure activa.
* Un archivo CSV con algunos datos. Puede descargar el archivo mostrado anteriormente desde [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) o bien puede crear su propio archivo. En este artículo se da por hecho que está usando el archivo de GitHub.

En general, para realizar las tareas explicadas en este artículo, hará lo siguiente:

1. Crear una cuenta de Azure Storage y un contenedor de almacenamiento de blobs y cargar un archivo de entrada con formato CSV en el contenedor.
3. Agregar un modelo de análisis de opiniones de la galería de Cortana Intelligence al área de trabajo de Azure Machine Learning e implementar este modelo como servicio web en el área de trabajo.
5. Crear un trabajo de Stream Analytics que llame a este servicio web como una función para determinar la opinión sobre la entrada de texto.
6. Iniciar el trabajo de Stream Analytics y comprobar el resultado.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Creación de un contenedor de almacenamiento y carga del archivo de entrada CSV
Para este paso puede usar cualquier archivo CSV, por ejemplo alguno de los disponibles en GitHub.

1. En Azure Portal, haga clic en **Nuevo** &gt; **Almacenamiento** &gt; **Cuenta de almacenamiento**.

   ![creación de una nueva cuenta de almacenamiento](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-create-storage-account.png)

2. Proporcione un nombre (`samldemo` en el ejemplo). El nombre solo puede incluir letras minúsculas y números y debe ser único en Azure. 

3. Especifique un grupo de recursos existente y una ubicación. Con respecto a la ubicación, se recomienda que todos los recursos creados en este tutorial usen la misma.

    ![especificación de los detalles de la cuenta de almacenamiento](./media/stream-analytics-machine-learning-integration-tutorial/create-sa1.png)

4. En Azure Portal, seleccione la cuenta de almacenamiento. En la hoja de la cuenta de almacenamiento, haga clic en **Contenedores** y luego haga clic en **+&nbsp;Contenedor** para crear el almacenamiento de blobs.

    ![creación de contenedor de blobs](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

5. Proporcione un nombre para el contenedor (`azuresamldemoblob` en el ejemplo) y compruebe que **Tipo de acceso** está establecido en **Blob**. Cuando haya terminado, haga clic en **Aceptar**.

    ![especificación de los detalles del contenedor de blobs](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

6. En la hoja **Contenedores**, seleccione el nuevo contenedor, con lo que se abre la hoja de ese contenedor.

7. Haga clic en **Cargar**.

    ![botón "Cargar" de un contenedor](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. En la hoja **Cargar blob**, especifique el archivo CSV que quiere usar para este tutorial. En **Tipo de blob**, seleccione **Blob en bloques** y establezca el tamaño de bloque en 4 MB, que es suficiente para este tutorial.

    ![carga del archivo de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

9. Haga clic en el botón **Cargar** en la parte inferior de la hoja.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adición del modelo de análisis de opinión desde la galería de Cortana Intelligence

Ahora que los datos de ejemplo están en un blob, puede habilitar el modelo de análisis de opiniones de la galería de Cortana Intelligence.

1. Vaya a la página de [modelo predictivo de análisis de opiniones](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) de la galería de Cortana Intelligence.  

2. Haga clic en **Abrir en Studio**.  
   
   ![Aprendizaje automático de Análisis de transmisiones, abrir Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Inicie sesión para dirigirse al área de trabajo. Seleccione una ubicación.

4. Haga clic en **Ejecutar** en la parte inferior de la página. El proceso se ejecuta, lo que lleva aproximadamente un minuto.

   ![ejecución del experimento en Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Una vez que el proceso se ha ejecutado correctamente, seleccione **Implementar servicio web** en la parte inferior de la página.

   ![implementación del experimento como un servicio web en Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Para comprobar que el modelo de análisis de opiniones está listo para su uso, haga clic en el botón **Probar**. Proporcione algún texto de entrada, como "Me encanta Microsoft". 

   ![prueba del experimento en Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Si la prueba funciona, verá un resultado similar al ejemplo siguiente:

   ![resultados de la prueba en Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. En la columna **Aplicaciones**, haga clic en el vínculo de **libro de Excel 2010 o anterior** para descargar un libro de Excel. El libro contiene una clave de API y la dirección URL que se necesitan más adelante para configurar el trabajo de Stream Analytics.

    ![Aprendizaje automático de Análisis de transmisiones, vista rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Creación de un trabajo de Análisis de transmisiones que usa el modelo de Aprendizaje automático

Ahora puede crear un trabajo de Stream Analytics que lea los tweets de ejemplo del archivo CSV de almacenamiento de blobs. 

### <a name="create-the-job"></a>Creación del trabajo

1. Vaya al [Portal de Azure](https://portal.azure.com).  

2. Haga clic en **Nuevo** > **Internet de las cosas** > **Trabajo de Stream Analytics**. 

   ![ruta de acceso del portal de Azure para acceder a un nuevo trabajo de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/azure-portal-new-iot-sa-job.png)
   
3. Ponga el nombre `azure-sa-ml-demo` al trabajo, especifique una suscripción, especifique un grupo de recursos existente o cree uno nuevo y seleccione la ubicación del trabajo.

   ![especificación de la configuración del nuevo trabajo de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   

### <a name="configure-the-job-input"></a>Configuración de la entrada de trabajo
El trabajo obtiene su entrada del archivo CSV cargado anteriormente en el almacenamiento de blobs.

1. Una vez creado el trabajo, en la opción **Topología de trabajo** de la hoja del trabajo, haga clic en el cuadro **Entradas**.  
   
   ![cuadro "Entradas" de la hoja del trabajo de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

2. En la hoja **Entradas**, haga clic en **+ Agregar**.

   ![botón "Agregar" para agregar una entrada al trabajo de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-button.png)  

3. Rellene la hoja **Nueva entrada** con estos valores:

    * **Alias de entrada**: use el nombre `datainput`.
    * **Tipo de origen**: seleccione **Flujo de datos**.
    * **Origen**: seleccione **Almacenamiento de blobs**.
    * **Opción de importación**: seleccione **Usar almacenamiento de blobs de la suscripción actual**. 
    * **Cuenta de almacenamiento**. Seleccione la cuenta de almacenamiento creada anteriormente.
    * **Contenedor**. Seleccione el contenedor creado anteriormente (`azuresamldemoblob`).
    * **Formato de serialización de eventos**. Seleccione **CSV**.

    ![Configuración de la nueva entrada de trabajo](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

4. Haga clic en **Crear**.

### <a name="configure-the-job-output"></a>Configuración de la salida del trabajo
El trabajo envía los resultados al mismo almacenamiento de blobs del que obtiene la entrada. 

1. En la opción **Topología de trabajo** de la hoja del trabajo, haga clic en el cuadro **Salidas**.  
  
   ![Creación de una nueva salida para el trabajo de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

2. En la hoja **Salidas**, haga clic en **+ Agregar** y luego agregue una salida con el alias `datamloutput`. 

3. En **Receptor**, seleccione **Almacenamiento de blobs**. Luego rellene las demás opciones de salida con los mismos valores que usó para el almacenamiento de blobs de la entrada:

    * **Cuenta de almacenamiento**. Seleccione la cuenta de almacenamiento creada anteriormente.
    * **Contenedor**. Seleccione el contenedor creado anteriormente (`azuresamldemoblob`).
    * **Formato de serialización de eventos**. Seleccione **CSV**.

   ![Configuración de la nueva salida de trabajo](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

4. Haga clic en **Crear**.   


### <a name="add-the-machine-learning-function"></a>Incorporación de la función de Machine Learning 
Arriba ha publicado un modelo de Machine Learning en un servicio web. En este escenario, cuando se ejecuta el trabajo de Stream Analysis, envía cada tweet de ejemplo de la entrada al servicio web para el análisis de opiniones. El servicio web de Machine Learning devuelve una opinión (`positive`, `neutral` o `negative`) y una probabilidad de que el tweet sea positivo. 

En esta sección del tutorial se define una función en el trabajo de Stream Analysis. Se puede invocar a la función para enviar un tweet al servicio web y obtener la respuesta. 

1. Asegúrese de que tiene la dirección URL y la clave de API del servicio web que ha descargado anteriormente en el libro de Excel.

2. Vuelva a la hoja de información general del trabajo.

3. En **Configuración**, seleccione **Funciones** y luego haga clic en **+ Agregar**.

   ![Adición de una función al trabajo de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-function1.png) 

4. Escriba `sentiment` como alias de la función y rellene el resto de la hoja con estos valores:

    * **Tipo de función**: seleccione **Aprendizaje automático de Azure**.
    * **Opción de importación**: seleccione **Importar de una suscripción distinta**. Esto le ofrece la oportunidad de escribir la dirección URL y la clave.
    * **Dirección URL**: pegue la dirección URL del servicio web.
    * **Clave**: pegue la clave de API.
  
    ![Configuración para agregar una función de Machine Learning al trabajo de Stream Analytics](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  
    
5. Haga clic en **Crear**.

### <a name="create-a-query-to-transform-the-data"></a>Creación de una consulta para transformar los datos

Stream Analytics usa una consulta declarativa basada en SQL para examinar la entrada y procesarla. En esta sección se crea una consulta que lee cada tweet de entrada y luego invoca a la función de Machine Learning para llevar a cabo el análisis de opiniones. La consulta entonces envía el resultado a la salida que se ha definido (almacenamiento de blobs).

1. Vuelva a la hoja de información general del trabajo.

2.  En **Topología de trabajo**, haga clic en el cuadro **Consulta**.

    ![Creación de una consulta para el trabajo de Streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/create-query.png)  

3. Escriba la siguiente consulta:

    ```
    WITH sentiment AS (  
    SELECT text, sentiment(text) as result from datainput  
    )  

    Select text, result.[Score]  
    Into datamloutput
    From sentiment  
    ```    

    La consulta invoca a la función creada anteriormente (`sentiment`) para realizar el análisis de opiniones en cada tweet de la entrada. 

4. Haga clic en **Guardar** para guardar la consulta.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Inicio del trabajo de Stream Analytics y consulta de la salida

Ya se puede iniciar el trabajo de Stream Analytics.

### <a name="start-the-job"></a>Inicio del trabajo
1. Vuelva a la hoja de información general del trabajo.

2. Haga clic en **Iniciar** en la parte superior de la hoja.

    ![Creación de una consulta para el trabajo de Streaming Analytics](./media/stream-analytics-machine-learning-integration-tutorial/start-job.png)  

3. En **Iniciar trabajo**, seleccione **Personalizado** y luego seleccione un día antes de la fecha de carga del archivo CSV en el almacenamiento de blobs. Cuando haya terminado, haga clic en **Iniciar**.  


### <a name="check-the-output"></a>Consulta de la salida
1. Deje que el trabajo se ejecute durante unos minutos hasta que vea actividad en el cuadro **Supervisión**. 

2. Si tiene alguna herramienta que suela usar para examinar el contenido del almacenamiento de blobs, úsela para examinar el contenedor `azuresamldemoblob`. También puede realizar los siguientes pasos en Azure Portal:

    1. En el portal, busque la cuenta de almacenamiento `samldemo` y, en ella, busque el contenedor `azuresamldemoblob`. En el contenedor verá dos archivos: el que contiene los tweets de ejemplo y un archivo CSV generado por el trabajo de Stream Analytics.
    2. Haga clic con el botón derecho en el archivo generado y luego seleccione **Descargar**. 

   ![Descarga de la salida del trabajo CSV desde el almacenamiento de blobs](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Abra el archivo CSV generado. Verá algo parecido al siguiente ejemplo:  
   
   ![Aprendizaje automático de Análisis de transmisiones, vista CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Visualización de métricas
También puede observar las métricas relacionadas con la función de Aprendizaje automático de Azure. Las siguientes métricas relacionadas con la función se muestran en el cuadro **Supervisión** de la hoja de trabajo:

* **Solicitudes de función** indica el número de solicitudes enviadas al servicio web Machine Learning.  
* **Eventos de la función** indica el número de eventos de la solicitud. De forma predeterminada, cada solicitud de un servicio web Machine Learning contiene hasta 1000 eventos.  


## <a name="next-steps"></a>Pasos siguientes

* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Integración de la API de REST y Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)



