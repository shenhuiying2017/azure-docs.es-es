---
title: Repositorios de Azure Container Registry | Microsoft Docs
description: "Uso de los repositorios de Azure Container Registry para imágenes de Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: cristyg
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: dd4feff057269ed7106990bb63eed7fcffa2dbec
ms.contentlocale: es-es
ms.lasthandoff: 06/10/2017


---
# <a name="azure-container-registry-repositories"></a>Repositorios de Azure Container Registry

Los registros de contenedor de Azure son compatibles con un gran número de servicios y orquestadores. Para que sea más fácil realizar el seguimiento de los servicios y los agentes de origen desde los que se usa ACR, hemos empezado con el campo de encabezado de Docker del archivo Docker.config.



## <a name="viewing-repositories-in-the-portal"></a>Visualización de repositorios en el portal

Los encabezados de ACR tienen el formato:
```
X-Meta-Source-Client: <cloud>/<service>/<optionalservicename>
```

* cloud: Azure, Azure Stack u otras nubes de Azure específicas del gobierno o el país. Aunque las nubes de gobierno y Azure Stack no se admiten actualmente, este parámetro permite la compatibilidad en el futuro.
* service: nombre del servicio.
* optionalservicename: parámetro opcional para los servicios con subservicios o para especificar una SKU (por ejemplo, las aplicaciones web se corresponden con Azure/app-service/web-apps).

Se recomienda que los orquestadores y los servicios asociados usen valores de encabezado específicos para ayudar a con la telemetría. Si quieren, los usuarios también pueden modificar el valor pasado al encabezado.

A continuación se encuentran los valores que queremos que usen los asociados de ACR para rellenar el campo "X-Meta-Source-Client":

| Nombre de servicio              | Encabezado                                |
| ------------------------- | ------------------------------------- |
| Azure Container Service   | azure/compute/azure-container-service |
| App Service - Web Apps    | azure/app-service/web-apps            |
| App Service - Logic Apps  | azure/app-service/logic-apps          |
| Batch                     | azure/compute/batch                   |
| Cloud Console             | azure/cloud-console                   |
| Functions                 | azure/compute/functions               |
| Internet of Things - Hub  | azure/iot/hub                         |
| HDInsight                 | azure/data/hdinsight                  |
| Jenkins                   | azure/jenkins                         |
| Machine Learning          | azure/data/machile-learning           |
| Service Fabric            | azure/compute/service-fabric          |
| VSTS                      | azure/vsts                            |


## <a name="next-steps"></a>Pasos siguientes
[Más información sobre los registros, y los servicios y orquestadores compatibles](container-registry-intro.md)

