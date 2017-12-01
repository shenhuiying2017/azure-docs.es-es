---
title: "Información general sobre Azure Functions | Microsoft Docs"
description: "Entender cómo utilizar Azure Functions para optimizar las cargas de trabajo asincrónicas en minutos."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "Azure funciones, funciones, procesamiento de eventos, webhooks, proceso dinámico, arquitectura sin servidor"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d60c898225b944801504f38d536262134a31e021
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2017
---
# <a name="an-introduction-to-azure-functions"></a>Introducción a Azure Functions  
Azure Functions es una solución para ejecutar fácilmente pequeños fragmentos de código, o "funciones", en la nube. Simplemente, puede escribir el código que necesita para el problema en cuestión, sin preocuparse de toda la aplicación o la infraestructura para ejecutarlo. Functions puede hacer que el desarrollo sea aún más productivo y, además, le permite utilizar el lenguaje de desarrollo que prefiera, como C#, F#, Node.js, Java o PHP. Pague solo el tiempo durante el que se ejecuta el código y confíe en Azure para escalar según sea necesario. Azure Functions permite desarrollar aplicaciones [sin servidor](https://azure.microsoft.com/overview/serverless-computing/) en Microsoft Azure.

Este tema proporciona información general de alto nivel de Azure Functions. Si desea comenzar de inmediato y empezar a trabajar con Functions, comience con el artículo [Creación de su primera función de Azure](functions-create-first-azure-function.md). Si busca información más técnica acerca de las Funciones, consulte [Referencia para desarrolladores de Funciones de Azure](functions-reference.md).

## <a name="features"></a>Características
Estas son algunas características clave de Functions:

