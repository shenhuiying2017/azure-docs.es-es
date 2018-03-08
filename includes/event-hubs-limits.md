---
title: "archivo de inclusión"
description: "archivo de inclusión"
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
En la tabla siguiente se enumeran las cuotas y los límites específicos de [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Para más información sobre los precios de Event Hubs, consulte los [precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Límite | Scope | Notas | Valor |
| --- | --- | --- | --- | --- |
| Número de centros de eventos por espacio de nombres |Espacio de nombres |Se rechazarán las solicitudes posteriores para la creación de un nuevo centro de eventos. |10 |
| Número de particiones por centro de eventos |Entidad |- |32 |
| Número de grupos de consumidores por centro de eventos |Entidad |- |20  |
| Número de conexiones de AMQP por espacio de nombres |Espacio de nombres |Las solicitudes posteriores de conexiones adicionales se rechazarán y el código que realizó la llamada recibe una excepción. |5.000 |
| Tamaño máximo de evento de Event Hubs|Entidad |- |256 KB |
| Tamaño máximo del nombre de un centro de eventos |Entidad |- |50 caracteres |
| Número de destinatarios no de época por grupo de consumidores |Entidad |- |5 |
| Período de retención máximo de datos de eventos |Entidad |- |1-7 días |
| Unidades de rendimiento máximo |Espacio de nombres |Si se supera el límite de unidades de rendimiento, los datos se limitan y se genera un excepción **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Para solicitar un número mayor de unidades de rendimiento para el nivel Estándar rellene una [solicitud de soporte técnico](/azure/azure-supportability/how-to-create-azure-support-request). Las [unidades de rendimiento adicionales](../articles/event-hubs/event-hubs-auto-inflate.md) se encuentran disponibles en bloques de 20 y están sujetas a un compromiso de compra. |20  |
| Número de reglas de autorización por espacio de nombres |Espacio de nombres|Se rechazarán las solicitudes posteriores de creación de reglas de autorización.|12 |
