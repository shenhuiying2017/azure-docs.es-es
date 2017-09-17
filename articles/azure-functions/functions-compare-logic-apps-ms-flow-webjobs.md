---
title: "Elección entre Flow, Logic Apps, Functions y WebJobs | Microsoft Docs"
description: "Compare y contraste los servicios de integración en la nube de Microsoft y decida cuál usar."
services: functions,app-service\logic
documentationcenter: na
author: cephalin
manager: wpickett
tags: 
keywords: "microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, procesamiento de eventos, proceso dinámico, arquitectura sin servidor"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 43e01efaa16ff54d1f526076224979b7b9688e80
ms.contentlocale: es-es
ms.lasthandoff: 09/02/2017

---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Elección entre Flow, Logic Apps, Functions y WebJobs
En este artículo se comparan y contrastan los siguientes servicios en la nube de Microsoft. Todos ellos pueden resolver los problemas de integración y la automatización de los procesos empresariales:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service](../app-service-web/web-sites-create-web-jobs.md)

Todos estos servicios son útiles cuando se agrupan sistemas dispares. Todos pueden definir entradas, acciones, condiciones y salidas. Todos se pueden ejecutar según una programación o un desencadenador. Sin embargo, cada servicio tiene ventajas únicas y no se trata de saber cuál es el mejor, sino cuál es el más adecuado para su situación. Con frecuencia, la mejor manera de crear rápidamente una solución de integración completa y escalable es combinar estos servicios.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow frente a Logic Apps
Podemos hablar sobre Microsoft Flow y Azure Logic Apps de forma conjunta porque ambos son servicios de integración con una *configuración de línea base*. Facilitan la creación de flujos de trabajo y procesos, y la integración con varias aplicaciones SaaS y de empresa. 

* Flow se crea encima de Logic Apps
* Tienen el mismo diseñador de flujo de trabajo
* [conectores](../connectors/apis-list.md) que funcionan en uno también pueden funcionar en el otro

Flows permite a cualquier oficinista realizar integraciones sencillas (por ejemplo, obtener SMS para correos electrónicos importantes) sin pasar por los desarrolladores o el departamento de TI. Por otro lado, Logic Apps puede permitir integraciones avanzadas o críticas (por ejemplo, procesos B2B) donde se requieren procedimientos DevOps y de seguridad. Lo normal es que la complejidad de un flujo de trabajo crezca con el tiempo. En consecuencia, puede comenzar con un flujo al principio y luego convertirlo en una aplicación lógica cuando sea necesario.

La tabla siguiente le ayuda a determinar qué es lo mejor para una integración dada, si Flow o Logic Apps.

