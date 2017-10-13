---
title: "Preparación del modelo de implementación en Azure Machine Learning Studio | Microsoft Docs"
description: "Se describe cómo preparar el modelo entrenado de implementación como un servicio web mediante la conversión del experimento de entrenamiento de Machine Learning Studio en un experimento de predicción."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: garye
ms.openlocfilehash: 96434d6c5a2cf73e425c02d35d546ab33b7ac13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Preparación del modelo de implementación en Azure Machine Learning Studio

Azure Machine Learning Studio ofrece las herramientas necesarias para desarrollar un modelo de análisis predictivo y después hacerlo operativo mediante la implementación como un servicio web de Azure.

Para ello, utilice Studio para crear un experimento, denominado *experimento de entrenamiento*, donde puede entrenar, puntuar y editar el modelo. Cuando esté satisfecho, el modelo estará listo para implementarlo mediante la conversión del experimento de entrenamiento en un *experimento predictivo* que está configurado para puntuar los datos de usuario.

Puede ver un ejemplo de este proceso en [Tutorial: Desarrollo de una solución de análisis predictivo para la evaluación del riesgo de crédito en Azure Machine Learning](walkthrough-develop-predictive-solution.md).

En este artículo se profundiza en los detalles de cómo un experimento de entrenamiento se convierte en un experimento predictivo y de cómo se implementa ese experimento predictivo. Al comprender estos detalles, puede obtener información sobre cómo configurar el modelo implementado para que sea más eficaz.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Información general 

El proceso de convertir un experimento de entrenamiento en un experimento predictivo implica tres pasos:

1. Reemplace los módulos de algoritmo de aprendizaje automático con el modelo entrenado.
2. Recortar el experimento solo a los módulos que se necesitan para efectuar la puntuación. Un experimento de entrenamiento incluye una serie de módulos que son necesarios para el entrenamiento, pero que no son necesarios una vez que el modelo está entrenado.
3. Defina cómo el modelo aceptará los datos del usuario de servicio web y qué datos se devolverán.

> [!TIP]
> En el experimento de entrenamiento, se ha interesado por el entrenamiento y la puntuación del modelo con la utilización de datos propios. Pero una vez implementado, los usuarios enviarán datos nuevos al modelo y este devolverá resultados de predicción. Por lo tanto, al convertir el experimento de entrenamiento en un experimento predictivo para preparar la implementación, tenga en cuenta cómo otros usuarios usarán el modelo.
> 
> 

## <a name="set-up-web-service-button"></a>Botón Configurar servicio web
Después de ejecutar el experimento, tras hacer clic en **Ejecutar** en la parte inferior del lienzo de experimento, haga clic en el botón **Configurar servicio web**, para lo que debe seleccionar la opción **Servicio web predictivo**. **Configurar servicio web** realiza automáticamente los tres pasos de conversión del experimento de entrenamiento en un experimento predictivo:

1. Guarda el modelo entrenado en la sección **Modelos entrenados** de la paleta de módulo (a la izquierda del lienzo de experimento). Después reemplaza el algoritmo de aprendizaje automático y los módulos [Entrenar modelo][train-model] con el modelo entrenado guardado.
2. Analiza el experimento y quita los módulos que ya se han usado solo para entrenamiento y que ya no son necesarios.
3. Inserta los módulos de _Entrada de servicio web_ y _Salida de servicio web_ en las ubicaciones predeterminadas del experimento (estos módulos aceptan y devuelven datos de usuario).

Por ejemplo, en el siguiente experimento se entrena un modelo de árbol de decisión aumentada de dos clases con los datos del censo de muestra:

![experimento de entrenamiento][figure1]

Los módulos de este experimento realizan básicamente cuatro funciones diferentes:

![Funciones del módulo][figure2]

Al convertir este experimento de entrenamiento en un experimento predictivo, algunos de estos módulos ya no se necesitan o ahora tienen un objetivo diferente:

* **Datos** : los datos de este conjunto de datos de ejemplo no se usan durante la puntuación (el usuario del servicio web proporcionará los datos que se van a puntuar). Sin embargo, los metadatos de este conjunto de datos, como los tipos de datos, son utilizados por el modelo formado. Por ello, necesita mantener el conjunto de datos en el experimento predictivo para que pueda ofrecer estos metadatos.

* **Preparación**: en función de los datos de usuario que se vayan a enviar para puntuar, estos módulos pueden ser necesarios, o no, para procesar los datos entrantes. El botón **Configurar servicio web** no les afecta; debe decidir cómo administrarlos.
  
    En este ejemplo, en el conjunto de datos de muestra pueden faltar valores, por lo que se ha incluido un módulo [Limpiar datos que faltan][clean-missing-data] para administrarlos. Además, el conjunto de datos de muestra incluye columnas que no son necesarias para entrenar el modelo. Por tanto, se ha incluido el módulo [Seleccionar columnas de conjunto de datos][select-columns] para excluir esas columnas adicionales del flujo de datos. Si sabe que a los datos que se van a enviar para puntuar a través del servicio web no les van a faltar valores, puede quitar el módulo [Limpiar datos que faltan][clean-missing-data]. Sin embargo, dado que el módulo [Seleccionar columnas de conjunto de datos][select-columns] ayuda a definir las columnas de datos que el modelo entrenado espera, es necesario mantener ese módulo.

