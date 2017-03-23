---
title: "Cómo un modelo de Azure Machine Learning se convierte en servicio web | Microsoft Docs"
description: "Información general de cómo progresa el modelo de Azure Machine Learning desde un experimento de desarrollo a un servicio web aplicado."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 66fb3dc316ce25aea4dff4add5c25b7f0f56ad7a
ms.openlocfilehash: 8c5a3ca30ae0539cded6cf9cd93ba1a42e19ba58
ms.lasthandoff: 01/31/2017


---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Progreso de un modelo de Machine Learning de un experimento a un servicio web aplicado
Azure Machine Learning Studio ofrece un lienzo interactivo que permite desarrollar, ejecutar, probar e iterar un ***experimento*** que representa un modelo de análisis predictivo. Hay una gran variedad de módulos que le permiten realizar las acciones siguientes:

* Introducir datos en el experimento
* Manipulación de los datos.
* Entrenar un modelo con algoritmos de aprendizaje automático
* Puntuación del modelo
* Evaluar los resultados
* Generar los resultados finales

Cuando esté satisfecho con el experimento, puede implementarlo como un ***servicio web Azure Machine Learning clásico*** o un ***servicio web Azure Machine Learning nuevo*** para que los usuarios puedan enviar datos nuevos y recibir los resultados de vuelta.

En este artículo ofrecemos una información general de cómo progresa el modelo de Machine Learning desde un experimento de desarrollo hasta un servicio web aplicado.

> [!NOTE]
> Hay otras maneras de desarrollar e implementar modelos de Aprendizaje automático, pero este artículo se centra en cómo usar Estudio de aprendizaje automático. Por ejemplo, para leer una descripción sobre cómo crear un servicio web predictivo clásico con R, consulte la publicación del blog [Build & Deploy Predictive web Apps Using RStudio and Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx) (Creación e implementación de aplicaciones web predictivas con RStudio y Azure Machine Learning).
> 
> 

Aunque Azure Machine Learning Studio está diseñado para ayudarle a desarrollar e implementar un *modelo de análisis predictivo*, Studio se puede usar para desarrollar un experimento que no incluya ningún modelo de este tipo. Por ejemplo, un experimento podría simplemente insertar datos, manipularlos y, después, producir los resultados. Al igual que un experimento de análisis predictivo, puede implementar este experimento no predictivo como un servicio web, pero es un proceso más sencillo porque el experimento no entrena ni puntúa un modelo de aprendizaje automático. Aunque este no es el uso típico de Studio, se debe incluir en la explicación siguiente para que podamos darle una explicación completa de su funcionamiento.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Desarrollo e implementación de un servicio web predictivo
Estas son las fases que sigue una solución típica cuando la desarrolla e implementa mediante Estudio de aprendizaje automático:

