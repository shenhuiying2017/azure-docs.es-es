---
title: "Replicación geográfica de un registro de contenedor de Azure"
description: "Introducción a la creación y la administración de registros de contenedor de Azure con replicación geográfica."
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: overview-article
ms.date: 10/24/2017
ms.author: stevelas
ms.openlocfilehash: 92df5a37d62dc9731842a4312339aa571072a487
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="geo-replication-in-azure-container-registry"></a>Replicación geográfica en Azure Container Registry

Las empresas que quieran tener una presencia local o una copia de seguridad activa, pueden optar por ejecutar servicios desde varias regiones de Azure. Como procedimiento recomendado, si coloca un registro de contenedor en cada región donde se ejecuten imágenes, podrá realizar operaciones cercanas a la red y habilitar transferencias de capa de imagen rápidamente y de forma fiable.

La replicación geográfica permite que un registro de contenedor de Azure funcione como un registro único que atienda a varias regiones con varios registros regionales maestros.

> [!IMPORTANT]
> La característica de replicación geográfica de Azure Container Registry está actualmente en su **versión preliminar**. Las versiones preliminares están a su disposición con la condición de que acepte los [términos de uso adicionales](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Es posible que algunos de los aspectos de esta característica cambien antes de ofrecer disponibilidad general.
>

Un registro con replicación geográfica le proporciona las siguientes ventajas:

* Nombres de registro, imágenes y etiquetas únicos que se pueden usar en varias regiones.
* Acceso al registro cercano a la red desde implementaciones regionales.
* No hay tarifas de salida adicionales, ya que las imágenes se extraen de un registro replicado local que está en la misma región que el host de contenedor.
* Administración única de un registro en varias regiones.

## <a name="example-use-case"></a>Ejemplo de caso de uso
Contoso ejecuta un sitio web de presencia pública ubicado en los Estados Unidos, Canadá y Europa. Para poder atender a estos mercados con contenido local y cercano a la red, Contoso ejecuta los clústeres de Kubernetes de [Azure Container Service](/azure/container-service/kubernetes/) (ACS) en el Este y Oeste de EE. UU, Canadá central y Europa Occidental. La aplicación del sitio web se implementa como una imagen de Docker y utiliza el mismo código e imagen en todas las regiones. El contenido (que es local en esa región) se recupera de una base de datos que se aprovisiona de forma única en cada región. Cada implementación regional tiene su configuración única para recursos tales como la base de datos local.

El equipo de desarrollo que está ubicado en Seattle (WA) usará el centro de datos del Oeste de EE. UU.

![Inserciones en varios registros](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Inserciones en varios registros*

Antes de usar las características de replicación geográfica, Contoso tenía un registro basado en los Estados Unidos de la zona Oeste de EE. UU. con un registro adicional en Europa Occidental. Para dar servicio a estas regiones, el equipo de desarrollo tenía que insertar imágenes en dos registros diferentes.

```bash
docker push contoso.azurecr.io/pubic/products/web:1.2
docker push contosowesteu.azurecr.io/pubic/products/web:1.2
```
![Extracciones desde varios registros](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Extracciones desde varios registros*

Entre los desafíos típicos de tener varios registros se incluyen:

* Todos los clústeres del Este de EE. UU., el Oeste de EE. UU. y Canadá central extraen contenido del registro del Oeste de EE. UU., por lo que se aplican cuotas de salida cada vez que uno de esos hosts de contenedor remotos extrae imágenes de los centros de datos del Oeste de EE. UU.
* El equipo de desarrollo debe insertar imágenes en los registros del Oeste de EE. UU. y Europa Occidental.
* El equipo de desarrollo debe configurar y mantener todas las implementaciones regionales con nombres de imagen que hagan referencia al registro local.
* El acceso al registro debe configurarse para cada región.

## <a name="benefits-of-geo-replication"></a>Ventajas de la replicación geográfica

![Extracciones desde un registro con replicación geográfica](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Al usar la característica de replicación geográfica de Azure Container Registry, disfrutará de las siguientes ventajas:

* Podrá administrar un registro único en todas las regiones: `contoso.azurecr.io`.
* Podrá administrar una única configuración de las implementaciones de imagen, ya que todas las regiones usarán la misma dirección URL de imagen: `contoso.azurecr.io/public/products/web:1.2`.
* Podrá insertar contenido en un registro único, mientras que ACR administra la replicación geográfica, incluyendo los webhooks regionales para las notificaciones locales.

## <a name="configure-geo-replication"></a>Configuración de la replicación geográfica
Configurar la replicación geográfica es tan fácil como hacer clic en las regiones de un mapa.

La replicación geográfica es una característica que solo está disponible para [registros Premium](container-registry-skus.md). Si el registro no es Premium, puede cambiarlo de Básico y Estándar a Premium en [Azure Portal](https://portal.azure.com):

![Cambiar las SKU en Azure Portal](media/container-registry-skus/update-registry-sku.png)

Para configurar la replicación geográfica del registro Premium, inicie sesión en Azure Portal en http://portal.azure.com.

Vaya a Azure Container Registry y seleccione **Replicaciones**:

![Replicaciones en la interfaz de usuario del registro de contenedor en Azure Portal](media/container-registry-geo-replication/registry-services.png)

Se mostrará un mapa que indica todas las regiones de Azure actuales:

 ![Mapa de regiones de Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Los hexágonos azules representan las réplicas actuales.
* Los hexágonos verdes representan las regiones de réplica posibles.
* Los hexágonos grises representan regiones de Azure que aún no están disponibles para la replicación.

Para configurar una réplica, seleccione un hexágono verde y, a continuación, seleccione **Crear**:

 ![Interfaz de usuario de la creación de la replicación en Azure Portal](media/container-registry-geo-replication/create-replication.png)

Para configurar réplicas adicionales, seleccione los hexágonos verdes de otras regiones y, a continuación, haga clic en **Crear**.

ACR comenzará entonces a sincronizar imágenes entre las réplicas configuradas. Una vez completada la operación, el portal muestra el mensaje *Listo*. Tenga en cuenta que el estado de la réplica no se actualiza de forma automática en el portal. Para ver el estado actualizado, use el botón Actualizar.

## <a name="geo-replication-pricing"></a>Precios de la replicación geográfica

La replicación geográfica es una característica de la [SKU Premium](container-registry-skus.md) de Azure Container Registry. Cuando replica un registro en las áreas indicadas, se aplica la tarifa del registro Premium para cada región.

En el ejemplo anterior, Contoso consolidó dos registros en uno y agregó réplicas en el Este de EE. UU., Canadá central y Europa Occidental. Por ello, pagaría cuatro veces al mes la tarifa Premium y sin tener ninguna configuración o administración adicional. Ahora, cada región extrae sus imágenes de forma local, lo que mejora el rendimiento y la confiabilidad sin tener que aplicar ninguna tarifa de salida de red desde las regiones del Oeste de EE. UU., Canadá y el Este de EE. UU.

## <a name="summary"></a>Resumen

Gracias a la replicación geográfica, puede administrar los centros de datos regionales como una sola nube global. Como las imágenes se usan en muchos servicios de Azure, puede sacar provecho de un plano de administración único al tiempo que mantiene la rapidez, la confiabilidad y la cercanía a la red de las extracciones de imágenes locales.

## <a name="next-steps"></a>pasos siguientes

Consulte la serie de tres partes del tutorial [Geo-replication in Azure Container Registry (Replicación geográfica en Azure Container Registry)](container-registry-tutorial-prepare-registry.md). Aprenda a crear un registro con replicación geográfica, a compilar un contenedor y a implementarlo con un solo comando `docker push` en varias instancias regionales de Web App for Containers.

> [!div class="nextstepaction"]
> [Replicación geográfica en Azure Container Registry](container-registry-tutorial-prepare-registry.md)