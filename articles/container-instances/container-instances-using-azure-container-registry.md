---
title: "Implementación en Azure Container Instances desde Azure Container Registry | Azure Docs"
description: "Implementación en Azure Container Instances desde Azure Container Registry"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: cdac6362f0d51b3144024efd28af09eb6d97515f
ms.contentlocale: es-es
ms.lasthandoff: 08/04/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Implementación en Azure Container Instances desde Azure Container Registry

Azure Container Registry es un registro privado basado en Azure para imágenes de contenedor de Docker. En este artículo se describe cómo implementar imágenes de contenedor almacenadas en Azure Container Registry en Azure Container Instances.

## <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

La CLI de Azure incluye comandos para crear y administrar contenedores en Azure Container Instances. Si especifica una imagen privada en el comando `create`, también puede especificar la contraseña del registro de imagen que se requiere para autenticarse con el registro de contenedor.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

El comando `create` también admite especificar `image-registry-login-server` y `image-registry-username`. Sin embargo, el servidor de inicio de sesión de Azure Container Registry siempre es *NombreDeRegistro*.azurecr.io y el nombre de usuario predeterminado es *NombreDeRegistro*, por lo que estos valores se infieren del nombre de la imagen si no se proporcionan de forma explícita.

## <a name="using-an-azure-resource-manager-template"></a>Uso de una plantilla de Azure Resource Manager

Puede especificar las propiedades de Azure Container Registry en una plantilla de Azure Resource Manager; para ello, se debe incluir la propiedad `imageRegistryCredentials` en la definición del grupo de contenedores:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Para evitar almacenar la contraseña del registro de contenedor directamente en la plantilla, se recomienda que la almacene como un secreto en [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) y que haga referencia a ella en la plantilla mediante la [integración nativa entre Azure Resource Manager y Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="using-the-azure-portal"></a>Uso de Azure Portal

Si se mantienen imágenes de contenedor en Azure Container Registry, se puede crear fácilmente un contenedor en Azure Container Instances a través de Azure Portal.

1. En Azure Portal, vaya al registro de contenedor.

2. Elija Repositorios.

    ![Menú de Azure Container Registry en Azure Portal][acr-menu]

3. Elija el repositorio desde el que desea realizar la implementación.

4. Haga clic con el botón derecho en la etiqueta de la imagen de contenedor que desea implementar.

    ![Menú contextual para iniciar el contenedor con Azure Container Instances][acr-runinstance-contextmenu]

5. Escriba un nombre para el contenedor y un nombre para el grupo de recursos. También puede cambiar los valores predeterminados si lo desea.

    ![Menú Crear para Azure Container Instances][acr-create-deeplink]

6. Una vez completada la implementación, puede ir al grupo de contenedores desde el panel de notificaciones para buscar la dirección IP y otras propiedades.

    ![Vista de detalles del grupo de contenedores de Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo crear contenedores, insertarlos en un registro de contenedor privado e implementarlos en Azure Container Instances al [completar el tutorial](container-instances-tutorial-prepare-app.md).

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
