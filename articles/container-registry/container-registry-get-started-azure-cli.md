---
title: "Guía de inicio rápido: Creación de un registro privado de Docker en Azure con la CLI de Azure"
description: "Aprenda rápidamente a crear un registro de contenedor privado de Docker con la CLI de Azure."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: db1fb3deec4b70a9341753a59910aeb0e002bca0
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Creación de un registro de contenedor con la CLI de Azure

Azure Container Registry es un servicio de registro de contenedores de Docker administrado usado para almacenar imágenes de contenedor de Docker privadas. En esta guía se detalla la creación de una instancia de Azure Container Registry con la CLI de Azure, la inserción de una imagen del contenedor en el registro y, por último, la implementación del contenedor desde el registro en Azure Container Instances (ACI).

Para esta guía de inicio rápido es preciso que use la versión 2.0.27 de la CLI de Azure o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Install Azure CLI 2.0][azure-cli] (Instalación de la CLI de Azure 2.0).

También debe tener instalado Docker localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac][docker-mac], [Windows][docker-windows] o [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create][az-group-create]. Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

En esta guía de inicio rápido, va a crear un registro *Básico*. Azure Container Registry está disponible en varias SKU diferentes, que se describen brevemente en la tabla siguiente. Para obtener detalles ampliados de cada una, consulte [SKU de Azure Container Registry][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Cree una instancia de ACR mediante el comando [az acr create][az-acr-create].

El nombre del registro debe ser único dentro de Azure y contener entre 5 y 50 caracteres alfanuméricos. En el ejemplo siguiente, se usa *myContainerRegistry007*. Actualice este valor a uno único.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Cuando se crea el registro, el resultado es similar al siguiente:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

En el resto de esta guía de inicio rápido, `<acrName>` es el marcador de posición del nombre del registro de contenedores.

## <a name="log-in-to-acr"></a>Inicio de sesión en ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. Para ello, utilice el comando [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

El comando devolverá un mensaje `Login Succeeded` una vez completado.

## <a name="push-image-to-acr"></a>Inserción de una imagen en ACR

Para insertar una imagen en Azure Container Registry, primero debe tener una imagen. Si aún no tiene ninguna imagen de contenedor local, ejecute el siguiente comando para extraer una imagen existente de Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

Para poder insertar una imagen en el registro, debe etiquetarla con el nombre completo del servidor de inicio de sesión de ACR. Ejecute el siguiente comando para obtener el nombre completo del servidor inicio de sesión de la instancia de ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Etiquete la imagen mediante el comando [docker tag][docker-tag]. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión de la instancia de ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por último, use el comando [docker push][docker-push] para insertar la imagen en la instancia de ACR. Reemplace `<acrLoginServer>` por el nombre del servidor de inicio de sesión de la instancia de ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Lista de imágenes de contenedor

En el ejemplo siguiente se enumeran los repositorios de un registro:

```azurecli
az acr repository list --name <acrName> --output table
```

Salida:

```bash
Result
----------------
aci-helloworld
```

En el ejemplo siguiente se muestran las etiquetas del repositorio **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Salida:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Implementación de la imagen en ACI

Para implementar una instancia del contenedor del registro que creó, debe proporcionar las credenciales del registro utilizadas en la implementación. En escenarios de producción se debe usar una [entidad de servicio][container-registry-auth-aci] para acceder al registro de contenedores, pero para abreviar en esta guía de inicio rápido, habilite al usuario administrador en el registro con el siguiente comando:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

Una vez habilitado el administrador, el nombre de usuario es el mismo que el nombre del registro y se puede recuperar la contraseña con este comando:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Para implementar la imagen del contenedor con un 1 núcleo de CPU y 1 GB de memoria, ejecute el comando siguiente. Reemplace `<acrName>`, `<acrLoginServer>` y `<acrPassword>` por los valores obtenidos en los dos comandos anteriores.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Debería recibir una respuesta inicial de Azure Resource Manager con los detalles del contenedor. Para supervisar el estado del contenedor y comprobar si se está ejecutando, repita el comando [az container show][az-container-show]. Debería tardar menos de un minuto.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Visualización de la aplicación

Cuando la implementación en ACI se realice correctamente, recupere el nombre de dominio completo del contenedor con el comando [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Salida de ejemplo: `"aci-demo.eastus.azurecontainer.io"`

Para ver la aplicación en ejecución, vaya a la dirección IP pública en su explorador favorito.

![Aplicación Hola mundo en el explorador][aci-app-browser]

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar el comando [az group delete][az-group-delete] para eliminar el grupo de recursos, la instancia de ACR y todas las imágenes del contenedor.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una instancia de Azure Container Registry con la CLI de Azure, insertado una imagen del contenedor en el registro e iniciado dicha instancia en Azure Container Instances. Siga el tutorial de Azure Container Instances para información más detallada de ACI.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az_container_show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md