* **Opción de lenguaje**: escriba funciones usando el lenguaje C#, F# o Javascript de su elección. Consulte [Lenguajes admitidos](supported-languages.md) para ver otras opciones.
* **Modelo de precios de pago por uso** : pague solo el tiempo que haya empleado ejecutando el código. Consulte la opción del plan de hospedaje de Consumo en la [sección de precios](#pricing).  
* **Traiga sus propias dependencias** : Funciones de Azure admite NuGet y NPM, para que pueda usar sus bibliotecas favoritas.  
* **Seguridad integrada** : proteja las funciones desencadenadas por HTTP con los proveedores de OAuth como Azure Active Directory, Facebook, Google, Twitter y cuenta Microsoft.  
* **Integración simplificada** : fácil aprovechamiento de los servicios de Azure y ofertas de software como servicio (SaaS). Para ver algunos ejemplos, consulte la [sección de integraciones](#integrations).  
* **Desarrollo flexible**: codifique las funciones directamente en el portal o configure la integración continua e implemente el código mediante [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md) y otras [herramientas de desarrollo compatibles](../app-service/app-service-deploy-local-git.md).  
* **Código abierto** : el tiempo de ejecución de Funciones de Azure es de código abierto y está [disponible en GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>¿Qué puedo hacer con las funciones?
Functions es una excelente solución para procesar datos e integrar sistemas, trabajar con Internet de las cosas (IoT) y generar API simples y microservicios. Puede usar Functions para tareas como procesamiento de imágenes o pedidos, mantenimiento de archivos o para tareas que quiera ejecutar según una programación. 

Funciones de Azure ofrece plantillas para comenzar con situaciones clave, incluidas las siguientes:

* **HTTPTrigger** : desencadenar la ejecución del código mediante una solicitud HTTP. Para obtener un ejemplo, vea [Creación de la primera función](functions-create-first-azure-function.md).
* **TimerTrigger** : ejecutar limpieza u otras tareas de lote dentro de una programación predefinida. Para obtener un ejemplo, vea [Creación de una función desencadenada por un temporizador](functions-create-scheduled-function.md).
* **GitHub webhook** : responder a los eventos que se producen en los repositorios de GitHub. Para obtener un ejemplo, vea [Creación de una función desencadenada por Webhook de GitHub](functions-create-github-webhook-triggered-function.md).
* **Webhook genérico** : procesar las solicitudes HTTP de webhook de cualquier servicio que admita webhooks. Para obtener un ejemplo, vea [Creación de una función desencadenada por un webhook genérico](functions-create-generic-webhook-triggered-function.md).
* **CosmosDBTrigger**: procese documentos de Azure Cosmos DB cuando se agregan o se actualizan en las colecciones en una base de datos NoSQL. Para obtener un ejemplo, vea [Creación de una función desencadenada por Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md).
* **BlobTrigger** : procesar blobs de Azure Storage cuando se agregan a los contenedores. Esta función se puede usar para cambiar el tamaño de las imágenes. Para más información, consulte [Enlaces de Blob Storage](functions-bindings-storage-blob.md).
* **QueueTrigger** : responder a mensajes conforme llegan a una cola de Azure Storage. Para obtener un ejemplo, vea [Crear una función desencadenada por Azure Queue Storage](functions-create-storage-queue-triggered-function.md).
* **EventHubTrigger**: responder a eventos proporcionados a un Centro de eventos de Azure. Especialmente útil en escenarios de Internet de las cosas, procesamiento del flujo de trabajo o de la experiencia del usuario y en instrumentación de aplicaciones. Para más información, consulte [Enlaces de Event Hubs](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger**: permite conectar el código a otros servicios de Azure o servicios locales, mediante la escucha de las colas de mensajes. Para más información, consulte [Enlaces de Service Bus](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger**: permite conectar el código a otros servicios de Azure o a servicios locales mediante la suscripción a temas. Para más información, consulte [Enlaces de Service Bus](functions-bindings-service-bus.md).

Azure Functions admite *desencadenadores* que son formas de iniciar la ejecución del código, y *enlaces* que son formas de simplificar la codificación para los datos de entrada y salida. Para una descripción detallada de los desencadenadores y los enlaces que proporciona Azure Functions, consulte [Referencias para desarrolladores de desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md).

## <a name="integrations"></a>Integraciones
Azure Functions se integra con diversos servicios de Azure y de terceros. Dichos servicios pueden desencadenar una función e iniciar su ejecución, o bien servir de entrada y salida del código. Las siguientes integraciones de servicio son compatibles con Azure Functions:

* Azure Cosmos DB
* Azure Event Hubs 
* Azure Event Grid
* Azure Mobile Apps (tablas)
* Azure Notification Hubs
* Azure Service Bus (colas y temas)
* Azure Storage (blob, colas y tablas) 
* GitHub (webhooks)
* Local (mediante Service Bus)
* Twilio (mensajes SMS)

## <a name="pricing"></a>¿Cuánto cuesta Funciones de Azure?
Azure Functions tiene dos tipos de planes de precios. Elija la que mejor se adapte a sus necesidades: 

* **Plan de Consumo**: cuando se ejecuta la función, Azure proporciona todos los recursos informáticos necesarios. No tiene que preocuparse de la administración de recursos y solo paga por el tiempo que haya empleado ejecutando el código. 
* **plan de App Service** : se ejecutan las funciones igual que aplicaciones web, móviles y de API. Cuando ya se usa App Service para las otras aplicaciones, las funciones pueden ejecutarse en el mismo plan sin costo adicional. 

Para más información sobre planes de hospedaje, vea [Comparación de los planes de hospedaje de Azure Functions](functions-scale.md). Puede encontrar todos los detalles de precios en la [página de Precios de Funciones](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Pasos siguientes
* [Creación de su primera función de Azure](functions-create-first-azure-function.md)  
  Comience de inmediato y cree su primera función mediante el inicio rápido de Azure Functions. 
* 
              [Referencia para desarrolladores de Azure Functions](functions-reference.md)  
Proporciona información técnica sobre el tiempo de ejecución de Azure Functions y una referencia para las funciones de codificación y la definición de enlaces y desencadenadores.
* 
              [Prueba de Azure Functions](functions-test-a-function.md)  
describe las diversas herramientas y técnicas para probar sus funciones.
* [How to scale Azure Functions](functions-scale.md)  
  Trata los planes de servicio disponibles con Azure Functions, incluido el plan de hospedaje de Consumo, y cómo elegir el plan adecuado. 
* [¿Qué es Azure App Service?](../app-service/app-service-web-overview.md)  
  Azure Functions aprovecha Azure App Service para obtener una funcionalidad básica como son las implementaciones, las variables de entorno y los diagnósticos. 

