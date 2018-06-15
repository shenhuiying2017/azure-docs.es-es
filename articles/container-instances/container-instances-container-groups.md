---
title: Grupos de contenedores de Azure Container Instances
description: Descripción del funcionamiento de los grupos de contenedores en Azure Container Instances
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: cb3d8c27a82c7dfc5fd71c1c7d589e81890e5cfb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32163306"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupos de contenedores en Azure Container Instances

El recurso de nivel superior de Azure Container Instances es el *grupo de contenedores*. Este artículo describe qué son los grupos de contenedores y qué tipos de escenarios permiten.

## <a name="how-a-container-group-works"></a>Cómo funciona un grupo de contenedores

Un grupo de contenedores es una colección de contenedores que se programan en la misma máquina host. Los contenedores de un grupo comparten un ciclo de vida, la red local y los volúmenes de almacenamiento. Es similar al concepto de *pod* en [Kubernetes][kubernetes-pod] y [DC/OS][dcos-pod].

El siguiente diagrama muestra un ejemplo de un grupo de contenedores que incluye varios contenedores:

![Diagrama de grupos de contenedores][container-groups-example]

Este grupo de contenedores de ejemplo:

* Se programó en una única máquina host.
* Se le asigna una etiqueta de nombre DNS.
* Expone una única dirección IP pública, con un puerto expuesto.
* Consta de dos contenedores. Un contenedor escucha en el puerto 80, mientras el otro escucha en el puerto 5000.
* Incluye dos recursos compartidos de archivos de Azure como montajes de volumen y cada contenedor monta uno de los recursos compartidos de forma local.

> [!NOTE]
> Los grupos de varios contenedores están restringidos actualmente a los contenedores Linux. Aunque estamos trabajando para traer todas las características a los contenedores Windows, puede encontrar diferencias en la plataforma actual en la [disponibilidad de cuotas y regiones en Azure Container Instances](container-instances-quotas.md).

## <a name="deployment"></a>Implementación

Los *grupos* de contenedores tienen una asignación de recursos mínima de 1 vCPU y 1 GB de memoria. Los *contenedores* individuales dentro de un grupo de contenedores pueden aprovisionarse con menos de 1 vCPU y 1 GB de memoria. Dentro de un grupo de contenedores, se puede personalizar la distribución de recursos a varios contenedores dentro de los límites establecidos en el nivel de grupo de contenedores. Por ejemplo, dos contenedores con 0,5 vCPU cada uno que residen en un grupo contenedores que tiene asignado 1 vCPU.

## <a name="networking"></a>Redes

Los grupos de contenedores comparten una dirección IP y un espacio de nombres de puerto en esa dirección IP. Para permitir que los clientes externos lleguen a un contenedor dentro del grupo, debe exponer el puerto en la dirección IP y desde el contenedor. Dado que los contenedores dentro del grupo comparten un espacio de nombres de puerto, no se admite la asignación de puertos. Los contenedores dentro de un grupo pueden comunicar entre sí a través de localhost en los puertos que han expuesto, incluso si estos puertos no se exponen externamente en la dirección IP del grupo.

## <a name="storage"></a>Storage

Puede especificar volúmenes externos para montar dentro de un grupo de contenedores. Puede asignar los volúmenes en rutas de acceso específicas dentro de los contenedores individuales en un grupo.

## <a name="common-scenarios"></a>Escenarios comunes

Los grupos de varios contenedores son útiles en casos donde quiera dividir una única tarea funcional en un número pequeño de imágenes de contenedor. Luego, estas imágenes las pueden entregar diferentes equipos y pueden tener diversos requisitos de recursos.

Ejemplos posibles de uso serían:

* Un contenedor de aplicación y un contenedor de registro. El contenedor de registro recopila la salida de registros y métricas de la aplicación principal y los escribe en un almacenamiento a largo plazo.
* Un contenedor de aplicación y un contenedor de supervisión. Periódicamente, el contenedor de supervisión realiza una solicitud a la aplicación para asegurarse de que se está ejecutando y responde correctamente, y genera una alerta si no es así.
* Un contenedor para servir una aplicación web y un contenedor para extraer el contenido más reciente desde el control de código fuente.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de cómo implementar un grupo de múltiples contenedores con una plantilla de Azure Resource Manager:

> [!div class="nextstepaction"]
> [Implementación de un grupo de contenedores](container-instances-multi-container-group.md)

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
