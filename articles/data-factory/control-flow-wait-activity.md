---
title: Actividad Wait en Azure Data Factory | Microsoft Docs
description: "La actividad Wait pausa la ejecución de la canalización para el período especificado."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: shlo
ms.openlocfilehash: 892e61397bb6e54781665f210434a4ab9ccb2632
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2017
---
# <a name="wait-activity-in-azure-data-factory"></a>Actividad Wait en Azure Data Factory
Cuando use una actividad Wait en una canalización, esta espera durante el período de tiempo especificado antes de continuar con la ejecución de actividades sucesivas. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory V1](v1/data-factory-introduction.md).

## <a name="syntax"></a>Sintaxis

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>Propiedades de tipo

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
name | Nombre de la actividad `Wait`. | String | Sí
type | Debe establecerse en **Wait**. | String | Sí
waitTimeInSeconds | El número de segundos que esperará la canalización antes de seguir con el procesamiento. | Entero | Sí

## <a name="example"></a>Ejemplo

> [!NOTE]
> En esta sección se proporcionan definiciones JSON y comandos de PowerShell de ejemplo para ejecutarlas en la canalización. Para ver una guía con instrucciones paso a paso para crear una canalización de Data Factory con definiciones de JSON y Azure PowerShell, consulte [Tutorial: Creación de una factoría de datos y una canalización con PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="pipeline-with-wait-activity"></a>Canalización con actividad Wait
En este ejemplo, la canalización tiene dos actividades: **Until** y **Wait**. La actividad Wait está configurada para que espere durante un segundo. La canalización ejecuta la actividad Web en un bucle con un tiempo de espera de un segundo entre cada ejecución. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>Pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory: 

- [Actividad If Condition](control-flow-if-condition-activity.md)
- [Actividad Execute Pipeline](control-flow-execute-pipeline-activity.md)
- [Actividad For Each](control-flow-for-each-activity.md)
- [Actividad Get Metadata](control-flow-get-metadata-activity.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad Web](control-flow-web-activity.md)
- [Actividad Unit](control-flow-until-activity.md)