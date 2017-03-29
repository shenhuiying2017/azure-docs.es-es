---
title: "Integración de Azure Stream Analytics y Machine Learning | Microsoft Docs"
description: "Cómo utilizar una función definida por el usuario y Aprendizaje automático en un trabajo de Análisis de transmisiones"
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: cfced01f-ccaa-4bc6-81e2-c03d1470a7a2
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d1ffb9aba0eb1e17d1efd913f536f6f3997fccbb
ms.openlocfilehash: 7b635b1810536f5b3eb1371d687e9c355e604e41
ms.lasthandoff: 02/14/2017

---

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Análisis de opiniones mediante Análisis de transmisiones de Azure y Aprendizaje automático de Azure
Este artículo está pensado para ayudarle a configurar rápidamente un trabajo sencillo de Análisis de transmisiones de Azure con la integración de Aprendizaje automático de Azure. Usaremos un modelo de Aprendizaje automático de análisis de opinión de la galería de Cortana Intelligence para analizar datos de texto de transmisión y determinar la puntuación de opinión en tiempo real. La información de este artículo puede ayudarle a entender los escenarios como el análisis de opinión en tiempo real sobre la transmisión de datos en Twitter, analizar los registros de chats del cliente con el personal de soporte técnico y evaluar los comentarios en foros, blogs y vídeos, además de muchos otros escenarios de puntuación predictiva.

En este artículo se ofrece un archivo CSV de ejemplo con texto como entrada en Almacenamiento de blobs de Azure, que se muestra en la siguiente imagen. El trabajo aplicará el modelo de análisis de opinión como una función definida por el usuario (UDF) en los datos de texto de ejemplo desde el almacén de blobs. El resultado final se colocará en el mismo almacén de blobs en otro archivo CSV. 

