---
title: Referencia de esquema de webhook de Azure Container Registry
description: Referencia de carga de solicitud JSON de webhook para Azure Container Registry.
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: 84f0277a7b1a5bd7dfe2178f78f34140b1dd2642
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-webhook-reference"></a>Referencia de webhook de Azure Container Registry

También puede [configurar webhooks](container-registry-webhook.md) para el registro de contenedor que generan eventos cuando se realizan determinadas acciones en el mismo. Por ejemplo, puede habilitar webhooks que se desencadenan en las operaciones `push` y `delete` de una imagen de contenedor. Cuando se desencadena un webhook, Azure Container Registry emite una solicitud HTTP o HTTPS que contiene información sobre el evento a un punto de conexión que especifique. El punto de conexión, a continuación, puede procesar el webhook y actuar en consecuencia.

Las secciones siguientes detallan el esquema de las solicitudes de webhook generadas por eventos compatibles. Las secciones de evento contienen el esquema de carga para el tipo de evento, una carga de solicitud de ejemplo y uno o más comandos de ejemplo que desencadenarán el webhook.

Para obtener información acerca de cómo configurar webhooks para Azure Container Registry, consulte [Uso de webhooks de Azure Container Registry](container-registry-webhook.md).

## <a name="webhook-requests"></a>Solicitudes de webhook

### <a name="http-request"></a>Solicitud HTTP

Un webhook desencadenado realiza una solicitud `POST` HTTP a la dirección URL del punto de conexión que especificó cuando configuró el webhook.

### <a name="http-headers"></a>Encabezados HTTP

Las solicitudes de webhook incluir un elemento `Content-Type` de `application/json` si no ha especificado un encabezado personalizado `Content-Type` para el webhook.

No se agregan otros encabezados a la solicitud aparte de los encabezados personalizados que pueda haber especificado para el webhook.

## <a name="push-event"></a>Evento de inserción

Webhook que se desencadena cuando se inserta una imagen de contenedor en un repositorio.

### <a name="push-event-payload"></a>Carga del evento de inserción

|Elemento|Escriba|Descripción|
|-------------|----------|-----------|
|`id`|String|El identificador del evento de webhook.|
|`timestamp`|DateTime|La hora en la que se desencadenó el evento de webhook.|
|`action`|String|La acción que desencadenó el evento de webhook.|
|[Destino](#target)|Tipo complejo|El destino del evento que desencadenó el evento de webhook.|
|[Solicitud](#request)|Tipo complejo|La solicitud que generó el evento de webhook.|

### <a name="target"></a>Destino

|Elemento|Escriba|Descripción|
|------------------|----------|-----------|
|`mediaType`|String|El tipo MIME del objeto al que se hace referencia.|
|`size`|Int32|El número de bytes del contenido. Igual que el campo de longitud.|
|`digest`|String|El resumen del contenido, de acuerdo con la especificación de API HTTP V2 del registro.|
|`length`|Int32|El número de bytes del contenido. Igual que el campo de tamaño.|
|`repository`|String|El nombre del repositorio.|
|`tag`|String|El nombre de etiqueta de imagen.|

### <a name="request"></a>request

|Elemento|Escriba|Descripción|
|------------------|----------|-----------|
|`id`|String|El identificador de la solicitud que inició el evento.|
|`host`|String|El nombre de host accesible desde el exterior de la instancia del registro, según lo especificado por el encabezado de host HTTP en las solicitudes entrantes.|
|`method`|String|El método de la solicitud que generó el evento.|
|`useragent`|String|El encabezado de agente de usuario de la solicitud.|

### <a name="payload-example-push-event"></a>Ejemplo de carga: evento de inserción

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Comando de la [CLI de Docker](https://docs.docker.com/engine/reference/commandline/cli/) de ejemplo que desencadena el webhook del evento de **inserción**:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Eliminar evento

Webhook que se desencadena cuando se elimina un repositorio o un manifiesto. No se desencadena cuando se elimina una etiqueta.

### <a name="delete-event-payload"></a>Carga del evento de eliminación

|Elemento|Escriba|Descripción|
|-------------|----------|-----------|
|`id`|String|El identificador del evento de webhook.|
|`timestamp`|DateTime|La hora en la que se desencadenó el evento de webhook.|
|`action`|String|La acción que desencadenó el evento de webhook.|
|[Destino](#delete_target)|Tipo complejo|El destino del evento que desencadenó el evento de webhook.|
|[Solicitud](#delete_request)|Tipo complejo|La solicitud que generó el evento de webhook.|

### <a name="delete_target"></a> destino

|Elemento|Escriba|Descripción|
|------------------|----------|-----------|
|`mediaType`|String|El tipo MIME del objeto al que se hace referencia.|
|`digest`|String|El resumen del contenido, de acuerdo con la especificación de API HTTP V2 del registro.|
|`repository`|String|El nombre del repositorio.|

### <a name="delete_request"></a> solicitud

|Elemento|Escriba|Descripción|
|------------------|----------|-----------|
|`id`|String|El identificador de la solicitud que inició el evento.|
|`host`|String|El nombre de host accesible desde el exterior de la instancia del registro, según lo especificado por el encabezado de host HTTP en las solicitudes entrantes.|
|`method`|String|El método de la solicitud que generó el evento.|
|`useragent`|String|El encabezado de agente de usuario de la solicitud.|

### <a name="payload-example-delete-event"></a>Ejemplo de carga: evento de eliminación

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Comandos de la [CLI de Azure 2.0](/cli/azure/acr) de ejemplo que desencadenan un webhook del evento de **eliminación**:

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Pasos siguientes

[Uso de webhooks de Azure Container Registry](container-registry-webhook.md)