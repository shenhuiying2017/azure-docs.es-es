---
title: Supervisión de contenedores en Azure Container Instances
description: Aprenda a supervisar el consumo de los recursos de computación, como la CPU y la memoria, en los contenedores de Azure Container Instances.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: nepeters
ms.openlocfilehash: 814346bd8021b996b64cd7f0311f31b13b32a8c8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Supervisión de los recursos de los contenedores en Azure Container Instances

Azure Monitor proporciona información acerca de los recursos de computación que utilizan las instancias de los contenedores. Utilice Azure Monitor para hacer un seguimiento de la CPU y el uso de la memoria en grupos de contenedores y en contenedores individuales. Esta información sobre el uso de los recursos le ayuda a determinar cuál es la mejor configuración para la CPU y la memoria en los grupos de contenedores.

En este documento, se explica cómo se recopila el uso de la memoria y la CPU en las instancias de contenedores mediante Azure Portal y la CLI de Azure.

> [!IMPORTANT]
> En este momento, las métricas de uso de recursos solamente están disponibles para los contenedores de Linux.
>

## <a name="available-metrics"></a>Métricas disponibles

Azure Monitor dispone de métricas sobre el uso de la **CPU** y la **memoria** en Azure Container Instances. Estas dos métricas están disponibles para grupos de contenedores y contenedores individuales.

Las métricas relacionadas con la CPU se expresan en **millares de núcleos**. Un millar de núcleo corresponde a una milésima (1/1000) de un núcleo de CPU, por lo que 500 millares de núcleos (o 500 m) representan el 50 % del uso de un núcleo de CPU.

Las métricas relacionadas con la memoria se expresan en **bytes**.

## <a name="get-metrics---azure-portal"></a>Obtención de métricas mediante Azure Portal

Cuando se crea un grupo de contenedores, los datos de Azure Monitor están disponibles en Azure Portal. Para ver las métricas de un grupo de contenedores, seleccione el grupo de recursos y después el grupo de contenedores. Aquí puede ver unos gráficos creados previamente sobre el uso de la CPU y la memoria.

![gráfico dual][dual-chart]

Si tiene un grupo formado por varios contenedores, utilice una [dimensión][monitor-dimension] para representar las métricas de cada contenedor. Para crear un gráfico utilizando las métricas de cada contenedor, siga estos pasos:

1. En el menú de navegación izquierdo, seleccione **Supervisión**.
2. Seleccione un grupo de contenedores y una métrica (CPU o memoria).
3. Seleccione el botón de dimensión verde y **Nombre del contenedor**.

![dimensión][dimension]

## <a name="get-metrics---azure-cli"></a>Obtención de métricas mediante la CLI de Azure

La información sobre el uso que hacen las instancias de contenedor de la CPU y la memoria también puede obtenerse mediante la CLI de Azure. En primer lugar, debe utilizar el siguiente comando para obtener el identificador del grupo de contenedores. Sustituya `<resource-group>` por el nombre del grupo de recursos y `<container-group>` por el nombre del grupo de contenedores.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Utilice el comando siguiente para obtener las métricas de uso de la **CPU**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

Utilice el comando siguiente para obtener las métricas de uso de la **memoria**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

En un grupo de varios contenedores, la dimensión `containerName` se puede agregar para devolver esta información de cada contenedor.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la supervisión en Azure, consulte esta [introducción a la supervisión de Azure][azure-monitoring].

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../monitoring-and-diagnostics/monitoring-metric-charts.md#what-are-multi-dimensional-metrics
