---
title: "Creación de canalizaciones de datos predictivas con Azure Data Factory | Microsoft Docs"
description: "Describe cómo crear canalizaciones predictivas con Factoría de datos de Azure y Azure Machine Learning"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 05ae7cdc78e909c9aaa2b690d03eff8da09b6242
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-and-azure-data-factory"></a>Creación de canalizaciones predictivas con Azure Machine Learning y Azure Data Factory

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

## <a name="introduction"></a>Introducción
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, que se encuentra en versión preliminar, vea el artículo acerca de la [transformación de datos mediante Machine Learning en Data Factory, versión 2](../transform-data-using-machine-learning.md).


### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) permite compilar, probar e implementar soluciones de análisis predictivo. Desde una perspectiva general, esto se realiza en tres pasos:

1. **Crear un experimento de entrenamiento**. Este paso se lleva a cabo mediante Azure ML Studio. ML Studio es un entorno de desarrollo visual de colaboración que se emplea para entrenar y probar un modelo de análisis predictivo con datos de entrenamiento.
2. **Convertirlo en un experimento predictivo**. Una vez que el modelo se ha entrenado con datos existentes y está listo para usarlo para puntuar nuevos datos, debe preparar y simplificar el experimento para la puntuación.
3. **Implementarlo como un servicio web**. Puede publicar el experimento de puntuación como un servicio web de Azure. Los usuarios pueden enviar datos al modelo a través de este punto de conexión de servicio web y recibir las predicciones de resultado para el modelo.  

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory es un servicio de integración de datos basado en la nube que organiza y automatiza el **movimiento** y la **transformación** de los datos. Con Azure Data Factory se pueden crear soluciones de integración de datos que pueden ingerir datos de distintos almacenes de datos, transformarlos y procesarlos, y publicar los datos resultantes en los almacenes de datos.

El servicio Data Factory permite crear canalizaciones de datos que mueven y transforman datos y, después, ejecutar los procesos según una programación especificada (cada hora, diariamente, semanalmente, etc.). También proporciona excelentes visualizaciones para mostrar el linaje y las dependencias entre las canalizaciones de datos y para poder supervisar todas las canalizaciones de datos desde una única vista unificada, con el fin de facilitar la identificación de los problemas y la configuración de alertas de supervisión.

Consulte los artículos [Introducción a Azure Data Factory](data-factory-introduction.md) y [Creación de la primera canalización de datos](data-factory-build-your-first-pipeline.md) para empezar a trabajar rápidamente con el servicio Azure Data Factory.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory y Machine Learning juntos
Azure Data Factory permite crear fácilmente canalizaciones que utilizan un servicio web de [Azure Machine Learning][azure-machine-learning] publicado para realizar análisis predictivos. Mediante la **actividad de ejecución de lotes** en una canalización de Azure Data Factory, puede invocar un servicio web de Azure Machine Learning para realizar predicciones sobre los datos en el lote. Consulte la sección [Invocación de un servicio web de Azure Machine Learning mediante la actividad de ejecución de lotes](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) para obtener detalles.

Pasado algún tiempo, los modelos predictivos en los experimentos de puntuación de Aprendizaje automático de Azure tienen que volver a entrenarse con nuevos conjuntos de datos de entrada. Puede volver a entrenar un modelo de Aprendizaje automático de Azure de una canalización de Factoría de datos realizando los pasos siguientes:

1. Publicar el experimento de entrenamiento (experimento no predictivo) como un servicio web. Tiene que llevar a cabo este paso en Azure ML Studio, tal como hizo para exponer el experimento predictivo como un servicio web en el escenario anterior.
2. Usar la actividad de ejecución de lotes de Azure Machine Learning para invocar el servicio web para el experimento de entrenamiento. Básicamente, puede emplear la actividad de ejecución de lotes de Azure Machine Learning para invocar el servicio web de aprendizaje y el servicio web de puntuación.

Cuando haya terminado el reciclaje, actualice el servicio web de puntuación (experimento predictivo expuesto como servicio web) con el modelo recién entrenado mediante la **actividad de recursos de actualización de Azure Machine Learning**. Para obtener más información, consulte el artículo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Actualización de modelos mediante la actividad de recursos de actualización).

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Invocación de un servicio web mediante la actividad de ejecución de lotes
Factoría de datos de Azure se usa para coordinar el procesamiento y el movimiento de datos y, posteriormente, realizar la ejecución por lotes mediante Azure Machine Learning. Estos son los pasos de nivel superior:

