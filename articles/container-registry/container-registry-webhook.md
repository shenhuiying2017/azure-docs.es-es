---
title: Webhooks de Azure Container Registry
description: Aprenda a usar webhooks para desencadenar eventos cuando determinadas acciones tengan lugar en uno de los repositorios del registro.
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acr, azure-container-registry
keywords: Docker, Contenedores, ACR
ms.assetid: 
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2017
ms.author: nepeters
ms.openlocfilehash: 5a9dab91aafb92f944b473f05144242143e36477
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2017
---
# <a name="using-azure-container-registry-webhooks"></a>Webhooks de Azure Container Registry

Un registro de contenedor de Azure almacena y administra imágenes privadas de contenedor de Docker, de una forma similar a la que Docker Hub almacena imágenes públicas. Use webhooks para desencadenar eventos cuando determinadas acciones tienen lugar en uno de los repositorios de registro. Los webhooks pueden responder a eventos en el nivel de registro o pueden limitarse a una etiqueta de repositorio específica.

Para más información y conceptos, vea [¿Qué es Azure Container Registry?](./container-registry-intro.md).

## <a name="prerequisites"></a>Requisitos previos

- Registro administrado por contenedores de Azure: cree un registro de contenedor administrado en la suscripción de Azure. Por ejemplo, use [Azure Portal](container-registry-get-started-portal.md) o la [CLI de Azure](container-registry-get-started-azure-cli.md).
- CLI de docker: para configurar el equipo local como un host de Docker y tener acceso a los comandos de la CLI de Docker, instale [Docker Engine](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Creación del webhook con Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya hasta el registro en el que desea crear webhooks.

2. En la hoja de contenedor, seleccione **Webhooks** bajo **SERVICIOS**.

3. Seleccione **Agregar** en la barra de herramientas de la hoja Webhook.

4. Complete el formulario *Crear webhook* con la siguiente información:

| Valor | Descripción |
|---|---|
| Nombre | El nombre que desea dar al webhook. Puede contener solo caracteres en minúsculas y números, y su longitud debe oscilar entre 5 y 50 caracteres. |
| URI de servicio | El identificador URI donde el webhook debe enviar notificaciones POST. |
| Encabezados personalizados | Los encabezados que van a pasar junto con la solicitud POST. Deben tener el formato "clave: valor". |
| Acciones de desencadenador | Acciones que desencadenan el webhook. Actualmente los webhooks pueden activarse mediante acciones de inserción o eliminación en una imagen. |
| Estado | El estado del webhook después de su creación. Esto está habilitada de manera predeterminada. |
| Scope | El ámbito en el que trabaja el webhook. De forma predeterminada, el ámbito sirve para todos los eventos del registro. Se puede especificar para un repositorio o etiqueta con el formato "repositorio: etiqueta". |

Formulario de webhook de ejemplo:

![Interfaz de usuario de creación de webhook ACR en Azure Portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Crear el webhook con la CLI de Azure

Para crear un webhook mediante la CLI de Azure, use el comando [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook de prueba

### <a name="azure-portal"></a>Azure Portal

Antes de usar el webhook en acciones de inserción y eliminación de imágenes de contenedor, puede probarlo mediante el botón **Ping**. El comando Ping envía una solicitud POST genérica al punto de conexión especificado y registra la respuesta. Puede ayudarle a comprobar que ha configurado correctamente el webhook.

1. Seleccione el webhook que desea probar.
2. En la barra de herramientas superior, seleccione **Ping**.
3. Compruebe la respuesta del punto de conexión en la columna **ESTADO HTTP**.

![Interfaz de usuario de creación de webhook ACR en Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>CLI de Azure

Para probar un webhook de ACR con la CLI de Azure, use el comando [az acr webhook ping](/cli/azure/acr/webhook#ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver los resultados, use el comando [az acr webhook list-events](/cli/azure/acr/webhook#list-events).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Eliminación del webhook

### <a name="azure-portal"></a>Azure Portal

Se puede eliminar cada webhook seleccionando uno y, después, con el botón **Eliminar** en Azure Portal.

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```
