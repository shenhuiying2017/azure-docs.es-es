---
title: "Creación del registro de contenedores privado de Docker: CLI de Azure | Microsoft Docs"
description: "Introducción a la creación y la administración de registros de contenedor privados de Docker mediante la CLI de Azure 2.0"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Creación de un registro de contenedores administrado mediante la CLI de Azure

Azure Container Registry es un servicio de registro de contenedores de Docker administrado usado para almacenar imágenes de contenedor de Docker privadas. Esta guía detalla la creación de una instancia administrada de Azure Container Registry mediante la CLI de Azure.

Azure Container Registry está en versión preliminar y no está disponible en todas las regiones.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para esta guía de inicio rápido es preciso que ejecute la CLI de Azure versión 2.0.4 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos con el comando [az group create](/cli/azure/group#create). Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure. 

En el ejemplo siguiente, se crea un grupo de recursos denominado *myResourceGroup* en la ubicación *westcentralus*.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

Creación de una instancia de ACR mediante el comando [az acr create](/cli/azure/acr#create).

> [!NOTE]
> Cuando cree un registro, especifique un nombre de dominio de nivel superior único global que contenga solo letras y números.

 El nombre del registro en el ejemplo es *myContainerRegistry1*, sustitúyalo por un nombre único de su elección.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

Cuando se crea el registro, el resultado es similar al siguiente:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Inicio de sesión en la instancia de ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. Para ello, utilice el comando [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

Al finalizar, el comando devuelve un mensaje que indica que el inicio de sesión se ha realizado correctamente.

## <a name="use-azure-container-registry"></a>Uso de Azure Container Registry

### <a name="list-container-images"></a>Lista de imágenes de contenedor

Use los comandos de la CLI `az acr` para consultar las imágenes y etiquetas en un repositorio.

> [!NOTE]
> Actualmente, Container Registry no admite el comando `docker search` para consultar las imágenes y etiquetas.

### <a name="list-repositories"></a>Lista de repositorios

En el ejemplo siguiente se enumeran los repositorios de un registro en formato JSON (notación de objetos JavaScript):

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Lista de etiquetas

En el ejemplo siguiente se muestran las etiquetas del repositorio **samples/nginx**, en formato JSON:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía se ha creado una instancia administrada de Azure Container Registry mediante la CLI de Azure.

> [!div class="nextstepaction"]
> [Insertar la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md)

