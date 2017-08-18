---
title: Grupos de contenedores de Azure Container Instances
description: "Descripción del funcionamiento de los Grupos de contenedores en Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 5b2abb76fc2bfcf719292db1fdafc8b353d13a3c
ms.contentlocale: es-es
ms.lasthandoff: 08/09/2017

---

# <a name="container-groups-in-azure-container-instances"></a>Grupos de contenedores en Azure Container Instances

El recurso de nivel superior en Azure Container Instances es un grupo de contenedores. Este artículo describe qué son los grupos de contenedores y qué tipos de escenarios permiten.

## <a name="how-a-container-group-works"></a>Cómo funciona un grupo de contenedores

Un grupo de contenedores es una colección de contenedores que se programan en el mismo equipo host y que comparten el ciclo de vida, la red local y los volúmenes de almacenamiento. Es similar al concepto de un *pod* en [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) y [DC/OS](https://dcos.io/docs/1.9/deploying-services/pods/).

El siguiente diagrama muestra un ejemplo de un grupo de contenedores que incluye varios contenedores.

![Ejemplo de grupo de contenedores][container-groups-example]

Observe lo siguiente:

- El grupo está programado en un único equipo host.
- El grupo expone una única dirección IP pública, con un puerto expuesto.
- El grupo se compone de dos contenedores. Un contenedor escucha en el puerto 80, mientras el otro escucha en el puerto 5000.
- El grupo incluye dos recursos compartidos de archivos de Azure como montajes de volumen y cada contenedor monta uno de los recursos compartidos de forma local.

### <a name="networking"></a>Redes

Los grupos de contenedores comparten una dirección IP y un espacio de nombres de puerto en esa dirección IP. Para permitir que los clientes externos lleguen a un contenedor dentro del grupo, debe exponer el puerto en la dirección IP y desde el contenedor. Dado que los contenedores dentro del grupo comparten un espacio de nombres de puerto, no se admite la asignación de puertos. Los contenedores dentro de un grupo pueden comunicar entre sí a través de localhost en los puertos que han expuesto, incluso si estos puertos no se exponen externamente en la dirección IP del grupo.

### <a name="storage"></a>Almacenamiento

Puede especificar volúmenes externos para montar dentro de un grupo de contenedores. Puede asignar los volúmenes en rutas de acceso específicas dentro de los contenedores individuales en un grupo.

## <a name="common-scenarios"></a>Escenarios comunes

Los grupos de múltiples contenedores son útiles en casos donde desea dividir una única tarea funcional en un pequeño número de imágenes de contenedor, que se pueden entregar por distintos equipos y tienen requisitos de recursos independientes. Ejemplos posibles de uso serían:

- Un contenedor de aplicación y un contenedor de registro. El contenedor de registro recopila la salida de registros y métricas de la aplicación principal y los escribe en un almacenamiento a largo plazo.
- Una aplicación y un contenedor de supervisión. Periódicamente, el contenedor de supervisión realiza una solicitud a la aplicación para asegurarse de que se está ejecutando y responde correctamente y genera una alerta si no lo está.
- Un contenedor para servir una aplicación web y un contenedor para extraer el contenido más reciente desde el control de código fuente.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de cómo [implementar un grupo de múltiples contenedores](container-instances-multi-container-group.md) con una plantilla de Azure Resource Manager.

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png
