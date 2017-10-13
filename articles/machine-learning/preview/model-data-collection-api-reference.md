---
title: "Referencia sobre la API de recopilación de datos de modelos de Azure Machine Learning | Microsoft Docs"
description: "Referencia sobre la API de recopilación de datos de modelos de Azure Machine Learning."
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 7a0fda8a44d13bcaba84b4124d9b693c05874154
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Referencia sobre la API de recopilación de datos de modelos de Azure Machine Learning

La recopilación de datos del modelo permite archivar entradas del modelo y predicciones de un servicio web de aprendizaje automático. Consulte la [guía de procedimientos de recopilación de datos de modelos](how-to-use-model-data-collection.md) para aprender a instalar `azureml.datacollector` en la máquina Windows y Linux.

En esta guía de referencia de la API, se usa un enfoque paso a paso sobre cómo recopilar entradas de modelo y predicciones de un servicio web de aprendizaje automático.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Habilitamiento de la recopilación de datos de modelos en el entorno de Azure Machine Learning Workbench

 Busque el archivo conda\_dependencies.yml en el proyecto en la carpeta aml_config y haga que incluya el módulo azureml.datacollector en la sección pip tal y como se muestra a continuación.\_ Tenga en cuenta que esto es solo una subsección de un archivo conda\_dependencies.yml completo:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>Actualmente, puede usar el módulo de recopilador de datos en Azure Machine Learning Workbench mediante la ejecución en modo de Docker. El modo local no funciona en todos los entornos.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Habilitamiento de la recopilación de datos de modelo en el archivo de puntuaciones

En el archivo de puntuaciones que se utiliza para la operacionalización, importe el módulo del recopilador de datos y la clase ModelDataCollector:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Creación de instancias del recopilador de datos de modelos
Cree una nueva instancia de una clase ModelDataCollector:

dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Vea los detalles sobre las clases y parámetros:

### <a name="class"></a>Clase
| Nombre | Descripción |
|--------------------|--------------------|
| ModelDataCollector | Una clase en el espacio de nombres de azureml.datacollector. Una instancia de esta clase se utilizará para recopilar los datos del modelo. Un único archivo de puntuaciones puede contener varios ModelDataCollectors. Cada instancia debe utilizarse para recopilar datos en una ubicación discreta en el archivo de puntuaciones de forma que el esquema de los datos recopilados siga siendo coherente, (es decir, las entradas y la predicción)|


### <a name="parameters"></a>parameters

| Nombre | Tipo | Descripción |
|-------------|------------|-------------------------|
| model_name | cadena | el nombre del modelo para el cual se están recopilando los datos |
| identificador | cadena | la ubicación en el código que identifica estos datos, es decir "RawInput" o "Prediction" |
| feature_names | lista de cadenas | una lista de nombres de características que se convierte en el encabezado del archivo csv cuando se proporcionan |
| model_management_account_id | cadena | el identificador de la cuenta de administración de modelos donde se almacena este modelo. Este se rellenará automáticamente si los modelos se operacionalizan a través de AML |
| webservice_name | cadena | el nombre del servicio web en el que está implementado este modelo actualmente. Este se rellenará automáticamente si los modelos se operacionalizan a través de AML |
| model_id | cadena | El identificador único para este modelo en el contexto de una cuenta de administración de modelos. Este se rellenará automáticamente si los modelos se operacionalizan a través de AML |
| model_version | cadena | el número de versión de este modelo en el contexto de una cuenta de administración de modelos. Este se rellenará automáticamente si los modelos se operacionalizan a través de AML |



 

## <a name="collecting-the-model-data"></a>Recopilación de los datos del modelo

Puede recopilar los datos del modelo mediante una instancia de la clase ModelDataCollector que se creó anteriormente.

    dc.collect(input_data, user_correlation_id="")

Vea los detalles sobre el método y los parámetros:

### <a name="method"></a>Método
| Nombre | Descripción |
|--------------------|--------------------|
| collect | Sirve para recopilar los datos de una entrada de modelo o predicción|


### <a name="parameters"></a>parameters

| Nombre | Tipo | Descripción |
|-------------|------------|-------------------------|
| input_data | varios tipos | los datos que se van a recopilar (actualmente acepta la lista de tipos, numpy.array, pandas.DataFrame, pyspark.sql.DataFrame). Para los tipos dataframe, si existe un encabezado con nombres de características, esta información se incluirá en el destino de los datos (sin necesidad de pasar los nombres de las características de manera explícita en el constructor de ModelDataCollector) |
| user_correlation_id | cadena | un identificador de correlación opcional, que puede proporcionar el usuario para poner en correlación esta predicción |

