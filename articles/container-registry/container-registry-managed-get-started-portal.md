---
title: "Creación de un registro privado de Docker: Azure Portal | Microsoft Docs"
description: "Introducción a la creación y la administración de registros de contenedor privados de Docker mediante Azure Portal"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Creación de un registro de contenedores administrado mediante Azure Portal

Azure Container Registry es un servicio de registro de contenedores de Docker administrado usado para almacenar imágenes de contenedor de Docker privadas. Esta guía detalla la creación de una instancia administrada de Azure Container Registry mediante Azure Portal.

Azure Container Registry está en versión preliminar y no está disponible en todas las regiones.

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en Azure Portal: http://portal.azure.com/.

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.

2. Busque **Azure Container Registry** en Marketplace y selecciónelo.

3. Haga clic en **Crear** para abrir la hoja de creación de ACR.

    ![Configuración de Container Registry](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. En la hoja **Azure Container Registry**, escriba la siguiente información. Cuando haya terminado, haga clic en **Crear**.

    a. **Nombre del registro**: un nombre de dominio de nivel superior único global para el registro específico. En este ejemplo, el nombre del registro es *myAzureContainerRegistry1*, pero puede sustituirlo por un nombre único de su elección. El nombre puede contener solo letras y números.

    b. **Grupo de recursos**: seleccione un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) existente o escriba el nombre para crear uno.

    c. **Ubicación**: seleccione una ubicación para el centro de datos de Azure en la que el servicio esté [disponible](https://azure.microsoft.com/regions/services/) como, por ejemplo, **centro-sur de EE. UU**.

    d. **Usuario administrador**: si lo desea, habilite un usuario administrador para acceder al registro. Puede cambiar esta configuración después de crear el registro.

    e. **Usar registro administrado**: seleccione Sí para que ACR administre automáticamente el almacenamiento del registro y el uso de webhooks y la autenticación de AAD.

    f. **Plan de tarifa**: seleccione un plan de tarifa, consulte precios de ACR para más información.

## <a name="log-in-to-acr-instance"></a>Inicio de sesión en la instancia de ACR

Antes de insertar y extraer imágenes de contenedor, debe iniciar sesión en la instancia de ACR. 

Para ello, utilice la CLI de Azure 2.0. En primer lugar, si es necesario, inicie sesión en Azure mediante el comando [az login](/cli/azure/#login). 

```azurecli
az login
```

A continuación, use el comando [az acr login](/cli/azure/acr#login) para iniciar sesión en Azure Container Registry.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

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

En esta guía se ha creado una instancia administrada de Azure Container Registry mediante Azure Portal.

> [!div class="nextstepaction"]
> [Insertar la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md)