* **Entrenar**: estos módulos se usan para entrenar el modelo. Cuando hace clic en **Configurar servicio web**, estos módulos se reemplazan por un único módulo que contiene el modelo entrenado. Este módulo nuevo se guarda en la sección **Modelos entrenados** de la paleta de módulos.

* **Puntuación**: en este ejemplo, el módulo [Dividir datos][split] se usa para dividir el flujo de datos en datos de prueba y datos de entrenamiento. En el experimento predictivo, ya no se entrena, por lo que [Dividir datos][split] se puede eliminar. De forma similar, el segundo módulo [Puntuar modelo][score-model] y el módulo [Evaluar modelo][evaluate-model] se usan para comparar los resultados de los datos de prueba, por lo que estos módulos no son necesarios en el experimento predictivo. No obstante, el módulo [Puntuar modelo][score-model] restante es necesario para devolver un resultado de puntuación a través del servicio web.

Este es el aspecto de nuestro ejemplo después de hacer clic en **Configurar servicio web**:

![Experimento predictivo convertido][figure3]

El trabajo realizado por **Configurar servicio web** puede ser suficiente para preparar el experimento a fin de implementarlo como servicio web. Sin embargo, es aconsejable realizar algún trabajo adicional específico en su experimento.

### <a name="adjust-input-and-output-modules"></a>Ajustar los módulos de entrada y salida
En el experimento de formación, utilizó un conjunto de datos de entrenamiento y, a continuación, realizó algún procesamiento para obtener los datos en un formato necesitado por el algoritmo de aprendizaje automático. Si los datos que espera recibir a través del servicio web no necesitan este procesamiento, puede omitirlo: conecte la salida del **módulo Entrada de servicio web** a un módulo diferente del experimento. Los datos de usuario ahora no alcanzarán el modelo en esta ubicación.

Por ejemplo, de forma predeterminada **Configurar servicio web** coloca el módulo **Entrada de servicio web** en la parte superior del flujo de datos, como se muestra en la ilustración anterior. No obstante, puede colocar manualmente la **Entrada de servicio web** después de los módulos de procesamiento de datos:

![Mover la entrada del servicio web][figure4]

Los datos de entrada que se proporciona a través del servicio web pasarán ahora directamente al módulo del modelo de puntuación sin ningún procesamiento previo.

De igual forma, **Configurar servicio web** coloca de manera predeterminada el módulo de salida de servicio web en la parte inferior del flujo de datos. En este ejemplo, el servicio web devolverá al usuario la salida del módulo [Puntuar modelo][score-model], que incluye el vector de los datos de entrada completo, además de los resultados de puntuación.
Sin embargo, si prefiere que devuelva otra cosa, puede agregar módulos adicionales antes del módulo de **salida del servicio web**. 

Por ejemplo, para que se devuelvan los resultados de puntuación y no el vector completo de los datos de entrada, agregue un módulo [Seleccionar columnas de conjunto de datos][select-columns] para excluir todas las columnas, excepto los resultados de puntuación. A continuación, mueva el módulo **Salida de servicio web** a la salida del módulo [Seleccionar columnas de conjunto de datos][select-columns]. El experimento tiene el siguiente aspecto:

![Mover la salida del servicio web][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Agregar o quitar módulos de procesamiento de datos adicionales
Si hay más módulos en el experimento de los que sabe que se necesitarán durante la puntuación, se pueden eliminar. Por ejemplo, al haber movido el módulo **Entrada de servicio web** a un punto posterior a los módulos de procesamiento de datos, se puede eliminar el módulo [Limpiar datos que faltan][clean-missing-data] del experimento predictivo.

Nuestro experimento predictivo tiene ahora el siguiente aspecto:

![Eliminación de módulos adicionales][figure6]


### <a name="add-optional-web-service-parameters"></a>Agregar parámetros de servicio web opcionales
En algunos casos, puede permitir al usuario del servicio web cambiar el comportamiento de los módulos cuando se accede al servicio. *parámetros del servicio web* le permiten hacer esto.

Un ejemplo común es la configuración de un módulo [Importar datos][import-data] para que el usuario del servicio web implementado pueda especificar un origen de datos diferente al acceder al servicio web. También puede configurar el módulo [Exportar datos][export-data] para que se pueda especificar un destino diferente.

Puede definir parámetros de servicio web y asociarlos con uno o más parámetros de módulo, y puede especificar si son obligatorios u opcionales. El usuario del servicio web proporciona valores para estos parámetros cuando se tiene acceso al servicio y las acciones del módulo se modifican en consecuencia.

Para más información sobre los parámetros del servicio web y cómo usarlos, vea [Usar parámetros de servicio web Azure Machine Learning][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Implementar el experimento predictivo como servicio web
Ahora que ha preparado el experimento predictivo suficientemente, puede implementarlo como servicio web de Azure. Mediante el servicio web, los usuarios pueden enviar datos a su modelo y el modelo devolverá las predicciones.

Para más información sobre el proceso de implementación completo, consulte [Implementar un servicio web Azure Machine Learning][deploy]

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
