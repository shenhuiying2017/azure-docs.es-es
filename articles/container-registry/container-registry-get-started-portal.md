---
title: "Guía de inicio rápido: Creación de un registro privado de Docker en Azure con Azure Portal"
description: "Aprenda rápidamente a crear un registro de contenedor privado de Docker en Azure Portal."
services: container-registry
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 033ef0cdd607ced5de6c975e071e0ce37e677201
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Creación de un registro de contenedor mediante Azure Portal

Azure Container Registry es un registro privado de Docker en Azure donde se pueden almacenar y administrar las imágenes de contenedor privado de Docker. En esta guía de inicio rápido, creará un registro de contenedor con Azure Portal.

Para realizar este manual de inicio rápido, debe Docker instalado localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

Seleccione **Nuevo** > **Contenedores** > **Azure Container Registry**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-01]

Escriba valores para **Nombre del Registro** y **Grupo de recursos**. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. Cree un nuevo grupo de recursos denominado `myResourceGroup` y, en **SKU**, seleccione "Classic". Seleccione **Crear** para implementar la instancia de ACR.

![Creación de un registro de contenedor en Azure Portal][qs-portal-03]

Azure Container Registry está disponible actualmente en varias SKU: `Classic`, `Basic`, `Standard` y `Premium`. Aunque `Basic`, `Standard` y `Premium` proporcionan funcionalidades avanzadas, como almacenamiento administrado y webhooks, actualmente se encuentran en versión preliminar y no están disponibles en algunas regiones de Azure. En esta guía de inicio rápido, seleccionaremos la SKU `Classic` por su disponibilidad en todas las regiones.

Cuando aparezca el mensaje **Implementación correcta**, seleccione el registro de contenedor en el portal y luego seleccione **Claves de acceso**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-05]

En **Usuario administrador**, seleccione **Habilitar**. Anote los siguientes valores:

* Servidor de inicio de sesión
* Nombre de usuario
* Contraseña

Estos valores los usará en los pasos siguientes al trabajar con el registro con la CLI de Docker.

![Creación de un registro de contenedor en Azure Portal][qs-portal-06]

## <a name="log-in-to-acr"></a>Inicio de sesión en ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. Para ello, use el comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Reemplace el *nombre de usuario*, la *contraseña* y el *servidor de inicio de sesión* por los anotados en el paso anterior.

```
docker login --username <username> --password <password> <login server>
```

Al finalizar, el comando devuelve "Inicio de sesión realizado correctamente".

## <a name="push-image-to-acr"></a>Inserción de una imagen en ACR

Para insertar una imagen en Azure Container Registry, primero debe tener una imagen. Si es necesario, ejecute el siguiente comando para extraer una imagen existente de Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Antes de insertar la imagen en el registro, debe etiquetar la imagen con el nombre del servidor de inicio de sesión de ACR. Etiquete la imagen mediante el comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Reemplace *servidor de inicio de sesión* por el nombre del servidor de inicio de sesión que anotó anteriormente.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Por último, use el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) para insertar la imagen en la instancia de ACR. Reemplace *servidor de inicio de sesión* por el nombre del servidor de inicio de sesión de su instancia de ACR.

```
docker push <login server>/aci-helloworld:v1
```

La salida de un comando `docker push` correcto es parecida a:

```
The push refers to a repository [uniqueregistryname.azurecr.io/aci-helloworld]
7c701b1aeecd: Pushed
c4332f071aa2: Pushed
0607e25cc175: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:f2867748615cc327d31c68b1172cc03c0544432717c4d2ba2c1c2d34b18c62ba size: 1577
```

## <a name="list-container-images"></a>Lista de imágenes de contenedor

Para mostrar las imágenes en su instancia de ACR, vaya al registro en el portal, seleccione **Repositorios** y luego seleccione el repositorio que creó con `docker push`.

En este ejemplo, se selecciona el repositorio **aci-helloworld**, y se puede ver la imagen etiquetada con `v1` en **ETIQUETAS**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-09]

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, elimine el grupo de recursos **myResourceGroup**. De esta forma eliminará el grupo de recursos, la instancia de ACR y todas las imágenes de contenedor.

![Creación de un registro de contenedor en Azure Portal][qs-portal-08]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, creará una instancia de Azure Container Registry con la CLI de Azure. Si quiere usar Azure Container Registry con Azure Container Instances, continúe con el tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-portal-01]: ./media/container-registry-get-started-portal/qs-portal-01.png
[qs-portal-02]: ./media/container-registry-get-started-portal/qs-portal-02.png
[qs-portal-03]: ./media/container-registry-get-started-portal/qs-portal-03.png
[qs-portal-04]: ./media/container-registry-get-started-portal/qs-portal-04.png
[qs-portal-05]: ./media/container-registry-get-started-portal/qs-portal-05.png
[qs-portal-06]: ./media/container-registry-get-started-portal/qs-portal-06.png
[qs-portal-07]: ./media/container-registry-get-started-portal/qs-portal-07.png
[qs-portal-08]: ./media/container-registry-get-started-portal/qs-portal-08.png
[qs-portal-09]: ./media/container-registry-get-started-portal/qs-portal-09.png