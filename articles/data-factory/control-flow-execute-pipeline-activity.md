---
title: "Actividad de ejecución de canalización en Azure Data Factory | Microsoft Docs"
description: "Aprenda a usar la actividad de ejecución de canalización para invocar una canalización de Data Factory desde otra canalización de Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: shlo
ms.openlocfilehash: 39f687a4de9a79e88d11e246cd0097dd9346c0ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Actividad de ejecución de canalización en Azure Data Factory
La actividad de ejecución de canalización permite que una canalización de Data Factory invoque otra canalización.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory V1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Sintaxis

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Propiedades de tipo
Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
name | Nombre de la actividad de ejecución de canalización. | String | Sí
type | Se debe establecer en **ExecutePipeline**. | String | Sí
pipeline | Referencia a la canalización dependiente que invoca esta canalización. Un objeto de referencia de canalización tiene dos propiedades: **referenceName** y **type**. La propiedad referenceName especifica el nombre de la canalización de referencia. La propiedad type se debe establecer en PipelineReference. | PipelineReference | Sí
parameters | Parámetros que se deben pasar a la canalización invocada | Objeto JSON que asigna nombres de parámetro a los valores de argumento | No
waitOnCompletion | Define si la ejecución de la actividad espera que finalice la ejecución de la canalización dependiente. | El valor predeterminado es false. | Booleano | No

## <a name="sample"></a>Muestra
Este escenario consta de dos canalizaciones:

- **Canalización principal**. Esta canalización tiene una actividad de ejecución de canalización que llama a la canalización invocada. La canalización principal toma dos parámetros: `masterSourceBlobContainer` y `masterSinkBlobContainer`.
- **Canalización invocada**. Esta canalización tiene una actividad de copia que copia los datos de un origen de Azure Blob a un receptor de Azure Blob. La canalización invocada toma dos parámetros: `sourceBlobContainer` y `sinkBlobContainer`.

### <a name="master-pipeline-definition"></a>Definición de la canalización principal

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobCountainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "datasets": [],
    "linkedServices": [],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Definición de la canalización invocada

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "datasets": [
      {
        "name": "SourceBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sourceBlobContainer",
              "type": "Expression"
            },
            "fileName": "salesforce.txt"
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      },
      {
        "name": "sinkBlobDataset",
        "properties": {
          "type": "AzureBlob",
          "typeProperties": {
            "folderPath": {
              "value": "@pipeline().parameters.sinkBlobContainer",
              "type": "Expression"
            }
          },
          "linkedServiceName": {
            "referenceName": "BlobStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ],
    "linkedServices": [
      {
        "name": "BlobStorageLinkedService",
        "properties": {
          "type": "AzureStorage",
          "typeProperties": {
            "connectionString": {
              "value": "DefaultEndpointsProtocol=https;AccountName=*****",
              "type": "SecureString"
            }
          }
        }
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="running-the-pipeline"></a>Ejecutar la canalización

Para ejecutar la canalización principal de este ejemplo, se pasan los siguientes valores para los parámetros masterSourceBlobContainer y masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

La canalización principal reenvía estos valores a la canalización invocada, como se muestra en el ejemplo siguiente: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory: 

- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad de obtención de metadatos](control-flow-get-metadata-activity.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)