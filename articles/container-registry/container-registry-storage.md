---
title: Almacenamiento de imágenes en Azure Container Registry
description: Información detallada acerca de cómo se almacenan las imágenes del contenedor de Docker en Azure Container Registry, incluida la seguridad, la redundancia y la capacidad.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 92e60b4213cb80d193a7c35f68b8f9fd099481d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165102"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Almacenamiento de imágenes en Azure Container Registry

Cada instancia de Azure Container Registry, ya sea [Básico, Estándar o Premium](container-registry-skus.md), goza de características avanzadas de almacenamiento de Azure, como el cifrado en reposo y redundancia geográfica para proteger la seguridad de los datos de la imagen. En las secciones siguientes se describen las características y los límites del almacenamiento de imágenes en Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Cifrado en reposo

Todas las imágenes del contenedor en el registro se cifran en reposo. Azure cifra automáticamente una imagen antes de almacenarla y la descifra sobre la marcha cuando usted o sus aplicaciones y servicios extraen la imagen.

## <a name="geo-redundant-storage"></a>Almacenamiento con redundancia geográfica

Azure usa un esquema de almacenamiento con redundancia geográfica para proteger contra la pérdida de las imágenes del contenedor. Azure Container Registry replica automáticamente las imágenes del contenedor en varios centros de datos geográficamente distantes, para evitar que se pierdan si se produce un error de almacenamiento regional.

## <a name="geo-replication"></a>Replicación geográfica

Para escenarios que requieren una garantía de alta disponibilidad aún mayor, considere el uso de la característica [Replicación geográfica](container-registry-geo-replication.md) de los registros Premium. La replicación geográfica ayuda a protegerse contra la pérdida del acceso a su registro en caso de un error regional *total*, no solo de un error de almacenamiento. La replicación geográfica ofrece otras ventajas, como almacenamiento de imágenes cercano a la red para una inserción y extracción más rápida en escenarios de desarrollo o implementación distribuidos.

## <a name="image-limits"></a>Límites de imágenes

En la tabla siguiente se describen los límites de almacenamiento y la imagen de contenedor que se usa de Azure Container Registry.

| Recurso | Límite |
| -------- | :---- |
| Repositorios | Sin límite |
| Imágenes | Sin límite |
| Capas | Sin límite |
| Etiquetas | Sin límite|
| Storage | 5 TB |

Unas cifras muy altas de etiquetas y repositorios pueden afectar al rendimiento del registro. Como parte de la rutina de mantenimiento del registro, elimine periódicamente los repositorios, las etiquetas y las imágenes que no se estén usando con la [CLI de Azure](/cli/azure/acr), la [API de REST](/rest/api/containerregistry/) de ACR o [Azure Portal][portal]. Los recursos de registro eliminados, como repositorios, imágenes o etiquetas *no* pueden recuperarse después de su eliminación.

## <a name="storage-cost"></a>Coste del almacenamiento

Para información completa acerca de los precios, consulte [Precios de Container Registry][pricing].

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información acerca de los diferentes SKU de Azure Container Registry (Básico, Estándar o Premium), consulte [SKU de Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
