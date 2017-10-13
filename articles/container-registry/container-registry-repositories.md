---
title: Repositorios de Azure Container Registry | Microsoft Docs
description: "Uso de los repositorios de Azure Container Registry para imágenes de Docker"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: d737df98fee955b57166cb44f45f61dff906514e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-container-registry-repositories"></a>Repositorios de Azure Container Registry

Azure Container Registry permite almacenar imágenes de contenedor en repositorios. Mediante el almacenamiento de imágenes en repositorios, puede tener grupos de imágenes (o versión de imágenes) en entornos aislados. Puede especificar estos repositorios al insertar imágenes en el registro.


## <a name="prerequisites"></a>Requisitos previos
* **Registro de contenedor de Azure**: cree un registro de contenedor en la suscripción de Azure. Por ejemplo, use [Azure Portal](container-registry-get-started-portal.md) o la [CLI de Azure 2.0](container-registry-get-started-azure-cli.md).
* **CLI de docker**: para configurar el equipo local como un host de Docker y tener acceso a los comandos de la CLI de Docker, instale [Docker Engine](https://docs.docker.com/engine/installation/).
* **Extraer una imagen**: extraiga una imagen desde el registro público de Docker Hub, etiquétela e insértela en el registro. Para obtener instrucciones sobre cómo insertar y extraer imágenes, vea [Inserción de la primera imagen en un registro de contenedor privado de Docker mediante la CLI de Docker](container-registry-get-started-docker-cli.md).


## <a name="viewing-repositories-in-the-portal"></a>Visualización de repositorios en el portal

Una vez que haya insertado imágenes en el registro de contenedor, puede ver una lista de los repositorios que hospedan imágenes en Azure Portal.

Si ha seguido los pasos descritos en [Inserción de la primera imagen en un registro de contenedor privado de Docker mediante la CLI de Docker](container-registry-get-started-docker-cli.md), ahora debería tener una imagen de Nginx en el registro de contenedor. Como parte de las instrucciones, debe haber especificado un espacio de nombres para la imagen. En el ejemplo siguiente, el comando inserta la imagen de NGinx en el repositorio de "ejemplos":

```
docker push myregistry.azurecr.io/samples/nginx
```
 Azure Container Registry es compatible con los espacios de nombres del repositorio de varios niveles. Esta característica le permite agrupar colecciones de imágenes relacionadas con una aplicación específica o una colección de aplicaciones con equipos operativos o de desarrollo específicos. Para más información sobre los repositorios de registros de contenedor, consulte [Introducción a los registros de contenedores privados de Docker](container-registry-intro.md).

Para ver los repositorios de registro de contenedor:

1. Iniciar sesión en el portal de Azure
2. En la hoja **Azure Container Registry**, seleccione el registro que desea inspeccionar.
3. En la hoja de registro, haga clic en **Repositorios** para ver una lista de todos los repositorios y sus imágenes.
4. (Opcional) Seleccione una imagen específica para ver etiquetas.

![Repositorios del portal](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos, ya está listo para empezar a usar el registro. Por ejemplo, empiece por implementar imágenes del contenedor en un clúster de [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/).
