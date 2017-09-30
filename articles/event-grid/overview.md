---
title: "Introducción a Azure Event Grid"
description: Describe Azure Event Grid y sus conceptos.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/11/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 358c1f4bca2ced207caf599db2fb1453ca8bc41e
ms.contentlocale: es-es
ms.lasthandoff: 09/19/2017

---

# <a name="an-introduction-to-azure-event-grid"></a>Una introducción a Azure Event Grid

Azure Event Grid permite crear fácilmente aplicaciones con arquitecturas basadas en eventos. Seleccione el recurso de Azure al que le gustaría suscribirse y asigne el controlador de eventos o el punto de conexión de WebHook para enviar el evento. Event Grid tiene compatibilidad integrada para eventos procedentes de los servicios de Azure, como los blobs de almacenamiento y los grupos de recursos. Event Grid también tiene soporte personalizado para aplicaciones y eventos de otros fabricantes, con temas personalizados y webhooks personalizados. 

Puede usar filtros para enrutar eventos específicos a distintos puntos de conexión, multidifusión a varios puntos de conexión y asegurarse de que los eventos se entregan de forma confiable. Event Grid también tiene compatibilidad integrada para eventos personalizados y de terceros.

En la versión preliminar, Event Grid admite las ubicaciones **westus2** y **westcentralus**. Se agregarán otras regiones.

Este artículo ofrece información general sobre Azure Event Grid. Para comenzar a usar rápidamente Event Grid, consulte [Creación y enrutamiento de eventos personalizados con Azure Event Grid](custom-event-quickstart.md).

![Modelo funcional de Event Grid](./media/overview/event-grid-functional-model.png)

Actualmente, Blob Storage no está disponible públicamente como publicador. Debe registrarse para que la versión preliminar reaccione a los eventos de Blob Storage. Para más información, vea [Enrutamiento de eventos de Blob Storage a un punto de conexión web personalizado (versión preliminar)](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json).

## <a name="concepts"></a>Conceptos

Hay cinco conceptos en Azure Event Grid que le permiten empezar a trabajar:

* **Eventos**: ¿qué ha ocurrido?
* **Orígenes de eventos y publicadores**: ¿dónde tuvo lugar el evento?
* **Temas**: el punto de conexión donde los publicadores envían los eventos.
* **Suscripciones a eventos**: el punto de conexión o mecanismo integrado para enrutar eventos, a veces a varios controladores. Los controladores también usan las suscripciones para filtrar los eventos de entrada de forma inteligente.
* **Controladores de eventos**: la aplicación o servicio que reacciona al evento.

Para más información acerca de estos conceptos, consulte [Conceptos en Azure Event Grid](concepts.md).

## <a name="capabilities"></a>Capacidades

Estas son algunas características clave de Azure Event Grid:

* **Simplicidad**: seleccione y haga clic para conseguir eventos desde el recurso de Azure a cualquier controlador de eventos o punto de conexión.
* **Filtrado avanzado**: filtre por tipo de evento o por ruta de acceso del publicador del evento para asegurarse de que los controladores de eventos solo reciben eventos pertinentes.
* **Distribución ramificada**: suscriba varios puntos de conexión al mismo evento para enviar copias del evento a tantos lugares como sea necesario.
* **Confiabilidad**: uso de 24 horas de reintento con retroceso exponencial para asegurarse de que se entregan los eventos.
* **Pago por evento**: pague solo por la cantidad utilizada en Event Grid.
* **Alto rendimiento**: cree cargas de trabajo de gran volumen en Event Grid con soporte para millones de eventos por segundo.
* **Eventos integrados**: desarrolle y ejecute rápidamente con eventos integrados definidos por el recurso.
* **Eventos personalizados**: use la ruta, el filtrado y la entrega confiable de eventos personalizados de Event Grid en su aplicación.

## <a name="built-in-publisher-and-handler-integration"></a>Integración incorporada del publicador y el controlador

Azure ofrece compatibilidad de eventos integrada con numerosos servicios, incluidos tanto publicadores como controladores.

### <a name="publishers"></a>Publicadores

Actualmente, los siguientes servicios de Azure tienen compatibilidad de publicador integrada para Event Grid:

* Grupos de recursos (operaciones de administración)
* Suscripciones de Azure (operaciones de administración)
* Event Hubs
* Temas personalizados

Otros servicios de Azure se agregarán este año.

### <a name="handlers"></a>Controladores

Actualmente, los siguientes servicios de Azure tienen compatibilidad de controlador integrada para Event Grid: 

