---
title: SKU de Azure Container Registry
description: Comparaciones entre los diferentes niveles de servicio disponibles en Azure Container Registry
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: mmacy
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: stevelas
ms.openlocfilehash: 630bc088fcb6d3c7e5bb3a9713107c3fb6653ec6
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2017
---
# <a name="azure-container-registry-skus"></a>SKU de Azure Container Registry

Azure Container Registry (ACR) está disponible en varios niveles de servicio, conocidos como SKU. Estas SKU proporcionan precios predecibles y varias opciones de uso del registro de Docker privado en Azure. Elegir una SKU de nivel superior proporciona más rendimiento y escalabilidad. Sin embargo, todas las SKU proporcionan las mismas funcionalidades de programación, lo que permite a un desarrollador empezar a trabajar con el nivel Básico y convertirlo en Estándar y Premium a medida que aumenta el uso del registro.

## <a name="basic"></a>Básica
Un punto de entrada optimizado para los costos para que los desarrolladores aprendan sobre Azure Container Registry. Los registros básicos tienen las mismas funcionalidades de programación que los estándar y premium (integración de la autenticación de Azure Active Directory, eliminación de imágenes y webhooks), sin embargo, existen restricciones de uso y tamaño.

## <a name="standard"></a>Estándar
Los registros estándar ofrecen las mismas funcionalidades que los básicos, pero con mayores límites de almacenamiento y un mayor rendimiento de las imágenes. Los registros estándar deberían satisfacer las necesidades de la mayoría de los escenarios de producción.

## <a name="premium"></a>Premium
Los registros premium proporcionan límites superiores en las restricciones como las operaciones de almacenamiento y simultáneas, por lo que permiten trabajar con escenarios de mayor volumen. Además de la mayor capacidad de rendimiento de las imágenes, el nivel premium agrega características como la [replicación geográfica](container-registry-geo-replication.md) para la administración de un único registro en varias regiones y el mantenimiento de un registro cercano a la red para cada implementación.

## <a name="classic"></a>Clásico
La SKU de registro clásica habilitó la versión inicial del servicio Azure Container Registry en Azure. Los registros clásicos se basan en una cuenta de almacenamiento que Azure crea en su suscripción, lo que limita la capacidad de ACR de proporcionar funcionalidades de nivel superior, como el aumento del rendimiento y la replicación geográfica. Debido a sus funcionalidades limitadas, se prevé que la SKU clásica quede en desuso en el futuro.

