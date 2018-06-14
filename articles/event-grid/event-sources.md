---
title: Orígenes de eventos de Azure Event Grid
description: Describe los orígenes de eventos compatibles con Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302133"
---
# <a name="event-sources-in-azure-event-grid"></a>Orígenes de eventos de Azure Event Grid

Un origen de evento es donde se produce el evento. Varios servicios de Azure se configuran automáticamente para enviar eventos. También puede crear aplicaciones personalizadas que envían eventos. Las aplicaciones personalizadas no necesitan estar hospedadas en Azure para usar Event Grid para la distribución de eventos.

Este artículo contiene vínculos a contenido para cada origen de eventos.

## <a name="azure-subscriptions"></a>Suscripciones de Azure

Suscríbase a eventos de suscripciones de Azure para responder a los cambios en los recursos de una suscripción de Azure.

|Título |DESCRIPCIÓN  |
|---------|---------|
| [Integración de Azure Automation con Event Grid y Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Cree una máquina virtual, que envía un evento. El evento desencadena un runbook de Automation que etiqueta la máquina virtual y genera un mensaje que se envía a un canal de Equipos de Microsoft. |
| [Esquema del evento](event-schema-subscriptions.md) | Muestra los campos en los eventos de suscripción de Azure. |

## <a name="custom-topics"></a>Temas personalizados

Suscríbase a temas personalizados para responder a eventos de la aplicación.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Creación y enrutamiento de eventos personalizados con la CLI de Azure](custom-event-quickstart.md) | Muestra cómo utilizar la CLI de Azure para enviar eventos personalizados. |
| [Creación y enrutamiento de eventos personalizados con Azure PowerShell](custom-event-quickstart-powershell.md) | Muestra cómo utilizar Azure PowerShell para enviar eventos personalizados. |
| [Creación y enrutamiento de eventos personalizados con Azure Portal](custom-event-quickstart-portal.md) | Muestra cómo utilizar Azure Portal para enviar eventos personalizados. |
| [Publicación en tema personalizado](post-to-custom-topic.md) | Se describe cómo publicar un evento en un tema personalizado. |
| [Enrutamiento de eventos personalizados a Azure Queue Storage](custom-event-to-queue-storage.md) | Describe cómo enviar eventos personalizados a una instancia de Queue Storage. |
| [Esquema del evento](event-schema.md) | Muestra los campos de eventos personalizados. |

## <a name="event-hubs"></a>Event Hubs

Suscríbase a eventos de Event Hubs para responder a eventos de archivos de captura.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md) | Cuando Event Hubs crea un archivo de captura, Event Grid envía un evento a una aplicación de función. La aplicación recupera el archivo de captura y migra los datos a un almacenamiento de datos. |
| [Esquema del evento](event-schema-event-hubs.md) | Muestra los campos de los eventos de Event Hubs. |

## <a name="iot-hub"></a>IoT Hub

Suscríbase a eventos de IoT Hub para responder a eventos creados y eliminados del dispositivo.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Envío de notificaciones por correo electrónico sobre eventos de Azure IoT Hub mediante Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Una aplicación lógica envía un correo electrónico de notificación cada vez que se agrega un dispositivo al centro de IoT. |
| [Reacción a eventos de IoT Hub usando Event Grid para desencadenar acciones](../iot-hub/iot-hub-event-grid.md) | Información general de la integración de IoT Hubs con Event Grid. |
| [Esquema del evento](event-schema-iot-hub.md) | Muestra los campos de los eventos de IoT Hub. |

## <a name="media-services"></a>Media Services

Suscríbase a eventos de Media Services para responder a eventos de estado de trabajo.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Reacción a eventos de Media Services](../media-services/latest/reacting-to-media-services-events.md) | Información general de la integración de Media Services con Event Grid. |
| [Enrutamiento de eventos de Azure Media Services a un punto de conexión web personalizado mediante la CLI](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Muestra cómo enviar eventos desde Media Services. |
| [Esquema del evento](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Muestra los campos de los eventos de Media Services. |

## <a name="resource-groups"></a>Grupos de recursos

Suscríbase a eventos de grupos de recursos para responder a los cambios de los recursos de un grupo de recursos.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps) | Una aplicación lógica supervisa los cambios realizados en una máquina virtual y envía mensajes de correo electrónico sobre dichos cambios. |
| [Esquema del evento](event-schema-resource-groups.md) | Muestra los campos de eventos de grupos de recursos. |

## <a name="service-bus"></a>Azure Service Bus

Suscríbase a eventos de Service Bus para responder a mensajes sin un agente de escucha activo.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Ejemplos de integración de Azure Service Bus en Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envía mensajes de temas de Service Bus a la aplicación de función y a la aplicación lógica. |
| [Introducción a la integración de Azure Service Bus en Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Información general de la integración de Service Bus con Event Grid. |
| [Esquema del evento](event-schema-service-bus.md) | Muestra los campos de los eventos de Service Bus. |

## <a name="storage"></a>Storage

Suscríbase a eventos de Blob Storage para responder a eventos creados y eliminados del blob.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado con la CLI de Azure](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Muestra cómo utilizar la CLI de Azure para enviar eventos de Blob Storage. |
| [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado con PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Muestra cómo utilizar Azure PowerShell para enviar eventos de Blob Storage. |
| [Reacción a eventos de Blob Storage](../storage/blobs/storage-blob-event-overview.md) | Información general de la integración de Blob Storage con Event Grid. |
| [Esquema del evento](event-schema-blob-storage.md) | Muestra los campos de los eventos de Blob Storage. |

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
