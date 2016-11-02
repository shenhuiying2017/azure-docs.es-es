<properties
    pageTitle="Implementación de un servicio web Machine Learning | Microsoft Azure"
    description="Cómo convertir un experimento de entrenamiento en un experimento predictivo, prepararlo para la implementación y luego implementarlo como servicio web Azure Machine Learning."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="garye"/>


# <a name="deploy-an-azure-machine-learning-web-service"></a>Implementación de un servicio web Azure Machine Learning

Azure Machine Learning permite compilar, probar e implementar soluciones de análisis predictivo.

Desde una perspectiva general, esto se realiza en tres pasos:

- **[Crear un experimento de entrenamiento]** : el Estudio de aprendizaje automático de Azure es un entorno de desarrollo visual de colaboración que se utiliza para entrenar y probar un modelo de análisis predictivo con los datos de entrenamiento que proporcione.
- **[Convertirlo en un experimento predictivo]**: una vez que se ha entrenado el modelo con datos existentes y está listo para usarse con el objetivo de puntuar nuevos datos, debe prepararlo y simplificarlo para realizar predicciones.
- **Implementarlo como un servicio web**: puede implementar el experimento predictivo como un servicio web de Azure [nuevo] o [clásico]. Los usuarios pueden enviar datos al modelo y recibir las predicciones de su modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Crear un experimento de entrenamiento

Para entrenar un modelo de análisis predictivo, utilice el Estudio de aprendizaje automático de Azure para crear un experimento de entrenamiento en el que incluya varios módulos para cargar los datos de entrenamiento, prepare los datos según sea necesario, aplique los algoritmos de aprendizaje automático y evalúe los resultados. Puede iterar en un experimento y probar algoritmos distintos de aprendizaje automático para comparar y evaluar los resultados.

El proceso de creación y administración de experimentos de entrenamiento se trata más detenidamente en otros artículos. Para obtener más información, consulte estos artículos:

