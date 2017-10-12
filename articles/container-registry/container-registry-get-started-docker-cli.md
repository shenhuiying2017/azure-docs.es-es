---
title: "Inserción de imagen de Docker en el registro privado de Azure | Microsoft Docs"
description: "Inserción y extracción de imágenes de Docker en un registro de contenedor privado de Azure mediante la CLI de Docker"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 10f01e4e8c86bbbfa17cf2559caca645ff13bdcc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Inserción de la primera imagen en un registro de contenedor privado de Docker mediante la CLI de Docker
Un registro de contenedor de Azure almacena y administra imágenes privadas de contenedor de [Docker](http://hub.docker.com), de una forma similar a la que [Docker Hub](https://hub.docker.com/) almacena imágenes públicas. Use la [interfaz de la línea de comandos de Docker](https://docs.docker.com/engine/reference/commandline/cli/) (CLI de Docker) para [iniciar sesión](https://docs.docker.com/engine/reference/commandline/login/), [insertar](https://docs.docker.com/engine/reference/commandline/push/), [extraer](https://docs.docker.com/engine/reference/commandline/pull/) y realizar otras operaciones en el registro de contenedor.

Para más información sobre el entorno y los conceptos, consulte [la información general](container-registry-intro.md)



## <a name="prerequisites"></a>Requisitos previos
* **Registro de contenedor de Azure**: cree un registro de contenedor en la suscripción de Azure. Por ejemplo, use [Azure Portal](container-registry-get-started-portal.md) o la [CLI de Azure 2.0](container-registry-get-started-azure-cli.md).
* **CLI de docker**: para configurar el equipo local como un host de Docker y tener acceso a los comandos de la CLI de Docker, instale [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Inicio de sesión en un registro
Ejecute `docker login` para iniciar sesión en el registro de contenedor con sus [credenciales de registro](container-registry-authentication.md).

En el ejemplo siguiente se pasa el identificador y la contraseña de una [entidad de servicio](../active-directory/active-directory-application-objects.md) de Azure Active Directory. Por ejemplo, puede que haya asignado una entidad de servicio al registro para ver un escenario de automatización.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Asegúrese de especificar el nombre completo del registro (todo en minúsculas). En este ejemplo, es `myregistry.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Pasos para extraer e insertar una imagen
En el ejemplo siguiente se descarga la imagen de Nginx desde el registro público de Docker Hub, se le asigna una etiqueta para el registro de contenedor de Azure privado, se le inserta en el registro y luego se extrae de nuevo.

**1. Extraiga la imagen oficial de Docker para Nginx**

En primer lugar, extraiga la imagen pública de Nginx al equipo local.

```
docker pull nginx
```
**2. Inicie el contenedor de Nginx**

El comando siguiente inicia el contenedor local de Nginx de forma interactiva en el puerto 8080, permitiéndole ver la salida de Nginx. Quita el contenedor en ejecución una vez detenido.

```
docker run -it --rm -p 8080:80 nginx
```

Vaya a [http://localhost:8080](http://localhost:8080) para ver el contenedor en ejecución. Se muestra una pantalla similar a la siguiente.

![Nginx en un equipo local](./media/container-registry-get-started-docker-cli/nginx.png)

Para detener el contenedor en ejecución, pulse [CTRL] + [C].

**3. Cree un alias de la imagen en el registro**

El siguiente comando crea un alias de la imagen, con una ruta de acceso completa al registro. Este ejemplo especifica el espacio de nombres `samples` para evitar el desorden en la raíz del registro.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4. Inserte la imagen en el registro**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5. Extraiga la imagen del registro**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6. Inicie el contenedor de Nginx del registro**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Vaya a [http://localhost:8080](http://localhost:8080) para ver el contenedor en ejecución.

Para detener el contenedor en ejecución, pulse [CTRL] + [C].

**7. (Opcional) Quite la imagen**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>Límites simultáneos
En algunos escenarios, la ejecución de llamadas de forma simultánea podría producir errores. La tabla siguiente contiene los límites de llamadas simultáneas con las operaciones "Insertar" y "Extraer" en el registro de contenedor de Azure:

| Operación  | Límite                                  |
| ---------- | -------------------------------------- |
| EXTRAER       | Hasta 10 extracciones simultáneas por registro |
| INSERTAR       | Hasta 5 inserciones simultáneas por registro |

## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos, ya está listo para empezar a usar el registro. Por ejemplo, empiece por implementar imágenes del contenedor en un clúster de [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/).