![Aprendizaje automático de Análisis de transmisiones](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

La imagen siguiente muestra esta configuración. Para un escenario más realista, puede sustituir el almacenamiento de blobs por datos de Twitter de transmisión desde una entrada de Centros de eventos de Azure. Además, puede crear una virtualización en tiempo real de [Microsoft Power BI](https://powerbi.microsoft.com/) de la opinión agregada.    

![Aprendizaje automático de Análisis de transmisiones](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Requisitos previos
Los requisitos previos para completar las tareas que se muestran en este artículo son:

* Una suscripción de Azure activa.
* Un archivo CSV con algunos datos. Puede descargar el archivo que se muestra en la figura 1 de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv), o bien puede crear su propio archivo. En este artículo, se supone que usa el proporcionado para su descarga en GitHub.

En un nivel alto, para completar las tareas demostradas en este artículo, realizará las siguientes tareas:

1. Carga del archivo de entrada CSV en Almacenamiento de blobs de Azure.
2. Adición de un modelo de análisis de opinión de la galería de Cortana Intelligence al área de trabajo de Aprendizaje automático de Azure.
3. Implementación de este modelo como un servicio web dentro del área de trabajo de Aprendizaje automático.
4. Creación de un trabajo de Análisis de transmisiones que llama a este servicio web como una función para determinar la opinión sobre la entrada de texto.
5. Inicio del trabajo de Análisis de transmisiones y observación de la salida.

## <a name="create-a-storage-blob-and-upload-the-csv-input-file"></a>Creación de un blob de almacenamiento y carga del archivo de entrada CSV
En este paso, puede usar cualquier archivo CSV, como el que se ha especificado como disponible para su descarga en GitHub. Cargar el archivo CSV es simple, porque se trata de una opción que se incluye en la creación de un blob de almacenamiento.

Para crear una cuenta de almacenamiento nueva en este tutorial, haga clic en **Nuevo**, busque "cuenta de almacenamiento" y, luego, seleccione el icono que aparece para la cuenta de almacenamiento y proporcione los detalles para crear la cuenta. Escriba un **nombre** (en el ejemplo, azuresamldemosa), cree un **grupo de recursos** o use uno existente y especifique una **ubicación** (aquí es importante que todos los recursos creados en esta demostración usen la misma ubicación, si es posible).

![creación de cuenta de almacenamiento](./media/stream-analytics-machine-learning-integration-tutorial/create-sa.png)

Una vez que termine, puede hacer clic en Blob service y crear un contenedor de blobs.

![creación de contenedor de blobs](./media/stream-analytics-machine-learning-integration-tutorial/create-sa2.png)

A continuación, proporcione un **nombre** para el contenedor (en el ejemplo, azuresamldemoblob) y compruebe que el **tipo de acceso** esté definido en "blob".

![creación del tipo de acceso al blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa3.png)

Ahora podemos rellenar el blob con los datos. Seleccione **Archivos** y, luego, seleccione el archivo de la unidad local que descargó de GitHub. Seleccioné Blob en bloques y 4 MB como tamaño. Esto debería bastar para la demostración. Luego, seleccione **Cargar** y el portal creará un blob con el texto de ejemplo para usted.

![creación de archivo de carga de blob](./media/stream-analytics-machine-learning-integration-tutorial/create-sa4.png)

Ahora que los datos de ejemplo están en un blob, es momento de habilitar el modelo de análisis de sentimiento en la galería de Cortana Intelligence.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Adición del modelo de análisis de opinión desde la galería de Cortana Intelligence
1. Descargue el [modelo de análisis de opinión predictivo](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) desde la galería de Cortana Intelligence.  
2. En Machine Learning Studio, seleccione **Abrir en Studio**.  
   
   ![Aprendizaje automático de Análisis de transmisiones, abrir Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Inicie sesión para dirigirse al área de trabajo. Seleccione la ubicación que mejor se adapte a la suya.
4. Haga clic en **Ejecutar** en la parte inferior de la página.  
5. Una vez que el proceso se ejecute correctamente, seleccione **Implementar servicio web**.
6. El modelo de análisis de opinión ya está listo para su uso. Para realizar la validación, seleccione el botón **Probar** y proporcione la entrada de texto, como "Me encanta Microsoft". La prueba debe devolver un resultado similar al siguiente:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Aprendizaje automático de Análisis de transmisiones, datos de análisis](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

En la columna **Aplicaciones**, seleccione el vínculo para **libro Excel 2010 o anterior** para obtener la clave de API y la dirección URL que necesitará después para configurar el trabajo de Stream Analytics. (Este paso solo es necesario para usar un modelo de Aprendizaje automático desde otro área de trabajo de cuenta de Azure. Este artículo asume que este es el caso para solucionar el escenario).  

Tome nota de la clave de acceso y de la dirección URL de servicio web desde el archivo Excel descargado, tal como se muestra a continuación:  

![Aprendizaje automático de Análisis de transmisiones, vista rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Creación de un trabajo de Análisis de transmisiones que usa el modelo de Aprendizaje automático
1. Vaya al [Portal de Azure](https://portal.azure.com).  
2. Haga clic en **Nuevo** > **Inteligencia y análisis** > **Stream Analytics**. Escriba un nombre para el trabajo en **Nombre del trabajo**, especifique un grupo de recursos existente o cree uno nuevo, según corresponda, y escriba la ubicación correcta del trabajo en el campo **Ubicación**.    
   
   ![creación de trabajo](./media/stream-analytics-machine-learning-integration-tutorial/create-job-1.png)
   
3. Una vez creado el trabajo, en la pestaña **Entradas**, seleccione **Agregar una entrada**.  
   
   ![Aprendizaje automático de Análisis de transmisiones, agregar entrada de Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input.png)  

4. Seleccione **Agregar** y, luego, especifique un **alias de entrada**, seleccione **Flujo de datos**, **Blob Storage** como la entrada y, luego, seleccione **Siguiente**.  
5. En la página del asistente **Configuración del almacenamiento de blobs** del asistente, proporcione el nombre del contenedor de blobs de la cuenta de almacenamiento definida anteriormente cuando se cargaron los datos. Haga clic en **Siguiente**. En **Formato de serialización de eventos**, seleccione **CSV**. Acepte los valores predeterminados para el resto de la página **Configuración de serialización** . Haga clic en **Aceptar**.  
   
   ![incorporación de contenedor de blobs de entrada](./media/stream-analytics-machine-learning-integration-tutorial/create-job-add-input-blob.png)

6. En la pestaña **Salidas**, seleccione **Agregar una salida**.  
   
   ![Aprendizaje automático de Análisis de transmisiones, agregar salida](./media/stream-analytics-machine-learning-integration-tutorial/create-output.png)  

7. Haga clic en **Almacenamiento de blobs**, y, a continuación, especifique los mismos parámetros, excepto para el contenedor. El valor para **Entrada** se configuró para leerse desde el contenedor denominado “test”, donde se cargó el archivo **CSV**. Para **Salida**, especifique “testoutput”.
8. Valide que la **configuración de serialización** de la salida esté establecido en **CSV** y, luego, seleccione el botón **Aceptar**.
   
   ![Aprendizaje automático de Análisis de transmisiones, agregar salida](./media/stream-analytics-machine-learning-integration-tutorial/create-output2.png) 

9. En la pestaña **Funciones**, seleccione **Agregar una función de Machine Learning**.  
   
   ![Aprendizaje automático de Análisis de transmisiones, agregar función de Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/add-function.png)  

10. En la página **Configuración de servicio web Machine Learning** , busque el área de trabajo de Aprendizaje automático, el servicio web y el punto de conexión predeterminado. Para este artículo, aplique la configuración manualmente para familiarizarse con la configuración de un servicio web para cualquier área de trabajo, siempre y cuando conozca la dirección URL y tenga la clave de API. Especifique la dirección **URL** y la **clave de API** del punto de conexión. Haga clic en **Aceptar**. Tenga en cuenta que el **alias de función** es "sentimiento".  
    
    ![Aprendizaje automático de Análisis de transmisiones, servicio web Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/add-function-endpoints.png)    

11. En la pestaña **Consulta** , modifique la consulta de la siguiente forma:    
    
    ```
    WITH sentiment AS (  
      SELECT text, sentiment(text) as result from datainput  
    )  
    
    Select text, result.[Scored Labels]  
    Into testoutput  
    From sentiment  
    ```    

12. Haga clic en **Guardar** para guardar la consulta.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Inicio del trabajo de Análisis de transmisiones y observación de la salida
1. Haga clic en **Iniciar** en la parte superior de la página del trabajo.
2. En el **cuadro de diálogo Iniciar consulta**, seleccione **Hora personalizada** y, luego, seleccione un día antes de cuando cargó el archivo CSV en Blob Storage. Haga clic en **Aceptar**.  
3. Vaya a Almacenamiento de blobs mediante la herramienta que se utiliza para cargar el archivo CSV, por ejemplo, Visual Studio.
4. Pocos minutos después de iniciado el trabajo, se crea el contenedor de salida y se carga en él un archivo CSV.  
5. Abra el archivo en el editor de CSV predeterminado. Debe mostrarse algo similar a lo siguiente:  
   
   ![Aprendizaje automático de Análisis de transmisiones, vista CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusión
En este artículo se muestra cómo crear un trabajo de Análisis de transmisiones que lea los datos de texto de transmisión y aplica el análisis de opinión a los datos en tiempo real. Puede hacerlo todo sin tener que preocuparse por las complejidades de la creación de un modelo de análisis de opinión. Esta es una de las ventajas de Cortana Intelligence Suite.

También puede observar las métricas relacionadas con la función de Aprendizaje automático de Azure. Para hacerlo, seleccione la pestaña **Supervisar**. Se muestran las métricas relacionadas con tres funciones.  

* **Solicitudes de función** indica el número de solicitudes enviadas al servicio web Machine Learning.  
* **Eventos de la función** indica el número de eventos de la solicitud. De forma predeterminada, cada solicitud de un servicio web Machine Learning contiene hasta 1000 eventos.  
  
    ![Aprendizaje automático de Análisis de transmisiones, vista de supervisión de Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/job-monitor.png)  