- [Creación de un experimento sencillo en el Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md)
- [Desarrollo de una solución predictiva con Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importar datos de entrenamiento en Estudio de aprendizaje automático de Azure](machine-learning-data-science-import-data.md)
- [Administrar iteraciones de experimentos en Estudio de aprendizaje automático de Azure](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir un experimento de entrenamiento en experimento predictivo

Una vez que haya entrenado el modelo, estará listo para convertir el experimento de entrenamiento en un experimento predictivo para puntuar datos nuevos.

Al efectuar la conversión a un experimento predictivo, estará preparando el modelo entrenado para implementarlo como servicio web de puntuación. Los usuarios del servicio web pueden enviar datos de entrada a su modelo y este les devolverá los resultados de predicción. Cuando efectúe la conversión a un experimento predictivo, tenga en cuenta la forma en que prevé que los demás usen el modelo.

Para convertir su experimento de entrenamiento en un experimento predictivo, haga clic en **Ejecutar** en la parte inferior del lienzo de experimento, haga clic en **Set Up Web Service** (Configurar servicio web) y luego en **Predictive Web Service** (Servicio web predictivo).

![Convertir en experimento de puntuación](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Para obtener más información sobre cómo realizar esta conversión, consulte [Conversión de un experimento de entrenamiento de Machine Learning en un experimento predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md).

A continuación, se describe el proceso de implementación de un experimento predictivo como servicio web nuevo. También puede implementarlo como servicio web clásico.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Implementación del experimento predictivo como servicio web nuevo

Una vez preparado el experimento predictivo, puede implementarlo como servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar datos a su modelo, y este devolverá las predicciones.

Para implementar un experimento predictivo, haga clic en la opción **Ejecutar** de la parte inferior del lienzo del experimento. Cuando el experimento haya terminado de ejecutarse, haga clic en **Deploy Web Service** (Implementar servicio web) y seleccione **Deploy Web Service [[New]]** (Implementar servicio web [nuevo]).  Se abrirá la página de implementación del portal Servicios web Machine Learning. 

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Página de implementación de experimentos del portal Servicios web Machine Learning

En la página de implementación de experimentos, escriba un nombre para el servicio web.
Seleccione un plan de tarifa. Si ya tiene uno, puede seleccionarlo; si no, debe crear uno nuevo para el servicio. 

1.  En el menú desplegable **Price Plan** (Plan de precios), seleccione un plan existente o elija la opción **Select new plan** (Seleccionar nuevo plan).
2.  En **Nombre del plan**, escriba un nombre que identifique el plan en la factura.
3.  Seleccione uno de los **niveles de planes mensuales**. Los niveles de los planes predeterminados son los de la región predeterminada, y el servicio web se implementa en dicha región.

Haga clic en la página **Implementar** para que se abra la página **Inicio rápido** del servicio web.

A través de la página Inicio rápido del servicio web podrá acceder a las tareas más comunes que se realizarán después de crear un servicio web nuevo, así como instrucciones sobre cómo hacerlo. A partir de ahí, puede acceder fácilmente a las páginas Prueba y Consume (Consumo).

<!-- ![Deploy the Web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Prueba del servicio web

Para probar el nuevo servicio web, haga clic en la opción **Test web service** (Probar servicio web) de las tareas comunes. En la página Prueba puede probar el servicio web como servicio de solicitud-respuesta (RRS, Request-Response Service) o de ejecución por lotes (BES, Batch Execution Service). 

En la página de pruebas RRS se muestran las entradas, las salidas y los parámetros globales definidos para el experimento. Para probar el servicio web, puede escribir manualmente los valores adecuados de las entradas o bien introducir un archivo con formato de valores separados por comas (CSV) que contenga los valores de prueba. 

Para realizar pruebas RRS, en el modo de vista de lista, escriba los valores adecuados de las entradas y haga clic en **Test Request-Response**(Probar solicitud-respuesta). Los resultados de predicción se mostrarán en la columna de salida de la izquierda.

![Implementación del servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Para realizar pruebas BES, haga clic en **Lote**. En la página de pruebas por lotes, haga clic en la opción Examinar de la entrada y seleccione un archivo CSV que contenga los valores de ejemplo adecuados. Si no dispone de un archivo CSV y ha creado el experimento predictivo con Machine Learning Studio, puede descargar el conjunto de datos del experimento predictivo y utilizarlo.

Para ello, abra Estudio de aprendizaje automático de Microsoft Azure. Abra el experimento predictivo y haga clic con el botón derecho en la entrada del experimento. En el menú contextual, seleccione **conjunto de datos** y, después, haga clic en **Descargar**. 

![Implementación del servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Haga clic en **Probar**. El estado del trabajo de ejecución por lotes se muestra a la derecha de **Test Batch Jobs** (Probar trabajos por lotes).

![Implementación del servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the Web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

En la página **CONFIGURACIÓN**, puede cambiar la descripción y el título, actualizar la clave de la cuenta de almacenamiento y habilitar los datos de ejemplo para el servicio web.

![Configurar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Una vez que haya implementado el servicio web, puede hacer lo siguiente:

- **Acceder** a él través de la API del servicio web
- **Administrarlo** a través del portal Servicios web Azure Machine Learning o el Portal de Azure clásico 
- **Actualizarlo** si cambia el modelo

### <a name="access-the-web-service"></a>Acceso al servicio web

Una vez implementado el servicio web desde Machine Learning Studio, puede enviar datos al servicio y recibir respuestas mediante programación.

La página **Consume** (Consumo) ofrece toda la información que necesita para acceder al servicio web. Por ejemplo, la clave de API se ofrece para permitir el acceso autorizado al servicio. 

Para obtener más información sobre el acceso a un servicio web Machine Learning, consulte [Consumo de un servicio web Azure Machine Learning implementado](machine-learning-consume-Web-services.md).

### <a name="manage-your-new-web-service"></a>Administración de servicios web nuevos

Puede administrar los servicios web clásicos mediante el portal Servicios web Machine Learning. En la [página principal del portal](https://services.azureml-test.net/), haga clic en **Servicios web**. En la página de servicios web, puede eliminar o copiar servicios. Para supervisar un servicio concreto, haga clic en el servicio y, después, haga clic en **Panel**. Para supervisar los trabajos por lotes asociados con el servicio web, haga clic en **Batch Request Log** (Registro de solicitudes por lotes).

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Implementación del experimento predictivo como servicio web clásico

Ahora que ha preparado el experimento predictivo suficientemente, puede implementarlo como servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar datos a su modelo, y este devolverá las predicciones.

Para implementar el experimento predictivo, haga clic en **Ejecutar** en la parte inferior del lienzo del experimento y luego haga clic en **Deploy Web Service** (Implementar servicio web). El servicio web está configurado y se colocará en el panel del servicio web.

![Implementación del servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Para probar el servicio web, haga clic en el vínculo **Probar** del panel del servicio web. Aparecerá un cuadro de diálogo que le pide los datos de entrada del servicio. Estas son las columnas esperadas del experimento puntuación. Escriba un conjunto de datos y, a continuación, haga clic en **Aceptar**. Los resultados generados por el servicio web se muestran en la parte inferior del panel.

Puede hacer clic en el vínculo de versión preliminar **Probar** para probar el servicio en el portal Servicios web Azure Machine Learning del modo que se mostró anteriormente en la sección Servicio web nuevo.

![Prueba del servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

En la página **CONFIGURACIÓN** puede cambiar el nombre para mostrar del servicio y darle una descripción. El nombre y la descripción se muestran en el [Portal de Azure clásico](http://manage.windowsazure.com/), donde se administran los servicios web.

Puede dar una descripción para los datos de entrada, los de salida y los parámetros del servicio web escribiendo una cadena para cada columna en **INPUT SCHEMA** (ESQUEMA DE ENTRADA), **OUTPUT SCHEMA** (ESQUEMA DE SALIDA) y **WEB SERVICE PARAMETER** (PARÁMETRO DE SERVICIO WEB). Estas descripciones se utilizan en la documentación de código de ejemplo dada para el servicio web.

Puede habilitar el registro para diagnosticar cualquier error que vea al acceder al servicio web. Para obtener más información, vea [Habilitación del registro para los servicios web Machine Learning](machine-learning-web-services-logging.md).

![Configurar el servicio web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

También puede configurar los puntos de conexión del servicio web en el portal Servicios web Azure Machine Learning de una forma parecida al procedimiento mostrado anteriormente en la sección Servicio web nuevo. Las opciones son diferentes: puede agregar o cambiar la descripción del servicio, habilitar el registro y habilitar datos de ejemplo para las pruebas.

### <a name="access-the-web-service"></a>Acceso al servicio web

Una vez implementado el servicio web desde Machine Learning Studio, puede enviar datos al servicio y recibir respuestas mediante programación.

El panel ofrece toda la información que necesita para acceder a su servicio web. Por ejemplo, la clave de API se proporciona para permitir el acceso autorizado al servicio, y las páginas de ayuda de API sirven para ayudarle a empezar a escribir el código.

Para obtener más información sobre el acceso a un servicio web Machine Learning, consulte [Consumo de un servicio web Azure Machine Learning implementado](machine-learning-consume-Web-services.md).

### <a name="manage-the-web-service"></a>Administración del servicio web

Hay varias acciones que puede realizar para supervisar un servicio web. Puede actualizarlo y eliminarlo. También puede agregar puntos de conexión adicionales para un servicio web clásico aparte del punto de conexión predeterminado que se crea cuando se implementa.

Para obtener más información, consulte [Administración de un área de trabajo de Azure Machine Learning](machine-learning-manage-workspace.md) y [Administración de un servicio web mediante el portal Servicios web Azure Machine Learning](machine-learning-manage-new-webservice.md).
  
<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Web service endpoints using the REST API, see **Azure machine learning Web service endpoints**.
-->

## <a name="update-the-web-service"></a>Actualización del servicio web

Puede realizar cambios en el servicio web, como actualizar el modelo con datos de entrenamiento adicionales y volver a implementarlo, lo que sobrescribirá el servicio web original.

Para actualizar el servicio web, abra el experimento predictivo original que usó para implementarlo y haga una copia modificable haciendo clic en **GUARDAR COMO**. Realice los cambios y haga clic en **Publicar servicio web**. 

Como ya ha implementado este experimento antes, se le preguntará si quiere sobrescribir (servicio web clásico) o actualizar (servicio web nuevo) el servicio existente. Al hacer clic en **SÍ** o **Actualizar**, el servicio web existente se detendrá y, en su lugar, se implementará el nuevo experimento predictivo.

> [AZURE.NOTE] Si ha realizado cambios de configuración en el servicio web original como, por ejemplo, escribir un nuevo nombre para mostrar o una descripción, deberá escribir esos valores de nuevo.

Una opción para actualizar el servicio web es volver a entrenar el modelo mediante programación. Para obtener más información, consulte [Volver a entrenar modelos de aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md).


<!-- internal links -->
[Crear un experimento de entrenamiento]: #create-a-training-experiment
[Convertirlo en un experimento predictivo]: #convert-the-training-experiment-to-a-predictive-experiment
[nuevo]: #deploy-the-predictive-experiment-as-a-new-Web-service
[clásico]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Acceso]: #access-the-Web-service
[Administración]: #manage-the-Web-service-in-the-azure-management-portal
[Actualización]: #update-the-Web-service



<!--HONumber=Oct16_HO2-->


