---
title: Conceptos de Azure Event Grid
description: Describe Azure Event Grid y sus conceptos. Define varios componentes clave de Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 5b69478bf00284594b984fde452f6bed4e73859b
ms.contentlocale: es-es
ms.lasthandoff: 09/19/2017

---

# <a name="concepts-in-azure-event-grid"></a>Concepts de Azure Event Grid

Los principales conceptos de Azure Event Grid son:

## <a name="events"></a>Eventos

Una evento es la cantidad mínima de información que describe completamente algo que se ha producido en el sistema.  Todos los eventos tienen información común, como: origen del evento, hora en que el evento ha tenido lugar e identificador único.  Cada evento tiene además información específica que solo es relevante para el evento concreto. Por ejemplo, un evento sobre un nuevo archivo que se crea en Azure Storage contiene detalles sobre el archivo, como, por ejemplo, el valor de lastTimeModified. O bien, un evento sobre un reinicio de máquina virtual contiene el nombre de la máquina virtual y el motivo de reinicio. Cada evento tiene 64 KB de datos como máximo.

## <a name="event-sourcespublishers"></a>Orígenes/publicadores de eventos

Un origen de evento es donde se produce el evento. Por ejemplo, Azure Storage es el origen de evento de los eventos creados por blob. Azure VM Fabric es el origen de evento de los eventos de máquina virtual. Los orígenes de evento son responsables de la publicación de eventos en Event Grid.

## <a name="topics"></a>Temas

Los publicadores clasifican los eventos en temas. El tema incluye un punto de conexión al que el publicador envía los eventos. Para responder a determinados tipos de eventos, los suscriptores deciden los temas a los que se suscriben. Los temas ofrecen también un esquema de eventos para que los suscriptores puedan averiguar cómo usar los eventos correctamente.

Los temas del sistema son temas integrados que ofrecen los servicios de Azure. Los temas personalizados son temas de terceros y de aplicación.

## <a name="event-subscriptions"></a>Suscripciones a eventos

Una suscripción indica a Event Grid los eventos sobre un tema que a un suscriptor le interesa recibir.  Una suscripción contiene también información sobre cómo deben entregarse los eventos al suscriptor.

## <a name="event-handlers"></a>Controladores de eventos

Desde la perspectiva de Event Grid, un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento.  Event Grid admite varios tipos de suscriptor. Según el tipo de suscriptor, Event Grid sigue distintos procedimiento para garantizar la entrega del evento.  En el caso de los controladores de eventos de webhook HTTP, el evento se reintenta hasta que el controlador devuelve un código de estado de `200 – OK`. En la cola de Azure Storage, los eventos se reintentan hasta que Queue service puede procesar correctamente la inserción del mensaje en la cola.

## <a name="filters"></a>Filtros

Al suscribirse a un tema, puede filtrar los eventos que se envían al punto de conexión. Puede filtrar por tipo de evento o por patrón de asunto. Para más información, vea [Esquema de suscripción de Event Grid](subscription-creation-schema.md).

## <a name="security"></a>Seguridad

El evento proporciona seguridad para suscribirse a temas y publicar temas. Para suscribirse, debe tener los permisos adecuados en el recurso o el tema. Para publicar, debe tener un token de SAS o autenticación de clave para el tema. Para más información, vea [Event Grid security and authentication](security-authentication.md) (Seguridad y autenticación de Event Grid).

## <a name="failed-delivery"></a>Error de entrega

Cuando Event Grid no puede confirmar que un evento se ha recibido en el punto de conexión del suscriptor, vuelve a entregarlo. Para más información, vea [Entrega y reintento de entrega de mensajes de Event Grid](delivery-and-retry.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