![Flujo de implementación](media/machine-learning-model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Ilustración 1 - Fases de un modelo de análisis predictivo típico*

### <a name="the-training-experiment"></a>El experimento de entrenamiento
El ***experimento de entrenamiento*** es la fase inicial del proceso de desarrollo de un servicio web en Machine Learning Studio. El propósito del experimento de entrenamiento es brindarle un lugar para desarrollar, probar, iterar y finalmente entrenar un modelo de aprendizaje automático. Incluso puede entrenar varios modelos simultáneamente mientras busca la mejor solución, pero una vez que haya terminado de experimentar, puede seleccionar un único modelo entrenado y eliminar el resto del experimento. Para ver un ejemplo de desarrollo de un experimento de análisis predictivo, consulte [Desarrollo de una solución de análisis predictivo para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>El experimento predictivo
Cuando ya tenga un modelo entrenado en el experimento de entrenamiento, haga clic en **Set Up Web Service** (Configurar servicio web) y seleccionar **Predictive Web Service** (Servicio web predictivo) en Machine Learning Studio para iniciar el proceso de conversión del experimento de entrenamiento en un ***experimento predictivo***. El propósito del experimento predictivo es usar el modelo entrenado para puntuar nuevos datos, con el fin de ponerlo en marcha en algún momento como un servicio web de Azure.

Esta conversión se realiza a través de los pasos siguientes:

* Conversión del conjunto de módulos usados para el entrenamiento en un único módulo y su almacenamiento como un modelo entrenado
* Eliminación de los módulos extraños no relacionados con la puntuación
* Adición de puertos de entrada y salida que usará el servicio web final

Es posible que desee hacer más cambios para que el experimento predictivo esté listo para implementarse como un servicio web. Por ejemplo, si desea que el servicio web genere solo un subconjunto de resultados, puede agregar un módulo de filtrado antes del puerto de salida.

En este proceso de conversión no se descarta el experimento de entrenamiento. Cuando se complete el proceso, tendrá dos pestañas en Studio: una para el experimento de entrenamiento y otra para el experimento predictivo. De este modo, puede realizar cambios en el experimento de entrenamiento antes de implementar el servicio web y volver a compilar el experimento predictivo. O bien, puede guardar una copia del experimento de entrenamiento para iniciar otra línea de experimentación.

> [!NOTE]
> Al hacer clic en **Predictive Web Service** (Servicio web predictivo), se inicia un proceso automático para convertir el experimento de entrenamiento en un experimento predictivo, lo que funciona correctamente en la mayoría de los casos. Si el experimento de entrenamiento es complejo (por ejemplo, tiene varias rutas de acceso para el entrenamiento que se unen), es posible que prefiera realizar esta conversión manualmente. Para obtener más información, consulte [Conversión de un experimento de entrenamiento de Machine Learning en un experimento predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>El servicio web
Cuando crea firmemente que el experimento predictivo está listo, puede implementar el servicio como servicio web clásico o nuevo basado en Azure Resource Manager. Para aplicar el modelo implementándolo como *servicio web Machine Learning clásico*, haga clic en **Deploy Web Service** (Implementar servicio web) y seleccione **Deploy Web Service [Classic]** (Implementar servicio web [clásico]). Para implementarlo como *servicio web Machine Learning nuevo*, haga clic en **Deploy Web Service** (Implementar servicio web) y seleccione **Deploy Web Service [New]** (Implementar servicio web [nuevo]). Hecho esto, los usuarios ya pueden enviar datos al modelo mediante la API de REST del servicio web y recibir los resultados de vuelta. Para obtener más información, consulte [Consumo de un servicio web Azure Machine Learning implementado en un experimento de Machine Learning](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>El caso no típico: creación de un servicio web no predictivo
Si el experimento no entrena un modelo de análisis predictivo, no tendrá que crear un experimento de entrenamiento y un experimento de puntuación, sino solo un experimento que puede implementar como un servicio web. Machine Learning Studio detecta si el experimento contiene un modelo predictivo analizando los módulos que se han usado.

Después de que haya iterado en el experimento y esté satisfecho con él:

1. Haga clic en **Set Up Web Service** (Configurar servicio web) y seleccione **Retraining Web Service** (Servicio web de reentrenamiento): los nodos de entrada y salida se agregan automáticamente.
2. Haga clic en **Ejecutar**
3. Haga clic en **Deploy Web Service** (Implementar servicio web) y seleccione **Deploy Web Service [Classic]** (Implementar servicio web [clásico]) o **Deploy Web Service [New]** (Implementar servicio web [nuevo]), en función del entorno en el que quiera realizar la implementación.

Hecho esto, el servicio web se habrá implementado, y puede acceder a él y a administrarlo como si fuera un servicio web predictivo.

## <a name="updating-your-web-service"></a>Actualización del servicio web
Una vez implementado el experimento como un servicio web, ¿qué ocurre si tiene que actualizarlo?

Depende de lo que necesite actualizar:

**Quiere cambiar la entrada o salida, o quiere modificar la forma en que el servicio web manipula los datos**

Si no modifica el modelo, pero solo quiere cambiar la forma en que el servicio web administra los datos, puede editar el experimento predictivo y después volver a hacer clic en **Deploy Web Service** (Implementar servicio web) y seleccionar **Deploy Web Service [Classic]** (Implementar servicio web [clásico]) o **Deploy Web Service [New]** (Implementar servicio web [nuevo]). Se detiene el servicio web, se implementa el experimento predictivo actualizado y se reinicia el servicio web.

Por ejemplo: imagine que el experimento predictivo devuelve toda la fila de datos de entrada con el resultado de la predicción. Puede decidir que quiere que el servicio web solo devuelva el resultado. Por tanto agrega un módulo de **columnas del proyecto** en el experimento predictivo, justo antes del puerto de salida, para excluir otras columnas que no sean el resultado. Al hacer clic en **Deploy Web Service** (Implementar servicio web) y volver a seleccionar **Deploy Web Service [Classic]** (Implementar servicio web [clásico]) o **Deploy Web Service [New]** (Implementar servicio web [nuevo]), el servicio web se actualiza.

**Quiere reciclar el modelo con nuevos datos**

Si quiere mantener su modelo de aprendizaje automático, pero le gustaría reciclarlo con nuevos datos, tiene dos opciones:

1. **Reentrenar el modelo mientras se ejecuta el servicio web**: si desea reentrenar el modelo mientras se ejecuta el servicio web predictivo, puede hacerlo realizando un par de modificaciones en el experimento de entrenamiento para convertirlo en un ***experimento de reentrenamiento*** y después puede implementarlo como un ***servicio web* de reentrenamiento**. Para obtener más información sobre cómo hacerlo, consulte [Reciclar modelos de Aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md).
2. **Volver al experimento de entrenamiento original y usar distintos datos de entrenamiento para desarrollar el modelo**: el experimento predictivo está vinculado al servicio web, pero el experimento de entrenamiento no está vinculado directamente de esta manera. Si modifica el experimento de entrenamiento original y hace clic en **Set Up Web Service** (Configurar servicio web), se creará un *nuevo* experimento predictivo que, cuando se implemente, creará un *nuevo* servicio web. Esta acción no solo actualiza el servicio web original.
   
   Si tiene que modificar el experimento de entrenamiento, ábralo y haga clic en **Guardar como** para realizar una copia. De este modo, el experimento de entrenamiento original, el experimento predictivo y el servicio web quedan intactos. Ahora puede crear un nuevo servicio web con los cambios. Después de implementar el nuevo servicio web, puede decidir si quiere detener el servicio web anterior o dejar que siga ejecutándose junto con el nuevo.

**Quiere entrenar un modelo diferente**

Si desea realizar cambios en el experimento predictivo original, como seleccionar un algoritmo de Aprendizaje automático diferente o probar un método de entrenamiento distinto, debe seguir el segundo procedimiento descrito anteriormente para reciclar el modelo: abra el experimento de entrenamiento, haga clic en **Guardar como** para hacer una copia e inicie la nueva ruta de acceso del desarrollo del modelo, de la creación del experimento predictivo y de la implementación del servicio web. Esto creará un nuevo servicio web no relacionado con el original (puede decidir que se siga ejecutando solo uno de ellos o ambos).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más detalles sobre el proceso de desarrollo y el experimento, consulte los artículos siguientes:

* conversión del experimento - [Convertir un experimento de entrenamiento de Aprendizaje automático en un experimento predictivo](machine-learning-convert-training-experiment-to-scoring-experiment.md)
* implementación del servicio web: [Implementación de un servicio web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)
* reciclaje del modelo - [Reciclar modelos de Aprendizaje automático mediante programación](machine-learning-retrain-models-programmatically.md)

Para ver ejemplos de todo el proceso, consulte:

* [Tutorial de Aprendizaje automático: Creación del primer experimento en Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md)
* [Tutorial: Desarrollo de una solución de análisis predictivo para la evaluación del riesgo de crédito en Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


