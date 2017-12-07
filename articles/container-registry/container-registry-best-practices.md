---
title: Procedimientos recomendados en Azure Container Registry
description: "Obtenga información sobre cómo de forma eficaz Azure Container Registry mediante los siguientes procedimientos recomendados."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 11/05/2017
ms.author: marsma
ms.openlocfilehash: 5ccbb3022dc38f13eed9b5aa24beb14dfdb3b5b6
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="best-practices-for-azure-container-registry"></a>Procedimientos recomendados para Azure Container Registry

Si sigue estos procedimientos recomendados, puede ayudar a maximizar el rendimiento y rentabilizar el uso del registro de Docker privado en Azure.

## <a name="network-close-deployment"></a>Implementación cercana a la red

Cree el registro de contenedor en la misma región de Azure en la que implementa los contenedores. Colocar el registro en una región que esté cercana a la red para los hosts del contenedor puede ayudar a reducir tanto la latencia como el costo.

La implementación cercana a la red es una de las razones principales para utilizar un registro de contenedor privado. Las imágenes de Docker tienen una excelente [construcción en capas](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) que permite implementaciones incrementales. Sin embargo, los nuevos nodos tienen que extraer todas las capas necesarias para una imagen determinada. Este `docker pull` inicial puede agregar rápidamente hasta varios gigabytes. Tener un registro privado próximo a la implementación minimiza la latencia de red.
Además, todas las nubes públicas, Azure incluido, implementan cuotas de salida de red. La extracción de imágenes desde un centro de datos a otro agrega cuotas de salida de red, además de latencia.

## <a name="geo-replicate-multi-region-deployments"></a>Implementación de la replicación geográfica en varias regiones

Utilice la característica de [replicación geográfica](container-registry-geo-replication.md) de Azure Container Registry si está implementando contenedores en varias regiones. Si se está prestando servicio a clientes globales de centros de datos locales o su equipo de desarrollo se encuentra en distintas ubicaciones, puede simplificar la administración del registro y minimizar la latencia mediante la replicación geográfica del registro. Esta característica, actualmente en versión preliminar, está disponible con registros [Premium](container-registry-skus.md#premium).

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

| Tipo | Escenario de ejemplo | Método recomendado |
|---|---|---|
| Identidad individual | Un desarrollador que extrae imágenes de su máquina de desarrollo, o las inserta en ella. | [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) |
| Identidad de servicio o sin periféricos | Canalizaciones de compilación e implementación donde el usuario no está directamente implicado. | [Entidad de servicio](container-registry-authentication.md#service-principal) |

Para obtener información detallada acerca de la autenticación de Azure Container Registry, consulte [Autenticación con un registro de contenedor privado de Docker](container-registry-authentication.md).

## <a name="next-steps"></a>Pasos siguientes

Azure Container Registry está disponible en varios niveles, denominados SKU, y cada uno de ellos proporciona distintas funcionalidades. Para más información sobre las SKU disponibles, consulte [SKU de Azure Container Registry](container-registry-skus.md).