|  | Flujo | Aplicaciones lógicas |
| --- | --- | --- |
| Público |trabajadores de oficina, usuarios empresariales |profesionales de TI, desarrolladores |
| Escenarios |Autoservicio |Críticas |
| Herramienta de diseño |En el explorador y aplicación móvil, solo UI |En el explorador y [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [visualización del código](../logic-apps/logic-apps-author-definitions.md) disponible |
| DevOps |Ad hoc, desarrollo en producción |control del código fuente, prueba, soporte técnico, automatización y manejabilidad en [Administración de recursos de Azure](../logic-apps/logic-apps-arm-provision.md) |
| Experiencia del administrador |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Seguridad |Procedimientos estándar: [soberanía de datos](https://wikipedia.org/wiki/Technological_Sovereignty), [cifrado en reposo](https://wikipedia.org/wiki/Data_at_rest#Encryption) para datos confidenciales, etc. |Garantía de seguridad de Azure: [Seguridad de Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [registros de auditoría](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), y mucho más. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions frente a Trabajos web
Analizaremos Azure Functions y Azure App Service WebJobs juntos porque ambos son servicios de integración *Code First* y están diseñados para los desarrolladores. Le permiten ejecutar un script o un fragmento de código en respuesta a distintos eventos, como los [nuevos blobs de almacenamiento](functions-bindings-storage.md) o [una solicitud de WebHook](functions-bindings-http-webhook.md). Estas son sus similitudes: 

* Ambos se basan en [Azure App Service](../app-service/app-service-value-prop-what-is.md) y disfrutan de características como [control del código fuente](../app-service-web/app-service-continuous-deployment.md), [autenticación](../app-service/app-service-authentication-overview.md) y [supervisión](../app-service-web/web-sites-monitor.md).
* Ambos son servicios orientados al desarrollador.
* Ambos admiten lenguajes de script y de programación estándar.
* Ambos con compatibles con NuGet y NPM.

Functions es la evolución natural de WebJobs ya que toma lo mejor de WebJobs y lo mejora. Estas mejoras incluyen: 

* Desarrollo, prueba y ejecución de código optimizados, directamente en el explorador.
* Integración incorporada con más servicios de Azure y servicios de terceros como [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Pago por uso, sin necesidad de pagar un [plan de App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* [Escalado dinámico](functions-scale.md)automático.
* Los clientes existentes de App Service aún pueden ejecutar un plan de App Service (para aprovechar las ventajas de recursos infrautilizados).
* Integración con Logic Apps.

En la tabla siguiente se resumen las diferencias entre Functions y WebJobs:

|  | Funciones | Trabajos web |
| --- | --- | --- |
| Escalado |Escalado sin configuración |escalado con plan de App Service |
| Precios |Pago por uso o parte del plan de App Service |parte del plan de App Service |
| Tipo ejecución |desencadenada, programada (por desencadenador de temporizador) |desencadenada, continua, programada |
| Desencadenar eventos |[temporizador](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Notification Hubs](functions-bindings-notification-hubs.md), [Azure Service Bus](functions-bindings-service-bus.md), [Azure Storage](functions-bindings-storage.md) |[Azure Storage](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Azure Service Bus](../app-service-web/websites-dotnet-webjobs-sdk-service-bus.md) |
| Desarrollo en el explorador |admitido | no admitido |
| Scripts de ventana |experimental |admitido |
| PowerShell |experimental |admitido |
| C# |admitido |admitido |
| F# |admitido |no admitido |
| Bash |experimental |admitido |
| PHP |experimental |admitido |
| Python |experimental |admitido |
| JavaScript |admitido |admitido |

El uso de Functions o WebJobs depende en última instancia de lo que esté haciendo con App Service. Si tiene una aplicación de App Service para la que quiere ejecutar fragmentos de código y los desea administrar juntos en el mismo entorno de DevOps, debería usar WebJobs. En los siguientes escenarios, use Functions:

* Desea ejecutar fragmentos de código en otros servicios de Azure o aplicaciones de terceros.
* Desea administrar por separado el código de integración de las aplicaciones de App Service.
* Desea llamar a fragmentos de código desde una aplicación Logic. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps y Functions juntos
Como se mencionó anteriormente, el servicio más adecuado dependerá de su situación. 

* Para una optimización empresarial sencilla, use Flow.
* Si su escenario de integración es demasiado avanzado para Flow, o si necesita funcionalidades de DevOps y cumplimiento de seguridad, use entonces Logic Apps.
* Si en uno de los pasos del escenario de integración hace falta una gran transformación personalizada o código especializado, escriba una función y luego desencadénela como una acción en la aplicación lógica.

Puede llamar a una aplicación lógica en un flujo. También puede llamar a una función en una aplicación lógica y a una aplicación lógica en una función. La integración entre Flow, Logic Apps y Functions sigue mejorando con el tiempo. Puede crear algo en un servicio y usarlo en los otros. Por lo tanto, cualquier inversión que realice en estas tres tecnologías merecerá la pena.

## <a name="next-steps"></a>Pasos siguientes
Comience con cada uno de estos servicios creando su primer flujo, aplicación lógica, aplicación de función o trabajo web. Haga clic en cualquiera de los siguientes vínculos:

* [Get started with Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/) (Introducción a Microsoft Flow)
* [Creación de una aplicación lógica](../logic-apps/logic-apps-create-a-logic-app.md)
* [Creación de su primera función de Azure](functions-create-first-azure-function.md)
* [Implementar trabajos web con Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

O bien, obtenga más información sobre estos servicios de integración con los siguientes vínculos:

* [Leveraging Azure Functions & Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Aprovechamiento de Azure Functions y Azure App Service para escenarios de integración) por Christopher Anderson
* [Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Webcast en directo sobre Logic Apps](http://aka.ms/logicappslive)
* [Preguntas frecuentes sobre Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
* [Recursos de documentación de WebJobs de Azure](../app-service-web/websites-webjobs-resources.md)


