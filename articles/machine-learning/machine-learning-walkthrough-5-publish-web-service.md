<properties
	pageTitle="Paso 5: Implementar el servicio web de Aprendizaje automático | Microsoft Azure"
	description="Paso 5 del tutorial Desarrollo de una solución predictiva: implementación de un experimento predictivo en Estudio de aprendizaje automático como servicio web."
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
	ms.date="09/08/2015"
	ms.author="garye"/>


# Paso 5 del tutorial: Implementar el servicio web de Aprendizaje automático de Azure

Este es el quinto paso del tutorial [Desarrollo de una solución predictiva con Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Implementación del servicio web**
6.	[Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para que este modelo predictivo pueda resultar útil a otros usuarios, lo implementaremos como servicio web en Azure.

Hasta ahora hemos estado experimentando con el entrenamiento de nuestro modelo. Sin embargo, el servicio implementado ya no va a realizar el entrenamiento: generará predicciones basadas en la entrada del usuario. Así que vamos a hacer una preparación y a implementar este experimento como un servicio web de trabajo al que pueden acceder los usuarios. Un usuario podrá enviar al servicio un conjunto de datos de una aplicación de crédito y el servicio devolverá la predicción del riesgo de crédito.

Para ello, necesitamos lo siguiente:

- Convertir el *experimento de entrenamiento* que hemos creado en un *experimento predictivo*
- Implementar el experimento predictivo como servicio web

Pero, en primer lugar, debemos reducir este experimento un poco. Tenemos dos modelos distintos en el experimento, pero ahora tenemos que seleccionar el modelo que se implementará.

Pongamos que hayamos decidido que el modelo de árbol ampliado es el mejor en este caso. Por tanto, lo primero que se debe hacer es eliminar el módulo [Máquina de vectores de soporte de dos clases][two-class-support-vector-machine] y los módulos que se usaron para entrenar. Puede que desee hacer una copia del experimento antes; para ello, haga clic en **Guardar como** en la parte inferior del lienzo de experimento.

Es necesario eliminar los siguientes módulos:

1.	[Máquina de vectores de soporte de dos clases][two-class-support-vector-machine]
2.	Los módulos [Entrenar modelo][train-model] y [Puntuar modelo][score-model] conectados a ella.
3.	[Normalizar datos][normalize-data] (ambos)
4.	[Evaluar modelo][evaluate-model]

Ahora estamos listos para implementar este modelo.

## Convertir un experimento de entrenamiento en experimento predictivo

La conversión a un experimento predictivo implica tres pasos:

1. Guardar el modelo que se ha entrenado y sustituir nuestros módulos de aprendizaje por él.
2. Recortar el experimento para quitar los módulos que fueron necesarios solo para el entrenamiento.
3. Definir dónde deben estar los nodos de entrada y de salida del servicio web

Afortunadamente, para realizar los tres pasos simplemente haga clic en el **Implementar servicio web** en la parte inferior del lienzo de experimento (seleccione la opción **Servicio web predictivo**).

Al hacer clic en **Implementar servicio web**, suceden varias cosas:

- El modelo que entrenamos se guarda como un módulo **Modelo entrenado** en la paleta de módulos a la izquierda del lienzo de experimento (puede encontrarlo en la paleta en **Modelos entrenados**).
- Se quitan los módulos que se utilizaron para el entrenamiento. Concretamente:
  - [Árbol de decisión ampliado de dos clases][two-class-boosted-decision-tree]
  - [Entrenar modelo][train-model]
  - [Dividir][split]
  - el segundo módulo [Ejecutar script R][execute-r-script] que se usó para probar los datos.
- El modelo entrenado guardado se agrega al experimento.
- Se agregan los módulos **Entrada de servicio web** y **Salida de servicio web**.

> [AZURE.NOTE]Se ha guardado el experimento en dos partes: el experimento de entrenamiento original y el nuevo experimento predictivo. Puede tener acceso a uno con las pestañas en la parte superior del lienzo de experimento.

Necesitamos realizar un paso adicional con nuestro experimento. Estudio de aprendizaje automático quitó un módulo [Ejecutar script R][execute-r-script] cuando quitó el módulo [Dividir][split], pero dejó el otro módulo [Ejecutar script R][execute-r-script]. Puesto que ese módulo solo se usa para el entrenamiento y las pruebas (proporciona una función de peso en los datos de ejemplo), ahora podemos quitarlo y conectar el [Editor de metadatos][metadata-editor] a [Puntuar modelo][score-model].

Nuestro experimento debería tener ahora un aspecto similar al siguiente:

![Scoring the trained model][4]


Quizás se pregunte por qué hemos dejado el conjunto de datos de los datos de datos de tarjeta de crédito alemana de UCI en el experimento predictivo. El servicio va a utilizar los datos del usuario, no el conjunto de datos original; entonces, ¿por qué los dejamos conectados?

Es cierto que el servicio no necesita los datos originales de la tarjeta de crédito. Pero sí necesita el esquema para esos datos, que incluye información como la cantidad de columnas que hay y cuáles son numéricas. Esta información del esquema es necesaria a fin de interpretar los datos del usuario. Dejamos estos componentes conectados para que el módulo de puntuación tenga el esquema del conjunto de datos cuando el servicio se esté ejecutando. No se utilizan los datos, sino solamente el esquema.

Ejecute el experimento por última vez (haga clic en **Ejecutar**). Si desea comprobar que el modelo sigue funcionando, haga clic en la salida del módulo [Puntuar modelo][score-model] y seleccione **Ver resultados**. Verá que aparecen los datos originales, junto con el valor de riesgo de crédito ("Etiquetas puntuadas") y el valor de probabilidad de la puntuación ("Probabilidades puntuadas").

## Implementación del servicio web

Para implementar un servicio web derivado de nuestro experimento, haga clic en **Implementar servicio web** debajo del lienzo. Estudio de aprendizaje automático implementa el experimento como servicio web y le dirige al panel de servicios.

> [AZURE.TIP]Puede actualizar el servicio web después de haberlo implementado. Por ejemplo, si desea cambiar el modelo, simplemente edite el experimento de entrenamiento, ajuste los parámetros del modelo y haga clic en **Implementar servicio web**. Cuando implemente el experimento de nuevo, sustituirá al servicio web, utilizando ahora el modelo actualizado.

Puede configurar el servicio haciendo clic en la pestaña **CONFIGURACIÓN**. Aquí puede modificar el nombre del servicio (recibe de manera predeterminada el nombre del experimento) y proporcionarle una descripción. También puede poner etiquetas más descriptivas para las columnas de entrada y salida.

## Prueba del servicio web
En la página **PANEL**, haga clic en el vínculo **Probar** en **Extremo predeterminado**. Aparecerá un cuadro de diálogo que le pide los datos de entrada del servicio. Se trata de las mismas columnas que aparecieron en el conjunto de datos original de riesgo de crédito alemán.

Escriba un conjunto de datos y, a continuación, haga clic en **Aceptar**.

Los resultados generados por el servicio web se muestran en la parte inferior del panel. De este modo tendremos el servicio configurado; los resultados que vea están generados por el módulo de puntuación.


----------

**A continuación: [Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png


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

<!---HONumber=Oct15_HO3-->