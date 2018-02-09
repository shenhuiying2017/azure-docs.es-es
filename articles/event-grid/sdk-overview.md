---
title: SDK de Azure Event Grid
description: "Se describen los SDK de Azure Event Grid. Estos SDK proporcionan administración, publicación y consumo."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK de Event Grid para administración y publicación

Event Grid proporciona SDK que permiten administrar los recursos y exponer eventos mediante programación.

## <a name="management-sdks"></a>SDK de administración

Los SDK de administración permiten crear, actualizar y eliminar suscripciones y temas de Event Grid. En la actualidad, están disponibles los siguientes SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>SDK de publicación

Los SDK de publicación permiten publicar eventos para temas, asegurándose de autenticar, formar el evento y publicarlo de forma asincrónica en el punto de conexión especificado. En la actualidad, están disponibles los siguientes SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>pasos siguientes

* Para ver una introducción a Event Grid, consulte el artículo acerca de [qué es Event Grid](overview.md).
* Para consultar los comandos de Event Grid en la CLI de Azure, vea [CLI de Azure](/cli/azure/eventgrid).
* Para consultar los comandos de Event Grid en PowerShell, vea [PowerShell](/powershell/module/azurerm.eventgrid).