---
title: Conceptos de Azure Event Grid
description: Describe Azure Event Grid y sus conceptos. Define varios componentes clave de Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/24/2018
ms.author: babanisa
ms.openlocfilehash: 8ddde98b448f4d6d6f24a2ee47acf9240593622c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="concepts-in-azure-event-grid"></a>Concepts de Azure Event Grid

En este artículo se describen los principales conceptos de Azure Event Grid.

## <a name="events"></a>Eventos

Una evento es la cantidad mínima de información que describe completamente algo que se ha producido en el sistema. Todos los eventos tienen información común, como: origen del evento, hora en que el evento ha tenido lugar e identificador único. Cada evento tiene además información específica que solo es relevante para el tipo de evento concreto. Por ejemplo, un evento sobre un nuevo archivo que se crea en Azure Storage contiene detalles sobre el archivo, como el valor `lastTimeModified`. O bien, un evento de Event Hubs tiene la dirección URL del archivo de captura. Cada evento tiene 64 KB de datos como máximo.

## <a name="event-sourcespublishers"></a>Orígenes/publicadores de eventos

Un origen de evento es donde se produce el evento. Por ejemplo, Azure Storage es el origen de evento de los eventos creados por blob. Azure VM Fabric es el origen de evento de los eventos de máquina virtual. Los orígenes de evento son responsables de la publicación de eventos en Event Grid.

Para información sobre la implementación de cualquiera de los orígenes de Event Grid admitidos, consulte [Event sources in Azure Event Grid](event-sources.md) (Orígenes de eventos en Azure Event Grid).

## <a name="topics"></a>Temas

Los publicadores clasifican los eventos en temas. El tema de Event Grid incluye un punto de conexión al que el publicador envía los eventos. Para responder a determinados tipos de eventos, los suscriptores deciden los temas a los que se suscriben. Los temas ofrecen también un esquema de eventos para que los suscriptores puedan aprender a usar los eventos correctamente.

Los temas del sistema son temas integrados que ofrecen los servicios de Azure. Los temas personalizados son temas de terceros y de aplicación.

Cuando diseñe la aplicación, tiene flexibilidad al decidir cuántos temas se crean. Para soluciones grandes, cree un tema personalizado para cada categoría de eventos relacionados. Por ejemplo, considere una aplicación que envía eventos relacionados con la modificación de las cuentas de usuario y el procesamiento de pedidos. Es poco probable que algún controlador de eventos quiera ambas categorías de eventos. Cree dos temas personalizados y deje que los controladores de eventos se suscriban a uno que les interese. Para soluciones pequeñas, puede que prefiera enviar todos los eventos a un solo tema. Los suscriptores de eventos se pueden filtrar por los tipos de evento que desean.

## <a name="event-subscriptions"></a>Suscripciones a eventos

Una suscripción indica a Event Grid los eventos sobre un tema que a un suscriptor le interesa recibir. Una suscripción contiene también información sobre cómo deben entregarse los eventos al suscriptor.

## <a name="event-handlers"></a>Controladores de eventos

Desde la perspectiva de Event Grid, un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Event Grid admite varios tipos de suscriptor. Según el tipo de suscriptor, Event Grid sigue distintos procedimiento para garantizar la entrega del evento. En el caso de los controladores de eventos de webhook HTTP, el evento se reintenta hasta que el controlador devuelve un código de estado de `200 – OK`. En la cola de Azure Storage, los eventos se reintentan hasta que Queue service puede procesar correctamente la inserción del mensaje en la cola.

Para información sobre cómo implementar cualquiera de los controladores admitidos de Event Grid, consulte [Event handlers in Azure Event Grid](event-handlers.md) (Controladores de eventos en Azure Event Grid).

## <a name="filters"></a>Filtros

Al suscribirse a un tema de Event Grid, puede filtrar los eventos que se envían al punto de conexión. Puede filtrar por tipo de evento o por patrón de asunto. Para más información, vea [Esquema de suscripción de Event Grid](subscription-creation-schema.md).

## <a name="security"></a>Seguridad

Event Grid proporciona seguridad para suscribirse a temas y publicarlos. Para suscribirse, debe tener los permisos adecuados en el recurso o el tema de Event Grid. Para publicar, debe tener un token de SAS o autenticación de clave para el tema. Para más información, vea [Event Grid security and authentication](security-authentication.md) (Seguridad y autenticación de Event Grid).

## <a name="failed-delivery"></a>Error de entrega

Si Event Grid no puede confirmar que un evento se ha recibido en el punto de conexión del suscriptor, vuelve a entregarlo. Para más información, vea [Entrega y reintento de entrega de mensajes de Event Grid](delivery-and-retry.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
