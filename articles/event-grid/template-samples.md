---
title: 'Plantillas de ejemplo de Azure Resource Manager: Event Grid | Microsoft Docs'
description: Plantillas de ejemplo de Azure Resource Manager para Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246277"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Plantillas de Azure Resource Manager para Event Grid

En la tabla siguiente se incluyen vínculos a plantillas de Azure Resource Manager para Event Grid.

| | |
|-|-|
|**Suscripciones de Event Grid**||
| [Personalización de tema y suscripción con el punto de conexión de WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Implementa un tema personalizado de Event Grid. Crea una suscripción a ese tema personalizado que usa un punto de conexión de WebHook. |
| [Personalización de la suscripción a un tema con el punto de conexión de EventHub](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Crea una suscripción de Event Grid a un tema personalizado que ya existe. La suscripción usa un centro de eventos para el punto de conexión. |
| [Suscripción del grupo de recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Crea una suscripción a eventos de un grupo de recursos. El grupo de recursos que especifique como destino durante la implementación es el origen de los eventos. La suscripción usa un centro de eventos para el punto de conexión. |
| [Cuenta y suscripción de Blob Storage](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Implementa una cuenta de Azure Blob Storage y crea una suscripción a eventos de dicha cuenta. |
| | |
