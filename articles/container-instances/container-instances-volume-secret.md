---
title: Montaje de un volumen secreto en Azure Container Instances
description: "Más información acerca de cómo montar un volumen secreto para almacenar información confidencial para que accedan a ella las instancias de Container Instances"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 6f8e1b6faac11b668a143f8013a198831a428c51
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montaje de un volumen secreto en Azure Container Instances

Obtenga información acerca de cómo montar un volumen *secreto* en las instancias de contenedor para el almacenamiento y la recuperación de información confidencial por parte de los contenedores de los grupos de contenedores.

> [!NOTE]
> El montaje de un volumen *secreto* está actualmente restringido a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones en Azure Container Instances](container-instances-quotas.md).

## <a name="secret-volume"></a>volumen secreto

Puede usar un volumen *secreto* para proporcionar información confidencial a los contenedores de un grupo de contenedores. El volumen *secreto* almacena los secretos especificados en los archivos del volumen, al que pueden tener acceso los contenedores del grupo de contenedores. Mediante el uso de los secretos de un volumen *secreto*, puede evitar colocar datos confidenciales, como claves SSH o credenciales de la base de datos, en el código de la aplicación.

Todos los volúmenes *secreto* están respaldados por [tmpfs][tmpfs], un sistema de archivos basado en RAM; su contenido nunca se escribe en el almacenamiento no volátil.

## <a name="mount-a-secret-volume"></a>Montaje de un volumen secreto

Para montar un volumen *secreto* en una instancia del contenedor, debe realizar la implementación con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

En primer lugar, complete la matriz `volumes` en la sección `properties` del grupo de contenedores de la plantilla. A continuación, para cada contenedor del grupo de contenedores en el que quiera montar el volumen *secreto*, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor.

Por ejemplo, la siguiente plantilla de Resource Manager crea un grupo de contenedores que consta de un único contenedor. El contenedor monta un volumen *secreto* que consta de dos secretos con codificación Base64.

[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Para ver un ejemplo de implementación de instancias de contenedor con una plantilla de Azure Resource Manager, consulte [Implementación de grupos de varios contenedores en Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>pasos siguientes

Obtenga información acerca de cómo montar otros tipos de volúmenes en Azure Container Instances:

* [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](container-instances-volume-azure-files.md)
* [Montaje de un volumen de emptyDir en Azure Container Instances](container-instances-volume-emptydir.md)
* [Montaje de un volumen de gitRepo en Azure Container Instances](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs