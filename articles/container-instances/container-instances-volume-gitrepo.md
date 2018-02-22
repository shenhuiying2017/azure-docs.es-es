---
title: Montaje de un volumen de gitRepo en Azure Container Instances
description: "Más información acerca de cómo montar un volumen de gitRepo para clonar un repositorio de GIT en las instancias de Container Instances"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 9acde9259fcb392458e7b2fa7d3369776978285e
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montaje de un volumen de gitRepo en Azure Container Instances

Obtenga información acerca de cómo montar un volumen de *gitRepo* para clonar un repositorio de GIT en las instancias de Container Instances.

> [!NOTE]
> El montaje de un volumen de *gitRepo* está actualmente restringido a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones en Azure Container Instances](container-instances-quotas.md).

## <a name="gitrepo-volume"></a>Volumen de gitRepo

El volumen de *gitRepo* monta un directorio y clona el repositorio de Git especificado en él en el inicio del contenedor. Al usar un volumen de *gitRepo* en las instancias de Container Instances, puede evitar agregar el código para hacerlo en las aplicaciones.

Al montar un volumen de *gitRepo*, puede establecer tres propiedades para configurar el volumen:

| Propiedad | Obligatorio | DESCRIPCIÓN |
| -------- | -------- | ----------- |
| `repository` | Sí | Dirección URL completa, incluidos `http://` o `https://`, del repositorio de GIT que se va a clonar.|
| `directory` | Sin  | Directorio en el que se debe clonar el repositorio. La ruta de acceso no debe contener "`..`" ni empezar por ello.  Si especifica "`.`", el repositorio se clona en el directorio del volumen. De lo contrario, el repositorio de GIT se clona en un subdirectorio del nombre indicado dentro del directorio del volumen. |
| `revision` | Sin  | Hash de confirmación de la revisión que se va a clonar. Si no se especifica, se clona la revisión de `HEAD`. |

## <a name="mount-a-gitrepo-volume"></a>Montaje de un volumen de gitRepo

Para montar un volumen de *gitRepo* en una instancia de Container Instances, debe realizar la implementación con una [plantilla de Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

En primer lugar, complete la matriz `volumes` en la sección `properties` del grupo de contenedores de la plantilla. A continuación, para cada contenedor del grupo de contenedores en el que quiera montar el volumen de *gitRepo*, rellene la matriz `volumeMounts` en la sección `properties` de la definición del contenedor.

Por ejemplo, la siguiente plantilla de Resource Manager crea un grupo de contenedores que consta de un único contenedor. El contenedor clona dos repositorios de GitHub especificados por los bloques del volumen de *gitRepo*. El segundo volumen incluye propiedades adicionales que especifican un directorio en el que clonar y el hash de confirmación de una revisión específica que se va a clonar.

[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

A continuación, se muestra la estructura del directorio resultante de los dos repositorios clonados definidos en la plantilla anterior:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Para ver un ejemplo de implementación de instancias de contenedor con una plantilla de Azure Resource Manager, consulte [Implementación de grupos de varios contenedores en Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>pasos siguientes

Obtenga información acerca de cómo montar otros tipos de volúmenes en Azure Container Instances:

* [Montaje de un recurso compartido de archivos de Azure en Azure Container Instances](container-instances-volume-azure-files.md)
* [Montaje de un volumen de emptyDir en Azure Container Instances](container-instances-volume-emptydir.md)
* [Montaje de un volumen secreto en Azure Container Instances](container-instances-volume-secret.md)
