---
title: "Actualización de los modelos de Machine Learning con Azure Data Factory | Microsoft Docs"
description: "Describe cómo crear canalizaciones predictivas con Factoría de datos de Azure y Azure Machine Learning"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 502bf8771bf7854755ccd72c7002110f1e25bd40
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="updating-azure-machine-learning-models-using-update-resource-activity"></a>Actualización de los modelos de Azure Machine Learning con la actividad de actualización de recurso

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Actividad de Hive](data-factory-hive-activity.md) 
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución de Batch de Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad Actualizar recurso de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)


> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, vea el artículo acerca de la [actualización de modelos de Machine Learning en Data Factory, versión 2](../update-machine-learning-models.md).

Este artículo complementa el artículo de integración principal Azure Data Factory - Azure Machine Learning: [Creación de canalizaciones predictivas con Azure Machine Learning y Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). Si aún no lo ha hecho, revise el artículo principal antes de leer este artículo. 

## <a name="overview"></a>Información general
Pasado algún tiempo, los modelos predictivos en los experimentos de puntuación de Aprendizaje automático de Azure tienen que volver a entrenarse con nuevos conjuntos de datos de entrada. Después de terminar con el nuevo entrenamiento, tendrá que actualizar el servicio web de puntuación con el modelo de Aprendizaje automático que volvió a entrenar. Los pasos más comunes para habilitar el nuevo entrenamiento y actualizar los modelos de Aprendizaje automático de Azure mediante los servicios web son:

