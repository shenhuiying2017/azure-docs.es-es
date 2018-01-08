---
title: Procedimientos recomendados en Azure Container Registry
description: "Obtenga información sobre cómo de forma eficaz Azure Container Registry mediante los siguientes procedimientos recomendados."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/20/2017
ms.author: marsma
ms.openlocfilehash: d94c6801f96ce684ebb912667dc4aa381c171216
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Procedimientos recomendados para Azure Container Registry

Si sigue estos procedimientos recomendados, puede ayudar a maximizar el rendimiento y rentabilizar el uso del registro de Docker privado en Azure.

## <a name="network-close-deployment"></a>Implementación cercana a la red

Cree el registro de contenedor en la misma región de Azure en la que implementa los contenedores. Colocar el registro en una región que esté cercana a la red para los hosts del contenedor puede ayudar a reducir tanto la latencia como el costo.

La implementación cercana a la red es una de las razones principales para utilizar un registro de contenedor privado. Las imágenes de Docker tienen una [construcción en capas](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) eficaz que permite realizar implementaciones incrementales. Sin embargo, los nuevos nodos tienen que extraer todas las capas necesarias para una imagen determinada. Este `docker pull` inicial puede agregar rápidamente hasta varios gigabytes. Tener un registro privado próximo a la implementación minimiza la latencia de red.
Además, todas las nubes públicas, Azure incluido, implementan cuotas de salida de red. La extracción de imágenes desde un centro de datos a otro agrega cuotas de salida de red, además de latencia.

## <a name="geo-replicate-multi-region-deployments"></a>Implementación de la replicación geográfica en varias regiones

Utilice la característica de [replicación geográfica](container-registry-geo-replication.md) de Azure Container Registry si está implementando contenedores en varias regiones. Si se está prestando servicio a clientes globales de centros de datos locales o su equipo de desarrollo se encuentra en distintas ubicaciones, puede simplificar la administración del registro y minimizar la latencia mediante la replicación geográfica del registro. Esta característica, actualmente en versión preliminar, está disponible con registros [Premium](container-registry-skus.md).

Para aprender a usar la replicación geográfica, vea el tutorial de tres partes [Geo-replication in Azure Container Registry](container-registry-tutorial-prepare-registry.md) (Replicación geográfica en Azure Container Registry).

## <a name="repository-namespaces"></a>Espacios de nombres del repositorio

Al aprovechar los espacios de nombres del repositorio, puede permitir el uso compartido de un registro único por varios grupos dentro de su organización. Los registros se pueden compartir por las implementaciones y los equipos. Azure Container Registry admite espacios de nombres anidados, lo que permite el aislamiento del grupo.

Por ejemplo, considere las siguientes etiquetas de imagen de contenedor. Las imágenes que se usan en toda la organización, como `aspnetcore`, se colocan en el espacio de nombres raíz, mientras que las imágenes del contenedor que pertenecen a los grupos de producción y marketing usan sus propios espacios de nombres.

```
contoso.azurecr.io/aspnetcore:2.0
contoso.azurecr.io/products/widget/web:1
contoso.azurecr.io/products/bettermousetrap/refundapi:12.3
contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42
```

## <a name="dedicated-resource-group"></a>Grupo de recursos dedicado

Como los registros de contenedor son recursos que se utilizan en varios hosts de contenedor, un registro debe residir en su propio grupo de recursos.

Aunque puede experimentar con un tipo de host específico, como Azure Container Instances, es probable que desee eliminar la instancia del contenedor cuando haya terminado. Sin embargo, también puede mantener la colección de imágenes que ha insertado en Azure Container Registry. Al colocar el registro en su propio grupo de recursos, se minimiza el riesgo de eliminar de manera accidental la colección de imágenes del registro cuando se elimina el grupo de recursos de la instancia de contenedor.

## <a name="authentication"></a>Autenticación

Al autenticar con un registro de contenedor de Azure, hay dos escenarios principales: la autenticación individual y la autenticación de servicio (o "sin periféricos"). En la tabla siguiente se proporciona una breve descripción de estos escenarios y el método recomendado de autenticación para cada uno.

| type | Escenario de ejemplo | Método recomendado |
|---|---|---|
| Identidad individual | Un desarrollador que extrae imágenes de su máquina de desarrollo, o las inserta en ella. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Identidad de servicio o sin periféricos | Canalizaciones de compilación e implementación donde el usuario no está directamente implicado. | [Entidad de servicio](container-registry-authentication.md#service-principal) |

Para obtener información detallada acerca de la autenticación de Azure Container Registry, consulte [Autenticación con un registro de contenedor privado de Docker](container-registry-authentication.md).

## <a name="manage-registry-size"></a>Administración del tamaño del registro

Las restricciones de almacenamiento de cada [SKU de registro de contenedor][container-registry-skus] están diseñadas para adaptarse a un escenario típico: **Básico** para obtener una introducción, **Estándar** para la mayoría de las aplicaciones de producción y **Premium** para obtener un rendimiento a gran escala y [replicación geográfica][container-registry-geo-replication]. Durante todo el ciclo de vida del registro, debe administrar su tamaño mediante la eliminación periódica del contenido sin usar.

Puede consultar el uso actual de un registro en la **información general** del registro de contenedor en Azure Portal:

![Información del uso del registro en Azure Portal][registry-overview-quotas]

Puede administrar el tamaño del registro mediante la [CLI de Azure][azure-cli] o [Azure Portal][azure-portal]. Solo las SKU administradas (Básico, Estándar, Premium) admiten la eliminación de imágenes y repositorios. No se pueden eliminar los repositorios, las imágenes ni las etiquetas en un registro Clásico.

### <a name="delete-in-azure-cli"></a>Eliminación en la CLI de Azure

Use el comando [az acr repository delete][az-acr-repository-delete] para eliminar un repositorio o contenido de un repositorio.

Para eliminar un repositorio, incluidas todas las etiquetas y los datos de la capa de imagen que incluye, especifique solo el nombre del repositorio al ejecutar [az acr repository delete][az-acr-repository-delete]. En el ejemplo siguiente, se elimina el repositorio *myapplication* y todas las etiquetas y los datos de la capa de imagen de este:

```azurecli
az acr repository delete --name myregistry --repository myapplication
```

También puede eliminar datos de la imagen de un repositorio mediante los argumentos `--tag` y `--manifest`. Para obtener más información sobre estos argumentos, consulte la [referencia del comando az acr repository delete][az-acr-repository-delete].

### <a name="delete-in-azure-portal"></a>Eliminación en Azure Portal

Para eliminar un repositorio de un registro en Azure Portal, primero navegue hasta el registro de contenedor. Luego, en **SERVICIOS**, seleccione **Repositorios** y haga clic con el botón derecho en el repositorio que quiera eliminar. Seleccione **Eliminar** para eliminar el repositorio y las imágenes de Docker que contiene.

![Eliminación de un repositorio en Azure Portal][delete-repository-portal]

De forma similar, también puede eliminar las etiquetas de un repositorio. Desplácese hasta el repositorio, haga clic con el botón derecho en la etiqueta que quiera eliminar en **ETIQUETAS** y seleccione **Eliminar**.

## <a name="next-steps"></a>pasos siguientes

Azure Container Registry está disponible en varios niveles, denominados SKU, y cada uno de ellos proporciona distintas funcionalidades. Para más información sobre las SKU disponibles, consulte [SKU de Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[azure-cli]: /cli/azure/overview
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
