<properties
	pageTitle="Paso 5: Implementar el servicio web de Aprendizaje automático | Microsoft Azure"
	description="Paso 5 del tutorial Desarrollo de una solución predictiva: implementación de un experimento predictivo en Estudio de aprendizaje automático como servicio web."
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
	ms.date="09/16/2016"
	ms.author="garye"/>


# Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure

Este es el quinto paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Implementación del servicio web**
6.	[Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para permitir que otros usuarios puedan usar el modelo de predicción que hemos desarrollado en este tutorial, se implementará como un servicio web en Azure.

Hasta ahora hemos estado experimentando con el entrenamiento de nuestro modelo. Sin embargo, el servicio implementado ya no va a realizar el entrenamiento: generará predicciones mediante la puntuación de la entrada del usuario en función de nuestro modelo. Así que vamos a hacer una preparación para convertir este experimento de un experimento de ***entrenamiento*** a un experimento ***predictivo***.

Se trata de un proceso de dos pasos:

1. Convertir el *experimento de entrenamiento* que hemos creado en un *experimento predictivo*
2. Implementar el experimento predictivo como servicio web

Pero, en primer lugar, debemos reducir este experimento un poco. Actualmente tenemos dos modelos distintos en el experimento, pero solo necesitamos un modelo para implementarlo como un servicio web.

Pongamos que hayamos decidido que el modelo de árbol ampliado es el mejor en este caso. Por tanto, lo primero que se debe hacer es eliminar el módulo [Máquina de vectores de soporte de dos clases][two-class-support-vector-machine] y los módulos que se usaron para entrenar. Puede que desee hacer una copia del experimento antes; para ello, haga clic en **Guardar como** en la parte inferior del lienzo de experimento.

Es necesario eliminar los siguientes módulos:

- [Máquina de vectores de soporte de dos clases][two-class-support-vector-machine]
- Los módulos [Entrenar modelo][train-model] y [Puntuar modelo][score-model] conectados a ella.
- [Normalizar datos][normalize-data] \(ambos)
- [Evaluar modelo][evaluate-model]

Simplemente seleccione el módulo y presione la tecla Supr o haga clic con el botón derecho en el módulo y seleccione **Eliminar**.

Ahora ya estamos listos para implementar este modelo con el [Árbol de decisión ampliado de dos clases][two-class-boosted-decision-tree].

## Convertir un experimento de entrenamiento en experimento predictivo

La conversión a un experimento predictivo implica tres pasos:

1. Guardar el modelo que se ha entrenado y sustituir nuestros módulos de aprendizaje
2. Recortar el experimento para quitar los módulos que fueron necesarios solo para el entrenamiento.
3. Definir dónde el servicio web aceptará la entrada y dónde generará la salida

Afortunadamente, para realizar los tres pasos simplemente haga clic en la opción **Set Up Web Service** (Configurar servicio web) de la parte inferior del lienzo del experimento (seleccione la opción **Predictive Web Service** [Servicio web predictivo]).

Al hacer clic en **Set Up Web Service** (Configurar servicio web), pasa lo siguiente:

- El modelo que entrenamos se guarda como un módulo **Modelo entrenado** único en la paleta de módulos a la izquierda del lienzo de experimento (puede encontrarlo en **Modelos entrenados**).
- Se quitan los módulos que se utilizaron para el entrenamiento. Concretamente:
  - [Árbol de decisión ampliado de dos clases][two-class-boosted-decision-tree]
  - [Entrenar modelo][train-model]
  - [Dividir datos][split]
  - el segundo módulo [Ejecutar script R][execute-r-script] que se usó para probar los datos.
- El modelo entrenado guardado se vuelve a agregar al experimento.
- Se agregan los módulos **Entrada de servicio web** y **Salida de servicio web**.

> [AZURE.NOTE] Se ha guardado el experimento en dos partes en pestañas que se han agregado en la parte superior del lienzo del experimento: el experimento de entrenamiento original está en la pestaña **Experimento de entrenamiento**, y los experimentos predictivos recién creados se encuentran en **Experimento predictivo**.

Necesitamos realizar un paso adicional con este experimento concreto. Hemos agregado dos módulos [Ejecutar script R][execute-r-script] para proporcionar una función de ponderación a los datos de entrenamiento y prueba. No necesitamos hacer esto en el modelo final. Estudio de aprendizaje automático quitó un módulo [Ejecutar script R][execute-r-script] al quitar el módulo [Dividir][split], de modo que ahora podemos quitar el otro y conectar [Editor de metadatos][metadata-editor] directamente a [Puntuar modelo][score-model].

Nuestro experimento debería tener ahora un aspecto similar al siguiente:

![Scoring the trained model][4]


> [AZURE.NOTE] Quizás se pregunte por qué hemos dejado el conjunto de datos de los datos de datos de tarjeta de crédito alemana de UCI en el experimento predictivo. El servicio va a utilizar los datos del usuario, no el conjunto de datos original; entonces, ¿por qué se deja el conjunto de datos original en el modelo?
>
>Es cierto que el servicio no necesita los datos originales de la tarjeta de crédito. Pero sí necesita el esquema para esos datos, que incluye información como la cantidad de columnas que hay y cuáles son numéricas. Esta información del esquema es necesaria a fin de interpretar los datos del usuario. Dejamos estos componentes conectados para que el módulo de puntuación tenga el esquema del conjunto de datos cuando el servicio se esté ejecutando. No se utilizan los datos, sino solamente el esquema.

Ejecute el experimento por última vez (haga clic en **Ejecutar**). Si desea comprobar que el modelo sigue funcionando, haga clic en la salida del módulo [Puntuar modelo][score-model] y seleccione **Ver resultados**. Verá que aparecen los datos originales, junto con el valor de riesgo de crédito ("Etiquetas puntuadas") y el valor de probabilidad de la puntuación ("Probabilidades puntuadas").

## Implementación del servicio web

Puede implementar el experimento como un servicio web clásico o nuevo basado en Azure Resource Manager.

### Implementación como servicio web clásico ###

Para implementar un servicio web clásico derivado de nuestro experimento, haga clic en **Deploy Web Service** (Implementar servicio web) debajo del lienzo y seleccione **Deploy Web Service [New]** (Implementar un servicio web [nuevo]). Estudio de aprendizaje automático implementa el experimento como servicio web y lo remite al panel del servicio web. Desde esta página puede volver al experimento (**View snapshot** [Ver instantánea] o **View latest** [Ver más reciente]) y ejecutar una prueba sencilla del servicio web (botón **Probar**; consulte la sección **Prueba del servicio web** a continuación). También hay información aquí para crear aplicaciones que puedan acceder al servicio web (más información al respecto en el siguiente paso de este tutorial).

![Panel del servicio web][6]

Puede configurar el servicio haciendo clic en la pestaña **CONFIGURACIÓN**. Aquí puede modificar el nombre del servicio (recibe de manera predeterminada el nombre del experimento) y proporcionarle una descripción. También puede poner etiquetas más descriptivas para las columnas de entrada y salida.

![Configurar el servicio web][5]

### Implementación como servicio web nuevo

Para implementar un servicio web nuevo derivado de nuestro experimento, haga clic en **Deploy Web Service** (Implementar servicio web) debajo del lienzo y seleccione **Deploy Web Service [New]** (Implementar un servicio web [nuevo]). Estudio de aprendizaje automático le lleva a la página de implementación de experimentos del portal de servicios web de Aprendizaje automático de Azure.

Escriba un nombre para el servicio web y elija un plan de precios. Si ya tiene uno, puede seleccionarlo; si no, debe crear uno nuevo para el servicio.

1.	En el menú desplegable **Price Plan** (Plan de precios), seleccione un plan existente o elija la opción **Select new plan** (Seleccionar nuevo plan).
2.	En **Nombre del plan**, escriba un nombre que identifique el plan en la factura.
3.	Seleccione uno de los **niveles de planes mensuales**. Tenga en cuenta que los niveles de los planes predeterminados son los de la región predeterminada y que el servicio web se implementa en dicha región.

Haga clic en las páginas **Implementar** e **Inicio rápido** del servicio web que se abre.

Puede configurar el servicio haciendo clic en la pestaña **CONFIGURACIÓN**. Aquí puede modificar el nombre del servicio (recibe de manera predeterminada el nombre del experimento) y proporcionarle una descripción. También puede habilitar o deshabilitar el registro de errores y los datos de ejemplo que pueden utilizarse para probar el servicio.

Para probar el servicio web, haga clic en la opción de menú **Probar** (consulte la sección **Prueba del servicio web** a continuación). Para saber cómo crear aplicaciones que puedan acceder al servicio web, haga clic en la opción de menú **Consume** (Consumo). Puede obtener más información al respecto en el siguiente paso de este tutorial.


> [AZURE.TIP] Puede actualizar el servicio web después de haberlo implementado. Por ejemplo, si desea cambiar el modelo, simplemente edite el experimento de entrenamiento, ajuste los parámetros del modelo y haga clic en **Implementar servicio web**. Después, seleccione **Deploy Web Service [Classic]** (Implementar un servicio web [clásico]) o **Deploy Web Service [New]** (Implementar un servicio web [nuevo]). Cuando implemente el experimento de nuevo, sustituirá al servicio web, utilizando ahora el modelo actualizado.


## Prueba del servicio web

**Prueba de un servicio web clásico** En la página **PANEL**, haga clic en el botón **Probar** de **Default Endpoint** (Punto de conexión predeterminado). Aparecerá un cuadro de diálogo que le pide los datos de entrada del servicio. Se trata de las mismas columnas que aparecieron en el conjunto de datos original de riesgo de crédito alemán.

Escriba un conjunto de datos y, a continuación, haga clic en **Aceptar**.

En el servicio web, los datos se escriben a través de los módulos **Web service input** (Entrada de servicio web) y [Metadata Editor][metadata-editor] \(Editor de metadatos), así como en el módulo [Score Model][score-model] \(Puntuar modelo) en el que se puntúa. Después, los resultados se generan desde el servicio web a través de los **resultados de servicio web**.

**Prueba de un servicio web nuevo**

En el portal de servicios web de Aprendizaje automático de Azure, haga clic en la opción de menú **Probar** de la parte superior de la página. Se abre la página de prueba y podrá escribir datos para el servicio. Los campos de entrada que se muestran corresponden a las columnas que aparecieron en el conjunto de datos original de riesgo de crédito alemán.

Escriba un conjunto de datos y, después, haga clic en **Test Request-Response** (Probar solicitud-respuesta).

También puede habilitar datos de ejemplo que pueden usarse para probar el servicio de solicitud-respuesta. Si ha creado el servicio web en Estudio de aprendizaje automático de Microsoft Azure, los datos de ejemplo se toman de los utilizados para entrenar el modelo.

Los resultados de la prueba se mostrarán en el lado derecho de la página en la columna de salida.

> [AZURE.TIP] La forma en la que hemos configurado el experimento predictivo permitirá que se devuelvan los resultados desde el módulo [Score Model][score-model] \(Puntuar modelo). Esto incluye todos los datos de entrada además del valor de riesgo de crédito y la probabilidad de puntuación. Si quiere que se devuelva algo diferente, por ejemplo, solo el valor de riesgo de crédito, podría insertar un módulo [Project Columns][project-columns] \(Columnas del proyecto) entre [Score Model][score-model] \(Puntuar modelo) y **Web service output** (Salida de servicio web) para eliminar las columnas que el servicio web no debe devolver.

## Administración del servicio web

**Administración de servicios web clásicos**

Cuando se haya implementado el servicio web clásico, puede administrarlo desde el [Portal de Azure clásico](https://manage.windowsazure.com).

1. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. En el panel de servicios de Microsoft Azure, haga clic en **APRENDIZAJE AUTOMÁTICO**.
3. Haga clic en el área de trabajo.
4. Haga clic en la ficha **Servicios web**.
5. Haga clic en el servicio web que acabamos de crear.
6. Haga clic en el punto de conexión "predeterminado".

Desde aquí, puede hacer tareas como supervisar el funcionamiento del servicio web y realizar ajustes de rendimiento cambiando el volumen de llamadas simultáneas que el servicio puede controlar. Incluso puede publicar el servicio web en Azure Marketplace.

Para obtener información, consulte:

- [Creación de extremos](machine-learning-create-endpoint.md)
- [Escalado del servicio web](machine-learning-scaling-webservice.md)
- [Publicación de los servicios web de Aprendizaje automático de Azure en Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

**Administración de servicios web nuevos**

Cuando se haya implementado el servicio web nuevo, puede administrarlo desde el [portal de servicios web clásicos de Aprendizaje automático de Azure](https://servics.azureml.net).

Para supervisar el rendimiento del servicio web, siga estos pasos:
1. Inicie sesión en el [portal de servicios web de Aprendizaje automático de Azure](https://servics.azureml.net).
2. Haga clic en **Servicios web**.
3. Haga clic en el servicio web.
4. Haga clic en **Panel**.

----------

**A continuación: [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)**

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
[project-columns]: https://msdn.microsoft.com/es-ES/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

<!---HONumber=AcomDC_0921_2016-->