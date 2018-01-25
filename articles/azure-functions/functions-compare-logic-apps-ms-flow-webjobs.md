---
title: "Elección entre Flow, Logic Apps, Functions y WebJobs | Microsoft Docs"
description: "Compare y contraste los servicios de integración en la nube de Microsoft y decida cuál usar."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: "microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7ffe44828735a5687008ebc5a7d8d9f017f49daa
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Elección entre Flow, Logic Apps, Functions y WebJobs
En este artículo se comparan y contrastan los siguientes servicios en la nube de Microsoft. Todos ellos pueden resolver los problemas de integración y la automatización de los procesos empresariales:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service](../app-service/web-sites-create-web-jobs.md)

Todos estos servicios son útiles cuando se agrupan sistemas dispares. Todos pueden definir entradas, acciones, condiciones y salidas. Todos se pueden ejecutar según una programación o un desencadenador. Sin embargo, cada servicio tiene ventajas únicas y no se trata de saber cuál es el mejor, sino cuál es el más adecuado para su situación. Con frecuencia, la mejor manera de crear rápidamente una solución de integración completa y escalable es combinar estos servicios.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow frente a Logic Apps
Podemos hablar sobre Microsoft Flow y Azure Logic Apps de forma conjunta porque ambos son servicios de integración con una *configuración de línea base*. Facilitan la creación de flujos de trabajo y procesos, y la integración con varias aplicaciones SaaS y de empresa. 

* Flow se crea encima de Logic Apps
* Tienen el mismo diseñador de flujo de trabajo
* [conectores](../connectors/apis-list.md) que funcionan en uno también pueden funcionar en el otro

Flow permite a los administrativos realizar integraciones sencillas (por ejemplo, un proceso de aprobación en una biblioteca de documentos de SharePoint) sin pasar por los desarrolladores o el departamento de TI. Por otro lado, Logic Apps puede permitir integraciones avanzadas (por ejemplo, procesos B2B) donde se requieren procedimientos DevOps y de seguridad. Lo normal es que la complejidad de un flujo de trabajo crezca con el tiempo. En consecuencia, puede comenzar con un flujo al principio y luego convertirlo en una aplicación lógica cuando sea necesario.

La tabla siguiente le ayuda a determinar qué es lo mejor para una integración dada, si Flow o Logic Apps.

