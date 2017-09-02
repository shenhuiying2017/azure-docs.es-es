---
title: Webhook de Azure Container Registry | Microsoft Docs
description: Webhook de Azure Container Registry
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
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: d0190f5725671c320d92b897f0dcef7a526a86e3
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="using-azure-container-registry-webhooks---azure-portal"></a>Uso de webhooks de Azure Container Registry: Azure Portal

Un registro de contenedor de Azure almacena y administra imágenes privadas de contenedor de Docker, de una forma similar a la que Docker Hub almacena imágenes públicas. Use webhooks para desencadenar eventos cuando determinadas acciones tienen lugar en uno de los repositorios de registro. Los webhooks pueden responder a eventos en el nivel de registro o pueden limitarse a una etiqueta de repositorio específica. 

Para más información sobre el entorno y los conceptos, consulte la [introducción](./container-registry-intro.md).

## <a name="prerequisites"></a>Requisitos previos 

- Registro administrado por contenedores de Azure: cree un registro de contenedor administrado en la suscripción de Azure. Por ejemplo, use Azure Portal o la CLI de Azure 2.0. 
- CLI de docker: para configurar el equipo local como un host de Docker y tener acceso a los comandos de la CLI de Docker, instale Docker Engine. 

## <a name="create-webhook-azure-portal"></a>Creación del webhook con Azure Portal

1. Inicie sesión en Azure Portal y navegue hasta el registro en el que desea crear el webhook. 

2. En la hoja de contenedor, seleccione la pestaña "Webhook". 

3. Seleccione "Agregar" en la barra de herramientas de la hoja Webhook. 

4. Complete el formulario *Crear webhook* con la siguiente información:

| Valor | Descripción |
|---|---|
| Nombre | El nombre que desea dar al webhook. Solo puede contener letras minúsculas y números, y tener entre 5 y 50 caracteres. |
| URI de servicio | El identificador URI donde el webhook debe enviar notificaciones POST. |
| Encabezados personalizados | Los encabezados que van a pasar junto con la solicitud POST. Deben tener el formato "clave: valor". |
| Acciones de desencadenador | Acciones que desencadenan el webhook. Actualmente los webhooks pueden activarse mediante acciones de inserción o eliminación en una imagen. |
| Estado | El estado del webhook después de su creación. Esto está habilitada de manera predeterminada. |
| Scope | El ámbito en el que trabaja el webhook. De forma predeterminada el ámbito sirve para todos los eventos del registro. Se puede especificar para un repositorio o etiqueta con el formato "repositorio: etiqueta". |

Formulario de webhook de ejemplo:

![Interfaz de usuario de DCOS](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Crear el webhook con la CLI de Azure

Para crear un webhook mediante la CLI de Azure, use el comando [az acr webhook create](/cli/azure/acr/webhook#create).

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook de prueba

### <a name="azure-portal"></a>Portal de Azure

Antes de usar el webhook en acciones de inserción y eliminación de imágenes de contenedor, se puede probar mediante el botón **Ping**. Cuando se utiliza, el comando Ping envía una solicitud post genérica al punto de conexión especificado y registra la respuesta. Esto resulta útil para comprobar que el webhook se ha configurado correctamente.

1. Seleccione el webhook que desea probar. 
2. En la barra de herramientas superior, seleccione la acción "Ping". 
3. Compruebe la solicitud y respuesta.

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

### <a name="azure-portal"></a>Portal de Azure

Se pueden eliminar los webhooks seleccionando un webhook y, después, el botón Eliminar en Azure Portal.

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```
