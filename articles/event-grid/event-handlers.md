---
title: Controladores de eventos de Azure Event Grid
description: Describe los controladores de eventos compatibles con Azure Event Grid
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: ccbabed3fd1df39ac2ba732064392a1a0ecede2a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="event-handlers-in-azure-event-grid"></a>Controladores de eventos de Azure Event Grid

Un controlador de eventos es el lugar al que se envía el evento. El controlador realiza alguna acción adicional para procesar el evento. Varios servicios de Azure se configuran automáticamente para controlar los eventos. También puede usar cualquier webhook para controlar los eventos. El webhook no necesita estar hospedado en Azure para controlar los eventos.

Este artículo contiene vínculos a contenido para cada controlador de eventos.

## <a name="azure-automation"></a>Azure Automation

Use Azure Automation para procesar los eventos con runbooks automatizados.

|Título  |DESCRIPCIÓN  |
|---------|---------|
|[Integración de Azure Automation con Event Grid y Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |Cree una máquina virtual, que envía un evento. El evento desencadena un runbook de Automation que etiqueta la máquina virtual y genera un mensaje que se envía a un canal de Equipos de Microsoft. |

## <a name="azure-functions"></a>Azure Functions

Use Azure Functions para responder sin servidor a los eventos.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Desencadenador de Event Grid para Azure Functions](../azure-functions/functions-bindings-event-grid.md) | Información general sobre el uso del desencadenador de Event Grid en Functions. |
| [Automatización del cambio de tamaño de las imágenes cargadas mediante Event Grid](resize-images-on-storage-blob-upload-event.md) | Los usuarios cargan imágenes a través de la aplicación web en la cuenta de almacenamiento. Cuando se crea un blob de almacenamiento, Event Grid envía un evento a la aplicación de función, que cambia el tamaño de la imagen cargada. |
| [Transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md) | Cuando Event Hubs crea un archivo de captura, Event Grid envía un evento a una aplicación de función. La aplicación recupera el archivo de captura y migra los datos a un almacenamiento de datos. |
| [Ejemplos de integración de Azure Service Bus en Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envía mensajes de temas de Service Bus a la aplicación de función y a la aplicación lógica. |

## <a name="logic-apps"></a>Logic Apps

Use Logic Apps para automatizar los procesos de negocios para responder a eventos.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Monitor virtual machine changes with Azure Event Grid and Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md) (Supervisión de los cambios en máquinas virtuales con Azure Event Grid y Logic Apps) | Una aplicación lógica supervisa los cambios realizados en una máquina virtual y envía mensajes de correo electrónico sobre dichos cambios. |
| [Envío de notificaciones por correo electrónico sobre eventos de Azure IoT Hub mediante Logic Apps](publish-iot-hub-events-to-logic-apps.md) | Una aplicación lógica envía un correo electrónico de notificación cada vez que se agrega un dispositivo al centro de IoT. |
| [Ejemplos de integración de Azure Service Bus en Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid envía mensajes de temas de Service Bus a la aplicación de función y a la aplicación lógica. |

## <a name="queue-storage"></a>Queue Storage

Use Queue Storage para recibir eventos que deben extraerse.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Enrutar eventos personalizados a Azure Queue Storage con la CLI de Azure y Event Grid](custom-event-to-queue-storage.md) | Describe cómo enviar eventos personalizados a una instancia de Queue Storage. |

## <a name="webhooks"></a>WebHooks

Use webhooks para puntos de conexión personalizables que respondan a eventos.

|Título  |DESCRIPCIÓN  |
|---------|---------|
| [Recepción de eventos en un punto de conexión de HTTP](receive-events.md) | Se describe cómo validar un punto de conexión de HTTP para recibir eventos de una suscripción a eventos y, a continuación, recibir y deserializar los eventos. |

## <a name="next-steps"></a>Pasos siguientes

* Para obtener una introducción a Event Grid, vea [Acerca de Event Grid](overview.md).
* Para comenzar a usar rápidamente Event Grid, vea [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).
