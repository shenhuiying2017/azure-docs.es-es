---
title: "Guía completa sobre cómo usar la API de ejecución de preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona información detallada sobre cómo ejecutar paquetes de preparación de datos y de orígenes de datos previamente diseñados"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 36814d238aabd12e7cc6947809c135130002eb46
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Ejecución de paquetes de preparación de datos y de orígenes de datos desde Python

Para utilizar un paquete de orígenes de datos de Azure Machine Learning o de preparación de datos de Azure Machine Learning en Python:

1. Vaya a la pestaña **Datos** del proyecto.

2. Haga clic con el botón derecho en el origen adecuado.

3. Seleccione **Generate Data Access Code File** (Generar archivo de código de acceso a datos).

Con esta acción se crea un breve script de Python que ejecuta el paquete y devuelve una trama de datos.

## <a name="data-sources"></a>Orígenes de datos

El módulo `azureml.dataprep.datasource` contiene una sola función para ejecutar un origen de datos y devolver una trama de datos: `load_datasource(path, secrets=None, spark=None)`.
- `path` es la ruta de acceso al origen de datos (archivo .dsource).
- `secrets` es un diccionario opcional que asigna claves a secretos.
- `spark` es un booleano opcional que especifica si se debe devolver una trama de datos de Spark o una trama de datos de Pandas. De forma predeterminada, Azure Machine Learning Workbench determina qué tipo de trama de datos se va a devolver en tiempo de ejecución en función del contexto.

## <a name="data-preparations-packages"></a>Paquetes de preparación de datos

El módulo `azureml.dataprep.package` contiene tres funciones que ejecutan un flujo de datos de un paquete de preparación de datos.

### <a name="execution-functions"></a>Funciones de ejecución

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` envía el flujo de datos especificado para su ejecución, pero no devuelve ninguna trama de datos.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` ejecuta el flujo de datos especificado y devuelve los resultados en forma de trama de datos.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` ejecuta el flujo de datos especificado en función de un origen de datos en memoria y devuelve los resultados en forma de trama de datos. El argumento `user_config` es un diccionario que asigna la ruta de acceso absoluta de un origen de datos (archivo .dsource) a un origen de datos en memoria representado en forma de lista de listas.

### <a name="common-arguments"></a>Argumentos comunes

- `package_path` es la ruta de acceso al paquete de preparación de datos (archivo .dprep).
- `dataflow_idx` es el índice de base cero del flujo de datos del paquete que se va a ejecutar. Si el flujo de datos especificado hace referencia a otros flujos de datos o a otros orígenes de datos, también se ejecutarán.
- `secrets` es un diccionario opcional que asigna claves a secretos.
- `spark` es un booleano opcional que especifica si se debe devolver una trama de datos de Spark o una trama de datos de Pandas. De forma predeterminada, Workbench determina qué tipo de trama de datos se va a devolver en tiempo de ejecución en función del contexto.
