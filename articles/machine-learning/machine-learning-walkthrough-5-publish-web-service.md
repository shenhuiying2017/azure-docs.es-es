<properties
    pageTitle="Paso 5: Implementación del servicio web Machine Learning | Microsoft Azure"
    description="Paso 5 del tutorial Desarrollo de una solución predictiva: Implementación de un experimento predictivo en Machine Learning Studio como servicio web."
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
    ms.date="10/05/2016"
    ms.author="garye"/>



# <a name="walkthrough-step-5:-deploy-the-azure-machine-learning-web-service"></a>Paso 5 del tutorial: Implementación del servicio web Azure Machine Learning

Este es el quinto paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.  [Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.  [Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  **Implementación del servicio web**
6.  [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para permitir que otros usuarios puedan usar el modelo predictivo desarrollado en este tutorial, se debe implementar como un servicio web en Azure.

Hasta ahora hemos estado experimentando con el entrenamiento de nuestro modelo. Sin embargo, el servicio implementado ya no va a realizar el entrenamiento, ya que genera predicciones mediante la puntuación de la entrada del usuario en función de nuestro modelo. Por lo tanto, hay que realizar unos cuantos preparativos para convertir este experimento de ***entrenamiento*** en un experimento ***predictivo***. 

Se trata de un proceso de dos pasos:  

1. Conversión del *experimento de entrenamiento* que hemos creado en un *experimento predictivo*
2. Implementación del experimento predictivo como servicio web

Pero, en primer lugar, debemos reducir este experimento un poco. Actualmente, hay dos modelos distintos en el experimento, pero solo necesitamos un modelo para implementarlo como un servicio web.  

Pongamos que hayamos decidido que el modelo de árbol ampliado es el mejor en este caso. Por tanto, lo primero que se debe hacer es eliminar el módulo [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases) y los módulos que se usaron para entrenarlo. Puede que desee hacer una copia del experimento antes; para ello, haga clic en **Guardar como** en la parte inferior del lienzo de experimento.

Es necesario eliminar los siguientes módulos:  

- [Two-Class Support Vector Machine][two-class-support-vector-machine] (Máquina de vectores de soporte de dos clases)
- Los módulos [Entrenar modelo][train-model] y [Score Model][score-model] (Modelo de puntuación) conectados a ella
- [Normalize Data][normalize-data] (Normalizar datos) (ambos)
- [Evaluate Model][evaluate-model] (Evaluar modelo)

Seleccione el módulo y presione la tecla Supr o haga clic con el botón derecho en el módulo y seleccione **Eliminar**.

Ahora ya estamos listos para implementar este modelo con el [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliado de dos clases).

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir un experimento de entrenamiento en experimento predictivo

La conversión a un experimento predictivo implica tres pasos:

1. Guardar el modelo que se ha entrenado y sustituir nuestros módulos de aprendizaje
2. Recortar el experimento para quitar los módulos que fueron necesarios solo para el entrenamiento.
3. Definir el lugar en el que el servicio web aceptará la entrada y dónde generará la salida

Afortunadamente, para realizar estos tres pasos, basta con hacer clic en la opción **Set Up Web Service** (Configurar servicio web) de la parte inferior del lienzo del experimento (seleccione la opción **Predictive web Service** [Servicio web predictivo]).

Al hacer clic en **Set Up Web Service** (Configurar servicio web), pasa todo lo siguiente:

- El modelo entrenado se guarda como un único módulo **Trained Model** (Modelo entrenado) en la paleta de módulos que se encuentra a la izquierda del lienzo de experimento (se encuentra en **Trained Models** [Modelos entrenados]).
- Se quitan los módulos que se utilizaron para el entrenamiento. Concretamente:
  - [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Árbol de decisión ampliado de dos clases)
  - [Entrenar modelo][train-model]
  - [Split Data][split] (Dividir datos)
  - el segundo módulo [Execute R Script][execute-r-script] (Ejecutar script R) que se usó para probar los datos
- El modelo entrenado guardado se vuelve a agregar al experimento.
- Se agregan los módulos de **entrada de servicio web** y de **salida de servicio web**.

> [AZURE.NOTE] Se ha guardado el experimento en dos partes en pestañas que se han agregado a la parte superior del lienzo del experimento: el experimento de entrenamiento original está en la pestaña **Experimento de entrenamiento** y, los experimentos predictivos recién creados, en **Experimento predictivo**.

Necesitamos realizar un paso adicional con este experimento concreto.
Hemos agregado dos módulos [Execute R Script][execute-r-script] (Ejecutar script R) para dar una función de ponderación a los datos de entrenamiento y prueba. No necesitamos hacer esto en el modelo final.

Machine Learning Studio quitó un módulo [Execute R Script][execute-r-script] (Ejecutar script R) cuando quitó el módulo [Split Data][split] (Dividir datos). Ahora se puede quitar el otro y conectar [Metadata Editor][metadata-editor] (Editor de metadatos) directamente a [Score Model][score-model] (Modelo de puntuación).    

Nuestro experimento debería tener ahora un aspecto similar al siguiente:  

![Scoring the trained model][4]  

> [AZURE.NOTE] Quizás se pregunte por qué hemos dejado el conjunto de datos de los datos de datos de tarjeta de crédito alemana de UCI en el experimento predictivo. El servicio va a utilizar los datos del usuario, no el conjunto de datos original; entonces, ¿por qué se deja el conjunto de datos original en el modelo?
>
>Es cierto que el servicio no necesita los datos originales de la tarjeta de crédito. Pero sí necesita el esquema para esos datos, que incluye información como la cantidad de columnas que hay y cuáles son numéricas. Esta información del esquema es necesaria para interpretar los datos del usuario. Dejamos estos componentes conectados para que el módulo de puntuación tenga el esquema del conjunto de datos cuando el servicio se esté ejecutando. No se utilizan los datos, sino solamente el esquema.  

Ejecute el experimento por última vez (haga clic en **Ejecutar**). Si desea comprobar que el modelo sigue funcionando, haga clic en la salida del módulo [Score Model][score-model] (Modelo de puntuación) y seleccione **Ver resultados**. Verá que aparecen los datos originales, junto con el valor de riesgo de crédito (las "etiquetas puntuadas") y el valor de probabilidad de la puntuación (las "probabilidades puntuadas"). 

## <a name="deploy-the-web-service"></a>Implementación del servicio web

Puede implementar el experimento como un servicio web clásico o nuevo basado en Azure Resource Manager.

### <a name="deploy-as-a-classic-web-service"></a>Implementación como servicio web clásico ###

Para implementar un servicio web clásico derivado de nuestro experimento, haga clic en **Deploy Web Service** (Implementar servicio web) debajo del lienzo y seleccione **Deploy Web Service [Classic]** (Implementar servicio web [clásico]). Machine Learning Studio implementa el experimento como servicio web y lo dirige al panel de dicho servicio web. Desde aquí, puede volver al experimento (**View snapshot** [Ver instantánea] o **View latest** [Ver más reciente]) y ejecutar una prueba sencilla del servicio web (consulte la sección **Prueba del servicio web** a continuación). Aquí también hay información para crear aplicaciones que puedan acceder al servicio web (más información al respecto en el siguiente paso de este tutorial).

![Panel del servicio web][6]

Puede configurar el servicio haciendo clic en la pestaña **CONFIGURACIÓN** . Aquí puede modificar el nombre del servicio (recibe de manera predeterminada el nombre del experimento) y proporcionarle una descripción. También puede poner etiquetas más descriptivas para las columnas de entrada y salida.  

![Configurar el servicio web][5]  

### <a name="deploy-as-a-new-web-service"></a>Implementación como servicio web nuevo

Para implementar un servicio web nuevo derivado de nuestro experimento, haga clic en **Deploy Web Service** (Implementar servicio web) debajo del lienzo y seleccione **Deploy Web Service [New]** (Implementar servicio web [nuevo]). Machine Learning Studio lo lleva a la página de implementación de experimentos del portal Servicios web Azure Machine Learning.

Escriba un nombre para el servicio web y seleccione un plan de precios. Si ya tiene uno, puede seleccionarlo; si no, debe crear uno nuevo para el servicio. 

1.  En el menú desplegable **Price Plan** (Plan de precios), seleccione un plan existente o elija la opción **Select new plan** (Seleccionar nuevo plan).
2.  En **Nombre del plan**, escriba un nombre que identifique el plan en la factura.
3.  Seleccione uno de los **niveles de planes mensuales**. Los niveles de los planes predeterminados son los de la región predeterminada, y el servicio web se implementa en dicha región.

Haga clic en la página **Implementar** para que se abra la página **Inicio rápido** del servicio web.

Puede configurar el servicio haciendo clic en la pestaña **CONFIGURACIÓN** . Aquí puede modificar el nombre del servicio (recibe de manera predeterminada el nombre del experimento) y proporcionarle una descripción. 

Para probar el servicio web, haga clic en la opción de menú **Probar** (consulte la sección **Prueba del servicio web** a continuación). Para obtener información sobre cómo crear aplicaciones que puedan acceder al servicio web, haga clic en la opción de menú **Consume** (Consumo) (más información al respecto en el siguiente paso de este tutorial).

> [AZURE.TIP] Puede actualizar el servicio web después de haberlo implementado. Por ejemplo, si desea cambiar el modelo, edite el experimento de entrenamiento, ajuste los parámetros del modelo y haga clic en **Deploy Web Service** (Implementar servicio web). Después, seleccione **Deploy Web Service [Classic]** (Implementar servicio web [clásico]) o **Deploy Web Service [New]** (Implementar servicio web [nuevo]). Cuando implementa el experimento de nuevo, este sustituye al servicio web, ahora con el modelo actualizado.  

## <a name="test-the-web-service"></a>Prueba del servicio web

### <a name="test-a-classic-web-service"></a>Prueba de un servicio web clásico

Puede probar el servicio en Machine Learning Studio o en el portal Servicios web Azure Machine Learning. Realizar las pruebas en el portal Servicios web Azure Machine Learning tiene la ventaja de que permite habilitar 

**Pruebas en Machine Learning Studio**

En la página **PANEL**, haga clic en el botón **Probar** en **Default Endpoint** (Punto de conexión predeterminado). Aparecerá un cuadro de diálogo que le pide los datos de entrada del servicio. Se trata de las mismas columnas que aparecieron en el conjunto de datos original de riesgo de crédito alemán.  

Escriba un conjunto de datos y, a continuación, haga clic en **Aceptar**. 

**Pruebas en el portal Servicios web Azure Machine Learning**

En la página **PANEL**, haga clic en el vínculo de versión preliminar **Probar** en **Default Endpoint** (Punto de conexión predeterminado). La página de prueba del portal Servicios web Azure Machine Learning para el punto de conexión del servicio web se abrirá y le pedirá los datos de entrada para el servicio. Se trata de las mismas columnas que aparecieron en el conjunto de datos original de riesgo de crédito alemán.

Haga clic en **Test Request-Response** (Probar solicitud-respuesta). 

En el servicio web, los datos se escriben a través del módulo de **entrada de servicio web**, del módulo [Metadata Editor][metadata-editor] (Editor de metadatos) y del módulo [Score Model][score-model] (Modelo de puntuación) el que se puntúa. Después, los resultados se generan desde el servicio web a través de la **salida de servicio web**.

**Prueba de un servicio web nuevo**

En el portal Servicios web Azure Machine Learning, haga clic en **Probar** en la parte superior de la página. Se abrirá la página **Prueba** y podrá escribir datos para el servicio. Los campos de entrada que se muestran corresponden a las columnas que aparecieron en el conjunto de datos original de riesgo de crédito alemán. 

Escriba un conjunto de datos y, después, haga clic en **Test Request-Response**(Probar solicitud-respuesta).

Los resultados de la prueba se mostrarán en el lado derecho de la página en la columna de salida. 

Al realizar pruebas en el portal Servicios web Azure Machine Learning, puede habilitar datos de ejemplo que puede utilizar para probar el servicio de solicitud-respuesta. Si creó el servicio web en Machine Learning Studio, los datos de ejemplo se toman de los utilizados para entrenar el modelo.

> [AZURE.TIP] La forma en la que hemos configurado el experimento predictivo permitirá que se devuelvan los resultados desde el módulo [Score Model][score-model]. Esto incluye todos los datos de entrada además del valor de riesgo de crédito y la probabilidad de puntuación. Si quiere que se devuelva algo diferente como, por ejemplo, solo el valor de riesgo de crédito, podría insertar un módulo [Project Columns][project-columns] (Columnas del proyecto) entre [Score Model][score-model] (Modelo de puntuación) y la **salida de servicio web** para eliminar las columnas que el servicio web no debe devolver. 

## <a name="manage-the-web-service"></a>Administración del servicio web

**Administración de un servicio web clásico**

Cuando se haya implementado el servicio web clásico, podrá administrarlo desde el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En el panel de servicios de Microsoft Azure, haga clic en **APRENDIZAJE AUTOMÁTICO**.
3. Haga clic en el área de trabajo.
4. Haga clic en la pestaña **Servicios web**.
5. Haga clic en el servicio web creado.
6. Haga clic en el punto de conexión "predeterminado".

Desde aquí, puede hacer tareas como supervisar el funcionamiento del servicio web y realizar ajustes de rendimiento cambiando el volumen de llamadas simultáneas que el servicio puede controlar.
Incluso puede publicar el servicio web en Azure Marketplace.

Para obtener información, consulte:

- [Creación de extremos](machine-learning-create-endpoint.md)
- [Escalado de servicios web](machine-learning-scaling-webservice.md)
- [Publicación de los servicios web Azure Machine Learning en Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Administración de un servicio web en el portal Servicios web Azure Machine Learning**

Cuando se haya implementado el servicio web, ya sea clásico o nuevo, podrá administrarlo desde el [portal Servicios web Azure Machine Learning](https://servics.azureml.net).

Para supervisar el rendimiento del servicio web, siga estos pasos:

1. Inicie sesión en el [portal Servicios web Azure Machine Learning](https://servics.azureml.net).
2. Haga clic en **Servicios web**.
3. Haga clic en el servicio web.
4. Haga clic en **Panel**.

----------

**Siguiente: [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[project-columns]: https://msdn.microsoft.com/en-us/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/


<!--HONumber=Oct16_HO2-->


