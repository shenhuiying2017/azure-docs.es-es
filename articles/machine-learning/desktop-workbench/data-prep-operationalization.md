---
title: "Guía completa sobre el uso de la puesta en operación de la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona información detallada sobre cómo ejecutar paquetes de preparación de datos y de orígenes de datos previamente diseñados"
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 0849747fe6d66d55d11c131b51b07d8f689774e1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparation-operationalization"></a>Puesta en operación de la preparación de datos 

## <a name="operationalization-scenario"></a>Escenario de la puesta en operación

Estos son los diferentes escenarios de puesta en operación de la preparación de datos que un usuario podría encontrarse.

### <a name="develop-your-model-based-on-training-data"></a>Desarrollo del modelo basado en datos de entrenamiento

Suponga que quiere crear e implementar un servicio de API de puntuación en tiempo real. El primer paso es desarrollar un modelo para puntuar en función de un conjunto de datos de entrenamiento. La preparación de datos importa una muestra de los datos de entrenamiento como un nuevo origen de datos. Una vez preparados los datos, el paquete DataPrep contiene los pasos de preparación. Con una cuenta de Experimentación de Machine Learning, el usuario puede recorrer en iteración un modelo de aprendizaje automático para generar etiquetas para cada entrada en los datos de entrenamiento.

Dado que es necesario actualizar las características de los datos, el usuario vuelve al paquete DataPrep para preparar los datos de una forma nueva y guardar esos pasos. Este proceso de iteración de generar nuevas características y ajustar el modelo de aprendizaje automático continúa hasta que el modelo puntúa con precisión los datos de prueba. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Implementación del modelo en el servicio Administración de modelos de Azure

Ahora tiene los datos de los clientes que le gustaría puntuar en tiempo real. Mediante el servicio Administración de modelos de Azure, puede implementar este modelo desde la CLI de Python de Azure Machine Learning como un servicio. El servicio implementado expone un punto de conexión REST para recibir los datos de los clientes en tiempo real y devolver la correspondiente etiquetas de salida desde el modelo entrenado.

Para facilitar su uso, el punto de conexión del modelo acepta los datos en formato JSON. La entrada debe ser una cadena JSON que representa una matriz de 2 dimensiones de datos, que se pasa a través de la transformación ReadJSONLiteral y se convierte en un origen de datos de DataPrep. El paquete de preparación de datos creado durante la fase de experimentación se puede ejecutar sobre los datos JSON transmitidos. A continuación, se puede pasar la trama de datos resultante al modelo entrenado para su puntuación.

## <a name="read-json-literal-transformation"></a>Transformación Read JSON Literal

### <a name="description"></a>DESCRIPCIÓN

Con fines de puesta en operación, la preparación de datos contiene una transformación **ReadJsonLiteral** para ejecutar una actividad y generar una trama de datos de Pandas o Spark. Esta transformación únicamente toma como entrada un paquete de preparación de datos existente y un origen de datos JSON. Esta transformación se expone a través de la CLI de Python DataPrep.

### <a name="instructions"></a>Instrucciones

#### <a name="pythoncli"></a>PythonCLI

En Azure Machine Learning Workbench, abra la interfaz de la línea de comandos (Archivo > Abrir símbolo del sistema).

En este ejemplo, el paquete de preparación de datos TrainingPreparationSteps se utiliza para preparar los datos y agregar la característica de volumen a cada entrada.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` tiene los siguientes parámetros opcionales
 - `dataflow_idx`: especifica el índice del flujo de datos deseado que va a ejecutar el paquete
 - `secrets`: diccionario del almacén de secretos (clave: secretId, valor: secreto almacenado)
 - `spark`: proporciona una sesión de spark para la ejecución de spark

#### <a name="input"></a>Entrada

Una entrada de la matriz de 2 dimensiones ("matriz de matrices"). En Python, la entrada debe ser una lista de listas. Puesto que JSON no tiene un tipo de fecha nativo, los objetos datetime.datetime de Python se convertirán en a cadenas de fecha con formato ISO. Para los puntos de conexión REST, la entrada debe ser una cadena literal JSON que representa una matriz JSON de 2 dimensiones.

#### <a name="output"></a>Salida

Una trama de datos Pandas o Spark. El tipo de trama de datos está determinado por el marco de trabajo seleccionado en la configuración de ejecución (`.runconfig`). Se puede pasar la trama de datos resultante como entrada al modelo entrenado para su puntuación.
