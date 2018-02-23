---
title: Montaje de un volumen de emptyDir en Azure Container Instances
description: "Más información sobre cómo montar un volumen emptyDir para compartir datos entre los contenedores de un grupo de contenedores de Azure Container Instances"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 01b44f357d160a48852ee843cd8bebd138e95dfc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Montaje de un volumen de emptyDir en Azure Container Instances

Aprenda a montar un volumen *emptyDir* para compartir datos entre los contenedores de un grupo de contenedores en Azure Container Instances.

> [!NOTE]
> El montaje de un volumen *emptyDir* está actualmente restringido a los contenedores de Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones en Azure Container Instances](container-instances-quotas.md).

## <a name="emptydir-volume"></a>Volumen emptyDir

El volumen *emptyDir* ofrece un directorio de escritura accesible para cada contenedor de un grupo de contenedores. Los contenedores del grupo pueden leer y escribir los mismos archivos en el volumen, que puede montarse mediante las mismas o distintas rutas de acceso en cada contenedor.

Algunos ejemplos de uso de un volumen *emptyDir*:

* Espacio de desecho
* Punto de control durante las tareas de ejecución prolongada
* Almacenamiento de datos recuperados por un contenedor sidecar y atendidos por un contenedor de aplicación

Los datos incluidos en un volumen *emptyDir* se conservan a través de bloqueos de contenedor. Pero no se garantiza que se conserven los datos de un volumen *emptyDir* en los contenedores que se reinicien.

## <a name="mount-an-emptydir-volume"></a>Montaje de un volumen emptyDir

Para montar un volumen emptyDir en una instancia de un contenedor, hay que realizar la implementación con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

En primer lugar, rellene la matriz `volumes` en la sección `properties` del grupo de contenedores de la plantilla. Luego, para cada contenedor del grupo de contenedores en el que quiera montar el volumen *emptyDir*, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor.

Por ejemplo, la siguiente plantilla de Resource Manager crea un grupo de contenedores que consta de dos contenedores y en cada uno de ellos se monta el volumen *emptyDir*:

[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Para ver un ejemplo de implementación de instancias de contenedor con una plantilla de Azure Resource Manager, consulte [Implementación de grupos de varios contenedores en Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>pasos siguientes

Aprenda a montar otros tipos de volúmenes en Azure Container Instances:

* [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](container-instances-volume-azure-files.md)
* [Montaje de un volumen de gitRepo en Azure Container Instances](container-instances-volume-gitrepo.md)
* [Montaje de un volumen secreto en Azure Container Instances](container-instances-volume-secret.md)