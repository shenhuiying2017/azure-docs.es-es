---
title: "Guía de inicio rápido: Creación de un registro privado de Docker en Azure con la CLI de Azure"
description: "Aprenda rápidamente a crear un registro de contenedor privado de Docker con la CLI de Azure."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 91f0aa093e0a1f7ed4d54a0cdf5ef53bc41cb6be
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Creación de un registro de contenedor con la CLI de Azure

Azure Container Registry es un servicio de registro de contenedores de Docker administrado usado para almacenar imágenes de contenedor de Docker privadas. En esta guía se detalla la creación de una instancia de Azure Container Registry mediante la CLI de Azure.

Para realizar los pasos de esta guía, es necesario ejecutar la versión 2.0.12 o superior de la CLI de Azure. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0](/cli/azure/install-azure-cli).

También debe tener instalado Docker localmente. Docker proporciona paquetes que permiten configurar Docker fácilmente en cualquier sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

En esta guía de inicio rápido, creamos un registro *Básico*. Azure Container Registry está disponible en varias SKU diferentes, que se describen brevemente en la tabla siguiente. Para obtener detalles ampliados de cada una, consulte [SKU del registro de contenedor](container-registry-skus.md).

Azure Container Registry está disponible actualmente en varias SKU: `Basic`, `Managed_Basic`, `Managed_Standard` y `Managed_Premium`. Si bien las SKU `Managed_*` proporcionan funcionalidades avanzadas, como almacenamiento administrado y webhooks, actualmente no están disponibles en algunas regiones de Azure cuando se usa la CLI de Azure. En esta guía de inicio rápido, seleccionaremos la SKU `Basic` por su disponibilidad en todas las regiones.

>[!NOTE]
> Registros administrados que están disponibles actualmente en todas las regiones. Sin embargo, la versión actual de la CLI de Azure no admite aún la creación de un registro administrado en todas las regiones. Se dispondrá de soporte técnico en la próxima versión de la CLI de Azure. Antes de su lanzamiento, utilice [Azure Portal](container-registry-get-started-portal.md) para crear registros administrados.

Creación de una instancia de ACR mediante el comando [az acr create](/cli/azure/acr#create).

El nombre del registro **debe ser único**. En el ejemplo siguiente, se usa *myContainerRegistry007*. Actualice este valor a uno único.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --sku Basic
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
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

En el resto de esta guía de inicio rápido, se usa `<acrname>` como marcador de posición para el nombre del registro de contenedor.

## <a name="log-in-to-acr"></a>Inicio de sesión en ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. Para ello, utilice el comando [az acr login](/cli/azure/acr#login).

```azurecli
az acr login --name <acrname>
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="push-image-to-acr"></a>Inserción de una imagen en ACR

Para insertar una imagen en Azure Container Registry, primero debe tener una imagen. Si es necesario, ejecute el siguiente comando para extraer una imagen creada anteriormente de Docker Hub.

```bash
docker pull microsoft/aci-helloworld
```

La imagen debe estar etiquetada con el nombre del servidor de inicio de sesión de ACR. Ejecute el siguiente comando para devolver el nombre del servidor de inicio de sesión de la instancia de ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Etiquete la imagen mediante el comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Reemplace *<acrLoginServer>* por el nombre del servidor de inicio de sesión de la instancia de ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por último, use el comando [docker push](https://docs.docker.com/engine/reference/commandline/push/) para insertar la imagen en la instancia de ACR. Reemplace *<acrLoginServer>* por el nombre del servidor de inicio de sesión de la instancia de ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Lista de imágenes de contenedor

En el ejemplo siguiente se enumeran los repositorios de un registro:

```azurecli
az acr repository list -n <acrname> -o table
```

Salida:

```bash
Result
----------------
aci-helloworld
```

En el ejemplo siguiente se muestran las etiquetas del repositorio **aci-helloworld**.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Salida:

```bash
Result
--------
v1
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no lo necesite, puede usar el comando [az group delete](/cli/azure/group#delete) para eliminar el grupo de recursos, la instancia de ACR y todas las imágenes del contenedor.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, creará una instancia de Azure Container Registry con la CLI de Azure. Si quiere usar Azure Container Registry con Azure Container Instances, continúe con el tutorial de Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial de Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)