1. Crear un experimento en [Azure ML Studio](https://studio.azureml.net).
2. Cuando esté satisfecho con el modelo, use Azure ML Studio para publicar servicios web para el **experimento de entrenamiento** y el **experimento predictivo o de puntuación**.

En la tabla siguiente se describen los servicios web empleados en este ejemplo.  Consulte [Volver a entrenar modelos de Machine Learning mediante programación](../../machine-learning/machine-learning-retrain-models-programmatically.md) para obtener información detallada.

- **Servicio web de entrenamiento**: recibe datos de entrenamiento y genera modelos entrenados. El resultado del nuevo entrenamiento es un archivo .ilearner en Blob Storage de Azure. El **punto de conexión predeterminado** se crea automáticamente para cuando publique el experimento de entrenamiento como un servicio web. Se pueden crear más puntos de conexión, pero el ejemplo usa solo el predeterminado.
- **Servicio web de puntuación**: recibe ejemplos de datos sin etiqueta y realiza predicciones. El resultado de la predicción puede presentarse en diversas formas, como un archivo .csv o como las filas de una Base de datos de SQL de Azure, dependiendo de la configuración del experimento. El punto de conexión predeterminado se crea automáticamente cuando se publica el experimento predictivo como un servicio web. 

La siguiente imagen muestra la relación entre los puntos de conexión de entrenamiento y de puntuación de Aprendizaje automático de Azure.

![SERVICIOS WEB](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

Puede invocar el **training web service** a través de **actividad de ejecución de lotes de Azure Machine Learning**. La invocación de un servicio web de entrenamiento es lo mismo que invocar un servicio web de Azure Machine Learning (servicio web de puntuación) para puntuar datos. Las secciones anteriores abarcan cómo invocar un servicio web de Azure Machine Learning a partir de una canalización de Azure Data Factory. 

Puede invocar el **scoring web service** a través de **Actividad de recursos de actualización de Aprendizaje automático de Azure** para actualizar el servicio web con el modelo recién entrenado. Los ejemplos siguientes proporcionan definiciones de servicios vinculados: 

## <a name="scoring-web-service-is-a-classic-web-service"></a>El servicio web de puntuación es un servicio web clásico
Si el servicio web de puntuación es un **servicio web clásico**, cree el segundo **punto de conexión no predeterminado y actualizable** mediante Azure Portal. Para conocer los pasos necesarios para ello, consulte el artículo [Creación de puntos de conexión](../../machine-learning/machine-learning-create-endpoint.md). Después de crear el punto de conexión actualizable no predeterminado, realice los siguientes pasos:

* Haga clic en **EJECUCIÓN DE LOTES** para obtener el valor del URI para la propiedad JSON **mlEndpoint**.
* Haga clic en vínculo **ACTUALIZAR RECURSO** para obtener el valor de URI para la propiedad JSON **updateResourceEndpoint**. La clave de API está en la página de punto de conexión (en la esquina inferior derecha).

![punto de conexión actualizable](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

En el ejemplo siguiente se proporciona una definición de JSON de ejemplo para el servicio vinculado de AzureML. El servicio vinculado utiliza apiKey para la autenticación.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

## <a name="scoring-web-service-is-azure-resource-manager-web-service"></a>El servicio web de puntuación es el servicio web Azure Resource Manager 
Si el servicio web es el nuevo tipo de servicio web que expone un punto de conexión de Azure Resource Manager, no es preciso agregar el segundo punto de conexión **no predeterminado**. En el servicio vinculado, **updateResourceEndpoint** tiene el formato: 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

Puede obtener valores para los marcadores de posición en la dirección URL al consultar el servicio web en el [Portal de servicios web Azure Machine Learning](https://services.azureml.net/). El nuevo tipo de punto de conexión del recurso de actualización requiere un token AAD (Azure Active Directory). Especifique **servicePrincipalId** y **servicePrincipalKey**en el servicio vinculado AzureML. Consulte [Uso del portal para crear una aplicación de Active Directory y una entidad de servicio con acceso a los recursos](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Esta es la definición de un servicio vinculado AzureML de ejemplo: 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

El escenario siguiente proporciona más detalles. Tiene un ejemplo para volver a entrenar y actualizar modelos de Azure Machine Learning a partir de una canalización de Azure Data Factory.

## <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Escenario: entrenamiento y actualización de un modelo de Aprendizaje automático de Azure
Esta sección proporciona una canalización de ejemplo que usa la **actividad Ejecución de lotes de Azure Machine Learning** para volver a entrenar un modelo. La canalización usa también la **actividad Actualizar recurso de Azure Machine Learning** para actualizar el modelo en el servicio web de puntuación. La sección también proporciona fragmentos JSON para todos los servicios vinculados, conjuntos de datos y canalización en el ejemplo.

Esta es la vista de diagrama de la canalización de ejemplo. Como se puede apreciar, la actividad Ejecución de lotes de Azure Machine Learning toma la entrada de entrenamiento y genera un resultado de entrenamiento (archivo iLearner). La Actividad de recursos de actualización de Aprendizaje automático de Azure toma este resultado de entrenamiento y actualiza el modelo en el punto de conexión de servicio web de puntuación. La Actividad de recursos de actualización no produce ningún resultado. El placeholderBlob es simplemente un conjunto de datos de salida ficticio que el servicio de Azure Data Factory necesita para ejecutar la canalización.

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

### <a name="azure-blob-storage-linked-service"></a>Servicio vinculado de almacenamiento de blobs de Azure:
Azure Storage contiene los siguientes datos:

* datos de aprendizaje. Los datos de entrada para el servicio web de entrenamiento de Azure Machine Learning.  
* archivo iLearner. La salida del servicio web de entrenamiento de Azure Machine Learning. Este archivo también es la entrada para la actividad Actualizar recurso.  

Esta es la definición de JSON de ejemplo del servicio vinculado:

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

### <a name="training-input-dataset"></a>Conjunto de datos de entrada de entrenamiento:
El siguiente conjunto de datos representa los datos de entrenamiento de entrada para el servicio web de entrenamiento de Aprendizaje automático de Azure. La actividad de ejecución de lotes de Azure Machine Learning toma este conjunto de datos como entrada.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

### <a name="training-output-dataset"></a>Conjunto de datos de salida de entrenamiento:
El siguiente conjunto de datos representa el archivo iLearner de salida del servicio web de entrenamiento de Aprendizaje automático de Azure. La actividad de ejecución de lotes de Azure Machine Learning produce este conjunto de datos. Este conjunto de datos es también la entrada para la Actividad de recursos de actualización de Aprendizaje automático de Azure.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

### <a name="linked-service-for-azure-ml-training-endpoint"></a>Servicio vinculado para el punto de conexión de entrenamiento de Aprendizaje automático de Azure
El siguiente fragmento JSON define un servicio vinculado de Azure Machine Learning que apunta al punto de conexión predeterminado del servicio web de entrenamiento.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

En **Azure ML Studio**, haga lo siguiente para obtener los valores de **mlEndpoint** y **apiKey**:

1. Haga clic en **SERVICIOS WEB** en el menú de la izquierda.
2. En la lista de servicios web, haga clic en el **servicio web de entrenamiento** .
3. Haga clic en Copiar junto al cuadro de texto **Clave de API** . Pegue la clave del portapapeles en el editor de JSON de Data Factory.
4. En **Azure ML Studio**, haga clic en el vínculo **EJECUCIÓN DE LOTES**.
5. Copie el **URI de solicitud** de la sección **Solicitar** y péguelo en el editor de JSON de Data Factory.   

### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Servicio vinculado para el punto de conexión de puntuación actualizable de Aprendizaje automático de Azure:
El siguiente fragmento JSON define un servicio vinculado de Azure Machine Learning que apunta al punto de conexión no predeterminado y actualizable del servicio web de puntuación.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

### <a name="placeholder-output-dataset"></a>Conjunto de datos de salida de marcador de posición:
La actividad Actualizar recurso de Azure Machine Learning no genera ningún resultado. Sin embargo, Azure Data Factory requiere un conjunto de datos de salida para impulsar la programación de una canalización. Por lo tanto, utilizamos un conjunto de datos ficticio o un marcador de posición en este ejemplo.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

### <a name="pipeline"></a>Canalización
La canalización tiene dos actividades: **AzureMLBatchExecution** y **AzureMLUpdateResource**. La actividad Ejecución de lotes de Azure Machine Learning toma los datos de entrenamiento como entrada y genera como resultado un archivo iLearner. La actividad invoca el servicio web de entrenamiento (el experimento de entrenamiento expuesto como servicio web) con los datos de entrenamiento de entrada y recibe el archivo ilearner desde el servicio web. El placeholderBlob es simplemente un conjunto de datos de salida ficticio que el servicio de Azure Data Factory necesita para ejecutar la canalización.

![diagrama de canalización](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```
