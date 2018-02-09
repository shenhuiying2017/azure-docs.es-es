---
title: "Publicación de eventos en un tema personalizado de Azure Event Grid"
description: "Se describe cómo publicar un evento en un tema personalizado de Azure Event Grid"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 43dcdf9ab0fee5f7e61ecdc42aaf40430e272d92
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publicación en un tema personalizado de Azure Event Grid

En este artículo se describe cómo publicar un evento en un tema personalizado. Muestra el formato de los datos de publicación y eventos. El [Acuerdo de Nivel de Servicio (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) solo se aplica a las publicaciones que coinciden con el formato esperado.

## <a name="endpoint"></a>Punto de conexión

Al enviar el método HTTP POST a un tema personalizado, use el formato del URI: `https://<topic-endpoint>?api-version=2018-01-01`.

Por ejemplo, un URI válido es: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Para obtener el punto de conexión de un tema personalizado con la CLI de Azure, use:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Para obtener el punto de conexión de un tema personalizado con Azure PowerShell, use:

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Encabezado

En la solicitud, incluya un valor de encabezado denominado `aeg-sas-key` que contenga una clave para la autenticación.

Por ejemplo, un valor de encabezado válido es `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Para obtener la clave de un tema personalizado con la CLI de Azure, use:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Para obtener la clave de un tema personalizado con PowerShell, use:

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Datos de evento

Para los temas personalizados, los datos de nivel superior contienen los mismos campos que los eventos estándar definidos por los recursos. Una de esas propiedades es una propiedad de datos que contiene propiedades únicas del tema personalizado. Como publicador de eventos, debe determinar las propiedades del objeto de datos. Use el esquema siguiente:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Para obtener una descripción de estas propiedades, vea [Esquema de eventos de Azure Event Grid](event-schema.md).

Por ejemplo, un esquema de datos de evento válido es:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="next-steps"></a>pasos siguientes

* Para obtener una introducción al enrutamiento de eventos personalizados, vea [Creación y enrutamiento de eventos personalizados con la CLI de Azure y Event Grid](custom-event-quickstart.md) o [Creación y enrutamiento de eventos personalizados con Azure PowerShell y Event Grid](custom-event-quickstart-powershell.md).
* Para más información sobre la clave de autenticación, vea [Seguridad y autenticación de Event Grid](security-authentication.md).
* Para más información acerca de la creación de una suscripción de Azure Event Grid, consulte [Esquema de suscripción de Event Grid](subscription-creation-schema.md).
