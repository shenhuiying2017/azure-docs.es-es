<properties
	pageTitle="Análisis de opiniones mediante Análisis de transmisiones de Azure y Aprendizaje automático de Azure | Microsoft Azure"
	description="Cómo utilizar una función definida por el usuario y Aprendizaje automático en un trabajo de Análisis de transmisiones"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>


<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="07/27/2016" 
	ms.author="jeffstok"
/>

# Análisis de opiniones mediante Análisis de transmisiones de Azure y Aprendizaje automático de Azure #

Este artículo está pensado para ayudarle a configurar rápidamente un trabajo sencillo de Análisis de transmisiones de Azure con la integración de Aprendizaje automático de Azure. Usaremos un modelo de Aprendizaje automático de análisis de opinión de la galería de Cortana Intelligence para analizar datos de texto de transmisión y determinar la puntuación de opinión en tiempo real. La información de este artículo puede ayudarle a entender los escenarios como el análisis de opinión en tiempo real sobre la transmisión de datos en Twitter, analizar los registros de chats del cliente con el personal de soporte técnico y evaluar los comentarios en foros, blogs y vídeos, además de muchos otros escenarios de puntuación predictiva.

En este artículo se ofrece un archivo CSV de ejemplo con texto como entrada en Almacenamiento de blobs de Azure, que se muestra en la siguiente imagen. El trabajo aplicará el modelo de análisis de opinión como una función definida por el usuario (UDF) en los datos de texto de ejemplo desde el almacén de blobs. El resultado final se colocará en el mismo almacén de blobs en otro archivo CSV.

