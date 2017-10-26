---
title: "Supervisión de un clúster de Azure Kubernetes con Datadog | Microsoft Docs"
description: "Supervisión de un clúster de Kubernetes en Azure Container Service utilizando Datadog"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: b2f88f663745f1caef856e9b2b5803000c284cb9
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-datadog"></a>Supervisión de un clúster de Azure Container Service con DataDog

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>Requisitos previos
En este tutorial se da por supuesto que ha [creado un clúster de Kubernetes con Azure Container Service](container-service-kubernetes-walkthrough.md).

También se da por supuesto que tiene la CLI de Azure `az` y las herramientas de `kubectl` instaladas.

Puede probar si tiene la herramienta `az` instalada mediante la ejecución de:

```console
$ az --version
```

Si no tiene la herramienta `az` instalada, se ofrecen instrucciones [aquí](https://github.com/azure/azure-cli#installation).

Puede probar si tiene la herramienta `kubectl` instalada mediante la ejecución de:

```console
$ kubectl version
```

Si no tiene la herramienta `kubectl` instalada, puede ejecutar:

```console
$ az acs kubernetes install-cli
```

## <a name="datadog"></a>Datadog
Datadog es un servicio de supervisión que recopila datos de supervisión de los contenedores dentro del clúster del servicio de contenedor de Azure. Datadog tiene un panel de integración de Docker donde puede ver las métricas específicas dentro de los contenedores. Las métricas que se recopilan de los contenedores están organizadas por CPU, memoria, red y E/S. Datadog divide las métricas en contenedores e imágenes.

Primero debe [crear una cuenta](https://www.datadoghq.com/lpg/)

## <a name="installing-the-datadog-agent-with-a-daemonset"></a>Instalación del agente de Datadog con un DaemonSet
Kubernetes utiliza DaemonSets para ejecutar una única instancia de un contenedor en cada host del clúster.
Son perfectos para ejecutar agentes de supervisión.

Una vez que ha iniciado sesión en Datadog, puede seguir las [Instrucciones de Datadog](https://app.datadoghq.com/account/settings#agent/kubernetes) para instalar los agentes de Datadog en el clúster mediante un DaemonSet.

## <a name="conclusion"></a>Conclusión
Eso es todo. Una vez que los agentes están en funcionamiento debería ver datos en la consola en unos minutos. Puede visitar el [panel de Kubernetes](https://app.datadoghq.com/screen/integration/kubernetes) integrado para ver un resumen del clúster.
