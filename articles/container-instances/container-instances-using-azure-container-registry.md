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
ms.service: 
ms.devlang: na
ms.topic: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2017
ms.author: seanmck
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: fcb21785584c3c5fb41f5ceb70346e3e05a30d93
ms.contentlocale: es-es
ms.lasthandoff: 07/26/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Implementación en Azure Container Instances desde Azure Container Registry

Azure Container Registry es un registro privado basado en Azure para imágenes de contenedor de Docker. En este artículo se describe cómo implementar imágenes de contenedor almacenadas en Azure Container Registry en Azure Container Instances.

## <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

La CLI de Azure incluye comandos para crear y administrar contenedores en Azure Container Instances. Si especifica una imagen privada en el comando `create`, también puede especificar la contraseña del registro de imagen que se requiere para autenticarse con el registro de contenedor.

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --image-registry-password myRegistryPassword --resource-group myresourcegroup
```

El comando `create` también admite especificar `image-registry-login-server` y `image-registry-username`. Sin embargo, de manera predeterminada, el servidor de inicio de sesión de Azure Container Registry simplemente es *NombreDeRegistro*.azurecr.io y el nombre de usuario es *NombreDeRegistro*, por lo que estos valores se infieren del nombre de la imagen si no se proporcionan de forma explícita.

## <a name="using-an-azure-resource-manager-template"></a>Uso de una plantilla de Azure Resource Manager

Puede especificar las propiedades de Azure Container Registry en una plantilla de Azure Resource Manager. Basta con incluir la propiedad `imageRegistryCredentials` en la definición del grupo de contenedores:

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Para evitar almacenar la contraseña en el registro de contenedor directamente en la plantilla, se recomienda que la almacene como un secreto en [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) y que haga referencia a ella en la plantilla mediante la [integración nativa entre Azure Resource Manager y Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md).


## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo crear contenedores, insertarlos en un registro de contenedor privado e implementarlos en Azure Container Instances al [completar el tutorial](container-instances-tutorial-prepare-app.md).