> [!NOTE]
> Debido a la degradación planeada de la SKU de registro clásica, se recomienda usar los niveles Básico, Estándar o Premium para todos los registros nuevos. Para obtener información sobre cómo convertir el registro clásico existente, vea [Cambio de SKU](#changing-skus).
>

## <a name="registry-sku-feature-matrix"></a>Matriz de características de la SKU del registro

En la tabla siguiente se detallan las características y los límites de los niveles de servicio Básico, Estándar y Premium.

| Característica | Básico | Standard | Premium |
|---|---|---|---|---|
| Storage | 10 GiB | 100 GiB| 500 GiB |
| Operaciones de lectura por minuto<sup>1, 2</sup> | 1000 | 300 000 | 10 000 000 |
| Operaciones de escritura por minuto<sup>1, 3</sup> | 100 | 500 | 2000 |
| Ancho de banda de descarga en MBps<sup>1</sup> | 30 | 60 | 100 |
| Ancho de banda de carga en MBps<sup>1</sup> | 10 | 20 | | 50 |
| Webhooks | 2 | 10 | 100 |
| Replicación geográfica | N/D | N/D | [Compatible *(versión preliminar)*](container-registry-geo-replication.md) |

<sup>1</sup> Las *operaciones de lectura*, las *operaciones de escritura* y el *ancho de banda* son estimaciones mínimas. ACR trata de mejorar el rendimiento que el uso requiere.

<sup>2</sup> La [extracción de docker](https://docs.docker.com/registry/spec/api/#pulling-an-image) se traduce en varias operaciones de lectura en función del número de capas de la imagen, además de la recuperación de manifiesto.

<sup>3</sup> La [inserción de docker](https://docs.docker.com/registry/spec/api/#pushing-an-image) se traduce en varias operaciones de escritura, en función del número de capas que se deben insertar. Un elemento `docker push` incluye *operaciones de lectura* para recuperar un manifiesto para una imagen existente.

## <a name="manage-registry-size"></a>Administración del tamaño del registro
Las restricciones de almacenamiento de cada SKU están diseñadas para adaptarlas a un escenario típico: Básico para obtener una introducción, Estándar para la mayoría de las aplicaciones de producción y Premium para obtener un rendimiento a gran escala y [replicación geográfica](container-registry-geo-replication.md). Durante todo el ciclo de vida del registro, debe administrar su tamaño mediante la eliminación periódica del contenido sin usar.

Puede consultar el uso actual de un registro en la **información general** del registro de contenedor en Azure Portal:

![Información del uso del registro en Azure Portal](media/container-registry-skus/registry-overview-quotas.png)

Puede administrar el tamaño del registro mediante la eliminación de repositorios en Azure Portal.

En **SERVICIOS**, seleccione **Repositorios**, haga clic en el repositorio que desea eliminar y luego seleccione **Eliminar**.

![Eliminación de un repositorio en Azure Portal](media/container-registry-skus/delete-repository-portal.png)

## <a name="changing-skus"></a>Cambio de SKU

Puede cambiar la SKU de un registro en Azure Portal.

En la **información general** del registro de Azure Portal, seleccione **Actualizar** y después seleccione una **SKU** nueva en la lista desplegable de SKU.

![Actualización de la SKU del registro de contenedor en Azure Portal](media/container-registry-skus/update-registry-sku.png)

## <a name="changing-from-classic"></a>Cambio a partir del nivel Clásico
Cuando se cambia un registro clásico al modo Básico, Estándar o Premium, Azure copia las imágenes de contenedor existentes de la cuenta de almacenamiento asociada a su suscripción en una cuenta de almacenamiento administrada por Azure. Este proceso puede tardar un tiempo.

Durante la conversión, `docker pull` continúa funcionando; sin embargo, `docker push` se bloquea hasta que se completa la conversión.

Una vez completada, ACR deja de usar la cuenta de almacenamiento de su suscripción.

### <a name="why-change-from-classic-to-basic-standard-or-premium"></a>¿Por qué cambiar del nivel Clásico a Básico, Estándar o Premium?

Debido a las funcionalidades limitadas de los registros clásicos, se recomienda cambiar los registros clásicos a los niveles Básico, Estándar o Premium. Estas SKU de nivel superior integran mejor el registro en las funcionalidades de Azure. Estas son algunas de las funcionalidades:

* Integración de Azure Active Directory para la autenticación individual (vea [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login))
* Compatibilidad de eliminación de imagen y etiqueta
* [Replicación geográfica](container-registry-geo-replication.md)
* [Webhooks](container-registry-webhook.md)

Lo más importante es que un registro clásico depende de la cuenta de almacenamiento que Azure aprovisionó automáticamente en su suscripción cuando creó el registro. En cambio, las SKU de los niveles Básico, Estándar y Premium se benefician del *almacenamiento administrado*. Es decir, Azure administra de forma transparente el almacenamiento de las imágenes por usted; no se crea una cuenta de almacenamiento diferente en su propia suscripción.

Algunas de las ventajas del almacenamiento administrado que ofrecen los registros básicos, estándar y premium:

* Las imágenes de contenedor se [cifran en reposo](../storage/common/storage-service-encryption.md).
* Las imágenes se almacenan mediante el [almacenamiento con redundancia geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage), lo que garantiza la copia de seguridad de las imágenes con replicación en varias regiones.
* Capacidad de [cambiar de SKU](#changing-skus), lo que aumenta el rendimiento al elegir una SKU de nivel superior. Con cada SKU, ACR puede cumplir los requisitos de rendimiento a medida que aumentan sus necesidades. La implementación subyacente de cómo ACR logra el rendimiento deseado se expresa como *intent* (con la selección de SKU de nivel superior), sin tener que administrar los detalles de la implementación.

## <a name="pricing"></a>Precios

Para obtener más información sobre cada una de las SKU de Azure Container Registry, consulte [Precios de Container Registry](https://azure.microsoft.com/pricing/details/container-registry/).

## <a name="next-steps"></a>Pasos siguientes

**Mapa de ruta de Azure Container Registry**

Visite el [mapa de ruta de ACR](https://aka.ms/acr/roadmap) en GitHub para obtener información sobre las próximas características del servicio.

**UserVoice en Azure Container Registry**

Envíe y vote las sugerencias de nuevas características de [UserVoice en ACR](https://feedback.azure.com/forums/903958-azure-container-registry).