<properties 
	pageTitle="Publicación de un servicio web de Aprendizaje automático | Microsoft Azure" 
	description="Cómo convertir un experimento de entrenamiento en un experimento de puntuación, preparar para la publicación y, a continuación, publicarlo como un servicio web de Aprendizaje automático de Azure." 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="garye"/>

#Publicar un servicio web de Aprendizaje automático de Azure

Aprendizaje automático de Azure permite compilar, probar e implementar soluciones de análisis predictivo.

Desde una perspectiva general, esto se realiza en tres pasos:

- **[Crear un experimento de formación]**: el Estudio de aprendizaje automático de Azure es un entorno de desarrollo visual de colaboración que se utiliza para entrenar y probar un modelo de análisis predictivo con los datos de entrenamiento que proporcione.
- **[Convertirlo a un experimento de puntuación]**: una vez que el modelo se ha entrenado con datos existentes y está listo para usarlo para puntuar nuevos datos, debe preparar y simplificar el experimento para la puntuación.
- **[Publicarlo como un servicio web]**: con un solo clic, puede publicar el experimento de puntuación como un servicio web de Azure. Los usuarios pueden enviar datos al modelo y recibir las predicciones de su modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Una vez que haya publicado el servicio web, puede:

- **[obtener acceso]** a él través de la API del servicio web;
- **[administrarlo]** mediante el Portal de administración de Azure; y
- **[actualizarlo]** si cambia el modelo.

[Crear un experimento de formación]: #create-a-training-experiment
[Convertirlo a un experimento de puntuación]: #convert-the-training-experiment-to-a-scoring-experiment
[Publicarlo como un servicio web]: #publish-the-scoring-experiment-as-a-web-service
[obtener acceso]: #access-the-web-service
[administrarlo]: #manage-the-web-service-in-the-azure-management-portal
[actualizarlo]: #update-the-web-service


##Crear un experimento de entrenamiento

Para entrenar un modelo de análisis predictivo, utilice el Estudio de aprendizaje automático de Azure para crear un experimento de entrenamiento en el que incluya varios módulos para cargar los datos de entrenamiento, prepare los datos según sea necesario, aplique los algoritmos de aprendizaje automático y evalúe los resultados. Puede iterar en un experimento y probar algoritmos distintos de aprendizaje automático para comparar y evaluar los resultados.

El proceso de creación y administración de experimentos de entrenamiento se trata más detenidamente en otros artículos; vea los siguientes artículos para obtener más información y ejemplos:

- [Creación de un experimento sencillo en el Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md)
- [Desarrollo de una solución predictiva con Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importar datos de entrenamiento en Estudio de aprendizaje automático de Azure](machine-learning-import-data.md)
- [Administrar iteraciones de experimentos en Estudio de aprendizaje automático de Azure](machine-learning-manage-experiment-iterations.md)

##Convertir un experimento de entrenamiento en experimento de puntuación

Una vez entrenado el modelo, está listo para usarlo para puntuar nuevos datos. Para ello, convierta el experimento de entrenamiento en un experimento de puntuación. Al efectuar la conversión a un experimento de puntuación, estará preparando el modelo formado para publicarlo como servicio web de puntuación. Los usuarios del servicio web enviarán datos de entrada a su modelo y el modelo devolverá los resultados de predicción. Por lo tanto, cuando efectúe la conversión a un experimento de puntuación, deberá tener en cuenta cómo espera que utilicen el modelo los demás.

![Convertir en experimento de puntuación](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Para obtener más detalles sobre cómo realizar esta conversión, consulte [Convertir un experimento de entrenamiento de aprendizaje automático en un experimento de puntuación](machine-learning-convert-training-experiment-to-scoring-experiment.md).


##Publicar el experimento de puntuación como un servicio web

Ahora que ha preparado el experimento de puntuación suficientemente, puede publicarlo como un servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar datos a su modelo y el modelo devolverá las predicciones.

Para publicar su experimento de puntuación, haga clic en **Ejecutar** en la parte inferior del lienzo del experimento, y luego haga clic en **PUBLICAR SERVICIO WEB**. El servicio web está configurado y se colocará en el panel del servicio web.

![Publicación del servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Para probar el servicio web, haga clic en el vínculo **Probar** de la pestaña **PANEL**. Aparecerá un cuadro de diálogo que le pide los datos de entrada del servicio. Estas son las columnas esperadas del experimento puntuación. Escriba un conjunto de datos y haga clic en **Aceptar**. Los resultados generados por el servicio web se muestran en la parte inferior del panel.

![Prueba del servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

En la pestaña **CONFIGURACIÓN** puede cambiar el nombre para mostrar del servicio y darle una descripción. El nombre y la descripción se muestran en el Portal de administración de Azure, donde se administran los servicios web. También puede habilitar el registro para diagnosticar los errores que verá al seleccionar el servicio web. Para más información, vea [Habilitar el registro para los servicios web de Aprendizaje automático](machine-learning-web-services-logging.md).

![Configurar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

También puede proporcionar una descripción para la entrada de datos, los datos de salida y los parámetros de servicio web al escribir una cadena para cada columna en **ESQUEMA DE ENTRADA**, **ESQUEMA DE SALIDA** y **PARÁMETRO DEL SERVICIO WEB**. Estas descripciones se utilizan en la documentación de código de ejemplo proporcionada para el servicio web.

##Acceso al servicio web

Una vez publicado el servicio web desde el Estudio de aprendizaje automático, puede enviar datos al servicio y recibir respuestas mediante programación.

El panel proporciona toda la información que necesita para tener acceso a su servicio web. Por ejemplo, la clave de API se proporciona para permitir el acceso autorizado al servicio, y las páginas de ayuda de API sirven para ayudarle a empezar a escribir el código.

Para más información sobre el acceso a un servicio web de Aprendizaje automático, vea [Cómo consumir un servicio web de Aprendizaje automático de Azure publicado](machine-learning-consume-web-services.md).


##Administrar el servicio web en el Portal de administración de Azure

En el Portal de administración de Azure, puede administrar los servicios web haciendo clic en el servicio **Aprendizaje automático**, abriendo el área de trabajo de Aprendizaje automático y, a continuación, abriendo el servicio web desde la pestaña **SERVICIOS WEB**. Desde esta página, puede supervisar el servicio web, actualizarlo y eliminarlo. También puede agregar un segundo extremo para el servicio web además del extremo predeterminado que se crea cuando se publica.

Para más información, vea [Administrar un área de trabajo de Aprendizaje automático de Azure](machine-learning-manage-workspace.md).
<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**. 
-->


##Actualizar el servicio web

Puede realizar cambios en el servicio web, como actualizar el modelo con datos de entrenamiento adicionales y volver a publicarlo, sobrescribiendo el servicio web original.

Para actualizar el servicio web, abra el experimento de puntuación original que usó para publicar el servicio web original y haga una copia modificable haciendo clic en **GUARDAR COMO**. Guarde los cambios y haga clic en **PUBLICAR SERVICIO WEB**. Puesto que ya ha publicado este experimento antes, el Estudio de aprendizaje automático le preguntará si desea sobrescribir el servicio existente. Si hace clic en **Sí**, el servicio web existente se detendrá y el nuevo experimento de puntuación se publicará en su lugar.

> [AZURE.NOTE]Si ha realizado cambios de configuración en el servicio web original, como, por ejemplo, escribir un nuevo nombre para mostrar o una descripción, necesitará escribir esos valores de nuevo.

Una opción para actualizar el servicio web es volver a entrenar el modelo mediante programación. Para obtener más información, consulte [Volver a entrenar modelos de aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md).

<!---HONumber=August15_HO6-->