* Funciones de Azure
* Logic Apps
* Azure Automation
* WebHooks
* Microsoft Flow

Otros servicios de Azure se agregarán este año.

## <a name="what-can-i-do-with-event-grid"></a>¿Qué puedo hacer con Event Grid?

Azure Event Grid proporciona varias funcionalidades que mejoran considerablemente el trabajo sin servidor, la automatización de operaciones y la integración: 

### <a name="serverless-application-architectures"></a>Arquitecturas de aplicación sin servidor

![Aplicación sin servidor](./media/overview/serverless_web_app.png)

Event Grid conecta orígenes de datos y controladores de eventos. Por ejemplo, use Event Grid para desencadenar al instante una función sin servidor que ejecute un análisis de la imagen cada vez que se agrega una fotografía nueva a un contenedor de Blob Storage. 

### <a name="ops-automation"></a>Automatización de operaciones

![Automatización de operaciones](./media/overview/Ops_automation.png)

Event Grid permite agilizar la automatización y simplificar el cumplimiento de directivas. Por ejemplo, Event Grid puede enviar una notificación a Azure Automation cuando se crea una máquina virtual o cuando se pone en marcha una instancia de SQL Database. Estos eventos se pueden usar para comprobar de forma automática que la configuración del servicio es conforme, poner metadatos en herramientas de operaciones, etiquetar máquinas virtuales o archivar elementos de trabajo.

### <a name="application-integration"></a>Integración de aplicaciones

![Integración de aplicaciones](./media/overview/app_integration.png)

Event Grid conecta su aplicación con otros servicios. Por ejemplo, cree un tema personalizado para enviar los datos de eventos de su aplicación a Event Grid y aprovechar la entrega confiable, el enrutamiento avanzado y la integración directa con Azure que ofrece. También puede usar Event Grid con Logic Apps para procesar datos en cualquier parte, sin necesidad de escribir código. 

## <a name="how-is-event-grid-different-from-other-azure-integration-services"></a>¿En qué se diferencia Event Grid de otros servicios de integración de Azure?

Event Grid es un panel posterior de eventos que habilita la programación orientada a eventos y reactiva. Está totalmente integrado con los servicios de Azure y puede integrarse con servicios de terceros. El mensaje del evento contiene la información que necesita para reaccionar a los cambios en los servicios y aplicaciones. Event Grid no es una canalización de datos y no entrega el objeto real que se ha actualizado.

Service Bus se adapta perfectamente a aplicaciones empresariales tradicionales que requieren transacciones, ordenación, detección de duplicados y coherencia inmediata. Event Grid está diseñado para la velocidad, la escala, la amplitud y el bajo costo en un modelo reactivo. Se adapta perfectamente a una arquitectura sin servidor.

Event Grid complementa otros servicios de Azure como Logic Apps y Event Hubs. Event Grid desencadena el comienzo del flujo de trabajo de la aplicación lógica. Event Hubs trabaja con Event Grid permitiéndole reaccionar a eventos de Event Hubs Capture y crear canalizaciones de entrada y transformación de datos.

## <a name="how-much-does-event-grid-cost"></a>¿Cuánto cuesta Event Grid?

Azure Event Grid usa un modelo de precios de pago por evento, por lo que solo se paga por lo que usa.

Event Grid tiene un costo de 0,60 $ por cada millón de operaciones (0,30 $ durante la versión preliminar) y las primeras 100.000 operaciones al mes son gratuitas. Estas operaciones se definen como eventos de entrada, coincidencia avanzada, intentos de entrega y llamadas de administración.  Puede encontrar más detalles en la [página de precios](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="next-steps"></a>Pasos siguientes

* [Crear eventos personalizados y suscribirse a ellos](custom-event-quickstart.md)  
  Para comenzar de inmediato y empezar a enviar sus propios eventos personalizados a cualquier punto de conexión con el inicio rápido de Azure Event Grid.
* [Uso de Logic Apps como controlador de eventos](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Tutorial sobre la creación de una aplicación con Logic Apps para reaccionar ante eventos enviados por Event Grid.
* [Transmisión de macrodatos a un almacén de datos](event-grid-event-hubs-integration.md)  
  Tutorial en el que se usa Azure Functions para transmitir datos desde Event Hubs al SQL Data Warehouse.
* [Referencia de la API de REST de Event Grid](/rest/api/eventgrid)  
  Proporciona más información técnica sobre Azure Event Grid y una referencia para administrar suscripciones a eventos, enrutamiento y filtrado.
