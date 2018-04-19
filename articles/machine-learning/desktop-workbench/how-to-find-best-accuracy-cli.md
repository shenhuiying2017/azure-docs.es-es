---
title: "Búsqueda de ejecuciones con la mejor precisión y la menor duración en Azure Machine Learning Workbench | Microsoft Docs"
description: "Un caso de uso completo para buscar la mejor precisión a través de CLI mediante Azure Machine Learning Workbench"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: bd8888d911730831435b87d3a60b48a7797eea98
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>Búsqueda de ejecuciones con la mejor precisión y la menor duración
Cuando hay varias ejecuciones, un caso de uso es buscar las que tienen la máxima precisión. Un enfoque consiste en usar la interfaz de la línea de comandos (CLI) con una consulta [JMESPath](http://jmespath.org/). Para obtener más información acerca de cómo usar JMESPath en la CLI de Azure, consulte [Uso de consultas JMESPath con CLI de Azure 2.0](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest). En el ejemplo siguiente, se crean cuatro ejecuciones con los siguientes valores de precisión: 0; 0,98; 1 y 1. Las ejecuciones se filtran si están en el intervalo `[MaxAccuracy-Threshold, MaxAccuracy]` donde `Threshold = .03`.

## <a name="sample-data"></a>Datos de ejemplo
Si no hay ejecuciones con un valor `Accuracy`, los pasos siguientes generarán ejecuciones para la realización de consultas.

En primer lugar, cree un archivo de Python en Azure Machine Learning Workbench, asígnele el nombre `log_accuracy.py` y péguelo en el siguiente código:
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

A continuación, cree el archivo `run.py` y péguelo el siguiente código:
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

Por último, abra la CLI con Workbench y ejecute el comando `python run.py` para enviar cuatro experimentos. Al finalizar el script, debería ver cuatro ejecuciones más en la pestaña `Run History`.

## <a name="query-the-run-history"></a>Consultar el historial de ejecuciones
El primer comando busca el valor de máxima precisión.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

Mediante este valor de precisión máxima de `1` y un valor de umbral de `0.03`, el segundo comando filtrará las ejecuciones mediante `Accuracy` y, después, las ordenará por valor de `duration` de forma ascendente.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> Si quiere que la comprobación del límite superior sea estricta, el formato de la consulta debe ser ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``

Si usa PowerShell, el código siguiente usa variables locales para almacenar el umbral y la precisión máxima:
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca del registro, consulte [Cómo usar el historial de ejecución y las métricas de modelo de Azure Machine Learning Workbench](how-to-use-run-history-model-metrics.md).    