![Aprendizaje automático de Análisis de transmisiones](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

La imagen siguiente muestra esta configuración. Para un escenario más realista, puede sustituir el almacenamiento de blobs por datos de Twitter de transmisión desde una entrada de Centros de eventos de Azure. Además, puede crear una virtualización en tiempo real de [Microsoft Power BI](https://powerbi.microsoft.com/) de la opinión agregada.

![Aprendizaje automático de Análisis de transmisiones](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

## Requisitos previos

Los requisitos previos para completar las tareas que se muestran en este artículo son:

-	Una suscripción de Azure activa.
-	Un archivo CSV con algunos datos. Puede descargar el archivo que se muestra en la figura 1 de [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv), o bien puede crear su propio archivo. En este artículo, se supone que usa el proporcionado para su descarga en GitHub.

En un nivel alto, para completar las tareas demostradas en este artículo, realizará las siguientes tareas:

1.	Carga del archivo de entrada CSV en Almacenamiento de blobs de Azure.
2.	Adición de un modelo de análisis de opinión de la galería de Cortana Intelligence al área de trabajo de Aprendizaje automático de Azure.
3.	Implementación de este modelo como un servicio web dentro del área de trabajo de Aprendizaje automático.
4.	Creación de un trabajo de Análisis de transmisiones que llama a este servicio web como una función para determinar la opinión sobre la entrada de texto.
5.	Inicio del trabajo de Análisis de transmisiones y observación de la salida.

## Carga del archivo de entrada CSV en Almacenamiento de blobs

En este paso, puede usar cualquier archivo CSV, como el que se ha especificado como disponible para su descarga en GitHub. Puede usar [Explorador de almacenamiento de Windows Azure](http://storageexplorer.com/) o Visual Studio para cargar el archivo o usar el código personalizado. Usamos ejemplos basados en Visual Studio.

1.	En Visual Studio, haga clic en **Azure** > **Almacenamiento** > **Adjuntar almacenamiento externo**. Especifique un **Nombre de cuenta** y una **Clave de cuenta**.

    ![Aprendizaje automático de Análisis de transmisiones, Explorador de servidores](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Expanda el almacenamiento que adjuntó en el paso 1, haga clic en **Crear contenedor de blobs** y, a continuación, especifique un nombre lógico. Después de crear el contenedor, ábralo para ver su contenido. (Se vaciará en este momento).

    ![Aprendizaje automático de Análisis de transmisiones, crear blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Para cargar el archivo CSV, haga clic en **Cargar blob** y, a continuación, haga clic en el **archivo desde el disco local**.

## Adición del modelo de análisis de opinión desde la galería de Cortana Intelligence

1.	Descargue el [modelo de análisis de opinión predictivo](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) desde la galería de Cortana Intelligence.
2.	En Estudio de aprendizaje automático de Microsoft Azure, haga clic en **Abrir en Studio**.

    ![Aprendizaje automático de Análisis de transmisiones, abrir Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Inicie sesión para dirigirse al área de trabajo. Seleccione la ubicación que mejor se adapte a la suya.
4.	Haga clic en **Ejecutar** en la parte inferior de la página.
5.	Una vez que el proceso se ejecute correctamente, haga clic en **Implementar servicio web**.
6.	El modelo de análisis de opinión ya está listo para su uso. Para validar, haga clic en el botón **Probar** y proporcione la entrada de texto, como "Me encanta Microsoft". La prueba debe devolver un resultado similar al siguiente:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Aprendizaje automático de Análisis de transmisiones, datos de análisis](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

En la columna **Aplicaciones**, haga clic en el vínculo para **libro Excel 2010 o anterior** para obtener la clave de API y la dirección URL que necesitará después para configurar el trabajo de Análisis de transmisiones. (Este paso solo es necesario para usar un modelo de Aprendizaje automático desde otro área de trabajo de cuenta de Azure. Este artículo asume que este es el caso para solucionar el escenario).

Tome nota de la clave de acceso y de la dirección URL de servicio web desde el archivo Excel descargado, tal como se muestra a continuación:

![Aprendizaje automático de Análisis de transmisiones, vista rápida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Creación de un trabajo de Análisis de transmisiones que usa el modelo de Aprendizaje automático

1.	Vaya al [Portal de Azure](https://manage.windowsazure.com).
2.	Haga clic en **Nuevo** > ** Servicios de datos** > **Análisis de transmisiones** > **Creación rápida**. Escriba un nombre para el trabajo en **Nombre del trabajo**, escriba la región adecuada para el trabajo en **Región**, y, a continuación, seleccione la cuenta en **Cuenta de almacenamiento de supervisión regional**.
3.	Una vez creado el trabajo, en la pestaña **Entradas**, haga clic en **Agregar una entrada**.

    ![Aprendizaje automático de Análisis de transmisiones, agregar entrada de Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	En la primera página del asistente **Agregar entrada**, haga clic en **Flujo de datos** y, a continuación, haga clic en **Siguiente**. En la siguiente página, haga clic en **Almacenamiento de blobs** como entrada y, a continuación, haga clic en **Siguiente**.
5.	En la página del asistente **Configuración del almacenamiento de blobs** del asistente, proporcione el nombre del contenedor de blobs de la cuenta de almacenamiento definida anteriormente cuando se cargaron los datos. Haga clic en **Siguiente**. Para **Formato de serialización de eventos**, haga clic en **CSV**. Acepte los valores predeterminados para el resto de la página **Configuración de serialización**. Haga clic en **Aceptar**.
6.	En la pestaña **Salidas**, haga clic en **Agregar una salida**.

    ![Aprendizaje automático de Análisis de transmisiones, agregar salida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Haga clic en **Almacenamiento de blobs**, y, a continuación, especifique los mismos parámetros, excepto para el contenedor. El valor **Entrada** se configuró para leerse desde el contenedor denominado "test", donde se cargó el archivo **CSV**. Para **Salida**, especifique “testoutput”. Los nombres de contenedor deben ser diferentes. Compruebe la existencia de este contenedor.
8.	Haga clic en **Siguiente** para definir la **Configuración de serialización** de la salida. Al igual que en la **entrada**, haga clic en **CSV** y, a continuación, haga clic en el botón **Aceptar**.
9.	En la pestaña **Funciones**, haga clic en **Agregar una función de aprendizaje automático**.

    ![Aprendizaje automático de Análisis de transmisiones, agregar función de Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	En la página **Configuración de servicio web Machine Learning**, busque el área de trabajo de Aprendizaje automático, el servicio web y el punto de conexión predeterminado. Para este artículo, aplique la configuración manualmente para familiarizarse con la configuración de un servicio web para cualquier área de trabajo, siempre y cuando conozca la dirección URL y tenga la clave de API. Especifique la dirección **URL** y la **clave de API** del punto de conexión. Haga clic en **OK**.

    ![Aprendizaje automático de Análisis de transmisiones, servicio web Machine Learning](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	En la pestaña **Consulta**, modifique la consulta de la siguiente forma:

 ```
 	WITH subquery AS (  
 		SELECT text, sentiment(text) as result from input  
  	)  
 
 	Select text, result.[Score]  
 	Into output  
 	From subquery  
 ```    
12.	Haga clic en **Guardar** para guardar la consulta.

## Inicio del trabajo de Análisis de transmisiones y observación de la salida

1.	Haga clic en **Iniciar** en la parte inferior de la página del trabajo.
2.	En el **cuadro de diálogo Iniciar consulta**, haga clic en **Hora personalizada** y, a continuación, seleccione una hora antes de cuando se cargó el archivo CSV en Almacenamiento de blobs. Haga clic en **Aceptar**.

    ![Aprendizaje automático de Análisis de transmisiones, hora personalizada](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Vaya a Almacenamiento de blobs mediante la herramienta que se utiliza para cargar el archivo CSV, por ejemplo, Visual Studio.
4.	Pocos minutos después de iniciado el trabajo, se crea el contenedor de salida y se carga en él un archivo CSV.
5.	Abra el archivo en el editor de CSV predeterminado. Debe mostrarse algo similar a lo siguiente:

    ![Aprendizaje automático de Análisis de transmisiones, vista CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Conclusión

En este artículo se muestra cómo crear un trabajo de Análisis de transmisiones que lea los datos de texto de transmisión y aplica el análisis de opinión a los datos en tiempo real. Puede hacerlo todo sin tener que preocuparse por las complejidades de la creación de un modelo de análisis de opinión. Esta es una de las ventajas de Cortana Intelligence Suite.

También puede observar las métricas relacionadas con la función de Aprendizaje automático de Azure. Para ello, haga clic en la pestaña **Supervisar**. Se muestran las métricas relacionadas con tres funciones.

- **Solicitudes de función** indica el número de solicitudes enviadas al servicio web Machine Learning.
- **Eventos de la función** indica el número de eventos de la solicitud. De forma predeterminada, cada solicitud de un servicio web Machine Learning contiene hasta 1000 eventos.
- **Solicitudes de función con error** indica el número de solicitudes con error en el servicio web Machine Learning.

    ![Aprendizaje automático de Análisis de transmisiones, vista de supervisión de Aprendizaje automático](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0803_2016-->