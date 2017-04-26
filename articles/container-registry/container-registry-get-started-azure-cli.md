---
title: "Creación del registro de contenedores privado de Docker: CLI de Azure | Microsoft Docs"
description: "Introducción a la creación y la administración de registros de contenedor privados de Docker mediante la CLI de Azure 2.0"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: e37a3194bb65ccf3bb6168a2f456902a9c48edc5
ms.lasthandoff: 03/28/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Creación de un registro de contenedor privado de Docker con la CLI de Azure 2.0
Use los comandos de la [CLI de Azure 2.0](https://github.com/Azure/azure-cli) para crear un registro de contenedor y administrar su configuración desde un equipo Linux, Mac o Windows. También puede crear y administrar registros de contenedor mediante [Azure Portal](container-registry-get-started-portal.md) o mediante programación con la [API de REST](https://go.microsoft.com/fwlink/p/?linkid=834376) de Container Registry.


* Para más información sobre el entorno y los conceptos, consulte [la información general](container-registry-intro.md)
* Para obtener ayuda sobre los comandos de la CLI de Container Registry (`az acr` comandos), pase el parámetro `-h` a cualquier comando.


## <a name="prerequisites"></a>Requisitos previos
* **CLI de Azure 2.0**: para instalar y empezar a trabajar con la CLI 2.0, consulte las [instrucciones de instalación](/cli/azure/install-azure-cli). Inicie sesión en la suscripción de Azure mediante la ejecución de `az login`. Para más información, consulte la [introducción a la CLI 2.0](/cli/azure/get-started-with-azure-cli).
* **Grupo de recursos**: cree un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) antes de crear un registro de contenedor o utilice un grupo de recursos ya existente. Asegúrese de que el grupo de recursos está en una ubicación donde el servicio Container Registry está [disponible](https://azure.microsoft.com/regions/services/). Para crear un grupo de recursos con la CLI 2.0, consulte la [referencia de la CLI 2.0](/cli/azure/group).
* **Cuenta de almacenamiento** (opcional): cree una [cuenta de almacenamiento](../storage/storage-introduction.md) estándar de Azure para realizar copias del registro de contenedor en la misma ubicación. Si no especifica una cuenta de almacenamiento al crear un registro con `az acr create`, el comando creará una automáticamente. Para crear una cuenta de almacenamiento con la CLI 2.0, consulte la [referencia de la CLI 2.0](/cli/azure/storage/account). Premium Storage no se admite actualmente.
* **Entidad de servicio** (opcional): cuando crea un registro con la CLI, el acceso a este no está configurado de forma predeterminada. Según sus necesidades, puede asignar una entidad de servicio de Azure Active Directory existente a un registro (o crear y asignar una nueva), o habilitar la cuenta de usuario del administrador del registro. Consulte las secciones más adelante en este artículo. Para más información acerca del acceso al registro, consulte [Authenticate with the container registry](container-registry-authentication.md) (Autenticación con el registro de contenedor).

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor
Ejecute el comando `az acr create` para crear un registro de contenedor.

> [!TIP]
> Cuando cree un registro, especifique un nombre de dominio de nivel superior único global que contenga solo letras y números. El nombre del registro en los ejemplos es `myRegistry1`, pero puede sustituirlo por un nombre único de su elección.
>
>

El siguiente comando utiliza los parámetros mínimos necesarios para crear el registro de contenedor `myRegistry1` en el grupo de recursos `myResourceGroup` en la ubicación centro-sur de EE. UU.:

```azurecli
az acr create -n myRegistry1 -g myResourceGroup -l southcentralus
```

* `--storage-account-name` es opcional. Si no se especifica, se crea una cuenta de almacenamiento con un nombre formado por el nombre del registro y una marca de tiempo del grupo de recursos especificado.

La salida será similar a la siguiente:

![az acr create output](./media/container-registry-get-started-azure-cli/acr_create.png)


Tenga en cuenta especialmente:

* `id`: el identificador del registro en la suscripción, que necesitará si desea asignar una entidad de servicio.
* `loginServer`: el nombre completo que especifique para [iniciar sesión en el registro](container-registry-authentication.md). En este ejemplo, el nombre es `myregistry1.exp.azurecr.io` (todo en minúsculas).

## <a name="assign-a-service-principal"></a>Asignación de una entidad de servicio
Use los comandos de la CLI 2.0 para asignar una entidad de servicio de Azure Active Directory a un registro. A la entidad de servicio en estos ejemplos se le asigna el rol de propietario, pero puede asignar [otros roles](../active-directory/role-based-access-control-configure.md) si lo desea.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Creación de una entidad de servicio y asignación de acceso al registro
En el siguiente comando, se le asigna a una nueva entidad de servicio un acceso de rol de propietario para el identificador del registro pasado con el parámetro `--scopes`. Especifique una contraseña segura con el parámetro `--password`.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Asignación de una entidad de servicio existente
Si ya tiene una entidad de servicio y desea asignar a esta un acceso de rol de propietario al registro, ejecute un comando similar al del ejemplo siguiente. Pase el identificador de la aplicación de la entidad de servicio mediante el parámetro `--assignee`:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Administración de las credenciales de administrador
Se crea automáticamente una cuenta de administrador para cada registro de contenedor, que estará deshabilitada de forma predeterminada. Los ejemplos siguientes muestran los comandos de la CLI `az acr` para administrar las credenciales de administrador del registro de contenedor.

### <a name="obtain-admin-user-credentials"></a>Obtención de las credenciales de administrador
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Habilitación del usuario de administrador para un registro existente
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Deshabilitación del usuario de administrador para un registro existente
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>Lista de las imágenes y etiquetas
Use los comandos de la CLI `az acr` para consultar las imágenes y etiquetas en un repositorio.

> [!NOTE]
> Actualmente, Container Registry no admite el comando `docker search` para consultar las imágenes y etiquetas.


### <a name="list-repositories"></a>Lista de repositorios
En el ejemplo siguiente se enumeran los repositorios de un registro en formato JSON (notación de objetos JavaScript):

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>Lista de etiquetas
En el ejemplo siguiente se muestran las etiquetas del repositorio **samples/nginx**, en formato JSON:

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Pasos siguientes
* [Insertar la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md)