|  | Flujo | Logic Apps |
| --- | --- | --- |
| Público |Administrativos, usuarios empresariales, administradores de SharePoint |Integradores profesionales y desarrolladores, profesionales de TI |
| Escenarios |Autoservicio |Integraciones avanzadas |
| Herramienta de diseño |En el explorador y aplicación móvil, solo UI |En el explorador y [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [visualización del código](../logic-apps/logic-apps-author-definitions.md) disponible |
| Administración del ciclo de vida de las aplicaciones (ALM) |Diseño y pruebas en entornos no destinados a producción, paso a producción cuando sea posible. |DevOps: control del código fuente, pruebas, soporte técnico, automatización y facilidad de administración en [Administración de recursos de Azure](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Experiencia del administrador |Administración de directivas de prevención de pérdida de datos (DLP) y entornos de flujo, seguimiento de licencias [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Administración de grupos de recursos, conexiones, administración de acceso y registro [https://portal.azure.com](https://portal.azure.com) |
| Seguridad |Registros de auditoría de Seguridad y cumplimiento de Office 365, Prevención de pérdida de datos (DLP), [cifrado en reposo](https://wikipedia.org/wiki/Data_at_rest#Encryption) para datos confidenciales, etcétera. |Garantía de seguridad de Azure: [Seguridad de Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [registros de auditoría](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), y mucho más. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions frente a Trabajos web
Analizaremos Azure Functions y Azure App Service WebJobs juntos porque ambos son servicios de integración *Code First* y están diseñados para los desarrolladores. Le permiten ejecutar un script o un fragmento de código en respuesta a distintos eventos, como los [nuevos blobs de almacenamiento](functions-bindings-storage.md) o [una solicitud de WebHook](functions-bindings-http-webhook.md). Estas son sus similitudes: 

* Ambos se basan en [Azure App Service](../app-service/app-service-web-overview.md) y disfrutan de características como [control del código fuente](../app-service/app-service-continuous-deployment.md), [autenticación](../app-service/app-service-authentication-overview.md) y [supervisión](../app-service/web-sites-monitor.md).
* Ambos son servicios orientados al desarrollador.
* Ambos admiten lenguajes de script y de programación estándar.
* Ambos con compatibles con NuGet y NPM.

Functions es la evolución natural de WebJobs ya que toma lo mejor de WebJobs y lo mejora. Estas mejoras incluyen: 

* Modelo de aplicaciones [sin servidor](https://azure.microsoft.com/overview/serverless-computing/).
* Desarrollo, prueba y ejecución de código optimizados, directamente en el explorador.
* Integración incorporada con más servicios de Azure y servicios de terceros como [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Pago por uso, sin necesidad de pagar un [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* [Escalado dinámico](functions-scale.md)automático.
* Los clientes existentes de App Service aún pueden ejecutar un plan de App Service (para aprovechar las ventajas de recursos infrautilizados).
* Integración con Logic Apps.

En la tabla siguiente se resumen las diferencias entre Functions y WebJobs:

|  | Functions | Trabajos web |
| --- | --- | --- |
| Escalado |Escalado sin configuración |escalado con plan de App Service |
| Precios |Pago por uso o parte del plan de App Service |parte del plan de App Service |
| Tipo ejecución |desencadenada, programada (por desencadenador de temporizador) |desencadenada, continua, programada |
| Desencadenar eventos |[Temporizador](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-cosmosdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [colas y blobs de Azure Storage](functions-bindings-storage-blob.md), [colas y temas de Azure Service Bus](functions-bindings-service-bus.md) |[colas y blobs de Azure Storage](functions-bindings-storage-blob.md), [colas y temas de Azure Service Bus](functions-bindings-service-bus.md) |
| Desarrollo en el explorador |Compatible |No compatible |
| C# |Compatible |Compatible |
| F# |Compatible |No compatible |
| JavaScript |Compatible |Compatible |
| Java |Vista previa | No compatible |
| Bash |Experimental |Compatible |
| Scripting de Windows (.cmd, .bat) |Experimental |Compatible |
| PowerShell |Experimental |Compatible |
| PHP |Experimental |Compatible |
| Python |Experimental |Compatible |
| TypeScript |Experimental |No compatible |

El uso de Functions o WebJobs depende en última instancia de lo que esté haciendo con App Service. Si tiene una aplicación de App Service para la que quiere ejecutar fragmentos de código y los desea administrar juntos en el mismo entorno de DevOps, debería usar WebJobs. En los siguientes escenarios, use Functions:

* Desea ejecutar fragmentos de código en otros servicios de Azure o aplicaciones de terceros.
* Desea administrar por separado el código de integración de las aplicaciones de App Service.
* Desea llamar a fragmentos de código desde una aplicación Logic. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps y Functions juntos
Como se mencionó anteriormente, el servicio más adecuado dependerá de su situación. 

* Para una optimización empresarial sencilla, use Flow.
* Si el escenario de integración es demasiado avanzado para Flow, o si necesita funcionalidades de DevOps, use Logic Apps.
* Si en uno de los pasos del escenario de integración hace falta una gran transformación personalizada o código especializado, escriba una función y luego desencadénela como una acción en la aplicación lógica.

Puede llamar a una aplicación lógica en un flujo. También puede llamar a una función en una aplicación lógica y a una aplicación lógica en una función. La integración entre Flow, Logic Apps y Functions sigue mejorando con el tiempo. Puede crear algo en un servicio y usarlo en los otros. Por lo tanto, cualquier inversión que realice en estas tres tecnologías merecerá la pena.

## <a name="next-steps"></a>pasos siguientes
Comience con cada uno de estos servicios creando su primer flujo, aplicación lógica, aplicación de función o trabajo web. Haga clic en cualquiera de los siguientes vínculos:

* [Get started with Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/) (Introducción a Microsoft Flow)
* [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creación de su primera función de Azure](functions-create-first-azure-function.md)
* [Implementar trabajos web con Visual Studio](../app-service/websites-dotnet-deploy-webjobs.md)

O bien, obtenga más información sobre estos servicios de integración con los siguientes vínculos:

* [Leveraging Azure Functions &amp; Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Aprovechamiento de Azure Functions y Azure App Service para escenarios de integración) por Christopher Anderson
* [Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast en directo sobre Logic Apps](http://aka.ms/logicappslive)
* [Preguntas frecuentes sobre Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

