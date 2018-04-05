---
title: 'Transformación de datos con cuadernos de Databricks: Azure | Microsoft Docs'
description: Aprenda a procesar o transformar datos mediante la ejecución de blocs de notas de Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: d4a57e45d5ddf55906fcf575df39135a227418ec
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformación de datos mediante la ejecución de blocs de notas de Databricks

La actividad Notebook de Azure Databricks en una [canalización de Data Factory](concepts-pipelines-activities.md) ejecuta un bloc de notas de Databricks en el área de trabajo de Azure Databricks. Este artículo se basa en el artículo sobre [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas. Azure Databricks es una plataforma administrada para ejecutar Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definición de la actividad Notebook de Databricks

Esta es la definición de JSON de ejemplo de una actividad Notebook de Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propiedades de la actividad Notebook de Databricks

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON:

|Propiedad|DESCRIPCIÓN|Obligatorio|
|---|---|---|
|Nombre|Nombre de la actividad en la canalización.|Sí|
|Descripción|Texto que describe para qué se usa la actividad.|Sin |
|Tipo|Para la actividad Notebook de Databricks, el tipo de actividad es DatabricksNotebook.|Sí|
|linkedServiceName|Nombre del servicio vinculado de Databricks en el que se ejecuta el bloc de notas de Databricks. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos).|Sí|
|notebookPath|La ruta de acceso absoluta del cuaderno que se va a ejecutar en el área de trabajo de Databricks. Esta ruta de acceso debe comenzar con una barra diagonal.|Sí|
|baseParameters|Una matriz de pares de clave y valor. Se pueden utilizar parámetros base para cada ejecución de actividad. Si el cuaderno toma un parámetro que no se ha especificado, se usará el valor predeterminado del cuaderno. Encuentre más información sobre los parámetros en los [cuadernos de Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Sin |