1. Cree un servicio vinculado de Azure Machine Learning. Necesita los siguientes valores:

   1. **URI de solicitud** para la API Ejecución de lotes. Para encontrar el URI de solicitud, haga clic en el vínculo **EJECUCIÓN DE LOTES** en la página de servicios web.
   2. **Clave de API** para el servicio web Azure Machine Learning publicado. Para encontrar la clave de API, haga clic en el servicio web que ha publicado.
   3. Use la actividad **AzureMLBatchExecution** .

      ![Panel de Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![URI del lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Escenario: Experimentos mediante entradas y salidas de servicios web que hacen referencia a datos de Azure Blob Storage
En este escenario, el servicio web de Azure Machine Learning realiza predicciones mediante datos de un archivo de un almacenamiento de blobs de Azure y almacena los resultados de predicción en el almacenamiento de blobs. El siguiente JSON define una canalización de Data Factory con una actividad AzureMLBatchExecution. La actividad tiene el conjunto de datos **DecisionTreeInputBlob** como entrada y **DecisionTreeResultBlob** como salida. **DecisionTreeInputBlob** se pasa como entrada al servicio web mediante la propiedad JSON **webServiceInput**. **DecisionTreeResultBlob** se pasa como salida al servicio web mediante la propiedad JSON **webServiceOutputs**.  

> [!IMPORTANT]
> Si el servicio web toma varias entradas, use la propiedad **webServiceInputs** en lugar de usar **webServiceInput**. Consulte la sección [El servicio web requiere varias entradas](#web-service-requires-multiple-inputs) para ver un ejemplo de cómo usar la propiedad webServiceInputs.
>
> Los conjuntos de datos a los que hacen referencia las propiedades **webServiceInput**/**webServiceInputs** y **webServiceOutputs** (en **typeProperties**) también se deben incluir en las **entradas** y las **salidas** de la actividad.
>
> En el experimento de Azure ML, la entrada del servicio web y los puertos de salida y parámetros globales tienen nombres predeterminados (input1 e input2) que se pueden personalizar. Los nombres que se utilizan para la configuración de globalParameters, webServiceOutputs y webServiceInputs deben coincidir exactamente con los de los experimentos. Puede ver la carga útil de la solicitud de ejemplo en la página de ayuda de ejecución de lotes del punto de conexión de Azure Machine Learning para comprobar la asignación esperada.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }                
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> Solo las entradas y salidas de la actividad AzureMLBatchExecution pueden pasarse como parámetros al servicio web. Por ejemplo, en el fragmento JSON anterior, DecisionTreeInputBlob es una entrada a la actividad AzureMLBatchExecution, que se pasa como entrada al servicio web mediante el parámetro webServiceInput.   
>
>

### <a name="example"></a>Ejemplo
Este ejemplo utiliza Azure Storage para almacenar los datos de entrada y salida.

Se recomienda que siga el tutorial [Compilación de la primera canalización con Data Factory][adf-build-1st-pipeline] antes de llevar a cabo este ejemplo. Utilice el editor de Data Factory para crear artefactos de Data Factory (servicios vinculados, conjuntos de datos, canalización) en este ejemplo.   

1. Cree un **servicio vinculado** para su instancia de **Azure Storage**. Si los archivos de entrada y salida están en diferentes cuentas de almacenamiento, necesita dos servicios vinculados. Este es un ejemplo de JSON:

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. Cree el **conjunto de datos** de **entrada** de Azure Data Factory. A diferencia de otros conjuntos de datos de Data Factory, estos deben contener tanto los valores de **folderPath** como de **fileName**. Puede utilizar la creación de particiones para hacer que cada ejecución de lotes (cada segmento de datos) se procese o produzca archivos de entrada y salida únicos. Probablemente necesitará incluir alguna actividad ascendente para transformar la entrada en el formato de archivo CSV y colocarlo en la cuenta de almacenamiento para cada segmento. En ese caso, no incluiría la configuración de **external** y **externalData** que se muestra en el ejemplo siguiente, y su valor de DecisionTreeInputBlob sería el conjunto de datos de salida de una actividad diferente.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
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

    Su archivo CSV de entrada debe tener la fila de encabezado de columna. Si está usando la **actividad de copia** para crear o mover el archivo CSV a Blob Storage, debe establecer la propiedad **blobWriterAddHeader** del receptor en **true**. Por ejemplo: 

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    Si el archivo CSV no tiene la fila de encabezado, es posible que vea el siguiente error: **Error en actividad: error de lectura de la cadena. Token inesperado: StartObject. Path '', line 1, position 1**.
3. Cree el **conjunto de datos** de **salida** de Azure Data Factory. En este ejemplo se usa la creación de particiones para crear una ruta de acceso de salida única para cada ejecución de segmento. Sin la creación de particiones, la actividad sobrescribiría el archivo.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. Cree un **servicio vinculado** de tipo **AzureMLLinkedService**; para ello, proporcione la clave de API y la URL de ejecución de lotes.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. Por último, cree una canalización que contenga una actividad **AzureMLBatchExecution** . En tiempo de ejecución, la canalización lleva a cabo los pasos siguientes:

   1. Obtiene la ubicación del archivo de entrada de los conjuntos de datos de entrada.
   2. Invoca a la API de ejecución de lotes de Azure Machine Learning.
   3. Copia el resultado de la ejecución por lotes en el blob proporcionado en el conjunto de datos de salida.

      > [!NOTE]
      > La actividad AzureMLBatchExecution puede tener cero o más entradas y una o más salidas.
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      Las fechas y horas de inicio (**start**) y de finalización (**end**) deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de la propiedad **end** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx) .

      > [!NOTE]
      > La especificación de la entrada para la actividad AzureMLBatchExecution es opcional.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Escenario: Experimentos mediante módulos Lector y Escritor para hacer referencia a datos de varios almacenamientos
Otro escenario común al crear experimentos de Aprendizaje automático de Azure es usar módulos Lector y Escritor. El módulo lector se usa para cargar datos en un experimento y el módulo escritor para guardar los datos de los experimentos. Para obtener información sobre los módulos lector y escritor, consulte los temas [Lector](https://msdn.microsoft.com/library/azure/dn905997.aspx) y [Escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library.     

Al usar los módulos lector y escritor, es recomendable emplear un parámetro de servicio web para cada propiedad de estos módulos. Estos parámetros web permiten configurar los valores en tiempo de ejecución. Por ejemplo, podría crear un experimento con un módulo lector que usa Azure SQL Database: XXX.database.windows.net. Una vez implementado el servicio web, quiere habilitar los consumidores del servicio web con el fin de especificar otro servidor Azure SQL Server denominado YYY.database.windows.net. Puede usar un parámetro de servicio web para permitir que se configure este valor.

> [!NOTE]
> Las entradas y salidas de servicio web son diferentes de los parámetros de servicio web. En el primer escenario, ha visto cómo pueden especificarse una entrada y una salida para un servicio web de Aprendizaje automático de Azure. En este escenario, se pasan parámetros para un servicio web que corresponden a las propiedades de los módulos lector y escritor.
>
>

Echemos un vistazo a un escenario de uso de parámetros de servicio web. Tiene un servicio web implementado de Azure Machine Learning que usa un módulo lector para leer datos de uno de los orígenes de datos admitidos por Azure Machine Learning (por ejemplo: Azure SQL Database). Después de realizar la ejecución de lotes, los resultados se escriben con un módulo escritor (Azure SQL Database).  No hay entradas ni salidas de servicio web definidas en los experimentos. En este caso, se recomienda que configure los parámetros de servicio web pertinentes para los módulos lector y escritor. De esta forma, se podrán configurar los módulos lector y escritor cuando se use la actividad AzureMLBatchExecution. Los parámetros de servicio web se especifican en la sección **globalParameters** de la actividad JSON como se indica a continuación.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

También puede usar [Funciones de Factoría de datos](data-factory-functions-variables.md) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Uso de un módulo lector para leer datos de varios archivos de blob de Azure
La canalización de macrodatos con actividades como Pig y Hive puede generar uno o varios archivos de salida sin extensiones. Por ejemplo, cuando se especifica una tabla externa de Hive, los datos de dicha tabla se pueden almacenar en el almacenamiento de blobs de Azure con el siguiente nombre 000000_0. Puede usar el módulo lector en un experimento para leer varios archivos y usarlos para realizar predicciones.

Al usar el módulo lector en un experimento de Azure Machine Learning, puede especificar Blob de Azure como entrada. Los archivos en Blob Storage de Azure pueden ser los archivos de salida (por ejemplo, 000000_0) que se generan mediante un script de Pig y Hive en HDInsight. El módulo de lector permite leer archivos (sin extensiones) mediante la configuración de la propiedad **Path to container, directory/blob**(Ruta de acceso al contenedor, directorio o blob). La **ruta de acceso al contenedor** apunta al contenedor y el **directorio o blob** apunta a la carpeta que contiene los archivos, tal y como se muestra en la siguiente imagen. Tenga en cuenta que el asterisco (\*) **especifica que todos los archivos de la carpeta o contenedor (es decir, data/aggregateddata/year=2014/month-6/\*)** se leen como parte del experimento.

![Propiedades de Blob de Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>Ejemplo
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Canalización con la actividad AzureMLBatchExecution con parámetros de servicio web

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }              
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

En el ejemplo JSON anterior:

* El servicio web implementado de Azure Machine Learning usa un módulo lector y otro escritor para leer y escribir datos desde y hacia una base de datos SQL de Azure. Este servicio web expone los cuatro parámetros siguientes: nombre de servidor de base de datos, nombre de base de datos, nombre de cuenta de usuario de servidor y contraseña de cuenta de usuario de servidor.  
* Las fechas y horas de inicio (**start**) y de finalización (**end**) deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de la propiedad **end** es opcional. Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9999-09-09** como valor de propiedad **end**. Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](https://msdn.microsoft.com/library/dn835050.aspx) .

### <a name="other-scenarios"></a>Otros escenarios
#### <a name="web-service-requires-multiple-inputs"></a>El servicio web requiere varias entradas
Si el servicio web toma varias entradas, use la propiedad **webServiceInputs** en lugar de usar **webServiceInput**. Los conjuntos de datos a los que hace referencia **webServiceInputs** también deben incluirse en las **entradas** de la actividad.

En el experimento de Azure ML, la entrada del servicio web y los puertos de salida y parámetros globales tienen nombres predeterminados (input1 e input2) que se pueden personalizar. Los nombres que se utilizan para la configuración de globalParameters, webServiceOutputs y webServiceInputs deben coincidir exactamente con los de los experimentos. Puede ver la carga útil de la solicitud de ejemplo en la página de ayuda de ejecución de lotes del punto de conexión de Azure Machine Learning para comprobar la asignación esperada.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Servicio web no requiere una entrada
Los servicios web de ejecución de lotes de Aprendizaje automático de Azure se pueden usar para ejecutar cualquier flujo de trabajo, por ejemplo, scripts R o Python, que puedan no requerir entradas. O bien, el experimento se podría configurar con un módulo Lector que no expone ningún GlobalParameters. En ese caso, la actividad AzureMLBatchExecution se configuraría de la siguiente manera:

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }              
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Servicio web no requiere entrada/salida
El servicio web de ejecución de lotes de Aprendizaje automático de Azure podría no tener configurada ninguna salida de servicio web. En este ejemplo, no hay ninguna entrada o salida de servicio web ni tampoco hay configurado ningún GlobalParameters. Todavía hay una salida configurada en la propia actividad, pero que no se presenta como webServiceOutput.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Servicio web usa lectores y escritores y la actividad solo se ejecuta cuando otras actividades se realizaron correctamente
Los módulos lector y escritor del servicio web de Aprendizaje automático de Azure se podrían configurar para ejecutarse con o sin GlobalParameters. Sin embargo, quizá quiera insertar llamadas de servicio en una canalización que use dependencias del conjunto de datos para invocar al servicio solo cuando se haya completado un procesamiento ascendente. También puede desencadenar otra acción una vez completada la ejecución por lotes con este enfoque. En ese caso, puede expresar las dependencias mediante entradas y salidas de la actividad, sin denominar a ninguna de ellas como entradas o salidas del servicio web.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

Las principales **ideas** obtenidas son:

* Si el punto de conexión del experimento usa una propiedad webServiceInput: se representa con un conjunto de datos de blobs y se incluye en las entradas de la actividad y en la propiedad webServiceInput. De lo contrario, se omite la propiedad webServiceInput.
* Si el punto de conexión del experimento utiliza webServiceOutput(s): se representan con conjuntos de datos de blobs y se incluyen en la salidas de la actividad y en la propiedad webServiceOutputs. Las salidas de la actividad y webServiceOutputs se asignan por el nombre de cada salida en el experimento. De lo contrario, se omite la propiedad webServiceOutputs.
* Si el punto de conexión del experimento expone una o más propiedades globalParameters, se proporcionan en la propiedad globalParameters como pares clave-valor. De lo contrario, se omite la propiedad globalParameters. Las claves distinguen mayúsculas de minúsculas. [Las funciones de Azure Data Factory](data-factory-functions-variables.md) se pueden usar en los valores.
* Es posible incluir conjuntos de datos adicionales en las propiedades de entradas y salidas de la actividad, sin que typeProperties de la actividad haga referencia a ellos. Estos conjunto de datos rigen la ejecución mediante el uso de las dependencias de segmento; de no ser así, la actividad AzureMLBatchExecution los omitirá.


## <a name="updating-models-using-update-resource-activity"></a>Actualización de modelos mediante la actividad de recursos de actualización
Cuando haya terminado el reciclaje, actualice el servicio web de puntuación (experimento predictivo expuesto como servicio web) con el modelo recién entrenado mediante la **actividad de recursos de actualización de Azure Machine Learning**. Para obtener más información, consulte el artículo [Updating models using Update Resource Activity](data-factory-azure-ml-update-resource-activity.md) (Actualización de modelos mediante la actividad de recursos de actualización).

### <a name="reader-and-writer-modules"></a>Módulos Lector y Escritor
Un escenario común para el uso de parámetros de servicio web es el uso de Lectores y escritores SQL de Azure. El módulo Lector se usa para cargar datos en un experimento desde los servicios de administración de datos fuera de Azure Machine Learning Studio. El módulo Escritor sirve para guardar datos desde los experimentos en servicios de administración de datos fuera de Azure Machine Learning Studio.  

Para obtener información acerca del lector o escritor de SQL/blob de Azure, consulte los temas [Lector ](https://msdn.microsoft.com/library/azure/dn905997.aspx)y [Escritor](https://msdn.microsoft.com/library/azure/dn905984.aspx) en MSDN Library. El ejemplo de la sección anterior utilizó el lector de Blob de Azure y el lector de Blob de Azure. En esta sección se trata el uso del lector SQL Azure y el escritor SQL Azure.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
**P:** Tengo varios archivos generados por medio de mis canalizaciones de macrodatos. ¿Puedo usar la actividad AzureMLBatchExecution para trabajar en todos los archivos?

**R:** Sí. Consulte **Uso de un módulo lector para leer datos de varios archivos de blob de Azure** para obtener más información.

## <a name="azure-ml-batch-scoring-activity"></a>Actividad de puntuación de lotes de Azure Machine Learning
Si va a usar la actividad **AzureMLBatchScoring** para la integración con Azure Machine Learning, se recomienda usar la actividad **AzureMLBatchExecution** más reciente.

La actividad AzureMLBatchExecution se introdujo en la versión de agosto de 2015 del SDK de Azure y Azure PowerShell.

Si desea continuar utilizando la actividad AzureMLBatchScoring, siga leyendo esta sección.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Actividad de puntuación de lotes de Azure Machine Learning con Azure Storage para entrada/salida

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Parámetros de servicio web
Para especificar los valores de los parámetros de un servicio web, agregue una sección **typeProperties** a la sección **AzureMLBatchScoringActivty** en el JSON de canalización, tal y como se muestra en el siguiente ejemplo:

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
También puede usar [Funciones de Factoría de datos](data-factory-functions-variables.md) para pasar valores para los parámetros de servicio web como se muestra en el siguiente ejemplo:

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Los parámetros de servicio web distinguen entre mayúsculas y minúsculas para garantizar que los nombres que especifica en JSON de actividad coinciden con los que muestra el servicio web.
>
>

## <a name="see-also"></a>Otras referencias
* [Entrada de blog de Azure: Introducción a la factoría de datos de Azure y a Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
