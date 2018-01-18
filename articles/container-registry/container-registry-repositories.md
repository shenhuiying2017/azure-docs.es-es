---
title: Repositorios de Azure Container Registry en Azure Portal
description: "Visualización de los repositorios de Azure Container Registry en Azure Portal."
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 593972e972207a27d1232fcb0c1bf220ac3a8def
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Visualización de los repositorios de registros de contenedor en Azure Portal

Azure Container Registry permite almacenar imágenes de contenedor Docker en repositorios. Mediante el almacenamiento de imágenes en repositorios, puede almacenar grupos de imágenes (o versiones de imágenes) en entornos aislados. Puede especificar estos repositorios al insertar imágenes en el registro y ver el contenido en Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

* **Registro de contenedor**: cree un registro de contenedor en la suscripción de Azure. Por ejemplo, use [Azure Portal](container-registry-get-started-portal.md) o la [CLI de Azure](container-registry-get-started-azure-cli.md).
* **CLI de Docker**: instale [Docker][docker-install] en la máquina local, lo que proporciona la interfaz de la línea de comandos de Docker.
* **Imagen de contenedor**: inserte una imagen en el registro de contenedor. Para instrucciones sobre cómo insertar y extraer imágenes, consulte [Inserción y extracción de una imagen](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Visualización de repositorios en Azure Portal

Puede ver una lista de los repositorios donde se hospedan las imágenes, así como las etiquetas de imagen, en Azure Portal.

Si siguió los pasos que aparecen en [Inserción y extracción de una imagen](container-registry-get-started-docker-cli.md) (y no eliminó posteriormente la imagen), debe tener una imagen de Nginx en el registro de contenedor. Las instrucciones de ese artículo especifican que debe etiquetar la imagen con un espacio de nombres, los "ejemplos" en `/samples/nginx`. Como recordatorio, el comando [docker push][docker-push] que se especificó en ese artículo era:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Como Azure Container Registry admite ese tipo de espacios de nombres de repositorio de varios niveles, puede definir el ámbito de las colecciones de imágenes relacionadas con una aplicación específica, o una colección de aplicaciones, a distintos equipos de desarrollo u operativos. Para más información sobre los repositorios de registros de contenedor, consulte [Introducción a los registros de contenedores privados de Docker](container-registry-intro.md).

Para ver un repositorio:

1. Inicie sesión en [Azure Portal][portal]
1. Seleccione la instancia de **Azure Container Registry** en la que insertó la imagen de Nginx
1. Seleccione **Repositorios** para ver una lista de los repositorios que contienen las imágenes en el registro
1. Seleccione un repositorio para ver las etiquetas de imagen dentro de ese repositorio

Por ejemplo, si insertó la imagen de Nginx como se indica en [Inserción y extracción de una imagen](container-registry-get-started-docker-cli.md), debería ver algo similar a lo siguiente:

![Repositorios del portal](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>pasos siguientes

Ahora que conoce los aspectos básicos que implica ver y trabajar con repositorios en el portal, intente usar Azure Container Registry con un clúster de [Azure Container Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md).

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
