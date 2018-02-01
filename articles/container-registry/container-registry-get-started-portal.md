---
title: "Guía de inicio rápido: Creación de un registro privado de Docker en Azure con Azure Portal"
description: "Aprenda rápidamente a crear un registro de contenedor privado de Docker en Azure Portal."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 12/06/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: eaf935c1060e53673351936111083d8bb44f05e7
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="create-a-container-registry-using-the-azure-portal"></a>Creación de un registro de contenedor mediante Azure Portal

Azure Container Registry es un registro privado de Docker en Azure donde se pueden almacenar y administrar las imágenes de contenedor privado de Docker. En esta guía de inicio rápido, creará un registro de contenedor con Azure Portal.

Para realizar este manual de inicio rápido, debe Docker instalado localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en https://portal.azure.com.

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

Seleccione **Nuevo** > **Contenedores** > **Azure Container Registry**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-01]

Escriba valores para **Nombre del Registro** y **Grupo de recursos**. El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. Cree un nuevo grupo de recursos denominado `myResourceGroup` y, en **SKU**, seleccione "Básico". Seleccione **Crear** para implementar la instancia de ACR.

![Creación de un registro de contenedor en Azure Portal][qs-portal-03]

En esta guía de inicio rápido, creamos un registro *Básico*. Azure Container Registry está disponible en varias SKU diferentes, que se describen brevemente en la tabla siguiente. Para obtener detalles ampliados de cada una, consulte [SKU de Azure Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Cuando aparezca el mensaje **Implementación correcta**, seleccione el registro de contenedor en el portal y luego seleccione **Claves de acceso**.

![Creación de un registro de contenedor en Azure Portal][qs-portal-05]

En **Usuario administrador**, seleccione **Habilitar**. Anote los siguientes valores:

* Servidor de inicio de sesión
* Nombre de usuario
* contraseña

Estos valores los usará en los pasos siguientes al trabajar con el registro con la CLI de Docker.

![Creación de un registro de contenedor en Azure Portal][qs-portal-06]

## <a name="log-in-to-acr"></a>Inicio de sesión en ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. Para ello, use el comando [docker login][docker-login]. Reemplace el *nombre de usuario*, la *contraseña* y el *servidor de inicio de sesión* por los anotados en el paso anterior.

```bash
docker login --username <username> --password <password> <login server>
```

El comando devolverá `Login Succeeded` una vez completado. También puede ver una advertencia de seguridad que recomienda el uso del parámetro `--password-stdin`. Cuando su uso esté fuera del ámbito de este artículo, recomendamos seguir este procedimiento recomendado. Para más información, vea la referencia del comando [docker login][docker-login].

## <a name="push-image-to-acr"></a>Inserción de una imagen en ACR

Para insertar una imagen en Azure Container Registry, primero debe tener una imagen. Si es necesario, ejecute el siguiente comando para extraer una imagen existente de Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Antes de insertar la imagen en el registro, debe etiquetar la imagen con el nombre del servidor de inicio de sesión de ACR. Etiquete la imagen mediante el comando [docker tag][docker-tag]. Reemplace *servidor de inicio de sesión* por el nombre del servidor de inicio de sesión que anotó anteriormente.

```
docker tag microsoft/aci-helloworld <login server>/aci-helloworld:v1
```

Por último, use el comando [docker push][docker-push] para insertar la imagen en la instancia de ACR. Reemplace *servidor de inicio de sesión* por el nombre del servidor de inicio de sesión de su instancia de ACR.

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

## <a name="next-steps"></a>pasos siguientes

En esta guía de inicio rápido, creará una instancia de Azure Container Registry con Azure Portal. Si quiere usar Azure Container Registry con Azure Container Instances, continúe con el tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutoriales de Azure Container Instances][container-instances-tutorial-prepare-app]

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

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
